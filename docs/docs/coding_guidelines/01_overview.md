# Overview

Author: `Ahmet Selman Güclü`

The WFP Hunger Map project follows a structured approach to development that combines Domain-Driven Design (DDD) principles with strict styling guidelines. This ensures maintainable, scalable code while preserving consistency across the codebase.

## Project Structure

The project is divided into two main architectural aspects:

### 1. Domain-Driven Design

The implementation of DDD separates the application into distinct domains, each handling specific business logic. The structure is organized into three main folders:

- `domain/`: Contains entities, interfaces, and repositories
- `infrastructure/`: Houses API implementations and external service integrations
- `operations/`: Manages business logic and complex operations

### 2. Styling and Code Quality

Code quality is maintained through:

- ESLint configuration based on Airbnb's React style guide
- Prettier for consistent code formatting
- Husky for pre-commit hooks
- Tailwind CSS for styling
- NextUI for component theming

## Example: Chatbot Implementation

Throughout this documentation, the chatbot feature serves as a practical example of how these principles are applied. The chatbot demonstrates:

1. Separation of concerns using DDD
2. Consistent styling implementation
3. State management patterns
4. Component structure

The chatbot's file structure illustrates these principles:

```
src/
├── domain/
│   ├── entities/
│   │   └── chatbot/
│   │       └── Chatbot.ts              # Core types and interfaces
│   ├── contexts/
│   │   └── ChatbotContext.tsx          # State management
│   └── repositories/
│       └── ChatbotRepository.ts        # Data access interface
├── infrastructure/
│   └── repositories/
│       └── ChatbotRepositoryImpl.ts    # API implementation
├── operations/
│   └── chatbot/
│       └── Chatbot.ts                  # Business logic
└── components/
    └── Chatbot/
        ├── Chatbot.tsx                 # Main component
        └── ChatbotSidebar.tsx          # UI subcomponent
```

Each subsequent section explores these aspects in detail, using the chatbot as a concrete example of the project's architectural decisions.
