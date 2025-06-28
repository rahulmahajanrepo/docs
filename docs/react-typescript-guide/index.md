# React 18+ with TypeScript: From Beginner to Advanced

## Table of Contents

### Part 1: React Fundamentals
1. [Introduction to React 18](./01-fundamentals/01-introduction.md)
   - What is React?
   - Virtual DOM
   - Key features in React 18
   - Client-side rendering with React 18

2. [Setting Up a React TypeScript Project](./01-fundamentals/02-setup.md)
   - Create React App with TypeScript
   - Vite with TypeScript
   - Project structure
   - TypeScript configuration

3. [Components and JSX](./01-fundamentals/03-components-jsx.md)
   - Functional Components
   - Class Components
   - JSX Syntax
   - TypeScript interfaces for Props
   - Example: Creating a Button component

4. [Props and TypeScript](./01-fundamentals/04-props-typescript.md)
   - Typing props
   - Required vs optional props
   - Default props
   - Children props
   - Generic components
   - Example: Creating a reusable Card component

### Part 2: React State and Lifecycle
5. [Component State](./02-state-lifecycle/01-component-state.md)
   - useState hook
   - State with TypeScript
   - Complex state objects
   - Example: Counter application with typed state

6. [Lifecycle and Effects](./02-state-lifecycle/02-lifecycle-effects.md)
   - Component lifecycle
   - useEffect hook
   - Cleanup functions
   - Dependency arrays
   - Example: Data fetching component

7. [Handling Events](./02-state-lifecycle/03-handling-events.md)
   - Event handling with TypeScript
   - Event types
   - Synthetic events
   - Example: Form inputs with event handling

### Part 3: Testing React Applications
8. [Testing Libraries Overview](./03-testing/01-testing-libraries.md)
   - Jest
   - React Testing Library
   - Enzyme
   - Vitest
   - Comparison table and recommendations

9. [Component Testing](./03-testing/02-component-testing.md)
   - Rendering components
   - Querying elements
   - Asserting rendered output
   - Example: Testing the Button component

10. [Testing User Interactions](./03-testing/03-interaction-testing.md)
    - Simulating user events
    - Testing form submissions
    - Example: Testing form validation

11. [Testing Hooks](./03-testing/04-testing-hooks.md)
    - Testing custom hooks
    - @testing-library/react-hooks
    - Example: Testing a data fetching hook

### Part 4: State Management
12. [React's Built-in State Management](./04-state-management/01-builtin-state.md)
    - Local state with useState
    - Lifting state up
    - When to use built-in state
    - Example: Parent-child state sharing

13. [Context API](./04-state-management/02-context-api.md)
    - Creating context with TypeScript
    - useContext hook
    - Context providers and consumers
    - Example: Theme switcher with Context

14. [Recoil](./04-state-management/03-recoil.md)
    - Core concepts
    - Atoms and selectors
    - Async data handling
    - TypeScript integration
    - Example: Todo application with Recoil

15. [State Management Comparison](./04-state-management/04-state-comparison.md)
    - Comparison table (useState, useReducer, Context, Redux, Recoil, Zustand, MobX)
    - Decision matrix: When to use what
    - Performance considerations
    - Example: Same app implemented with different state managers

### Part 5: Advanced Hooks
16. [useReducer](./05-advanced-hooks/01-usereducer.md)
    - Complex state logic
    - TypeScript with reducers
    - Example: Shopping cart with useReducer

17. [useMemo and useCallback](./05-advanced-hooks/02-usememo-usecallback.md)
    - Performance optimization
    - Memoization concepts
    - When to use (and not use)
    - TypeScript typing
    - Comparison with React.memo
    - Examples: optimizing expensive calculations

18. [useRef](./05-advanced-hooks/03-useref.md)
    - DOM references
    - Mutable values
    - TypeScript with refs
    - Example: Focus management

19. [Custom Hooks](./05-advanced-hooks/04-custom-hooks.md)
    - Creating reusable hooks
    - TypeScript with custom hooks
    - Example: useFetch data fetching hook

### Part 6: Advanced Patterns
20. [Higher-Order Components](./06-advanced-patterns/01-hocs.md)
    - HOC pattern with TypeScript
    - Common use cases
    - Example: withAuthentication HOC

21. [Render Props](./06-advanced-patterns/02-render-props.md)
    - Render props pattern
    - TypeScript implementation
    - Example: Toggle functionality

22. [Compound Components](./06-advanced-patterns/03-compound-components.md)
    - Building flexible component APIs
    - TypeScript challenges
    - Example: Tabs component

### Part 7: Design Patterns for React Applications
23. [Component Design Patterns](./07-design-patterns/01-component-patterns.md)
    - Controlled vs Uncontrolled components
    - Container/Presentational pattern
    - Smart vs Dumb components
    - Example: Form component patterns

24. [Application Architecture Patterns](./07-design-patterns/02-architecture-patterns.md)
    - Atomic Design methodology
    - Feature-based architecture
    - Domain-driven design with React
    - Example: Organizing a complex React application

25. [State Management Patterns](./07-design-patterns/03-state-patterns.md)
    - Finite State Machines with React
    - Command pattern for actions
    - Event sourcing in frontend
    - Example: Implementation using XState

26. [React 18 Concurrent Patterns](./07-design-patterns/04-concurrent-patterns.md)
    - Patterns for concurrent rendering
    - Work with transitions effectively
    - Leveraging React 18 features
    - Example: Search-as-you-type with concurrent features

27. [Design Systems](./07-design-patterns/05-design-systems.md)
    - Building a component library
    - Theming and styling patterns
    - Component API design
    - Example: Creating a themeable button system

### Part 8: Performance Optimization
28. [React.memo](./08-performance/01-react-memo.md)
    - Preventing unnecessary renders
    - TypeScript with React.memo
    - Example: Optimizing list rendering

29. [Code Splitting](./08-performance/02-code-splitting.md)
    - React.lazy and Suspense for client components
    - Route-based code splitting
    - Example: Lazy loading components

30. [Profiling and Debugging](./08-performance/03-profiling.md)
    - React DevTools
    - Performance measurement
    - Common performance issues

31. [Client-side Rendering Optimizations](./08-performance/04-client-rendering-optimizations.md)
    - Virtualized lists
    - Windowing techniques
    - Optimizing animations
    - Example: Implementing react-window

### Part 9: Micro Frontends
32. [Micro Frontend Architecture](./09-micro-frontends/01-architecture.md)
    - Concepts and benefits
    - Client-side composition approaches
    - TypeScript considerations

33. [Module Federation](./09-micro-frontends/02-module-federation.md)
    - Client-side setup and configuration
    - Sharing components
    - TypeScript challenges
    - Example: Creating micro frontend apps

34. [Sharing State Between Micro Frontends](./09-micro-frontends/03-sharing-state.md)
    - Communication patterns
    - Client-side state management strategies
    - Example: Connected micro frontends

### Part 10: Build Tools
35. [Webpack for React](./10-build-tools/01-webpack.md)
    - Configuration for React + TypeScript
    - Client-side optimizations
    - Common plugins

36. [Vite for React](./10-build-tools/02-vite.md)
    - Setup and configuration
    - Performance benefits
    - HMR with TypeScript

37. [Build Tools Comparison](./10-build-tools/03-tools-comparison.md)
    - Webpack vs Vite
    - Performance benchmarks
    - Development experience
    - Client-side build optimizations
    - Which to choose and when

## Appendices
- [TypeScript Cheat Sheet for React](./appendices/typescript-cheatsheet.md)
- [React 18 API Reference](./appendices/react18-api.md)
- [Recommended Libraries](./appendices/recommended-libraries.md)
- [Common Patterns and Anti-patterns](./appendices/patterns-antipatterns.md)
