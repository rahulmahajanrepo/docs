# React SPA Development Guide for Angular Developers

## Table of Contents
- [React SPA Development Guide for Angular Developers](#react-spa-development-guide-for-angular-developers)
  - [Table of Contents](#table-of-contents)
  - [Introduction](#introduction)
  - [React vs Angular: A Comparison](#react-vs-angular-a-comparison)
    - [Key Differences](#key-differences)
    - [Mental Model Shift](#mental-model-shift)
  - [Application Architecture](#application-architecture)
  - [Folder Structure](#folder-structure)
    - [Best Practices for Folder Structure](#best-practices-for-folder-structure)
  - [State Management](#state-management)
    - [Recoil](#recoil)
      - [Key Concepts](#key-concepts)
      - [Example: Setting Up Recoil](#example-setting-up-recoil)
    - [React's Built-in State Management](#reacts-built-in-state-management)
      - [When to Use Each](#when-to-use-each)
    - [When to Use What](#when-to-use-what)
      - [Best Practices](#best-practices)
  - [HTTP Services with Axios](#http-services-with-axios)
    - [Setting Up Axios](#setting-up-axios)
    - [Interceptors](#interceptors)
    - [Error Handling](#error-handling)
    - [API Service Pattern](#api-service-pattern)
  - [Component Design Best Practices](#component-design-best-practices)
  - [Naming Conventions](#naming-conventions)
  - [Sharing Components](#sharing-components)
    - [Example Directory Structure for Sharing Components](#example-directory-structure-for-sharing-components)
  - [Conclusion](#conclusion)

## Introduction

This guide is designed for developers transitioning from Angular to React, focusing on building a Single Page Application (SPA) with TypeScript, Recoil for state management, and best practices for REST API integration.

## React vs Angular: A Comparison

### Key Differences

| Aspect | React | Angular |
|--------|-------|---------|
| Type | Library | Framework |
| DOM Manipulation | Virtual DOM | Real DOM with change detection |
| Data Binding | One-way | Two-way (via NgModel) |
| State Management | External (Redux, Recoil, etc.) | Built-in services and RxJS |
| Templating | JSX/TSX (JavaScript/TypeScript XML) | HTML templates with directives |
| Dependency Injection | Not built-in (can use Context) | Built-in DI system |
| Routing | React Router (separate package) | Angular Router (built-in) |
| Form Handling | Uncontrolled or libraries like Formik | Reactive and Template-driven forms |
| TypeScript | Optional but well-supported | Core part of Angular |
| Learning Curve | Moderate | Steep |

### Mental Model Shift

Coming from Angular, the biggest mental shifts you'll need to make:

1. **Component Philosophy**: React components are more functional and less class-based
2. **Templates**: JSX/TSX instead of separate HTML templates
3. **State Management**: No built-in services or RxJS observables
4. **Module System**: No NgModules, just ES modules
5. **Lifecycle Hooks**: Different names and behavior (useEffect vs ngOnInit, etc.)

## Application Architecture

![React SPA Architecture](./Application-flow.png)

A well-structured React SPA consists of:

1. **Core Layer**: Authentication, HTTP client, error handling, logging
2. **Feature Modules**: Domain-specific functionality
3. **Shared Layer**: Reusable components, utilities, hooks
4. **State Management**: Global and local state

## Folder Structure

```
src/
├── assets/                  # Static assets
│   ├── images/
│   └── styles/
├── core/                    # Core functionality
│   ├── auth/                # Authentication
│   ├── http/                # HTTP client setup
│   ├── error-handling/      # Global error handling
│   └── logging/             # Logging services
├── shared/                  # Shared resources
│   ├── components/          # Reusable UI components
│   ├── hooks/               # Custom React hooks
│   ├── utils/               # Utility functions
│   ├── constants/           # Application constants
│   └── types/               # TypeScript types/interfaces
├── features/                # Feature modules
│   ├── feature1/
│   │   ├── components/      # Feature-specific components
│   │   ├── hooks/           # Feature-specific hooks
│   │   ├── state/           # Feature-specific state
│   │   ├── api/             # API service for this feature
│   │   └── types/           # Feature-specific types
│   ├── feature2/
│   └── feature3/
├── recoil/                  # Recoil state management
│   ├── atoms/               # State atoms
│   ├── selectors/           # Derived state
│   └── hooks/               # Recoil-related hooks
├── App.tsx                  # Root component
└── index.tsx                # Entry point
```

### Best Practices for Folder Structure

1. **Feature-First Organization**: Group by feature rather than technical role
2. **Colocation**: Keep related files close together
3. **Shared Components**: Extract truly reusable components to the shared folder
4. **Module Boundaries**: Clear import/export boundaries between features
5. **Index Files**: Use barrel exports for cleaner imports

## State Management

### Recoil

Recoil is a state management library for React that provides a way to manage global state with atomic model and derived state.

#### Key Concepts

1. **Atoms**: Basic units of state
2. **Selectors**: Derived state from atoms or other selectors
3. **RecoilRoot**: Provider component that wraps your app

#### Example: Setting Up Recoil

```tsx
// recoil/atoms/userAtom.ts
import { atom } from 'recoil';
import { User } from '../../shared/types';

export const userState = atom<User | null>({
  key: 'userState',
  default: null,
});

// recoil/selectors/userSelectors.ts
import { selector } from 'recoil';
import { userState } from '../atoms/userAtom';

export const isAuthenticatedSelector = selector({
  key: 'isAuthenticated',
  get: ({ get }) => {
    const user = get(userState);
    return user !== null;
  },
});

// App.tsx
import { RecoilRoot } from 'recoil';

function App() {
  return (
    <RecoilRoot>
      {/* Your app components */}
    </RecoilRoot>
  );
}

// Using Recoil in components
import { useRecoilState, useRecoilValue } from 'recoil';
import { userState } from '../recoil/atoms/userAtom';
import { isAuthenticatedSelector } from '../recoil/selectors/userSelectors';

function Profile() {
  const [user, setUser] = useRecoilState(userState);
  const isAuthenticated = useRecoilValue(isAuthenticatedSelector);
  
  // Component logic
}
```

### React's Built-in State Management

1. **useState**: Local component state
2. **useReducer**: More complex local state with actions
3. **useContext**: Sharing state without prop drilling

#### When to Use Each

```tsx
// Local component state with useState
function Counter() {
  const [count, setCount] = useState(0);
  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}

// More complex local state with useReducer
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

function ComplexCounter() {
  const [state, dispatch] = useReducer(reducer, { count: 0 });
  return (
    <div>
      <p>Count: {state.count}</p>
      <button onClick={() => dispatch({ type: 'increment' })}>+</button>
      <button onClick={() => dispatch({ type: 'decrement' })}>-</button>
    </div>
  );
}

// Sharing state with useContext
const ThemeContext = createContext('light');

function ThemedButton() {
  const theme = useContext(ThemeContext);
  return <button className={theme}>Themed Button</button>;
}

function App() {
  return (
    <ThemeContext.Provider value="dark">
      <ThemedButton />
    </ThemeContext.Provider>
  );
}
```

### When to Use What

| State Management | When to Use |
|------------------|-------------|
| **Recoil Atoms** | Global state shared across the entire app or multiple features |
| **Recoil Selectors** | Derived state that depends on atoms or other selectors |
| **React Context** | Theme, locale, or other app-wide settings without complex transformations |
| **useState** | Local component state that doesn't need to be shared |
| **useReducer** | Complex local state with multiple sub-values or actions |

#### Best Practices

1. **Minimize Global State**: Not everything needs to be in Recoil
2. **Isolate Feature State**: Create feature-specific atoms
3. **Derive Don't Duplicate**: Use selectors for derived state
4. **Colocation**: Keep state close to where it's used
5. **Mixed Approach**: It's OK to use Recoil for global state and useState for local state

## HTTP Services with Axios

### Setting Up Axios

```tsx
// core/http/axios-instance.ts
import axios from 'axios';
import { getAuthToken } from '../auth/auth-service';

const axiosInstance = axios.create({
  baseURL: process.env.REACT_APP_API_URL,
  timeout: 10000,
  headers: {
    'Content-Type': 'application/json',
  },
});

export default axiosInstance;
```

### Interceptors

Interceptors are a powerful feature in Axios that allow you to intercept requests or responses before they are handled.

```tsx
// core/http/interceptors.ts
import axiosInstance from './axios-instance';
import { getAuthToken } from '../auth/auth-service';
import { handleApiError } from '../error-handling/error-handler';

// Request interceptor - runs before request is sent
axiosInstance.interceptors.request.use(
  (config) => {
    // Add authorization token
    const token = getAuthToken();
    if (token) {
      config.headers.Authorization = `Bearer ${token}`;
    }
    
    // Add timestamp for cache busting if needed
    if (config.method === 'get') {
      config.params = {
        ...config.params,
        _t: Date.now(),
      };
    }
    
    return config;
  },
  (error) => {
    // Handle request errors
    console.error('Request error:', error);
    return Promise.reject(error);
  }
);

// Response interceptor - runs after response is received
axiosInstance.interceptors.response.use(
  (response) => {
    // Transform response data if needed
    return response;
  },
  (error) => {
    // Handle response errors
    return handleApiError(error);
  }
);

export default axiosInstance;
```

### Error Handling

```tsx
// core/error-handling/error-handler.ts
import { AxiosError } from 'axios';
import { logout } from '../auth/auth-service';

export interface ApiError {
  status: number;
  message: string;
  code?: string;
  fieldErrors?: Record<string, string[]>;
}

export function handleApiError(error: AxiosError): Promise<never> {
  if (!error.response) {
    // Network error
    return Promise.reject({
      status: 0,
      message: 'Network error. Please check your connection.',
    });
  }

  const { status, data } = error.response;
  
  // Handle authentication errors
  if (status === 401) {
    logout();
    return Promise.reject({
      status: 401,
      message: 'Your session has expired. Please log in again.',
    });
  }
  
  // Handle server errors
  if (status >= 500) {
    return Promise.reject({
      status,
      message: 'Server error. Please try again later.',
    });
  }
  
  // Return standardized error object
  return Promise.reject({
    status,
    message: data.message || 'An unexpected error occurred',
    code: data.code,
    fieldErrors: data.fieldErrors,
  });
}
```

### API Service Pattern

```tsx
// features/users/api/user-service.ts
import axiosInstance from '../../../core/http/axios-instance';
import { User, CreateUserRequest } from '../types';

const API_PATH = '/users';

export const userService = {
  getAll: async (): Promise<User[]> => {
    const response = await axiosInstance.get(API_PATH);
    return response.data;
  },
  
  getById: async (id: string): Promise<User> => {
    const response = await axiosInstance.get(`${API_PATH}/${id}`);
    return response.data;
  },
  
  create: async (user: CreateUserRequest): Promise<User> => {
    const response = await axiosInstance.post(API_PATH, user);
    return response.data;
  },
  
  update: async (id: string, user: Partial<User>): Promise<User> => {
    const response = await axiosInstance.put(`${API_PATH}/${id}`, user);
    return response.data;
  },
  
  delete: async (id: string): Promise<void> => {
    await axiosInstance.delete(`${API_PATH}/${id}`);
  },
};
```

## Component Design Best Practices

1. **Function Components with Hooks**: Prefer function components with hooks over class components
2. **Single Responsibility**: Each component should do one thing well
3. **Container/Presentational Pattern**: Separate data fetching from rendering
4. **Composition over Inheritance**: Use component composition to reuse code
5. **Custom Hooks**: Extract reusable logic into custom hooks
6. **Memoization**: Use React.memo, useMemo and useCallback to optimize performance

```tsx
// Bad: Mixing concerns
function UserList() {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);
  
  useEffect(() => {
    async function fetchUsers() {
      try {
        setLoading(true);
        const response = await userService.getAll();
        setUsers(response);
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    }
    fetchUsers();
  }, []);
  
  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;
  
  return (
    <div>
      <h2>Users</h2>
      <ul>
        {users.map(user => (
          <li key={user.id}>{user.name}</li>
        ))}
      </ul>
    </div>
  );
}

// Better: Separating concerns with custom hooks and presentational components
function useUsers() {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);
  
  useEffect(() => {
    async function fetchUsers() {
      try {
        setLoading(true);
        const response = await userService.getAll();
        setUsers(response);
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    }
    fetchUsers();
  }, []);
  
  return { users, loading, error };
}

function UserListItem({ user }) {
  return <li>{user.name}</li>;
}

function UserList() {
  const { users, loading, error } = useUsers();
  
  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;
  
  return (
    <div>
      <h2>Users</h2>
      <ul>
        {users.map(user => (
          <UserListItem key={user.id} user={user} />
        ))}
      </ul>
    </div>
  );
}
```

## Naming Conventions

1. **Files**:
   - Components: PascalCase (UserProfile.tsx)
   - Hooks: camelCase with "use" prefix (useAuth.ts)
   - Utilities: camelCase (formatDate.ts)
   - Constants: UPPER_SNAKE_CASE (API_ENDPOINTS.ts)
   - Contexts: PascalCase with "Context" suffix (AuthContext.tsx)

2. **Variables and Functions**:
   - Variables: camelCase (userProfile)
   - Functions: camelCase (fetchUserData)
   - React Components: PascalCase (UserProfile)
   - Interfaces/Types: PascalCase, often with "I" prefix (IUserProfile or UserProfile)

3. **CSS**:
   - CSS Modules: camelCase (styles.module.css)
   - CSS Classes: kebab-case (user-profile)
   - CSS-in-JS: camelCase

## Sharing Components

Components can be shared at different levels:

1. **Application-wide** - Place in `/shared/components`
2. **Feature-specific** - Place in `/features/[feature]/components`
3. **Page-specific** - Place alongside the page component

### Example Directory Structure for Sharing Components

```
src/
├── shared/                 # Application-wide shared components
│   └── components/
│       ├── Button/
│       ├── Card/
│       ├── Modal/
│       └── Form/
├── features/
│   ├── users/             # User management feature
│   │   ├── components/    # Components shared within the user feature
│   │   │   ├── UserCard/
│   │   │   └── UserFilter/
│   │   └── pages/
│   │       ├── UserList/
│   │       │   ├── components/  # Components specific to UserList page
│   │       │   │   └── UserTable/
│   │       │   └── UserList.tsx
│   │       └── UserDetail/
│   └── products/
└── ...
```

## Conclusion

Transitioning from Angular to React involves a mental shift in how you structure applications and manage state. By following the patterns in this guide, you'll be able to build a well-structured, maintainable React application using TypeScript and Recoil.

Remember these key takeaways:

1. Use Recoil for global state, useState for local state
2. Structure your application by features
3. Create clean HTTP services with proper error handling
4. Design components with single responsibility in mind
5. Follow consistent naming conventions
6. Share components at the appropriate level

Happy coding!
