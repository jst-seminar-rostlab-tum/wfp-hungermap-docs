# How it works

**Author:** `Haidong Xu`

## Infrastructure Layer Components
### Chatbot Context
- **File**: ChatbotContext.tsx
- **Description**: This file is defining a context for managing the state and operations related to a chatbot in this project, it provides context for managing chatbot state and operations and manages the state of the chatbot, including chats, current chat index.
- **State Variables**:
  - `chats` : Array of chat objects.
  - `currentChatIndex` : Index of the currently active chat.
  - `isOpen` : Boolean indicating if the chat interface is open.
  - `isMobile` : Boolean indicating if the device is mobile.
  - `isSidebarOpen` : Boolean indicating if the sidebar is open.

  - `setChats` : A function to update the `chats` state.
  - `setCurrentChatIndex` : A function to update the `currentChatIndex` state.
  - `setIsOpen` : A function to update the `isOpen` state.
  - `setIsSidebarOpen` : A function to update the `isSidebarOpen` state.

- **Key Methods**:
  - `openChat(chatIndex: number)` : A function to open a chat by its index.
  - `startNewChat(newChat?: IChat)` : A function to start a new chat, optionally with a provided chat object.
  - `initiateChatAboutReport(countryName: string, reportURL: string)` : A function to initiate a chat about a specific report.

This context will be used to provide and manage the state and operations related to the chatbot throughout the application.

### Chatbot Repository
- **File**: ChatbotRepositoryImpl.ts
- **Description**: Implements the ChatbotRepository interface for interacting with the chatbot backend.
- **Key Methods**:
  - `testHealth(): Promise<boolean> ` : Tests the health of the chatbot service.
  - `sendMessage(message: string, options: Partial<Omit<QueryRequest, 'query'>>): Promise<QueryResponse>` : Sends a message to the chatbot service.

## Presentation Layer Components
### HungerMapChatbot
- **File**: Chatbot.tsx
- **Description**: Main component for the HungerMap Chatbot. This is the component you should use when you want to show chatbot in web page. These variables and methods manage the state and behavior of the chatbot interface, providing a responsive and interactive user experience.
- **Key Variables**:
  - `inputRef: React.RefObject<HTMLTextAreaElement>` : A reference to the `<textarea>` element used for user input.
  - `chatEndRef: React.RefObject<HTMLDivElement>` : A reference to the `<div>` element used to scroll to the end of the chat.
  - `isResponseAnimated: boolean` : A state variable indicating whether the response animation is active.
  - `isUserMessageSent: boolean` : A state variable indicating whether a user message has been sent.
  - `currentChatIndex: number` : The index of the currently active chat.
  - `chats: IChat[]` : An array of chat objects representing the chat history.

- **Key Methode**:
  - `toggleChat` : Toggles the chat interface open or closed. Handles different behaviors for mobile and non-mobile devices.
  - `toggleFullScreen` : Toggles full-screen mode for the chat interface, applicable only for non-mobile devices.
  - `setTypingStatus(chatIndex: number, isTyping: boolean)` : Updates the typing status of a specific chat.
  - `handleSubmit(keyboardEvent: KeyboardEvent)` : Handles the submission of a user message when the Enter key is pressed.
  - `onTypingComplete` : Sets the typing status to false and enables response animation.
  - `handleAIResponse(text: string): Promise<void>` : is function is responsible for handling the response from the AI chatbot.

### ChatbotSidebar
- **File**: ChatbotSidebar.tsx
- **Description**: Sidebar component for displaying chat history and options.
- **Key Variables & Methode**:
  - `isFullScreen` : A boolean indicating if the sidebar is in full-screen mode.
  - `chats` : An array of chat objects.
  - `currentChatIndex` : The index of the currently selected chat.
  - `onSelectChat: (index: number) => void` : A function to handle chat selection.
  - `onStartNewChat: (newChat?: IChat) => void` : A function to handle starting a new chat.

### TypingText
- **File**: TypingText.tsx
- **Description**: Component for displaying typing text animation, it handles a typing animation effect where text is progressively displayed character by character and animates the display of text as if it is being typed.
