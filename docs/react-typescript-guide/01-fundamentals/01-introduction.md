# Introduction to React 18

React is a JavaScript library for building user interfaces, developed and maintained by Facebook (now Meta). It allows developers to create reusable UI components and manage the state of these components efficiently.

## What is React?

React is a declarative, component-based library for building user interfaces. Instead of directly manipulating the DOM, you describe how your UI should look at any given point in time, and React takes care of updating the DOM to match.

### Key Concepts:

- **Component-Based**: Build encapsulated components that manage their own state
- **Declarative**: Design simple views for each state in your application
- **Learn Once, Write Anywhere**: Develop new features without rewriting existing code

## Virtual DOM

One of React's key features is the Virtual DOM, which is an in-memory representation of the real DOM elements. When the state of a component changes:

1. React builds a new virtual DOM representation
2. Compares it with the previous virtual DOM (diffing)
3. Updates only the parts of the real DOM that have changed

This approach makes updates more efficient and leads to better performance.

## Key Features in React 18

React 18 introduces several important new features and improvements:

### 1. Concurrent Rendering

```tsx
// Using concurrent features with startTransition
import { startTransition } from 'react';

function SearchComponent() {
  const [searchQuery, setSearchQuery] = useState('');
  
  const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    // Mark state update as non-urgent
    startTransition(() => {
      setSearchQuery(e.target.value);
    });
  };
  
  return (
    <div>
      <input onChange={handleChange} />
      <SearchResults query={searchQuery} />
    </div>
  );
}
```

### 2. Automatic Batching

```tsx
// Before React 18:
function handleClick() {
  // These caused separate renders in React 17
  setCount(c => c + 1);
  setFlag(f => !f);
}

// In React 18:
function handleClick() {
  // These are automatically batched in React 18
  setCount(c => c + 1);
  setFlag(f => !f);
  // Only one render will occur
}
```

### 3. Transitions API

```tsx
import { useTransition } from 'react';

function TabContainer() {
  const [isPending, startTransition] = useTransition();
  const [tab, setTab] = useState('home');

  function selectTab(nextTab: string) {
    startTransition(() => {
      setTab(nextTab);
    });
  }
  
  return (
    <div>
      {/* Show a spinner while the transition is pending */}
      {isPending ? <Spinner /> : null}
      <TabButton onClick={() => selectTab('home')}>Home</TabButton>
      <TabButton onClick={() => selectTab('about')}>About</TabButton>
      <TabPanel active={tab === 'home'}>Home content</TabPanel>
      <TabPanel active={tab === 'about'}>About content</TabPanel>
    </div>
  );
}
```

### 4. Suspense on the Server

React 18 extends Suspense support to server-side rendering, allowing for streaming HTML and selective hydration.

```tsx
// Server-side suspense
import { Suspense } from 'react';

function Page() {
  return (
    <div>
      <Suspense fallback={<HeaderSkeleton />}>
        <Header />
      </Suspense>
      <Suspense fallback={<ContentSkeleton />}>
        <Content />
      </Suspense>
    </div>
  );
}
```

## React 18 vs Previous Versions

| Feature | React 17 | React 18 |
|---------|----------|----------|
| Rendering Mode | Synchronous | Concurrent (opt-in) |
| Batching | Manual/limited | Automatic |
| Suspense | Client-side only | Client & Server |
| Root API | ReactDOM.render() | ReactDOM.createRoot() |
| Auto-batching | Only for event handlers | All updates (including promises, timeouts) |
| Server Components | Not available | Experimental support |

## TypeScript Integration

React 18 enhances TypeScript integration with improved type definitions:

```tsx
// Example of TypeScript with React 18
import { useState, useTransition } from 'react';

interface User {
  id: number;
  name: string;
  email: string;
}

function UserProfile({ userId }: { userId: number }) {
  const [user, setUser] = useState<User | null>(null);
  const [isPending, startTransition] = useTransition();
  
  const fetchUserData = async () => {
    const response = await fetch(`/api/users/${userId}`);
    const userData: User = await response.json();
    
    startTransition(() => {
      setUser(userData);
    });
  };
  
  return (
    <div>
      {isPending ? (
        <div>Loading user data...</div>
      ) : user ? (
        <div>
          <h2>{user.name}</h2>
          <p>{user.email}</p>
        </div>
      ) : (
        <button onClick={fetchUserData}>Load User</button>
      )}
    </div>
  );
}
```

## Unit Testing Example

Let's see how we can test a simple React 18 component using React Testing Library:

```tsx
// Button.tsx
import React from 'react';

interface ButtonProps {
  onClick: () => void;
  disabled?: boolean;
  children: React.ReactNode;
}

export const Button: React.FC<ButtonProps> = ({ 
  onClick, 
  disabled = false, 
  children 
}) => {
  return (
    <button 
      onClick={onClick} 
      disabled={disabled}
      className={`btn ${disabled ? 'btn-disabled' : 'btn-active'}`}
    >
      {children}
    </button>
  );
};
```

```tsx
// Button.test.tsx
import { render, screen, fireEvent } from '@testing-library/react';
import { Button } from './Button';

describe('Button Component', () => {
  test('renders button with correct text', () => {
    const handleClick = jest.fn();
    render(<Button onClick={handleClick}>Click me</Button>);
    
    const buttonElement = screen.getByText('Click me');
    expect(buttonElement).toBeInTheDocument();
  });
  
  test('calls onClick handler when clicked', () => {
    const handleClick = jest.fn();
    render(<Button onClick={handleClick}>Click me</Button>);
    
    const buttonElement = screen.getByText('Click me');
    fireEvent.click(buttonElement);
    
    expect(handleClick).toHaveBeenCalledTimes(1);
  });
  
  test('is disabled when disabled prop is true', () => {
    const handleClick = jest.fn();
    render(<Button onClick={handleClick} disabled>Click me</Button>);
    
    const buttonElement = screen.getByText('Click me');
    expect(buttonElement).toBeDisabled();
    
    fireEvent.click(buttonElement);
    expect(handleClick).not.toHaveBeenCalled();
  });
});
```

This introduction provides a foundation for understanding React 18 and its integration with TypeScript. In the next sections, we'll explore setting up a React TypeScript project and diving deeper into components and JSX.
