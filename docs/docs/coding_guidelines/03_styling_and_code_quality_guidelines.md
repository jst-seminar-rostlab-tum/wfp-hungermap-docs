# Styling and Code Quality Guidelines

Author: `Ahmet Selman Güclü`

The WFP Hunger Map project implements a comprehensive styling and code quality system. This document outlines the tools, configurations, and best practices used to maintain consistent code style and quality across the project.

## Code Formatting and Quality Tools

### ESLint

The project uses ESLint with Airbnb's React style guide (configured in `.eslintrc.json`). Key features include:

- TypeScript integration
- Import sorting
- React best practices
- Accessibility rules

Example of ESLint rules in action:

```typescript
// ❌ Don't: Unorganized imports
import { Button } from '@nextui-org/react';
import React from 'react';
import clsx from 'clsx';
import { useChatbot } from '@/domain/contexts/ChatbotContext';

// ✅ Do: Organized imports following the project's style
import { useState } from 'react';
import clsx from 'clsx';
import { Button } from '@nextui-org/react';
import { useChatbot } from '@/domain/contexts/ChatbotContext';
```

### Prettier

Prettier is configured in VS Code (see `.vscode/settings.json`) to automatically format code on save. Key settings include:

- 2-space indentation
- Single quotes
- Trailing commas in ES5
- No semicolons

Example of Prettier formatting:

```typescript
// ❌ Don't: Inconsistent formatting
function Example({name,age}){
    return <div className='container'>
        <h1>Hello, {name}</h1>
    <p>Age: {age}</p>
        </div>
}

// ✅ Do: Consistent formatting (auto-fixed by Prettier)
function Example({ name, age }) {
  return (
    <div className="container">
      <h1>Hello, {name}</h1>
      <p>Age: {age}</p>
    </div>
  );
}
```

### Husky

The project uses Husky with commitlint to enforce commit message standards:

- `feat:` for new features
- `fix:` for bug fixes
- `docs:` for documentation changes

Example commit messages:

```bash
# ❌ Don't:
git commit -m "updated chatbot"
git commit -m "feat: chatbot updated with new features and fixes"

# ✅ Do:
git commit -m "feat: add message history to chatbot"
git commit -m "fix: resolve chatbot response delay"
```

## Styling Implementation

### NextUI

The project uses NextUI (@nextui-org/theme) as its component library, providing:

- Pre-built accessible components
- Consistent theming
- Dark mode support
- Integration with Tailwind CSS

Example of NextUI usage:

```typescript

// ❌ Don't: Custom button implementation

<buttonclassName="px-4 py-2 bg-blue-500 rounded-lg">

Clickme

</button>


// ✅ Do: NextUI component with theme support

<Buttoncolor="primary"variant="solid">

Clickme

</Button>

```

### Tailwind CSS

The project uses Tailwind CSS with custom configurations (see `tailwind.config.js`). Key features include:

1. **Custom Color System**

```typescript
// ❌ Don't: Using arbitrary values
<div className="bg-[#FF705B]">

// ✅ Do: Using theme colors
<div className="bg-warning">
```

2. **Responsive Design**

```typescript
// ❌ Don't: Non-responsive design
<h1 className="text-2xl">

// ✅ Do: Mobile-first responsive design
<h1 className="text-xl sm:text-2xl md:text-3xl">
```

3. **Dark Mode Support**

```typescript
// ❌ Don't: No dark mode support
<div className="bg-white">

// ✅ Do: Include dark mode variants
<div className="bg-white dark:bg-black">
```

Direct CSS should only be used when Tailwind utilities cannot achieve the desired result.

### Component Styling

Components use Tailwind's utility classes with consistent patterns:

```typescript
// ❌ Don't: Mixed styling approaches
function BadExample() {
  return (
    <div style={{ padding: '1rem' }} className="container">
      <h1 className="title" style={{ fontSize: '24px' }}>
        Hello
      </h1>
    </div>
  );
}

// ✅ Do: Consistent Tailwind usage
function GoodExample() {
  return (
    <div className={clsx(
      // Layout
      "container p-4",
      // Typography
      "text-lg font-bold",
      // Responsive
      "md:p-6 md:text-xl",
      // Theme
      "dark:bg-gray-800"
    )}>
      <h1>Hello</h1>
    </div>
  );
}
```

## Quality Checks and Fixes

### Development Workflow

1. **Automatic Checks**

   - ESLint runs on save in VS Code
   - Prettier formats on save
   - Husky validates commits
2. **Manual Commands**

```bash
# Check code style
yarn lint

# Fix automatic style issues
yarn lint:fix

# Format code
yarn format
```

## Summary

The project maintains high code quality through:

- Automated code formatting with Prettier
- Code quality checks with ESLint
- Commit validation with Husky
- Consistent styling with Tailwind CSS
- Clear component structure patterns
- Comprehensive theme system

These tools and practices ensure maintainable, consistent code while providing a great developer experience.
