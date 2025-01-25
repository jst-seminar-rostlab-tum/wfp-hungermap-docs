---
sidebar_position: 1
---

# Introduction

**Author:** `Haidong Xu`

## Background
The HungerMap Chatbot is designed to assist users in obtaining information related to hunger and food security. It leverages AI to provide responses to user queries, making it easier for users to access relevant information quickly. The chatbot can handle various types of queries, including those related to specific country reports, climate change impacts, and initiatives by organizations like the World Food Programme (WFP).

## Objectives

1. User Interaction
Search and Filter: Users can search for country-specific reports using a search bar. The search functionality filters the available data based on the user's input. Then the user can initiate a chat with the chatbot to ask questions or get more information about specific topics.

2. Chat Management
Chat State Management: The chatbot maintains the state of ongoing chats, including the current chat index, chat messages, and whether the chat is open or closed. New Chat Creation: Users can start new chats, and the chatbot will manage the creation and storage of these chats.

3. AI Response Handling
Message Processing: The chatbot processes user input and sends it to the AI backend for generating responses. Contextual Responses: The chatbot can handle contextual responses, especially for country-specific reports. It uses the context of the report to generate more relevant answers. Error Handling: The chatbot includes error handling to manage issues like server connectivity problems. It provides user-friendly error messages when such issues occur.

4. Integration with Backend Services
Health Check: The chatbot periodically checks the health of the backend services to ensure they are operational. Message Sending: The chatbot sends user messages to the backend AI service and retrieves responses. It supports various options for message sending, including context and previous messages.

5. User Experience Enhancements
Typing Indicators: The chatbot shows typing indicators to let users know when the AI is generating a response. Auto-Scrolling: The chat interface auto-scrolls to the latest message, ensuring users always see the most recent responses. Full-Screen Mode: Users can toggle full-screen mode for an immersive chat experience.

6. Data Persistence
Local Storage: The chatbot saves chat data to local storage, ensuring that users can resume their chats even after refreshing the page or closing the browser. Chat History: Users can view their chat history and select previous chats from a sidebar.

7. Accessibility and Responsiveness
Mobile Support: The chatbot is designed to be responsive and works seamlessly on mobile devices. Keyboard Accessibility: Users can interact with the chatbot using keyboard shortcuts, enhancing accessibility for users with disabilities.

8. Customization and Extensibility
Custom Prompts: The chatbot includes default prompts that users can click to quickly ask common questions. Extensible Architecture: The chatbot's architecture is designed to be extensible, allowing for easy addition of new features and integrations.

9. Security and Privacy
Data Security: The chatbot ensures that user data is securely transmitted and stored. Privacy Compliance: The chatbot adheres to privacy regulations, ensuring that user data is handled responsibly.

By achieving these objectives, the HungerMap Chatbot aims to provide a seamless and informative user experience, helping users access critical information about hunger and food security efficiently.