# **Reppy: Project & Feature Specification**

Version: 1.0  
Date: July 18, 2025

## **Overview**

Reppy is an AI-powered personal trainer designed to provide users with genuinely personalized, adaptive, and conversational fitness coaching. The system is built on a modern, three-part architecture consisting of a React Native frontend, an Express primary backend, and a dedicated FastAPI AI server that leverages a Retrieval-Augmented Generation (RAG) engine. This document outlines the specific features and technical responsibilities of each component.

## **1\. Frontend (React Native App)**

The frontend is the user's sole interaction point with the Reppy ecosystem. It is responsible for delivering a seamless, intuitive, and engaging user experience.

### **1.1. User Authentication & Onboarding**

* **Sign-Up/Login:** Users can create an account via email/password or social providers (Google/Apple). The app will manage token-based sessions for persistent login.  
* **Onboarding Questionnaire:** Upon first login, the user is guided through a multi-step onboarding process to collect critical data for initial routine generation:  
  * Fitness Goal (e.g., Bulking, Cutting, Endurance)  
  * Experience Level (Beginner, Intermediate, Advanced)  
  * Weekly Availability (Number of days)  
  * Personal Profile (Age, Sex, Height, Weight)  
  * Available Gym Equipment (Multi-select list)  
* **Initial Routine Generation:** After onboarding, the app sends the collected data to the backend to trigger the generation of the user's first weekly routine.

### **1.2. The AI Coach (Chat Interface)**

* **Primary Interaction Model:** The core of the app is a chat interface where the user interacts with their AI coach, "Reppy."  
* **Conversational Commands:** Users can send natural language messages to:  
  * Request a new or updated routine ("Generate a new plan for next week").  
  * Provide unstructured feedback ("My shoulder felt sore during the overhead press").  
  * Ask fitness-related questions ("What's the difference between a pyramid set and a drop set?").  
  * Engage in small talk.  
* **Streaming Responses:** The app will handle Server-Sent Events (SSE) to display the AI's responses token-by-token for a real-time chat feel.

### **1.3. Routine Execution & Tracking**

* **Calendar View:** A weekly calendar displays the user's scheduled workouts and rest days.  
* **Daily Workout Screen:** Tapping a workout day opens a detailed view:  
  * **Workout Title:** e.g., "Push Day \- Chest & Triceps".  
  * **Exercise List:** An ordered list of exercises for the day.  
  * **Exercise Details:** Each exercise includes its name, set type (e.g., Pyramid), and a detailed breakdown of sets, reps, weight, and rest times.  
  * **Embedded Demos:** A link or embedded player (YouTube/GIF) shows how to perform the exercise.  
* **Active Workout Mode:** A "Start Workout" button initiates a tracker with timers for sets and rest periods, allowing users to log their completed reps and weight for each set.

### **1.4. Structured Feedback System**

* **Post-Workout Prompts:** After completing a workout, the app will present simple UI elements (e.g., 👍/👎 buttons) for key exercises to gather explicit, structured feedback.  
* **Targeted API Calls:** This interaction will trigger a direct API call to the backend to log this structured feedback, bypassing the AI interpretation layer.

### **1.5. Profile and Settings**

* **User Profile:** A screen where users can view and edit their personal information, fitness goals, and equipment list.  
* **Preferences:** Users can manage settings such as unit system (kg/lbs) and push notification preferences.

### **1.6. Push Notifications**

* The app will handle push notifications for:  
  * Daily workout reminders.  
  * Encouragement to maintain a workout streak.  
  * Proactive check-in messages from the AI coach.

## **2\. Backend (Express API Server)**

The Express server is the central API gateway and the "System of Record." It handles all user-facing requests, manages the primary database, and acts as a secure proxy to the AI server.

### **2.1. Core Responsibilities**

* **Primary API Gateway:** Exposes all public-facing REST endpoints.  
* **User & Auth Management:** Handles all user registration, login, and session token management.  
* **Database Owner (PostgreSQL):** It is the only component authorized to perform CRUD operations on the PostgreSQL database.  
* **Data Aggregator & Proxy:** Gathers necessary data from PostgreSQL before making secure, server-to-server requests to the FastAPI AI Engine.

### **2.2. Database Management (PostgreSQL)**

* Manages all data persistence according to the finalized DDL, including:  
  * repy\_user\_l and repy\_user\_pref\_l  
  * repy\_schedule\_l and repy\_routine\_l  
  * repy\_exercise\_record\_l (workout history)  
  * repy\_chat\_message\_l  
  * repy\_feedback\_l (the permanent log of all user feedback)

### **2.3. Key API Endpoints**

* **/users:** CRUD operations for user profiles and settings.  
* **/auth:** Endpoints for login, logout, and token refresh.  
* **/routines:** Endpoints to create, read, and update workout routines and logs in PostgreSQL.  
* **/feedback (Structured):** An endpoint that receives structured feedback from the app, saves it to PostgreSQL, and then calls the corresponding FastAPI endpoint to update Qdrant.  
* **/ai/chat (Proxy):** A secure endpoint that:  
  1. Receives a chat request from the user.  
  2. Fetches the user's profile and recent performance history from PostgreSQL.  
  3. Aggregates this data into a single payload (ReppyRequest).  
  4. Forwards this payload to the FastAPI server's /chat endpoint.  
  5. Streams the AI's response back to the client.

## **3\. AI Engine (FastAPI Server)**

The FastAPI server is a specialized, private microservice. Its sole purpose is to handle all AI-related tasks and manage the vector database. It does not interact with the PostgreSQL database directly.

### **3.1. Core Responsibilities**

* **AI Logic Execution:** Contains all LangChain chains and RAG logic.  
* **Vector DB Owner (Qdrant):** It is the only component authorized to perform CRUD operations on the Qdrant vector database.  
* **Embedding Generation:** Manages the generation of vector embeddings using OpenAI's models.

### **3.2. Vector DB Management (Qdrant)**

* **exercise\_knowledge Collection:** Stores vector embeddings of all exercises, used for semantic search during routine generation.  
* **routine\_feedback\_log Collection:** Stores user feedback data to allow the RAG engine to retrieve context on user sentiment.

### **3.3. Key API Endpoints**

* **/chat:** The main RAG endpoint.  
  * **Receives** a ReppyRequest from the Express server.  
  * **Routes** the user's intent (e.g., routine\_generation, coaching, feedback\_expression).  
  * **Retrieves** relevant exercise candidates and user feedback from Qdrant.  
  * **Augments** a prompt with the retrieved context.  
  * **Generates** a response, using Pydantic tools to create structured output (like WeeklyRoutine) when necessary.  
  * **Streams** the response back to the Express server.  
* **/feedback:** An internal endpoint for Express to call.  
  * Receives structured feedback data.  
  * Upserts the data into the routine\_feedback\_log collection in Qdrant.  
* **/admin/exercises:** A set of secure, key-protected endpoints for admin use.  
  * Allows creating, updating, or deleting exercises in the exercise\_knowledge collection.  
  * When creating an exercise, it uses an OpenAI embedding model to generate a vector from the exercise's name and description before saving it to Qdrant.
