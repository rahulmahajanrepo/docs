# Comprehensive Guide to Using Recoil with React and TypeScript

## Table of Contents

- [Comprehensive Guide to Using Recoil with React and TypeScript](#comprehensive-guide-to-using-recoil-with-react-and-typescript)
  - [Table of Contents](#table-of-contents)
  - [Introduction](#introduction)
  - [React Fundamentals for Beginners](#react-fundamentals-for-beginners)
    - [Components and JSX](#components-and-jsx)
    - [Props and Component Composition](#props-and-component-composition)
    - [React's Context API](#reacts-context-api)
    - [React Provider Pattern](#react-provider-pattern)
  - [State Management in React](#state-management-in-react)
    - [useState Hook](#usestate-hook)
    - [useReducer Hook](#usereducer-hook)
    - [Context API for State Sharing](#context-api-for-state-sharing)
    - [Limitations of React's Built-in State Management](#limitations-of-reacts-built-in-state-management)
  - [Why Use Recoil?](#why-use-recoil)
    - [Overcoming Context Limitations](#overcoming-context-limitations)
    - [Performance Benefits](#performance-benefits)
    - [Better Developer Experience](#better-developer-experience)
    - [When to Choose Recoil vs. Built-in Options](#when-to-choose-recoil-vs-built-in-options)
  - [Installation](#installation)
  - [Core Concepts](#core-concepts)
    - [Atoms](#atoms)
    - [Selectors](#selectors)
    - [Hooks](#hooks)
  - [Basic Examples](#basic-examples)
    - [Counter Example](#counter-example)
    - [Todo List Example](#todo-list-example)
  - [Understanding State Types](#understanding-state-types)
    - [Local State](#local-state)
    - [Shared State](#shared-state)
    - [Global State](#global-state)
    - [Choosing the Right State Type](#choosing-the-right-state-type)
  - [TypeScript Integration](#typescript-integration)
    - [Typing Atoms and Selectors](#typing-atoms-and-selectors)
    - [Typing Custom Hooks](#typing-custom-hooks)
  - [Advanced Patterns](#advanced-patterns)
    - [Async Selectors with TypeScript](#async-selectors-with-typescript)
    - [Atom Family with TypeScript](#atom-family-with-typescript)
  - [Performance Optimization](#performance-optimization)
    - [Using Selectors for Filtering](#using-selectors-for-filtering)
    - [Using Recoil Snapshot for Debugging](#using-recoil-snapshot-for-debugging)
  - [Best Practices](#best-practices)
    - [Atom Key Naming Conventions](#atom-key-naming-conventions)
      - [Namespacing Patterns](#namespacing-patterns)
      - [Naming Structure](#naming-structure)
      - [Naming Best Practices](#naming-best-practices)
      - [Key Collision Prevention Strategies](#key-collision-prevention-strategies)
    - [Organizing Atoms and Selectors](#organizing-atoms-and-selectors)
      - [File Structure Strategies](#file-structure-strategies)
      - [Colocating Related State](#colocating-related-state)
      - [Custom Hooks for State Logic](#custom-hooks-for-state-logic)
    - [State Structure Recommendations](#state-structure-recommendations)
      - [Granular vs. Coarse-Grained Atoms](#granular-vs-coarse-grained-atoms)
      - [Handling Complex State Updates](#handling-complex-state-updates)
      - [State Dependencies and Derived State](#state-dependencies-and-derived-state)
  - [Testing with Recoil](#testing-with-recoil)
    - [Setting Up Your Testing Environment](#setting-up-your-testing-environment)
    - [Testing Atoms and Selectors](#testing-atoms-and-selectors)
      - [Unit Testing Atoms](#unit-testing-atoms)
      - [Unit Testing Selectors](#unit-testing-selectors)
    - [Testing Components with Recoil State](#testing-components-with-recoil-state)
      - [Testing Components Using RecoilRoot](#testing-components-using-recoilroot)
      - [Testing Components with Initialized State](#testing-components-with-initialized-state)
    - [Testing Strategies for Different State Patterns](#testing-strategies-for-different-state-patterns)
      - [Testing Local State](#testing-local-state)
      - [Testing Shared State](#testing-shared-state)
      - [Testing Global State](#testing-global-state)
    - [Testing Async Selectors](#testing-async-selectors)
    - [Testing Components with Suspense](#testing-components-with-suspense)
    - [Mocking Recoil Hooks](#mocking-recoil-hooks)
    - [Testing Best Practices](#testing-best-practices)
  - [Troubleshooting](#troubleshooting)
    - [Duplicate Atom Key Error](#duplicate-atom-key-error)
    - [Component Not Updating](#component-not-updating)
    - [TypeScript Errors](#typescript-errors)
  - [Conclusion](#conclusion)

## Introduction

Recoil is a state management library for React applications developed by Facebook. It provides a way to create a data-flow graph that flows from atoms (shared state) through selectors (pure functions) and down into React components. Recoil offers several advantages:

- Minimal and intuitive API

  Recoil offers a straightforward API centered around two core concepts: atoms and selectors. Atoms are units of state that components can subscribe to, while selectors transform this state. The API feels familiar to React developers since it adopts a hook-based approach similar to React's useState. This simplicity means less boilerplate compared to other state management solutions, allowing developers to focus on application logic rather than state management plumbing.

-  Native TypeScript support

  Recoil was built with TypeScript in mind from the beginning, providing strong typing out of the box. This ensures type safety throughout your application state, enabling autocomplete suggestions, catching type errors during development, and making refactoring safer. The TypeScript integration helps maintain state consistency across components and provides better documentation through type definitions.

- Code-splitting compatibility

  Unlike some state management libraries that require a single store, Recoil's atom-based approach naturally supports code splitting. Atoms can be defined and loaded alongside the components that use them, preventing unnecessary state from loading until needed. This approach complements React's lazy loading, allowing applications to maintain optimal bundle sizes even as they scale.

-  Concurrent mode friendly

  Recoil was designed with React's Concurrent Mode in mind. It doesn't block rendering with synchronous state updates and properly integrates with React's scheduling capabilities. This means UI remains responsive during heavy state operations, transitions feel smooth, and the library can take advantage of React's latest features like Suspense for data fetching. This future-proofs applications using Recoil.

- Derived data and queries with selectors

  Selectors provide a powerful way to transform and combine state without unnecessary re-renders. They:

  Can combine multiple atoms into derived values

    - Support asynchronous data fetching with built-in loading states
    - Automatically cache results and only recalculate when dependencies change
    - Create a declarative data flow that's easy to reason about
    - Enable complex state transformations without cluttering components
    - This approach separates data manipulation logic from UI components, making both easier to test and maintain.

## React Fundamentals for Beginners

If you're new to React and state management, this section provides a quick introduction to key React concepts that will help you understand how Recoil fits into the React ecosystem.

### Components and JSX

React applications are built using Components - self-contained, reusable code blocks that return what should appear on screen:

```jsx
// Function component example
function Greeting({ name }) {
  return <h1>Hello, {name}!</h1>;
}

// Using the component
<Greeting name="World" />
```

JSX is React's syntax extension that allows you to write HTML-like code in JavaScript. Behind the scenes, it's transformed into `React.createElement()` calls.

### Props and Component Composition

React components receive data through "props" (properties) and can be composed together:

```jsx
function Button({ text, onClick }) {
  return <button onClick={onClick}>{text}</button>;
}

function App() {
  return (
    <div>
      <Button text="Click me" onClick={() => alert('Clicked!')} />
    </div>
  );
}
```

This component composition model is fundamental to React's design.

### React's Context API

React Context provides a way to share values between components without having to explicitly pass props through every level:

```jsx
// Create a context with a default value
const ThemeContext = React.createContext('light');

function App() {
  // Provide a value to the context
  return (
    <ThemeContext.Provider value="dark">
      <ThemedComponent />
    </ThemeContext.Provider>
  );
}

function ThemedComponent() {
  // Consume the context value
  const theme = React.useContext(ThemeContext);
  return <div>Current theme: {theme}</div>;
}
```

Context is useful for sharing "global" data like themes, user authentication, or preferences.

### React Provider Pattern

The provider pattern is a common pattern used with Context to make state available throughout a component tree:

```jsx
function UserProvider({ children }) {
  const [user, setUser] = React.useState(null);
  
  // Authentication functions
  const login = (username, password) => {/* ... */};
  const logout = () => {/* ... */};
  
  return (
    <UserContext.Provider value={{ user, login, logout }}>
      {children}
    </UserContext.Provider>
  );
}

// Usage
function App() {
  return (
    <UserProvider>
      <Dashboard />
    </UserProvider>
  );
}
```

This pattern forms the foundation for many state management solutions, including Recoil's `RecoilRoot`.

## State Management in React

React offers several built-in ways to manage state before reaching for external libraries like Recoil.

### useState Hook

The `useState` hook is the most basic way to add state to a function component:

```jsx
function Counter() {
  const [count, setCount] = React.useState(0);
  
  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}
```

Key characteristics:
- Simple API for individual pieces of state
- Local to the component
- Re-renders the component when state changes
- Ideal for simple, component-specific state

### useReducer Hook

For more complex state logic, the `useReducer` hook provides a Redux-like approach:

```jsx
function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return { count: state.count + 1 };
    case 'decrement':
      return { count: state.count - 1 };
    default:
      throw new Error();
  }
}

function Counter() {
  const [state, dispatch] = React.useReducer(reducer, { count: 0 });
  
  return (
    <div>
      <p>Count: {state.count}</p>
      <button onClick={() => dispatch({ type: 'increment' })}>+</button>
      <button onClick={() => dispatch({ type: 'decrement' })}>-</button>
    </div>
  );
}
```

Key characteristics:
- More structured approach to state updates
- Good for complex state logic within a component
- Still local to the component
- Helps separate state logic from component rendering

### Context API for State Sharing

The Context API can be combined with hooks for simple shared state:

```jsx
const CounterContext = React.createContext(null);

function CounterProvider({ children }) {
  const [count, setCount] = React.useState(0);
  
  return (
    <CounterContext.Provider value={{ count, setCount }}>
      {children}
    </CounterContext.Provider>
  );
}

function CounterDisplay() {
  const { count } = React.useContext(CounterContext);
  return <div>Count: {count}</div>;
}

function CounterButtons() {
  const { setCount } = React.useContext(CounterContext);
  
  return (
    <div>
      <button onClick={() => setCount(c => c + 1)}>Increment</button>
      <button onClick={() => setCount(c => c - 1)}>Decrement</button>
    </div>
  );
}

function App() {
  return (
    <CounterProvider>
      <CounterDisplay />
      <CounterButtons />
    </CounterProvider>
  );
}
```

Key characteristics:
- Allows sharing state without prop drilling
- Works well for simple shared state
- Works well for infrequently changing values (themes, auth)
- No need for external libraries

### Limitations of React's Built-in State Management

While React's built-in state management is powerful, it has several limitations:

1. **Prop Drilling**: Without Context, passing state through many component layers leads to "prop drilling"
2. **Context Performance**: Context causes all consumers to re-render when the context value changes
3. **Lack of Code Splitting Support**: Context providers must be higher in the tree than all consumers
4. **Complex Derived State**: Calculating derived state from multiple contexts is cumbersome
5. **Debugging Challenges**: No built-in tooling for inspecting state across the component tree
6. **Async Integration**: No built-in solution for handling async state updates

These limitations become more apparent in larger applications with complex state requirements.

## Why Use Recoil?

### Overcoming Context Limitations

Recoil addresses many of the limitations of React's Context API:

1. **No Prop Drilling**: Components can access any atom from anywhere without prop drilling or wrapper hell
2. **Granular Updates**: Only components that use a specific atom re-render when it changes (unlike Context)
3. **Support for Code Splitting**: Atoms can be defined anywhere, including in lazily loaded components
4. **Easy Derived State**: Selectors make it simple to compute values based on multiple atoms
5. **Built-in Async Support**: Selectors can be async and work naturally with Suspense

Example comparing Context vs Recoil:

**With Context:**
```jsx
// Every component that uses UserContext re-renders when any part of user changes
function ProfilePage() {
  const { user, updateUser } = useContext(UserContext);
  return (
    <>
      <UserHeader name={user.name} />
      <UserPreferences 
        preferences={user.preferences} 
        updatePreferences={(prefs) => updateUser({...user, preferences: prefs})} 
      />
    </>
  );
}
```

**With Recoil:**
```jsx
// Components only re-render when the specific atoms they use change
function ProfilePage() {
  return (
    <>
      <UserHeader /> {/* Only re-renders when userName atom changes */}
      <UserPreferences /> {/* Only re-renders when userPreferences atom changes */}
    </>
  );
}

function UserHeader() {
  const userName = useRecoilValue(userNameState);
  return <h1>{userName}</h1>;
}

function UserPreferences() {
  const [preferences, setPreferences] = useRecoilState(userPreferencesState);
  // Component logic...
}
```

### Performance Benefits

Recoil offers several performance advantages:

1. **Fine-grained Updates**: Only components that actually use a particular piece of state re-render when it changes
2. **Memoized Selectors**: Selectors only recompute when their dependencies change
3. **Optimized for React Concurrent Mode**: Designed with React's concurrent features in mind
4. **Efficient State Sharing**: Sharing state between distant components doesn't impact components in between

### Better Developer Experience

Recoil also provides developer experience benefits:

1. **Simpler Debugging**: The `useRecoilSnapshot` hook and dev tools make inspecting state easier
2. **Familiar API**: The API is similar to React's built-in hooks, making it easy to learn
3. **TypeScript Integration**: First-class TypeScript support with proper typings
4. **Testing Support**: Easier to test components with state dependencies
5. **Atom Families**: Efficient way to handle collections of similar state

### When to Choose Recoil vs. Built-in Options

Consider using Recoil when:

1. Your application has complex state shared across many components
2. You need derived state that depends on multiple pieces of state
3. You have performance issues with Context re-renders
4. You need to incorporate async state (like API data) directly into your state graph
5. Your application is growing and needs a more scalable state solution

Stick with built-in options when:

1. Your application is simple with minimal shared state
2. Components mostly use local state
3. You're building a small library and want to minimize dependencies
4. Your shared state changes infrequently (like theme or authentication)

## Installation

To add Recoil to your React TypeScript project:

```bash
npm install recoil
# or
yarn add recoil
# or with pnpm
pnpm add recoil
```

Then, wrap your React application with the `RecoilRoot` component:

```tsx
import React from 'react';
import { RecoilRoot } from 'recoil';
import App from './App';

const Root = () => (
  <RecoilRoot>
    <App />
  </RecoilRoot>
);

export default Root;
```

The `RecoilRoot` component provides the context in which atoms have values. It should be placed at the root of your component tree, typically in your index.tsx file. All components that use Recoil state must be descendants of a `RecoilRoot`.

You can also use multiple `RecoilRoot` components in a single application, where each root creates a separate atom state scope:

```tsx
function SeparateStateExample() {
  return (
    <div>
      <RecoilRoot>
        <ComponentUsingRecoil /> {/* Uses one atom scope */}
      </RecoilRoot>
      <RecoilRoot>
        <ComponentUsingRecoil /> {/* Uses a different atom scope */}
      </RecoilRoot>
    </div>
  );
}
```

In this example, we have two instances of the same component wrapped in separate `RecoilRoot` components. This creates two completely isolated state containers:

- Both components can use the same atoms (with the same keys)
- Changes to state in one component won't affect the other component
- Each `RecoilRoot` maintains its own independent version of every atom

This pattern is useful for:

1. **Isolated components**: Create widgets that manage their own state without interfering with other instances
2. **Side-by-side comparisons**: Show the same component in different states simultaneously
3. **Sandboxed environments**: Create isolated environments for testing or previewing
4. **Modal dialogs**: Give modal windows their own state container separate from the main application

You can also use the `initializeState` prop to set up initial values for atoms in a specific RecoilRoot:

```tsx
<RecoilRoot
  initializeState={({ set }) => {
    set(counterState, 10);
    set(userState, { id: '123', name: 'Test User' });
  }}
>
  <ComponentWithInitializedState />
</RecoilRoot>
```

## Core Concepts

### Atoms

Atoms are units of state in Recoil. They are updateable and subscribable, meaning components can subscribe to them. When an atom updates, all subscribed components re-render.

```tsx
import { atom } from 'recoil';

// Define an atom with TypeScript
interface TodoItem {
  id: number;
  text: string;
  completed: boolean;
}

export const todoListState = atom<TodoItem[]>({
  key: 'todoListState', // unique ID
  default: [], // default value
});
```

Each atom needs:

- A **unique key** - This string identifies the atom across your application. If two atoms share the same key, you'll get a runtime error.
- A **default value** - The initial state when the application loads. This could be any valid value matching the atom's type.

When using atoms:
- Components subscribe to atoms using hooks like `useRecoilState` or `useRecoilValue`
- The atom's value persists as long as the `RecoilRoot` component is mounted
- When to use atoms: Choose atoms for state that might be used by multiple components or needs to persist beyond a single component's lifecycle

### Selectors

Selectors are pure functions that accept atoms or other selectors as input. They're used to compute derived data based on the state.

```tsx
import { selector } from 'recoil';
import { todoListState } from './atoms';

export const todoStatsState = selector({
  key: 'todoStatsState',
  get: ({ get }) => {
    const todoList = get(todoListState);
    const totalNum = todoList.length;
    const completedNum = todoList.filter((item) => item.completed).length;
    const uncompletedNum = totalNum - completedNum;
    
    return {
      totalNum,
      completedNum,
      uncompletedNum,
      percentCompleted: totalNum === 0 ? 0 : completedNum / totalNum,
    };
  },
});
```

Key points about selectors:
- The `get` function receives a `get` parameter that can access the value of any atom or other selector
- Selectors automatically recompute when any of their dependencies change
- Recoil memoizes selector results, so the calculation only runs when dependencies change
- Selectors are read-only by default; to make them writable, add a `set` function
- When to use selectors: For any derived data that can be computed from other atoms or selectors

### Hooks

Recoil provides several hooks to interact with its state:

- `useRecoilState`: Similar to `useState`, but for Recoil atoms
  ```tsx
  const [todos, setTodos] = useRecoilState(todoListState);
  // Now you can read todos and update them with setTodos
  ```

- `useRecoilValue`: Read-only access to atoms or selectors
  ```tsx
  const todos = useRecoilValue(todoListState);
  // You can read todos but not update them
  ```

- `useSetRecoilState`: Get a setter function without subscribing to updates
  ```tsx
  const setTodos = useSetRecoilState(todoListState);
  // Component won't re-render when todoListState changes
  // Useful when a component only needs to update state but doesn't read it
  ```

- `useResetRecoilState`: Reset an atom to its default value
  ```tsx
  const resetTodos = useResetRecoilState(todoListState);
  // Call resetTodos() to clear the todo list back to an empty array
  ```

Choosing the right hook:
- Use `useRecoilState` when you need to both read and write to an atom
- Use `useRecoilValue` when you only need to read (optimizes renders)
- Use `useSetRecoilState` when you only need to write (prevents unnecessary renders)
- Use `useResetRecoilState` when you need a convenient way to reset to default values

## Basic Examples

### Counter Example

```tsx
import React from 'react';
import { atom, useRecoilState } from 'recoil';

// Define an atom for the counter
const counterState = atom<number>({
  key: 'counterState',
  default: 0,
});

const Counter: React.FC = () => {
  const [count, setCount] = useRecoilState(counterState);
  
  return (
    <div>
      <h1>Count: {count}</h1>
      <button onClick={() => setCount(count + 1)}>Increment</button>
      <button onClick={() => setCount(count - 1)}>Decrement</button>
    </div>
  );
};

export default Counter;
```

Explanation of this counter example:

1. We define an atom called `counterState` with a default value of `0`. This creates a piece of state that can be accessed from anywhere in our app.

2. The `Counter` component uses `useRecoilState` to:
   - Get the current value of the counter (`count`)
   - Get a function to update the counter (`setCount`)

3. When a user clicks the "Increment" button, we call `setCount(count + 1)` which:
   - Updates the atom's value
   - Triggers a re-render of all components that use this atom
   - Works just like React's `useState` but is globally accessible

4. Unlike with `useState`, if this counter were used in multiple components, they would all stay in sync since they share the same atom.

### Todo List Example

```tsx
import React, { useState } from 'react';
import { atom, useRecoilState, useRecoilValue, selector } from 'recoil';

// Define types
interface Todo {
  id: number;
  text: string;
  completed: boolean;
}

// Define atoms
const todosState = atom<Todo[]>({
  key: 'todosState',
  default: [],
});

// Define selectors
const todoStatsSelector = selector({
  key: 'todoStats',
  get: ({ get }) => {
    const todos = get(todosState);
    const completedTodos = todos.filter(todo => todo.completed);
    
    return {
      total: todos.length,
      completed: completedTodos.length,
      uncompleted: todos.length - completedTodos.length,
      percentCompleted: todos.length === 0 ? 0 : completedTodos.length / todos.length * 100
    };
  }
});

// Components
const TodoList: React.FC = () => {
  const [todos, setTodos] = useRecoilState(todosState);
  const [newTodoText, setNewTodoText] = useState('');
  
  const addTodo = () => {
    if (!newTodoText.trim()) return;
    
    setTodos([
      ...todos,
      {
        id: Date.now(),
        text: newTodoText,
        completed: false
      }
    ]);
    setNewTodoText('');
  };
  
  const toggleTodo = (id: number) => {
    setTodos(
      todos.map(todo => 
        todo.id === id ? { ...todo, completed: !todo.completed } : todo
      )
    );
  };
  
  return (
    <div>
      <h1>Todo List</h1>
      
      <div>
        <input 
          type="text" 
          value={newTodoText} 
          onChange={(e) => setNewTodoText(e.target.value)}
          placeholder="Add a new todo"
        />
        <button onClick={addTodo}>Add</button>
      </div>
      
      <ul>
        {todos.map(todo => (
          <li 
            key={todo.id}
            style={{ textDecoration: todo.completed ? 'line-through' : 'none' }}
            onClick={() => toggleTodo(todo.id)}
          >
            {todo.text}
          </li>
        ))}
      </ul>
      
      <TodoStats />
    </div>
  );
};

const TodoStats: React.FC = () => {
  const stats = useRecoilValue(todoStatsSelector);
  
  return (
    <div>
      <h2>Stats</h2>
      <p>Total: {stats.total}</p>
      <p>Completed: {stats.completed}</p>
      <p>Uncompleted: {stats.uncompleted}</p>
      <p>Percent Completed: {stats.percentCompleted.toFixed(0)}%</p>
    </div>
  );
};

export default TodoList;
```

Explanation of the Todo List example:

1. **State Structure**: We define a Todo interface with `id`, `text`, and `completed` properties, and store the list in an atom.

2. **Derived State**: The `todoStatsSelector` computes statistics based on the todo list. It automatically recalculates when the todos change.

3. **Component Breakdown**:
   - `TodoList` component handles the list display and manipulation
   - `TodoStats` component only displays statistics without directly accessing the todo list

4. **State Updates**: The `addTodo` and `toggleTodo` functions modify the state by creating new arrays rather than mutating the existing one, which is important for React's rendering system.

5. **State Sharing**: Both components access the same state but for different purposes - one for manipulation and one for displaying derived information.

## Understanding State Types

When working with Recoil, it's important to understand the different types of state you might manage. Each has different patterns and best practices.

### Local State

Local state is state that only matters to a single component or a small part of your application. It doesn't need to be shared more widely.

**When to use local state with Recoil:**
- Component-specific functionality that doesn't affect other components
- State that doesn't need to persist when the component unmounts
- Forms or UI elements isolated to a specific component

**Implementation patterns:**

1. **Component-specific atoms:**

```tsx
// UserProfileForm.tsx
import { atom, useRecoilState } from 'recoil';

// Local atom defined within the component file
const formStateAtom = atom<{
  name: string;
  email: string;
  bio: string;
}>({
  key: 'UserProfileForm_formState',
  default: {
    name: '',
    email: '',
    bio: ''
  }
});

export function UserProfileForm() {
  const [formState, setFormState] = useRecoilState(formStateAtom);
  
  const handleNameChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    setFormState({
      ...formState,
      name: e.target.value
    });
  };
  
  // Similar handlers for other fields
  
  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    // Process form submission
    console.log('Submitting:', formState);
  };
  
  return (
    <form onSubmit={handleSubmit}>
      <div>
        <label htmlFor="name">Name:</label>
        <input
          id="name"
          value={formState.name}
          onChange={handleNameChange}
        />
      </div>
      
      <div>
        <label htmlFor="email">Email:</label>
        <input
          id="email"
          value={formState.email}
          onChange={(e) => setFormState({...formState, email: e.target.value})}
        />
      </div>
      
      <div>
        <label htmlFor="bio">Bio:</label>
        <textarea
          id="bio"
          value={formState.bio}
          onChange={(e) => setFormState({...formState, bio: e.target.value})}
        />
      </div>
      
      <button type="submit">Save Profile</button>
    </form>
  );
}
```

2. **Scoped RecoilRoot:**

For truly isolated local state, you can use a separate `RecoilRoot` for a component subtree:

```tsx
function LocalCounter() {
  // This component has its own isolated state universe
  return (
    <RecoilRoot>
      <CounterComponent />
    </RecoilRoot>
  );
}

// This can be reused in multiple places with independent state
function Dashboard() {
  return (
    <div>
      <LocalCounter />
      <LocalCounter />
    </div>
  );
}
```

### Shared State

Shared state is state that needs to be accessed and possibly modified by multiple related components, but isn't necessarily needed application-wide.

**When to use shared state with Recoil:**
- State shared between sibling components
- State that flows between parent and child components
- Feature-specific state used across multiple components

**Implementation patterns:**

1. **Feature-specific atoms in a shared file:**

```tsx
// userProfileAtoms.ts
import { atom, selector } from 'recoil';

export const userNameState = atom<string>({
  key: 'userProfile_name',
  default: ''
});

export const userEmailState = atom<string>({
  key: 'userProfile_email',
  default: ''
});

export const userBioState = atom<string>({
  key: 'userProfile_bio',
  default: ''
});

export const isProfileCompleteState = selector<boolean>({
  key: 'userProfile_isComplete',
  get: ({ get }) => {
    const name = get(userNameState);
    const email = get(userEmailState);
    
    return name.length > 0 && email.length > 0 && email.includes('@');
  }
});

// Usage in components
function ProfileName() {
  const [name, setName] = useRecoilState(userNameState);
  return (
    <div>
      <label>Name: </label>
      <input value={name} onChange={(e) => setName(e.target.value)} />
    </div>
  );
}

function ProfileEmail() {
  const [email, setEmail] = useRecoilState(userEmailState);
  return (
    <div>
      <label>Email: </label>
      <input value={email} onChange={(e) => setEmail(e.target.value)} />
    </div>
  );
}

function ProfileBio() {
  const [bio, setBio] = useRecoilState(userBioState);
  return (
    <div>
      <label>Bio: </label>
      <textarea value={bio} onChange={(e) => setBio(e.target.value)} />
    </div>
  );
}

function SaveButton() {
  const isComplete = useRecoilValue(isProfileCompleteState);
  
  return (
    <button disabled={!isComplete} onClick={saveProfile}>
      Save Profile
    </button>
  );
}

// Parent component assembling the shared state UI
function ProfileEditor() {
  return (
    <div>
      <h2>Edit Profile</h2>
      <ProfileName />
      <ProfileEmail />
      <ProfileBio />
      <SaveButton />
    </div>
  );
}
```

2. **Custom hooks for managing related state:**

```tsx
// useProfileEditor.ts
import { atom, useRecoilState, useRecoilValue, selector } from 'recoil';

const profileNameState = atom<string>({
  key: 'profileEditor_name',
  default: ''
});

const profileEmailState = atom<string>({
  key: 'profileEditor_email',
  default: ''
});

const isProfileValidState = selector<boolean>({
  key: 'profileEditor_isValid',
  get: ({ get }) => {
    const name = get(profileNameState);
    const email = get(profileEmailState);
    
    return name.length > 0 && email.includes('@');
  }
});

export function useProfileEditor() {
  const [name, setName] = useRecoilState(profileNameState);
  const [email, setEmail] = useRecoilState(profileEmailState);
  const isValid = useRecoilValue(isProfileValidState);
  
  const resetForm = () => {
    setName('');
    setEmail('');
  };
  
  const saveProfile = () => {
    if (isValid) {
      console.log('Saving profile:', { name, email });
      // API call would go here
    }
  };
  
  return {
    name,
    setName,
    email,
    setEmail,
    isValid,
    resetForm,
    saveProfile
  };
}

// Usage in components
function ProfileForm() {
  const {
    name, setName,
    email, setEmail,
    isValid, resetForm, saveProfile
  } = useProfileEditor();
  
  return (
    <form onSubmit={e => { e.preventDefault(); saveProfile(); }}>
      <div>
        <input 
          placeholder="Name" 
          value={name} 
          onChange={e => setName(e.target.value)} 
        />
      </div>
      <div>
        <input 
          placeholder="Email" 
          value={email} 
          onChange={e => setEmail(e.target.value)} 
        />
      </div>
      <div>
        <button type="submit" disabled={!isValid}>Save</button>
        <button type="button" onClick={resetForm}>Reset</button>
      </div>
    </form>
  );
}
```

### Global State

Global state is application-wide state that can be accessed from anywhere and affects many different parts of your application.

**When to use global state with Recoil:**
- User authentication information
- Application themes or preferences
- Shopping cart contents
- Notifications or messages
- Feature flags

**Implementation patterns:**

1. **Centralized atom definitions:**

```tsx
// globalState.ts
import { atom, selector } from 'recoil';

// User authentication state
export interface User {
  id: string;
  name: string;
  email: string;
  role: 'admin' | 'user' | 'guest';
}

export const currentUserState = atom<User | null>({
  key: 'global_currentUser',
  default: null
});

export const isLoggedInState = selector<boolean>({
  key: 'global_isLoggedIn',
  get: ({ get }) => get(currentUserState) !== null
});

export const isAdminState = selector<boolean>({
  key: 'global_isAdmin',
  get: ({ get }) => {
    const user = get(currentUserState);
    return user?.role === 'admin';
  }
});

// Theme state
export type ThemeMode = 'light' | 'dark' | 'system';

export const themeModeState = atom<ThemeMode>({
  key: 'global_themeMode',
  default: 'system'
});

// Notification state
export interface Notification {
  id: string;
  type: 'info' | 'success' | 'warning' | 'error';
  message: string;
}

export const notificationsState = atom<Notification[]>({
  key: 'global_notifications',
  default: []
});

// Usage in various components throughout the app
function Header() {
  const user = useRecoilValue(currentUserState);
  const isAdmin = useRecoilValue(isAdminState);
  
  return (
    <header>
      {user ? (
        <div>
          Welcome, {user.name}
          {isAdmin && <span className="admin-badge">Admin</span>}
        </div>
      ) : (
        <button>Login</button>
      )}
    </header>
  );
}

function ThemeToggle() {
  const [themeMode, setThemeMode] = useRecoilState(themeModeState);
  
  return (
    <select 
      value={themeMode} 
      onChange={e => setThemeMode(e.target.value as ThemeMode)}
    >
      <option value="light">Light</option>
      <option value="dark">Dark</option>
      <option value="system">System</option>
    </select>
  );
}

function NotificationCenter() {
  const [notifications, setNotifications] = useRecoilState(notificationsState);
  
  const dismissNotification = (id: string) => {
    setNotifications(notifications.filter(n => n.id !== id));
  };
  
  return (
    <div className="notification-center">
      {notifications.map(notification => (
        <div key={notification.id} className={`notification ${notification.type}`}>
          {notification.message}
          <button onClick={() => dismissNotification(notification.id)}>
            Dismiss
          </button>
        </div>
      ))}
    </div>
  );
}
```

2. **Global state with persistence:**

```tsx
// persistedState.ts
import { atom, AtomEffect } from 'recoil';

// Local storage effect for persistence
const localStorageEffect: <T>(key: string) => AtomEffect<T> = 
  key => ({ setSelf, onSet }) => {
    const savedValue = localStorage.getItem(key);
    if (savedValue != null) {
      setSelf(JSON.parse(savedValue));
    }
    
    onSet((newValue, _, isReset) => {
      isReset 
        ? localStorage.removeItem(key) 
        : localStorage.setItem(key, JSON.stringify(newValue));
    });
  };

// User preferences with persistence
export interface UserPreferences {
  darkMode: boolean;
  fontSize: 'small' | 'medium' | 'large';
  notifications: boolean;
}

export const userPreferencesState = atom<UserPreferences>({
  key: 'global_userPreferences',
  default: {
    darkMode: false,
    fontSize: 'medium',
    notifications: true
  },
  effects: [
    localStorageEffect<UserPreferences>('user_preferences')
  ]
});

// Usage
function PreferencesPanel() {
  const [preferences, setPreferences] = useRecoilState(userPreferencesState);
  
  return (
    <div className="preferences-panel">
      <h2>User Preferences</h2>
      
      <div>
        <label>
          <input 
            type="checkbox"
            checked={preferences.darkMode}
            onChange={e => setPreferences({
              ...preferences,
              darkMode: e.target.checked
            })}
          />
          Dark Mode
        </label>
      </div>
      
      <div>
        <label>Font Size:</label>
        <select 
          value={preferences.fontSize}
          onChange={e => setPreferences({
            ...preferences,
            fontSize: e.target.value as 'small' | 'medium' | 'large'
          })}
        >
          <option value="small">Small</option>
          <option value="medium">Medium</option>
          <option value="large">Large</option>
        </select>
      </div>
      
      <div>
        <label>
          <input 
            type="checkbox"
            checked={preferences.notifications}
            onChange={e => setPreferences({
              ...preferences,
              notifications: e.target.checked
            })}
          />
          Enable Notifications
        </label>
      </div>
    </div>
  );
}
```

### Choosing the Right State Type

Here's a decision framework for choosing the right state type:

1. **Ask: "Who needs this state?"**
   - Only one component? → **Local state**
   - Related components in a feature? → **Shared state**
   - Many unrelated components? → **Global state**

2. **Ask: "How long should this state live?"**
   - Only while a specific UI is visible? → **Local state**
   - Throughout a user workflow? → **Shared state**
   - Throughout the entire application session? → **Global state**

3. **Ask: "How often does this state change?"**
   - Frequently, like form inputs? → **Local state** (to minimize app-wide renders)
   - Occasionally, during specific interactions? → **Shared state**
   - Rarely, like auth or theme? → **Global state**

**State type comparison table:**

| Aspect | Local State | Shared State | Global State |
|--------|------------|--------------|--------------|
| **Scope** | Single component or small area | Feature or related components | Entire application |
| **Lifetime** | Component lifecycle | Feature interaction | Application session |
| **Example Use Cases** | Forms, toggles, local UI state | Wizards, multi-step forms, feature state | Auth, theme, notifications, cart |
| **Key Naming** | `componentName_stateName` | `featureName_stateName` | `global_stateName` |
| **File Organization** | Inside component file or hook | Feature-specific state folder | Global state folder |
| **Persistence Needs** | Rarely needed | Sometimes needed | Often needed |

## TypeScript Integration

Recoil works well with TypeScript, providing typesafe state management.

### Typing Atoms and Selectors

```tsx
import { atom, selector, DefaultValue } from 'recoil';

// Define your types
interface User {
  id: string;
  name: string;
  email: string;
}

// Define atoms with proper types
const currentUserIDState = atom<string | null>({
  key: 'currentUserID',
  default: null,
});

const usersState = atom<Record<string, User>>({
  key: 'users',
  default: {},
});

// Define a selector with proper types
const currentUserState = selector<User | undefined>({
  key: 'currentUser',
  get: ({ get }) => {
    const userId = get(currentUserIDState);
    const users = get(usersState);
    
    return userId ? users[userId] : undefined;
  },
  set: ({ set, get }, newValue) => {
    if (newValue instanceof DefaultValue) {
      set(currentUserIDState, null);
      return;
    }
    
    if (!newValue) {
      set(currentUserIDState, null);
      return;
    }
    
    const users = { ...get(usersState) };
    users[newValue.id] = newValue;
    
    set(usersState, users);
    set(currentUserIDState, newValue.id);
  },
});
```

In this example:

1. **Type Definitions**: We define a `User` interface to ensure type safety throughout our state.

2. **Typed Atoms**: The atoms are explicitly typed with `<string | null>` and `<Record<string, User>>` to ensure type safety.

3. **Selector Types**: The selector is typed as `<User | undefined>` to match its return value possibilities.

4. **DefaultValue Handling**: The `instanceof DefaultValue` check is necessary because Recoil uses this special type to indicate when a selector's value should be reset to its default.

5. **State Operations**: The selector's `set` function handles creating new copies of state rather than mutating existing objects, which is a React best practice.

Understanding the `DefaultValue` type:
- When you call `useResetRecoilState` on a selector, Recoil passes a `DefaultValue` instance to the selector's `set` function
- This allows the selector to distinguish between being set to `null`/`undefined` and being reset
- Without this check, you wouldn't be able to tell the difference between intentionally setting a value to `null` and resetting it

### Typing Custom Hooks

```tsx
import { useRecoilState, useRecoilValue, useSetRecoilState } from 'recoil';
import { currentUserState, User } from './atoms';

// Custom hook with TypeScript
export function useCurrentUser() {
  const [user, setUser] = useRecoilState(currentUserState);
  
  const updateUsername = (newName: string) => {
    if (user) {
      setUser({
        ...user,
        name: newName,
      });
    }
  };
  
  return {
    user,
    setUser,
    updateUsername,
  };
}
```

## Advanced Patterns

### Async Selectors with TypeScript

```tsx
import { selector, useRecoilValue } from 'recoil';
import { userIdState } from './atoms';

// Async selector with TypeScript
const userDataQuery = selector<User | null>({
  key: 'userData',
  get: async ({ get }) => {
    const userId = get(userIdState);
    
    if (!userId) return null;
    
    try {
      // TypeScript knows this will be a User
      const response = await fetch(`https://api.example.com/users/${userId}`);
      
      if (!response.ok) {
        throw new Error('Failed to fetch user data');
      }
      
      const userData: User = await response.json();
      return userData;
    } catch (error) {
      console.error('Error fetching user:', error);
      return null;
    }
  }
});

// Usage in a component
const UserProfile: React.FC = () => {
  const userData = useRecoilValue(userDataQuery);
  
  if (!userData) {
    return <p>Loading user data...</p>;
  }
  
  return (
    <div>
      <h1>{userData.name}</h1>
      <p>Email: {userData.email}</p>
    </div>
  );
};
```

This async selector example demonstrates:

1. **Data Fetching**: The selector fetches data from an API based on a user ID from another atom.

2. **Conditional Logic**: It only makes the API call if a user ID exists.

3. **Error Handling**: It has proper try/catch logic to handle API failures.

4. **Loading States**: When used with Suspense (covered later), this automatically handles loading states.

5. **TypeScript Integration**: The selector is properly typed as `<User | null>` to match all possible return values.

How it works:
- When a component reads this selector, Recoil checks if the value is already cached
- If not, it runs the selector's `get` function
- Since the function is async, Recoil suspends the component (if used with Suspense)
- Once the data loads, the component renders with the data
- If an error occurs, it can be caught with an Error Boundary

### Atom Family with TypeScript

```tsx
import { atomFamily, useRecoilState } from 'recoil';

interface TodoItem {
  id: number;
  text: string;
  completed: boolean;
}

// Create an atom family
const todoItemFamily = atomFamily<TodoItem, number>({
  key: 'todoItem',
  default: (id) => ({
    id,
    text: '',
    completed: false,
  }),
});

// Component using the atom family
const TodoItem: React.FC<{ id: number }> = ({ id }) => {
  const [item, setItem] = useRecoilState(todoItemFamily(id));
  
  const toggleCompletion = () => {
    setItem({ ...item, completed: !item.completed });
  };
  
  return (
    <div>
      <span style={{ textDecoration: item.completed ? 'line-through' : 'none' }}>
        {item.text}
      </span>
      <button onClick={toggleCompletion}>
        {item.completed ? 'Mark Incomplete' : 'Mark Complete'}
      </button>
    </div>
  );
};
```

Explanation of atom families:

1. **What Are Atom Families?** An atom family is a collection of atoms generated from a template. Each atom in the family is identified by a parameter (like an ID).

2. **Use Cases**: Perfect for collections of similar items where each needs its own state, like todos, user profiles, or form fields.

3. **Implementation**: 
   - The `atomFamily` function creates a function that returns atoms
   - Each call with a unique parameter returns a different atom
   - The `default` can be a static value or a function that generates defaults based on the parameter

4. **Advantages over a Single Atom**:
   - Only components using a specific item re-render when it changes (better performance)
   - Easier to manage individual items without affecting the whole collection
   - More intuitive for items that have independent existence

5. **When to Use**: Choose atom families when you have a collection where:
   - Items are frequently added/removed
   - Individual items change independently
   - You want to minimize re-renders of unrelated components

## Performance Optimization

### Using Selectors for Filtering

```tsx
import { atom, selector, useRecoilValue } from 'recoil';

interface Task {
  id: number;
  title: string;
  completed: boolean;
}

const tasksState = atom<Task[]>({
  key: 'tasks',
  default: [],
});

const taskFilterState = atom<'all' | 'completed' | 'incomplete'>({
  key: 'taskFilter',
  default: 'all',
});

const filteredTasksSelector = selector<Task[]>({
  key: 'filteredTasks',
  get: ({ get }) => {
    const filter = get(taskFilterState);
    const tasks = get(tasksState);
    
    switch (filter) {
      case 'completed':
        return tasks.filter(task => task.completed);
      case 'incomplete':
        return tasks.filter(task => !task.completed);
      default:
        return tasks;
    }
  }
});

// Component only re-renders when the filtered result changes
const TaskList: React.FC = () => {
  const filteredTasks = useRecoilValue(filteredTasksSelector);
  
  return (
    <ul>
      {filteredTasks.map(task => (
        <li key={task.id}>{task.title}</li>
      ))}
    </ul>
  );
};
```

Explanation of selector-based filtering:

1. **Problem Solved**: Without selectors, filtering in the component would cause re-renders even when the filtered result hasn't changed.

2. **Implementation Details**:
   - The `tasksState` atom stores all tasks
   - The `taskFilterState` atom stores the current filter type
   - The `filteredTasksSelector` computes the filtered list only when tasks or filter changes

3. **Performance Benefits**:
   - Components only re-render when the filtered result actually changes
   - The filtering logic is moved out of components, making them cleaner
   - The filtered result is cached, so multiple components can use it without recalculation

4. **When to Use This Pattern**: Implement this when you have:
   - Lists that need filtering, sorting, or other transformations
   - Multiple components displaying the same filtered data
   - Performance issues from frequent re-filtering on every render

### Using Recoil Snapshot for Debugging

```tsx
import { useRecoilCallback } from 'recoil';

const DebugButton: React.FC = () => {
  const logState = useRecoilCallback(({ snapshot }) => async () => {
    console.log('Current state:', snapshot.getLoadable(myAtom).contents);
    
    // Get the whole state
    for (const node of snapshot.getNodes_UNSTABLE()) {
      const value = await snapshot.getPromise(node);
      console.log(`${node.key}: `, value);
    }
  });
  
  return (
    <button onClick={logState}>Log Current State</button>
  );
};
```

This debugging pattern:

1. **What is a Snapshot?** A snapshot is an immutable copy of the entire Recoil state at a point in time.

2. **useRecoilCallback**: Unlike regular hooks, this doesn't cause re-renders when atoms change. It gives you access to the current state when the callback is called.

3. **Implementation Details**:
   - The `logState` function captures the current Recoil state when the button is clicked
   - `snapshot.getLoadable` gets the value of a specific atom
   - `snapshot.getNodes_UNSTABLE` gets all atoms and selectors in the app

4. **When to Use**:
   - During development to inspect state
   - For logging or analytics
   - For debugging complex state transitions
   - For creating debugging tools or state exploration interfaces

5. **Limitations**: The `_UNSTABLE` suffix indicates the API might change in future versions.

## Best Practices

### Atom Key Naming Conventions

Well-structured atom keys are crucial for maintaining a clean and maintainable Recoil application. Here are detailed conventions for naming and organizing atom keys:

#### Namespacing Patterns

Use namespaces to prevent key collisions and organize your atoms logically:

```tsx
// Feature-based namespacing
const userProfileNameState = atom<string>({
  key: 'userProfile_name',
  default: ''
});

const userProfileEmailState = atom<string>({
  key: 'userProfile_email',
  default: ''
});

// Domain-based namespacing
const cartItemsState = atom<CartItem[]>({
  key: 'cart_items',
  default: []
});

const cartTotalState = selector<number>({
  key: 'cart_total',
  get: ({ get }) => {
    // implementation
  }
});

// Module-based namespacing 
const authCurrentUserState = atom<User | null>({
  key: 'auth_currentUser',
  default: null
});

// Combined approach for large applications
const dashboardSettingsVisiblePanelsState = atom<string[]>({
  key: 'dashboard_settings_visiblePanels',
  default: ['summary', 'activity', 'notifications']
});
```

#### Naming Structure

Follow a consistent naming structure:

1. **Namespace prefix**: What module/feature/domain the atom belongs to
2. **Entity or purpose**: What data or concept the atom represents
3. **Specificity suffix**: Additional detail if needed
4. **Type indication**: Optional suffix for the type of state (rarely needed)

Examples:
- `userProfile_basicInfo`
- `auth_currentUser`
- `todoList_items`
- `settings_theme`
- `dashboard_visiblePanels`

#### Naming Best Practices

1. **Be consistent**: Choose a pattern and stick with it throughout your application
2. **Be specific**: Names should clearly indicate what state the atom represents
3. **Use camelCase**: Following JavaScript conventions
4. **Avoid overly generic names**: `data`, `value`, `state` are too vague
5. **Include data type only when helpful**: `userIds` is better than `userIdArray`
6. **Consider component scope**: For component-specific atoms, include the component name

#### Key Collision Prevention Strategies

1. **Project-wide prefix**: Use your app or organization name for truly global atoms:
   ```tsx
   const currentUserState = atom<User | null>({
     key: 'myApp_global_currentUser',
     default: null
   });
   ```

2. **Version prefixes**: For libraries or shared code that might be used in multiple places:
   ```tsx
   const themeState = atom<Theme>({
     key: 'uiKit_v1_theme',
     default: lightTheme
   });
   ```

3. **Use constants for keys**:
   ```tsx
   // atomKeys.ts
   export const ATOM_KEYS = {
     USER: {
       PROFILE: {
         BASIC_INFO: 'user_profile_basicInfo',
         PREFERENCES: 'user_profile_preferences'
       },
       SETTINGS: {
         NOTIFICATION: 'user_settings_notification'
       }
     },
     APP: {
       THEME: 'app_theme',
       LANGUAGE: 'app_language'
     }
   };
   
   // Usage
   const userProfileState = atom<UserProfile>({
     key: ATOM_KEYS.USER.PROFILE.BASIC_INFO,
     default: {}
   });
   ```

### Organizing Atoms and Selectors

#### File Structure Strategies

1. **Feature-based organization**:
   ```
   src/
   ├── features/
   │   ├── user/
   │   │   ├── atoms.ts       // All user-related atoms
   │   │   ├── selectors.ts   // All user-related selectors
   │   │   └── hooks.ts       // Custom hooks using the atoms/selectors
   │   ├── cart/
   │   │   ├── atoms.ts
   │   │   ├── selectors.ts
   │   │   └── hooks.ts
   │   └── products/
   │       ├── atoms.ts
   │       ├── selectors.ts
   │       └── hooks.ts
   ```

2. **State type organization**:
   ```
   src/
   ├── state/
   │   ├── local/     // Component-specific atoms
   │   ├── shared/    // Feature-specific atoms
   │   └── global/    // Application-wide atoms
   ```

3. **Domain-based organization**:
   ```
   src/
   ├── state/
   │   ├── entities/         // State representing data entities
   │   │   ├── users.ts
   │   │   ├── products.ts
   │   │   └── orders.ts
   │   ├── ui/               // UI-specific state
   │   │   ├── theme.ts
   │   │   ├── sidebar.ts
   │   │   └── notifications.ts
   │   └── app/              // Application-level state
   │       ├── auth.ts
   │       ├── settings.ts
   │       └── navigation.ts
   ```

4. **Barrel files for easier imports**:
   ```tsx
   // state/index.ts
   export * from './auth';
   export * from './user';
   export * from './settings';
   
   // Then in components:
   import { currentUserState, isAdminState, themeState } from 'state';
   ```

#### Colocating Related State

Group related atoms and selectors together:

```tsx
// userState.ts
import { atom, selector } from 'recoil';

// Basic user info
export const userProfileState = atom<UserProfile>({
  key: 'user_profile',
  default: null
});

// Derived properties
export const userFullNameState = selector<string>({
  key: 'user_fullName',
  get: ({ get }) => {
    const profile = get(userProfileState);
    if (!profile) return '';
    return `${profile.firstName} ${profile.lastName}`;
  }
});

export const userInitialsState = selector<string>({
  key: 'user_initials',
  get: ({ get }) => {
    const profile = get(userProfileState);
    if (!profile) return '';
    return `${profile.firstName[0]}${profile.lastName[0]}`;
  }
});

// User permissions and status
export const userRolesState = atom<string[]>({
  key: 'user_roles',
  default: []
});

export const isAdminState = selector<boolean>({
  key: 'user_isAdmin',
  get: ({ get }) => {
    const roles = get(userRolesState);
    return roles.includes('admin');
  }
});
```

#### Custom Hooks for State Logic

Create hooks that encapsulate state management logic:

```tsx
// useUserAuth.ts
import { useRecoilState, useRecoilValue } from 'recoil';
import { currentUserState, userPermissionsState, isLoggedInState } from './authState';

export function useUserAuth() {
  const [user, setUser] = useRecoilState(currentUserState);
  const [permissions, setPermissions] = useRecoilState(userPermissionsState);
  const isLoggedIn = useRecoilValue(isLoggedInState);
  
  const login = async (username: string, password: string) => {
    try {
      const response = await authApi.login(username, password);
      setUser(response.user);
      setPermissions(response.permissions);
      return true;
    } catch (error) {
      console.error('Login failed:', error);
      return false;
    }
  };
  
  const logout = () => {
    setUser(null);
    setPermissions([]);
  };
  
  const hasPermission = (permission: string) => {
    return permissions.includes(permission);
  };
  
  return {
    user,
    isLoggedIn,
    permissions,
    login,
    logout,
    hasPermission,
  };
}

// Usage in components
function LoginButton() {
  const { login } = useUserAuth();
  
  const handleLogin = async () => {
    const username = prompt('Username:');
    const password = prompt('Password:');
    if (username && password) {
      await login(username, password);
    }
  };
  
  return <button onClick={handleLogin}>Log In</button>;
}
```

### State Structure Recommendations

#### Granular vs. Coarse-Grained Atoms

**Granular Atoms**: Split state into small, focused atoms

```tsx
// Granular approach
const userNameState = atom<string>({
  key: 'user_name',
  default: ''
});

const userEmailState = atom<string>({
  key: 'user_email',
  default: ''
});

const userAgeState = atom<number>({
  key: 'user_age',
  default: 0
});
```

**Coarse-Grained Atoms**: Group related state into single atoms

```tsx
// Coarse-grained approach
const userProfileState = atom<{
  name: string;
  email: string;
  age: number;
}>({
  key: 'user_profile',
  default: {
    name: '',
    email: '',
    age: 0
  }
});
```

**Guidelines for choosing**:
1. Use granular atoms when:
   - Different components need different pieces of the state
   - Parts of the state update at different frequencies
   - You want to minimize re-renders

2. Use coarse-grained atoms when:
   - The data is always used together
   - The data is logically a single entity
   - The data is updated as a unit (e.g., from an API)

#### Handling Complex State Updates

For complex state updates, create utility functions:

```tsx
// todoState.ts
import { atom } from 'recoil';

export interface Todo {
  id: number;
  text: string;
  completed: boolean;
  tags: string[];
}

export const todosState = atom<Todo[]>({
  key: 'todos',
  default: []
});

// Utility functions for updates
export function useTodoActions() {
  const [todos, setTodos] = useRecoilState(todosState);
  
  const addTodo = (text: string) => {
    const newTodo: Todo = {
      id: Date.now(),
      text,
      completed: false,
      tags: []
    };
    setTodos([...todos, newTodo]);
  };
  
  const toggleTodo = (id: number) => {
    setTodos(
      todos.map(todo =>
        todo.id === id ? { ...todo, completed: !todo.completed } : todo
      )
    );
  };
  
  const addTagToTodo = (todoId: number, tag: string) => {
    setTodos(
      todos.map(todo =>
        todo.id === todoId && !todo.tags.includes(tag)
          ? { ...todo, tags: [...todo.tags, tag] }
          : todo
      )
    );
  };
  
  const removeTagFromTodo = (todoId: number, tag: string) => {
    setTodos(
      todos.map(todo =>
        todo.id === todoId
          ? { ...todo, tags: todo.tags.filter(t => t !== tag) }
          : todo
      )
    );
  };
  
  return {
    todos,
    addTodo,
    toggleTodo,
    addTagToTodo,
    removeTagFromTodo
  };
}
```

#### State Dependencies and Derived State

Use selectors for derived state that depends on multiple atoms:

```tsx
// cartState.ts
import { atom, selector } from 'recoil';

interface CartItem {
  id: string;
  name: string;
  price: number;
  quantity: number;
}

export const cartItemsState = atom<CartItem[]>({
  key: 'cart_items',
  default: []
});

export const taxRateState = atom<number>({
  key: 'cart_taxRate',
  default: 0.1 // 10% tax
});

export const shippingCostState = atom<number>({
  key: 'cart_shippingCost',
  default: 5.99
});

// Derived state
export const subtotalState = selector<number>({
  key: 'cart_subtotal',
  get: ({ get }) => {
    const items = get(cartItemsState);
    return items.reduce((sum, item) => sum + (item.price * item.quantity), 0);
  }
});

export const taxAmountState = selector<number>({
  key: 'cart_taxAmount',
  get: ({ get }) => {
    const subtotal = get(subtotalState);
    const taxRate = get(taxRateState);
    return subtotal * taxRate;
  }
});

export const totalPriceState = selector<number>({
  key: 'cart_totalPrice',
  get: ({ get }) => {
    const subtotal = get(subtotalState);
    const taxAmount = get(taxAmountState);
    const shipping = get(shippingCostState);
    return subtotal + taxAmount + shipping;
  }
});
```

## Testing with Recoil

Testing components that use Recoil state requires special considerations. This section covers different testing strategies for Recoil atoms, selectors, and components.

### Setting Up Your Testing Environment

First, install the necessary testing packages:

```bash
npm install --save-dev jest @testing-library/react @testing-library/jest-dom @testing-library/user-event
```

For TypeScript support:

```bash
npm install --save-dev @types/jest ts-jest
```

### Testing Atoms and Selectors

#### Unit Testing Atoms

Testing atoms directly is straightforward since they're simple state containers:

```tsx
// atoms.ts
import { atom } from 'recoil';

export const counterState = atom<number>({
  key: 'counterState',
  default: 0,
});

// atoms.test.ts
import { counterState } from './atoms';

describe('Atom Tests', () => {
  test('counterState should have the correct key and default value', () => {
    expect(counterState.key).toBe('counterState');
    // Using snapshot testing for the config
    expect(counterState).toMatchSnapshot();
  });
});
```

Explanation of atom testing:

1. **What's Being Tested**: We're verifying that:
   - The atom has the expected key (important for persistence)
   - The atom has appropriate configuration (via snapshot testing)

2. **Snapshot Testing**: The `toMatchSnapshot()` matcher saves the serialized atom configuration and verifies it doesn't change unexpectedly in the future.

3. **Limitations**: This basic testing mainly verifies the atom's setup, not its runtime behavior in components.

4. **Why Test Atoms**: While simple, these tests help catch:
   - Accidental key changes (which would break persistence)
   - Unintended configuration changes
   - Atoms with invalid default values

#### Unit Testing Selectors

For selectors, you can test the transformation logic by mocking the atom values:

```tsx
// selectors.ts
import { selector } from 'recoil';
import { counterState } from './atoms';

export const doubledCounterState = selector({
  key: 'doubledCounterState',
  get: ({ get }) => {
    const count = get(counterState);
    return count * 2;
  },
});

// selectors.test.ts
import { snapshot_UNSTABLE } from 'recoil';
import { counterState } from './atoms';
import { doubledCounterState } from './selectors';

describe('Selector Tests', () => {
  test('doubledCounterState should double the counter value', () => {
    // Create a snapshot of Recoil state for testing
    const initialSnapshot = snapshot_UNSTABLE();
    
    // Test with initial value
    expect(initialSnapshot.getLoadable(doubledCounterState).valueOrThrow()).toBe(0);
    
    // Create a new snapshot with modified atom state
    const testSnapshot = snapshot_UNSTABLE(({ set }) => set(counterState, 5));
    
    // Test with modified value
    expect(testSnapshot.getLoadable(doubledCounterState).valueOrThrow()).toBe(10);
  });
});
```

Explanation of selector testing:

1. **Testing Approach**: We test selectors by:
   - Creating a Recoil snapshot with `snapshot_UNSTABLE`
   - Setting initial atom values with the snapshot's `set` method
   - Extracting selector values with `getLoadable().valueOrThrow()`
   - Verifying the selector computes the expected result

2. **Test Scenarios**:
   - Test with the default/initial state
   - Test with modified atom values
   - For complex selectors, test edge cases (empty arrays, null values, etc.)

3. **Snapshot vs. Loadable**:
   - A snapshot is an immutable copy of the entire Recoil state
   - A loadable represents the current status of an atom/selector (value, loading, or error)
   - `valueOrThrow()` gets the value if available or throws if there's an error

4. **Testing Pure Logic**: This approach tests the selector's pure calculation logic, separate from React components.

### Testing Components with Recoil State

#### Testing Components Using RecoilRoot

To test components that use Recoil state, wrap them in a `RecoilRoot` in your tests:

```tsx
// Counter.tsx
import React from 'react';
import { useRecoilState } from 'recoil';
import { counterState } from './atoms';

export const Counter: React.FC = () => {
  const [count, setCount] = useRecoilState(counterState);
  
  return (
    <div>
      <h1 data-testid="count-display">Count: {count}</h1>
      <button 
        data-testid="increment-button"
        onClick={() => setCount(count + 1)}
      >
        Increment
      </button>
    </div>
  );
};

// Counter.test.tsx
import React from 'react';
import { render, screen, fireEvent } from '@testing-library/react';
import { RecoilRoot } from 'recoil';
import { Counter } from './Counter';

describe('Counter Component', () => {
  test('renders with initial count of 0', () => {
    render(
      <RecoilRoot>
        <Counter />
      </RecoilRoot>
    );
    
    expect(screen.getByTestId('count-display')).toHaveTextContent('Count: 0');
  });
  
  test('increments count when button is clicked', () => {
    render(
      <RecoilRoot>
        <Counter />
      </RecoilRoot>
    );
    
    fireEvent.click(screen.getByTestId('increment-button'));
    expect(screen.getByTestId('count-display')).toHaveTextContent('Count: 1');
  });
});
```

#### Testing Components with Initialized State

Sometimes you need to test a component with a specific initial state:

```tsx
// Counter.test.tsx - with initialized state
import React from 'react';
import { render, screen } from '@testing-library/react';
import { RecoilRoot, MutableSnapshot } from 'recoil';
import { counterState } from './atoms';
import { Counter } from './Counter';

describe('Counter Component with Initialized State', () => {
  test('renders with initial count of 10', () => {
    // Initialize Recoil state for testing
    const initializeState = ({ set }: MutableSnapshot) => {
      set(counterState, 10);
    };
    
    render(
      <RecoilRoot initializeState={initializeState}>
        <Counter />
      </RecoilRoot>
    );
    
    expect(screen.getByTestId('count-display')).toHaveTextContent('Count: 10');
  });
});
```

### Testing Strategies for Different State Patterns

#### Testing Local State

When testing components that use local Recoil state (state used only by one component), the standard approach with `RecoilRoot` works well:

```tsx
// LocalStateComponent.tsx
import React from 'react';
import { atom, useRecoilState, RecoilRoot } from 'recoil';

// Local atom defined within the component file
const localCounterState = atom<number>({
  key: 'localCounterState',
  default: 0,
});

export const LocalCounter: React.FC = () => {
  const [count, setCount] = useRecoilState(localCounterState);
  
  return (
    <div>
      <h1 data-testid="local-count">Local Count: {count}</h1>
      <button 
        data-testid="local-increment"
        onClick={() => setCount(count + 1)}
      >
        Increment Local
      </button>
    </div>
  );
};

// Wrapper component that provides its own RecoilRoot
export const LocalStateExample: React.FC = () => (
  <RecoilRoot>
    <LocalCounter />
  </RecoilRoot>
);

// LocalStateComponent.test.tsx
import React from 'react';
import { render, screen, fireEvent } from '@testing-library/react';
import { LocalCounter, LocalStateExample } from './LocalStateComponent';
import { RecoilRoot } from 'recoil';

describe('Local State Testing', () => {
  test('LocalCounter increments when used directly with RecoilRoot', () => {
    render(
      <RecoilRoot>
        <LocalCounter />
      </RecoilRoot>
    );
    
    expect(screen.getByTestId('local-count')).toHaveTextContent('Local Count: 0');
    fireEvent.click(screen.getByTestId('local-increment'));
    expect(screen.getByTestId('local-count')).toHaveTextContent('Local Count: 1');
  });
  
  test('LocalStateExample works with its own RecoilRoot', () => {
    render(<LocalStateExample />);
    
    expect(screen.getByTestId('local-count')).toHaveTextContent('Local Count: 0');
    fireEvent.click(screen.getByTestId('local-increment'));
    expect(screen.getByTestId('local-count')).toHaveTextContent('Local Count: 1');
  });
});
```

#### Testing Shared State

When testing components that share state, ensure they're wrapped in the same `RecoilRoot`:

```tsx
// SharedState.tsx
import React from 'react';
import { atom, useRecoilState, useRecoilValue } from 'recoil';

export const sharedCounterState = atom<number>({
  key: 'sharedCounterState',
  default: 0,
});

export const CounterControls: React.FC = () => {
  const [count, setCount] = useRecoilState(sharedCounterState);
  
  return (
    <div>
      <button 
        data-testid="shared-increment"
        onClick={() => setCount(count + 1)}
      >
        Increment Shared
      </button>
      <button 
        data-testid="shared-reset"
        onClick={() => setCount(0)}
      >
        Reset
      </button>
    </div>
  );
};

export const CounterDisplay: React.FC = () => {
  const count = useRecoilValue(sharedCounterState);
  
  return <h1 data-testid="shared-count">Shared Count: {count}</h1>;
};

// SharedState.test.tsx
import React from 'react';
import { render, screen, fireEvent } from '@testing-library/react';
import { RecoilRoot } from 'recoil';
import { CounterControls, CounterDisplay, sharedCounterState } from './SharedState';

describe('Shared State Testing', () => {
  test('Components share state through Recoil', () => {
    render(
      <RecoilRoot>
        <CounterDisplay />
        <CounterControls />
      </RecoilRoot>
    );
    
    // Initial state
    expect(screen.getByTestId('shared-count')).toHaveTextContent('Shared Count: 0');
    
    // Increment changes state for both components
    fireEvent.click(screen.getByTestId('shared-increment'));
    expect(screen.getByTestId('shared-count')).toHaveTextContent('Shared Count: 1');
    
    // Reset changes state for both components
    fireEvent.click(screen.getByTestId('shared-reset'));
    expect(screen.getByTestId('shared-count')).toHaveTextContent('Shared Count: 0');
  });
  
  test('Components with initialized shared state', () => {
    render(
      <RecoilRoot initializeState={({ set }) => set(sharedCounterState, 5)}>
        <CounterDisplay />
        <CounterControls />
      </RecoilRoot>
    );
    
    expect(screen.getByTestId('shared-count')).toHaveTextContent('Shared Count: 5');
  });
});
```

#### Testing Global State

For testing components that use global application state:

```tsx
// globalState.ts
import { atom, selector } from 'recoil';

// Define user state
export interface User {
  id: string;
  name: string;
  isAdmin: boolean;
}

export const currentUserState = atom<User | null>({
  key: 'currentUserState',
  default: null,
});

export const isLoggedInState = selector({
  key: 'isLoggedInState',
  get: ({ get }) => {
    return get(currentUserState) !== null;
  },
});

export const isAdminState = selector({
  key: 'isAdminState',
  get: ({ get }) => {
    const user = get(currentUserState);
    return user?.isAdmin ?? false;
  },
});

// UserProfile.tsx
import React from 'react';
import { useRecoilValue, useSetRecoilState } from 'recoil';
import { currentUserState, isLoggedInState, isAdminState, User } from './globalState';

export const UserProfile: React.FC = () => {
  const isLoggedIn = useRecoilValue(isLoggedInState);
  const isAdmin = useRecoilValue(isAdminState);
  const user = useRecoilValue(currentUserState);
  const setUser = useSetRecoilState(currentUserState);
  
  const handleLogin = () => {
    setUser({
      id: '1',
      name: 'Test User',
      isAdmin: false,
    });
  };
  
  const handleLogout = () => {
    setUser(null);
  };
  
  const handleAdminToggle = () => {
    if (user) {
      setUser({
        ...user,
        isAdmin: !user.isAdmin,
      });
    }
  };
  
  if (!isLoggedIn) {
    return (
      <button data-testid="login-button" onClick={handleLogin}>
        Log in
      </button>
    );
  }
  
  return (
    <div>
      <h1 data-testid="user-name">{user?.name}</h1>
      <div data-testid="admin-status">
        {isAdmin ? 'Admin User' : 'Regular User'}
      </div>
      <button data-testid="toggle-admin" onClick={handleAdminToggle}>
        Toggle Admin Status
      </button>
      <button data-testid="logout-button" onClick={handleLogout}>
        Log out
      </button>
    </div>
  );
};

// UserProfile.test.tsx
import React from 'react';
import { render, screen, fireEvent } from '@testing-library/react';
import { RecoilRoot } from 'recoil';
import { UserProfile } from './UserProfile';
import { currentUserState, User } from './globalState';

describe('UserProfile with Global State', () => {
  test('shows login button when not logged in', () => {
    render(
      <RecoilRoot>
        <UserProfile />
      </RecoilRoot>
    );
    
    expect(screen.getByTestId('login-button')).toBeInTheDocument();
  });
  
  test('shows user information when logged in', () => {
    const testUser: User = {
      id: '1',
      name: 'Test User',
      isAdmin: false,
    };
    
    render(
      <RecoilRoot initializeState={({ set }) => set(currentUserState, testUser)}>
        <UserProfile />
      </RecoilRoot>
    );
    
    expect(screen.getByTestId('user-name')).toHaveTextContent('Test User');
    expect(screen.getByTestId('admin-status')).toHaveTextContent('Regular User');
  });
  
  test('can toggle admin status', () => {
    const testUser: User = {
      id: '1',
      name: 'Test User',
      isAdmin: false,
    };
    
    render(
      <RecoilRoot initializeState={({ set }) => set(currentUserState, testUser)}>
        <UserProfile />
      </RecoilRoot>
    );
    
    expect(screen.getByTestId('admin-status')).toHaveTextContent('Regular User');
    
    fireEvent.click(screen.getByTestId('toggle-admin'));
    expect(screen.getByTestId('admin-status')).toHaveTextContent('Admin User');
  });
  
  test('can log out', () => {
    const testUser: User = {
      id: '1',
      name: 'Test User',
      isAdmin: false,
    };
    
    render(
      <RecoilRoot initializeState={({ set }) => set(currentUserState, testUser)}>
        <UserProfile />
      </RecoilRoot>
    );
    
    expect(screen.getByTestId('user-name')).toBeInTheDocument();
    
    fireEvent.click(screen.getByTestId('logout-button'));
    expect(screen.getByTestId('login-button')).toBeInTheDocument();
  });
});
```

### Testing Async Selectors

Testing asynchronous selectors requires handling promises properly:

```tsx
// asyncSelectors.ts
import { atom, selector } from 'recoil';

export const userIdState = atom<string | null>({
  key: 'userId',
  default: null,
});

export interface UserData {
  id: string;
  name: string;
  email: string;
}

// Mock API function for testing
export const fetchUserData = async (id: string): Promise<UserData> => {
  // In a real app, this would be an API call
  return {
    id,
    name: `User ${id}`,
    email: `user${id}@example.com`,
  };
};

export const userDataQuery = selector<UserData | null>({
  key: 'userData',
  get: async ({ get }) => {
    const userId = get(userIdState);
    
    if (!userId) return null;
    
    try {
      return await fetchUserData(userId);
    } catch (error) {
      console.error('Error fetching user data:', error);
      throw error;
    }
  },
});

// asyncSelectors.test.ts
import { snapshot_UNSTABLE } from 'recoil';
import { userIdState, userDataQuery, fetchUserData } from './asyncSelectors';

// Mock the fetch function
jest.mock('./asyncSelectors', () => ({
  ...jest.requireActual('./asyncSelectors'),
  fetchUserData: jest.fn(),
}));

const mockFetchUserData = fetchUserData as jest.MockedFunction<typeof fetchUserData>;

describe('Async Selector Tests', () => {
  beforeEach(() => {
    mockFetchUserData.mockClear();
  });
  
  test('userDataQuery returns null when userId is null', async () => {
    const testSnapshot = snapshot_UNSTABLE();
    const result = await testSnapshot.getPromise(userDataQuery);
    
    expect(result).toBeNull();
    expect(mockFetchUserData).not.toHaveBeenCalled();
  });
  
  test('userDataQuery fetches user data when userId is provided', async () => {
    mockFetchUserData.mockResolvedValueOnce({
      id: '123',
      name: 'Test User',
      email: 'test@example.com',
    });
    
    const testSnapshot = snapshot_UNSTABLE(({ set }) => set(userIdState, '123'));
    const result = await testSnapshot.getPromise(userDataQuery);
    
    expect(mockFetchUserData).toHaveBeenCalledWith('123');
    expect(result).toEqual({
      id: '123',
      name: 'Test User',
      email: 'test@example.com',
    });
  });
  
  test('userDataQuery handles errors', async () => {
    mockFetchUserData.mockRejectedValueOnce(new Error('API error'));
    
    const testSnapshot = snapshot_UNSTABLE(({ set }) => set(userIdState, '456'));
    
    await expect(testSnapshot.getPromise(userDataQuery)).rejects.toThrow('API error');
  });
});
```

### Testing Components with Suspense

When testing components that use Suspense with Recoil, you need to account for the loading state:

```tsx
// UserDataComponent.tsx
import React, { Suspense } from 'react';
import { useRecoilValue, useSetRecoilState } from 'recoil';
import { userIdState, userDataQuery } from './asyncSelectors';

const UserData: React.FC = () => {
  const userData = useRecoilValue(userDataQuery);
  
  if (!userData) {
    return <div data-testid="no-user">No user selected</div>;
  }
  
  return (
    <div data-testid="user-data">
      <h1>{userData.name}</h1>
      <p>{userData.email}</p>
    </div>
  );
};

export const UserDataContainer: React.FC = () => {
  const setUserId = useSetRecoilState(userIdState);
  
  return (
    <div>
      <button 
        data-testid="load-user-1"
        onClick={() => setUserId('1')}
      >
        Load User 1
      </button>
      
      <button 
        data-testid="clear-user"
        onClick={() => setUserId(null)}
      >
        Clear User
      </button>
      
      <Suspense fallback={<div data-testid="loading">Loading...</div>}>
        <UserData />
      </Suspense>
    </div>
  );
};

// UserDataComponent.test.tsx
import React from 'react';
import { render, screen, fireEvent, act } from '@testing-library/react';
import { RecoilRoot } from 'recoil';
import { UserDataContainer } from './UserDataComponent';
import { fetchUserData } from './asyncSelectors';

// Mock the fetch function
jest.mock('./asyncSelectors', () => ({
  ...jest.requireActual('./asyncSelectors'),
  fetchUserData: jest.fn(),
}));

const mockFetchUserData = fetchUserData as jest.MockedFunction<typeof fetchUserData>;

describe('UserDataComponent with Suspense', () => {
  beforeEach(() => {
    mockFetchUserData.mockClear();
  });
  
  test('shows loading state and then user data', async () => {
    // Set up the mock to resolve after a small delay
    mockFetchUserData.mockImplementation(async (id) => {
      await new Promise(resolve => setTimeout(resolve, 100));
      return {
        id,
        name: `User ${id}`,
        email: `user${id}@example.com`,
      };
    });
    
    render(
      <RecoilRoot>
        <UserDataContainer />
      </RecoilRoot>
    );
    
    // Initially shows "No user selected"
    expect(screen.getByTestId('no-user')).toBeInTheDocument();
    
    // Click to load user
    await act(async () => {
      fireEvent.click(screen.getByTestId('load-user-1'));
    });
    
    // Should show loading state
    expect(screen.getByTestId('loading')).toBeInTheDocument();
    
    // Wait for data to load and check the result
    await screen.findByTestId('user-data');
    expect(screen.getByText('User 1')).toBeInTheDocument();
    expect(screen.getByText('user1@example.com')).toBeInTheDocument();
  });
  
  test('can clear user data', async () => {
    mockFetchUserData.mockResolvedValue({
      id: '1',
      name: 'User 1',
      email: 'user1@example.com',
    });
    
    render(
      <RecoilRoot>
        <UserDataContainer />
      </RecoilRoot>
    );
    
    // Click to load user
    await act(async () => {
      fireEvent.click(screen.getByTestId('load-user-1'));
    });
    
    // Wait for data to load
    await screen.findByTestId('user-data');
    
    // Clear the user
    fireEvent.click(screen.getByTestId('clear-user'));
    
    // Should show "No user selected" again
    expect(screen.getByTestId('no-user')).toBeInTheDocument();
  });
});
```

### Mocking Recoil Hooks

Sometimes you might want to mock Recoil hooks for component unit tests:

```tsx
// useCounter.ts
import { atom, useRecoilState } from 'recoil';

const counterState = atom<number>({
  key: 'counterState',
  default: 0,
});

export const useCounter = () => {
  const [count, setCount] = useRecoilState(counterState);
  
  const increment = () => setCount(count + 1);
  const decrement = () => setCount(count - 1);
  const reset = () => setCount(0);
  
  return {
    count,
    increment,
    decrement,
    reset,
  };
};

// CounterDisplay.tsx
import React from 'react';
import { useCounter } from './useCounter';

export const CounterDisplay: React.FC = () => {
  const { count, increment, decrement, reset } = useCounter();
  
  return (
    <div>
      <h1 data-testid="count-value">{count}</h1>
      <button data-testid="increment" onClick={increment}>+</button>
      <button data-testid="decrement" onClick={decrement}>-</button>
      <button data-testid="reset" onClick={reset}>Reset</button>
    </div>
  );
};

// CounterDisplay.test.tsx (with mocked hook)
import React from 'react';
import { render, screen, fireEvent } from '@testing-library/react';
import { CounterDisplay } from './CounterDisplay';
import { useCounter } from './useCounter';

// Mock the hook
jest.mock('./useCounter');

const mockUseCounter = useCounter as jest.MockedFunction<typeof useCounter>;

describe('CounterDisplay with mocked hook', () => {
  test('displays counter value and calls methods correctly', () => {
    const mockIncrement = jest.fn();
    const mockDecrement = jest.fn();
    const mockReset = jest.fn();
    
    mockUseCounter.mockReturnValue({
      count: 42,
      increment: mockIncrement,
      decrement: mockDecrement,
      reset: mockReset,
    });
    
    render(<CounterDisplay />);
    
    expect(screen.getByTestId('count-value')).toHaveTextContent('42');
    
    fireEvent.click(screen.getByTestId('increment'));
    expect(mockIncrement).toHaveBeenCalledTimes(1);
    
    fireEvent.click(screen.getByTestId('decrement'));
    expect(mockDecrement).toHaveBeenCalledTimes(1);
    
    fireEvent.click(screen.getByTestId('reset'));
    expect(mockReset).toHaveBeenCalledTimes(1);
  });
});
```

### Testing Best Practices

1. **Isolate tests**: Each test should focus on a specific aspect of functionality.

2. **Reset state between tests**: Use `beforeEach` to reset any mocks or state.

3. **Use data-testid attributes**: These make components easier to query in tests.

4. **Test state transitions**: Verify that state changes correctly in response to actions.

5. **Mock external dependencies**: Isolate Recoil tests by mocking API calls or other external dependencies.

6. **Test error states**: Ensure your components handle error states gracefully.

7. **Avoid testing implementation details**: Focus on testing behavior visible to users.

8. **Create test utilities**: For complex state setups, create utility functions:

```tsx
// testUtils.ts
import { MutableSnapshot, RecoilRoot } from 'recoil';
import { render, RenderOptions } from '@testing-library/react';

interface RecoilTestOptions extends RenderOptions {
  initializeState?: (snapshot: MutableSnapshot) => void;
}

export function renderWithRecoil(
  ui: React.ReactElement,
  options?: RecoilTestOptions
) {
  const { initializeState, ...renderOptions } = options || {};
  
  return render(
    <RecoilRoot initializeState={initializeState}>
      {ui}
    </RecoilRoot>,
    renderOptions
  );
}

// Usage in tests
import { renderWithRecoil } from './testUtils';
import { counterState } from './atoms';

test('Counter starts with specified value', () => {
  renderWithRecoil(<Counter />, {
    initializeState: ({ set }) => set(counterState, 10),
  });
  
  expect(screen.getByTestId('count-display')).toHaveTextContent('Count: 10');
});
```

## Troubleshooting

### Duplicate Atom Key Error

```
Duplicate atom key "myAtom". This is a serious error. Atom keys must be unique.
```

**Solution**: Ensure all atom and selector keys are unique throughout your application.

### Component Not Updating

If your component isn't updating when an atom changes:

1. Verify you're using `useRecoilState` or `useRecoilValue` and not just reading the atom directly
2. Check if you're modifying the state correctly (creating new references for objects/arrays)
3. Ensure your component is a child of `RecoilRoot`

### TypeScript Errors

For TypeScript errors related to missing types:

```tsx
// Make sure to properly type your atoms and selectors
const myAtom = atom<string | null>({
  key: 'myAtom',
  default: null,
});

// For complex objects, define interfaces
interface User {
  id: string;
  name: string;
  email: string;
}

const userState = atom<User | null>({
  key: 'user',
  default: null,
});
```

## Conclusion

Recoil provides a powerful and flexible state management solution for React applications with excellent TypeScript support. By leveraging atoms for state pieces and selectors for derived state, you can build applications with complex state requirements while maintaining type safety and code readability.

Remember to follow the best practices mentioned above to ensure your application remains maintainable and performant as it grows.
