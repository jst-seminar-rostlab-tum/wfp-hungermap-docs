---
sidebar_position: 3
---

## Architecture
### Container
- **File**: container.tsx
- **Description**: Manages dependency injection for various repositories used in the project.
- **Key Methods**:
  - `register<T>(key: string, dependency: T)` : Registers a dependency.
  - `resolve<T>(key: string): T` : Resolves a dependency.

### Chatbot Context
- **File**: ChatbotContext.tsx
- **Description**: Provides context for managing chatbot state and operations.
- **Key Methods**: 
- `openChat(chatIndex: number) : Opens a chat by index.`
- `startNewChat(newChat?: IChat): Starts a new chat.`
- `initiateChatAboutReport(countryName: string, reportURL: string): Initiates a chat about a specific report.`

### Chatbot Repository
- **File**: ChatbotRepositoryImpl.ts
- **Description**: Implements the ChatbotRepository interface for interacting with the chatbot backend.
- **Key Methods**:
- `testHealth() : Tests the health of the chatbot service.`
- `sendMessage(message: string, options: Partial<Omit<QueryRequest, 'query'>>): Promise<QueryResponse>: Sends a message to the chatbot service.`

## Components
### HungerMapChatbot
- **File**: Chatbot.tsx
- **Description**: Main component for the HungerMap Chatbot.
- **Key Features**:
  - **Toggle Chat**: Opens and closes the chat interface.
  - **Full-Screen Mode**: Toggles full-screen mode for the chat interface.
  - **Message Handling**: Processes user input and handles AI responses.
  - **Typing Indicators**: Shows typing indicators when the AI is generating a response.
  - **Sidebar**: Displays a sidebar with chat history and options to start new chats.

### ChatbotSidebar
- **File**: ChatbotSidebar.tsx
- **Description**: Sidebar component for displaying chat history and options.
- **Key Features**:
  - **Chat Selection**: Allows users to select a chat from the history.
  - **New Chat**: Provides an option to start a new chat.

### TypingText
- **File**: TypingText.tsx
- **Description**: Component for displaying typing text animation.
- **Key Features**:
  - **Typing Animation**: Animates the display of text as if it is being typed.

## State Management
### Chatbot Context
- **File**: ChatbotContext.tsx
- **Description**: Manages the state of the chatbot, including chats, current chat index, and UI states.
- **State Variables**:
- `chats : Array of chat objects.`
- `currentChatIndex : Index of the currently active chat.`
- `isOpen : Boolean indicating if the chat interface is open.`
- `isMobile : Boolean indicating if the device is mobile.`
- `isSidebarOpen : Boolean indicating if the sidebar is open.`

### Local State
- **File**: Chatbot.tsx
- **Description**: Manages local state within the 

### HungerMapChatbot component

- **State Variables**:
- `isFullScreen`: Boolean indicating if the chat is in full-screen mode.
- `isUserMessageSent`: Boolean indicating if a user message has been sent.
- `input`: String containing the current user input.
- `isResponseAnimated`: Boolean indicating if the response animation is active.

### Effects
- **File**: Chatbot.tsx
- **Description**: Uses React hooks to manage side effects.
- **Key Effects**:
  - **Save Chats**: Saves chats to local storage whenever they change.
  - **Scroll to End**: Scrolls to the end of the chat when new messages are added.
  - **Handle AI Response**: Triggers AI response handling when a user message is sent.
  - **Auto-Resize Input**: Automatically resizes the input textarea based on content length.
