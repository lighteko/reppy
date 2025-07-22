# **Reppy: Your Personal AI Fitness Companion**

## **The Reppy Philosophy: A Plan That Lives and Breathes With You**

Imagine a personal trainer who is available 24/7, knows your goals, understands your limits, and adapts your fitness plan not just week-to-week, but day-to-day. This is Reppy.

Reppy is more than just a fitness app; it's an intelligent companion designed to remove every obstacle between you and your health goals. Our core philosophy is that your fitness plan shouldn't be a static document you struggle to follow. It should be a living, breathing guide that evolves with you. Whether you're feeling energetic, tired, or just need a change, Reppy listens, understands, and adapts, ensuring every workout is effective, engaging, and perfectly suited for you.

## **Your Fitness Journey with Reppy**

From the moment you start, every part of the Reppy experience is designed to be seamless, motivating, and centered around you.

### **A Tailor-Made Beginning**

Your journey begins with a simple, friendly conversation. Reppy will ask you about your aspirations—whether you want to build muscle, improve endurance, or simply feel healthier. You'll share your schedule, what equipment you have access to (even if it's none at all\!), and a little about yourself.

There are no confusing menus or generic plans to choose from. Based on this initial chat, Reppy instantly crafts your very first, completely unique weekly workout plan. This isn't a template; it's Version 1 of your personal fitness journey, designed exclusively for you.

### **Meet Your AI Coach**

The heart of Reppy is your ongoing conversation with your AI coach. This is where the magic happens. You can talk to Reppy just like you would a human trainer:

* **Feeling ambitious?** Say, "I want to add an extra day of cardio next week."  
* **Feeling sore?** "My legs are really tired today, can we do something lighter?"  
* **Curious?** "What's the best way to warm up for a heavy lift?"  
* **Need a change?** "I'm getting bored of squats, can you suggest an alternative?"

Reppy understands and responds in real-time, adjusting your plan, offering encouragement, and answering your questions instantly.

### **Workout with Confidence**

When it's time to train, Reppy provides a clear, focused guide for your day's session. Each workout screen is designed to eliminate guesswork, showing you exactly what to do, how to do it, and for how long.

You'll see a simple, ordered list of exercises, complete with video demonstrations so you can perfect your form. As you move through your workout, Reppy tracks your sets, reps, and rest times, letting you focus on your effort, not on the logistics.

### **A Smarter Coach Every Day**

Reppy is designed to learn. After a workout, you can give simple feedback with a tap—a thumbs-up on an exercise you loved, or a thumbs-down on one that felt off.

Combine this with your conversational feedback, and Reppy gets progressively smarter. It learns which exercises you enjoy, where you're hitting plateaus, and how to best motivate you. This means Version 5 of your workout plan will be far more attuned to you than Version 1, creating a truly personalized and effective long-term fitness partnership.

## **The Technology Behind Reppy**

Reppy's seamless experience is powered by a modern, three-part architecture designed for security, scalability, and intelligence.

### **System Architecture Diagram**
```text
+------------------------+      (HTTPS Request)      +-------------------------+      (Secure Server-to-Server)      +-----------------------+
|                        | ------------------------> |                         | -----------------------------------> |                       |
|  React Native Frontend |                           |  Express API Server     |                                      |  FastAPI AI Server    |
|  (User Interface)      | <------------------------ |  (Primary Backend)      | <----------------------------------  |  (AI Engine)          |
|                        |      (Streaming SSE)      |                         |      (Streaming Response)            |                       |
+------------------------+                           +-----------+-------------+                                      +-----------+-----------+
                                                                  |                                                              |
                                                                  | (SQL Queries)                                                | (Vector Search/CRUD)
                                                                  v                                                              v
                                                      +-----------+-------------+                                      +-----------+-----------+
                                                      |                         |                                      |                       |
                                                      |   PostgreSQL Database   |                                      |   Qdrant Vector DB    |
                                                      |   (System of Record)    |                                      |   (AI Knowledge Base) |
                                                      +-------------------------+                                      +-----------------------+

```
### **Frontend (React Native App)**

The frontend is the user's entire world. It handles the user interface for onboarding, the AI chat, workout execution, and profile management. It communicates securely with the Express backend.

### **Backend (Express API Server)**

The Express server is the central gateway and the "System of Record." It manages all user authentication, profile data, and workout history in the **PostgreSQL** database. It acts as a secure proxy, gathering the necessary data before making requests to the AI engine.

### **AI Engine (FastAPI Server)**

The FastAPI server is the brain. It is a specialized microservice that handles all complex AI tasks. It manages the **Qdrant** vector database, which stores exercise knowledge and user feedback. This server performs the Retrieval-Augmented Generation (RAG) to understand user requests, retrieve relevant context, and generate intelligent, personalized responses and workout plans.
