# Domain Driven Design

Author: `Ahmet Selman Güclü`

Domain-Driven Design is implemented through a clear separation of concerns and a structured approach to managing business logic. The chatbot feature serves as an excellent example of how DDD principles are applied throughout the application, as it involves user interaction, state management, API communication, and complex business logic.

## Directory Structure and Layer Details

### 1. Domain Layer (`/src/domain`)

The domain layer represents the core business concepts and rules. For the chatbot, this includes the fundamental structures of what a chat is, how messages are formatted, and how the chat state is managed.

#### a. Entities (`/src/domain/entities`)

Defines the core data structures and types. For the chatbot, this includes the structure of messages and chat sessions:

```typescript
// src/domain/entities/chatbot/Chatbot.ts
export interface IMessage {
  id: string;
  content: string;
  role: SenderRole.USER | SenderRole.ASSISTANT;
  dataSources?: string[];
}

export interface IChat {
  id: string;
  title: string;
  reports_country_name?: string;
  isReportStarter?: boolean;
  messages: IMessage[];
  isTyping: boolean;
  timestamp: number;
}
```

This entity definition ensures that all parts of the application have a consistent understanding of what a chat consists of. The `isTyping` flag, for example, helps manage the UI state during message exchanges.

#### b. Contexts (`/src/domain/contexts`)

Provides state management and business operations throughout the application. The ChatbotContext manages the entire chat state and operations:

```typescript
// src/domain/contexts/ChatbotContext.tsx
interface ChatbotContextType {
  chats: IChat[];
  setChats: React.Dispatch<React.SetStateAction<IChat[]>>;
  currentChatIndex: number;
  setCurrentChatIndex: React.Dispatch<React.SetStateAction<number>>;
  openChat: (chatIndex: number) => void;
  startNewChat: (newChat?: IChat) => void;
  initiateChatAboutReport: (countryName: string, report: string) => Promise<void>;
  isOpen: boolean;
  isSidebarOpen: boolean;
  setIsOpen: React.Dispatch<React.SetStateAction<boolean>>;
  setIsSidebarOpen: React.Dispatch<React.SetStateAction<boolean>>;
}
```

This context provides all the necessary operations for managing chat sessions, including creating new chats, switching between chats, and handling the UI state of the chatbot window.

#### c. Repositories (`/src/domain/repositories`)

Defines interfaces for data access. For the chatbot, this includes methods for communicating with the chat API:

```typescript
// src/domain/repositories/ChatbotRepository.ts
interface ChatbotRepository {
  testHealth(): Promise<boolean>;
  sendMessage(message: string, options: Partial<Omit<QueryRequest, 'query'>>): Promise<QueryResponse>;
}
```

This interface defines the contract for how the application communicates with the chatbot backend, without specifying the implementation details.

### 2. Infrastructure Layer (`/src/infrastructure`)

The infrastructure layer implements the actual communication with external services. For the chatbot, this means implementing the API calls defined in the repository interface:

```typescript
// src/infrastructure/repositories/ChatbotRepositoryImpl.ts
export default class ChatbotRepositoryImpl implements ChatbotRepository {
  async testHealth(): Promise<boolean> {
    try {
      const response = await fetch(`${process.env.NEXT_PUBLIC_CHATBOT_API_URL}/health`);
      return response.ok;
    } catch {
      return false;
    }
  }

  async sendMessage(message: string, options: Partial<Omit<QueryRequest, 'query'>>): Promise<QueryResponse> {
    try {
      const payload: QueryRequest = {
        chatId: options.chatId || uuid(),
        reports_country_name: options.reports_country_name || '',
        query: message,
        // ... configuration options
      };

      const response = await fetch(`${process.env.NEXT_PUBLIC_CHATBOT_API_URL}/query`, {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify(payload),
      });
      // ... error handling and response processing
    }
  }
}
```

This implementation handles the actual HTTP requests to the chatbot API, including error handling and response processing. It's isolated from the rest of the application, making it easy to modify or replace without affecting other components.

### 3. Operations Layer (`/src/operations`)

The operations layer contains complex business logic that operates on domain entities. For the chatbot, this includes message processing and chat management:

```typescript
// src/operations/chatbot/Chatbot.ts
export default class ChatbotOperations {
  static processInput(submitEvent: React.FormEvent, chats: IChat[], currentChatIndex: number, text: string): IChat[] {
    submitEvent.preventDefault();
    if (chats[currentChatIndex].isTyping) return chats;
  
    if (text.trim()) {
      const updatedChats = structuredClone(chats);
      // Add user message to chat
      updatedChats[currentChatIndex].messages.push({
        id: crypto.randomUUID(),
        content: text,
        role: SenderRole.USER
      });
      // Update chat title if it's the first message
      if (updatedChats[currentChatIndex].title === `Chat ${updatedChats[currentChatIndex].id}`) {
        updatedChats[currentChatIndex].title = text.slice(0, 30) + (text.length > 30 ? '...' : '');
      }
      return updatedChats;
    }
    return chats;
  }

  static loadChatsFromStorage(): IChat[] {
    // Handles chat persistence and loading
    // ... storage logic
  }
}
```

This class contains pure business logic for processing chat messages and managing chat state, independent of UI or API concerns. It handles things like preventing duplicate submissions while typing and managing chat titles.

### 4. Components Layer (`/src/components`)

The components layer implements the UI using all the previous layers. For the chatbot, this means rendering the chat interface and handling user interactions:

```typescript
// src/components/Chatbot/Chatbot.tsx
export default function HungerMapChatbot() {
  const {
    chats,
    currentChatIndex,
    isOpen,
    isSidebarOpen,
    setIsSidebarOpen
  } = useChatbot();  // Uses the ChatbotContext

  return (
    <div>
      {/* Mobile overlay handling */}
      {isMobile && isSidebarOpen && (
        <div
          className="fixed inset-0 z-10 opacity-50 bg-white dark:bg-black"
          onClick={() => setIsSidebarOpen((prev) => !prev)}
        />
      )}
      <ScrollShadow hideScrollBar className="w-full h-full">
        {/* Welcome message or chat messages */}
        {chats[currentChatIndex].messages.length === 0 ? (
          <div className="flex flex-col items-center pt-4 space-y-4">
            <p className="text-center text-xl">{WELCOME_MESSAGE}</p>
            {/* ... rest of the component */}
          </div>
        )}
      </ScrollShadow>
    </div>
  );
}
```

This component brings together all the layers: it uses the ChatbotContext for state management, triggers operations for message processing, and ultimately renders the UI for user interaction.

## Dependency Injection

The dependency injection system ties all these layers together while maintaining loose coupling:

```typescript
// src/container.tsx
class Container {
  private dependencies: { [key: string]: unknown } = {};

  register<T>(key: string, dependency: T) {
    this.dependencies[key] = dependency;
  }

  resolve<T>(key: string): T {
    if (Object.prototype.hasOwnProperty.call(this.dependencies, key)) {
      return this.dependencies[key] as T;
    }
    throw new Error(`Dependency '${key}' is not registered.`);
  }
}

const container = new Container();
container.register('ChatbotRepository', new ChatbotRepositoryImpl());
```

This system allows the chatbot feature to be easily tested and modified by swapping implementations while maintaining the same interfaces. For example, the ChatbotRepository could be replaced with a mock implementation for testing without changing any other code.

## Summary

The chatbot example demonstrates how DDD principles help organize complex features, where each layer has a specific responsibility in the chatbot feature:

1.**Domain Layer** (`/src/domain/`)

- Defines what a chat and message look like (`entities/chatbot/Chatbot.ts`)
- Manages chat state and operations (`contexts/ChatbotContext.tsx`)
- Declares how to interact with the chat API (`repositories/ChatbotRepository.ts`)

2.**Infrastructure Layer** (`/src/infrastructure/`)

- Implements actual API communication (`repositories/ChatbotRepositoryImpl.ts`)
- Handles HTTP requests, responses, and error management

3.**Operations Layer** (`/src/operations/`)

- Contains chat-specific business logic (`chatbot/Chatbot.ts`)
- Processes messages, manages chat storage, handles chat titles

4.**Components Layer** (`/src/components/`)

- Renders the chat interface (`Chatbot/Chatbot.tsx`)
- Manages chat history sidebar (`Chatbot/ChatbotSidebar.tsx`)
- Integrates all layers for user interaction

This separation makes the code more maintainable and testable by:

- Isolating the chat UI from the business logic
- Separating API communication from state management
- Making dependencies explicit and replaceable
- Keeping related functionality grouped together
