# React SPA Development Guide

## Table of Contents
- [Introduction](#introduction)
- [React vs Angular: Key Differences](#react-vs-angular-key-differences)
- [Application Architecture](#application-architecture)
- [Folder Structure](#folder-structure)
- [State Management](#state-management)
- [HTTP Services and Error Handling](#http-services-and-error-handling)
- [Component Design Best Practices](#component-design-best-practices)
- [Micro-Frontend Architecture](#micro-frontend-architecture)
- [Example Application Walkthrough](#example-application-walkthrough)

## Introduction

This guide provides detailed instructions for building a Single Page Application (SPA) using React with TypeScript and Recoil for state management. It targets developers with Angular background transitioning to React.

The example application we'll consider is a financial portfolio management system with the following features:
- Portfolio view page with AG Grid displaying stocks/derivatives
- Stock market page showing available stocks to purchase
- Modal-based interactions for buy/sell operations
- Account management functionality

## React vs Angular: Key Differences

### Architecture Philosophy

```
+----------------+        +----------------+
|    Angular     |        |     React      |
+----------------+        +----------------+
| Full Framework |        |    Library     |
| Opinionated    |        |  Unopinionated |
| NgModules      |        | Component-only |
| Two-way binding|        | One-way flow   |
+----------------+        +----------------+
```

#### Angular
- Complete framework with built-in solutions for:
  - HTTP client
  - Form validation
  - Routing
  - State management (Services)
  - Dependency injection
- Uses TypeScript by default
- More opinionated about application structure
- Steeper learning curve but fewer decisions required

#### React
- Library focused on UI components
- Requires additional libraries for:
  - State management (Recoil, Redux, MobX, etc.)
  - Routing (React Router)
  - Form handling (React Hook Form, Formik)
- Community-driven ecosystem with multiple solutions for each concern
- More flexibility but requires more architectural decisions

### Component Model

#### Angular
- Class-based components with decorators
- Templates in separate HTML files (typically)
- Two-way binding with `[(ngModel)]`
- Uses services and dependency injection for sharing functionality
- Change detection based on zone.js

#### React
- Function components with hooks (modern approach)
- JSX/TSX combines markup with JavaScript/TypeScript
- Unidirectional data flow (props down, events up)
- Uses composition over inheritance
- Virtual DOM for efficient rendering

### State Management

```
Angular State Flow:
+-------------+     +--------------+     +--------------+
| Component A | --> | Service      | <-- | Component B  |
+-------------+     | (StateStore) |     +--------------+
                    +--------------+

React/Recoil State Flow:
+-------------+     +--------------+     +--------------+
| Component A | --> | Recoil Atoms | <-- | Component B  |
+-------------+     | & Selectors  |     +--------------+
                    +--------------+
```

#### Angular
- Services with RxJS observables
- NgRx (Redux-like) for complex applications
- Component-level state with properties

#### React
- Component state with useState hook
- Context API for shared state
- External libraries like Recoil or Redux for complex state

## Application Architecture

### High-Level Architecture

```
+----------------------------------------------------------+
|                       React SPA                          |
+----------------------------------------------------------+
|                                                          |
|  +----------------+  +----------------+  +-----------+   |
|  |   Components   |  |    Routing     |  |   State   |   |
|  +----------------+  +----------------+  +-----------+   |
|                                                          |
|  +----------------+  +----------------+  +-----------+   |
|  | HTTP Services  |  | Error Handling |  |  Shared   |   |
|  +----------------+  +----------------+  +-----------+   |
|                                                          |
+----------------------------------------------------------+
```

### Data Flow Architecture

```
+-------------+    +--------------+    +-------------+
|  UI Events  | -> | State Update | -> | Re-rendering|
+-------------+    +--------------+    +-------------+
      ^                                      |
      |                                      v
+-------------+    +--------------+    +-------------+
| API Response| <- | HTTP Service | <- |  UI State   |
+-------------+    +--------------+    +-------------+
```

## Folder Structure

```
src/
├── assets/               # Static files like images, fonts
├── components/           # Shared/reusable components
│   ├── common/           # Very generic components (buttons, inputs)
│   ├── layout/           # Layout components (header, footer, sidebar)
│   └── domain/           # Domain-specific shared components
├── features/             # Feature-specific modules
│   ├── portfolio/        # Portfolio feature
│   │   ├── components/   # Components specific to this feature
│   │   ├── hooks/        # Custom hooks for this feature
│   │   ├── services/     # Feature-specific services
│   │   ├── state/        # Feature-specific state (Recoil atoms/selectors)
│   │   ├── types/        # TypeScript types for this feature
│   │   └── index.ts      # Public API for the feature
│   ├── market/           # Market feature with similar structure
│   └── account/          # Account feature with similar structure
├── hooks/                # Global custom hooks
├── services/             # Global services (API, auth, etc.)
├── state/                # Global state management
│   ├── atoms/            # Recoil atoms
│   ├── selectors/        # Recoil selectors
│   └── index.ts          # State public API
├── types/                # Global TypeScript types
├── utils/                # Utility functions
├── App.tsx               # Root component
└── index.tsx             # Entry point
```

### Naming Conventions

- **Files**: Use kebab-case for files: `portfolio-grid.tsx`
- **Components**: Use PascalCase for component names: `PortfolioGrid`
- **Hooks**: Prefix with `use`: `usePortfolioData`
- **Context**: Suffix with `Context`: `PortfolioContext`
- **Atoms/Selectors**: Suffix with `Atom`/`Selector`: `portfolioDataAtom`
- **Types/Interfaces**: Prefix with `I` or `T`: `IPortfolioItem` or `TPortfolioItem`

## State Management

### When to Use Different State Management Approaches

```
Decision Flow:
+---------------------+
| Is state local and  |
| only used in one    | Yes
| component?          +-----> useState()
+---------------------+
          | No
          v
+---------------------+
| Is state shared     |
| between a few       | Yes
| related components? +-----> useContext()
+---------------------+
          | No
          v
+---------------------+
| Is state global     |
| or shared across    | Yes
| features?           +-----> Recoil
+---------------------+
```

### useState Hook
- **When to use**: For component-specific state that doesn't need to be shared
- **Advantages**: 
  - Simple to implement
  - Encapsulated within component
  - Doesn't add complexity to the application
- **Disadvantages**:
  - Limited to single component
  - Can lead to prop drilling when needed by children
  - Not suitable for complex state

### useContext Hook
- **When to use**: For state shared among a few related components
- **Advantages**:
  - Avoids prop drilling
  - Keeps related state together
  - Lighter than global state solutions
- **Disadvantages**:
  - Can cause unnecessary re-renders
  - Less structured than Recoil/Redux
  - Limited DevTools support

### Recoil
- **When to use**: For global state or complex state shared across features
- **Advantages**:
  - Atomic state model (fine-grained updates)
  - Selectors for derived state
  - Better performance than Context for complex state
  - Time-travel debugging
- **Disadvantages**:
  - Adds complexity
  - Requires learning a new library
  - Potential overuse for simple state needs

### Best Practices for State Management

1. **Start Simple**: Begin with useState, then migrate to more complex solutions as needed
2. **Co-locate State**: Keep state as close as possible to where it's used
3. **Avoid State Duplication**: Store canonical data in one place
4. **Categorize State**:
   - UI State: Modal open/closed, form input values
   - Server Cache: Data from API
   - Form State: Form values, validation, submission
   - URL State: Current route, query parameters

### Example Recoil State Structure for Portfolio Feature

```typescript
// atoms.ts
export const portfolioItemsAtom = atom<PortfolioItem[]>({
  key: 'portfolioItemsState',
  default: []
});

export const portfolioFilterAtom = atom<PortfolioFilter>({
  key: 'portfolioFilterState',
  default: { type: 'all' }
});

// selectors.ts
export const filteredPortfolioSelector = selector<PortfolioItem[]>({
  key: 'filteredPortfolioState',
  get: ({ get }) => {
    const items = get(portfolioItemsAtom);
    const filter = get(portfolioFilterAtom);
    
    if (filter.type === 'all') return items;
    return items.filter(item => item.type === filter.type);
  }
});
```

## HTTP Services and Error Handling

### API Service Structure

```
services/
├── api/
│   ├── client.ts             # Base axios instance
│   ├── interceptors.ts       # Request/response interceptors
│   └── endpoints/
│       ├── portfolio.ts      # Portfolio API endpoints
│       ├── market.ts         # Market API endpoints
│       └── account.ts        # Account API endpoints
└── error/
    ├── error-handler.ts      # Global error handling
    └── error-types.ts        # Error type definitions
```

### Implementing an API Client

```typescript
// client.ts
import axios, { AxiosInstance, AxiosRequestConfig } from 'axios';
import { setupInterceptors } from './interceptors';

export const createApiClient = (baseConfig?: AxiosRequestConfig): AxiosInstance => {
  const client = axios.create({
    baseURL: process.env.REACT_APP_API_BASE_URL,
    timeout: 10000,
    headers: {
      'Content-Type': 'application/json',
    },
    ...baseConfig,
  });
  
  setupInterceptors(client);
  
  return client;
};

export const apiClient = createApiClient();
```

### Interceptors in Detail

Interceptors are middleware functions that can modify requests before they're sent and responses before they're received by your application.

```typescript
// interceptors.ts
import { AxiosInstance, AxiosError, AxiosResponse } from 'axios';
import { refreshAuthToken } from '../auth/auth-service';
import { ErrorHandler } from '../error/error-handler';

export const setupInterceptors = (client: AxiosInstance): void => {
  // Request interceptors
  client.interceptors.request.use(
    (config) => {
      // Get token from storage
      const token = localStorage.getItem('auth_token');
      
      // Add auth header if token exists
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
      // Handle request error
      console.error('Request error:', error);
      return Promise.reject(error);
    }
  );
  
  // Response interceptors
  client.interceptors.response.use(
    (response: AxiosResponse) => {
      // You can transform response data here if needed
      return response;
    },
    async (error: AxiosError) => {
      const originalRequest = error.config;
      
      // Handle 401 Unauthorized - Token expired
      if (error.response?.status === 401 && !originalRequest._retry) {
        originalRequest._retry = true;
        
        try {
          // Attempt to refresh token
          const newToken = await refreshAuthToken();
          
          // Update auth header with new token
          client.defaults.headers.common['Authorization'] = `Bearer ${newToken}`;
          originalRequest.headers['Authorization'] = `Bearer ${newToken}`;
          
          // Retry the original request
          return client(originalRequest);
        } catch (refreshError) {
          // Token refresh failed, redirect to login
          window.location.href = '/login';
          return Promise.reject(refreshError);
        }
      }
      
      // Handle other errors
      ErrorHandler.handleApiError(error);
      
      return Promise.reject(error);
    }
  );
};
```

### Error Handling

```typescript
// error-handler.ts
import { AxiosError } from 'axios';
import { toast } from 'react-toastify';
import { ApiError, NetworkError, ValidationError } from './error-types';

export class ErrorHandler {
  static handleApiError(error: AxiosError): void {
    // Log the error
    console.error('API Error:', error);
    
    if (!error.response) {
      // Network error (no response from server)
      const networkError = new NetworkError('Network error occurred. Please check your connection.');
      toast.error(networkError.message);
      return;
    }
    
    const { status, data } = error.response;
    
    switch (status) {
      case 400:
        // Bad request - typically validation errors
        const validationError = new ValidationError(
          'Please check your inputs', 
          data.errors || []
        );
        toast.error(validationError.message);
        break;
        
      case 401:
        // Unauthorized - handled by interceptor
        break;
        
      case 403:
        // Forbidden
        toast.error('You do not have permission to perform this action');
        break;
        
      case 404:
        // Not found
        toast.error('The requested resource was not found');
        break;
        
      case 500:
      case 502:
      case 503:
      case 504:
        // Server errors
        toast.error('A server error occurred. Please try again later.');
        break;
        
      default:
        // Other errors
        const apiError = new ApiError('An unexpected error occurred', status);
        toast.error(apiError.message);
    }
  }
}
```

### Custom Hook for API Calls

```typescript
// use-api.ts
import { useState, useEffect } from 'react';
import { AxiosRequestConfig, AxiosResponse, AxiosError } from 'axios';
import { apiClient } from '../services/api/client';

interface ApiState<T> {
  data: T | null;
  loading: boolean;
  error: Error | null;
}

export function useApi<T>(
  url: string, 
  config: AxiosRequestConfig = {}
): [ApiState<T>, () => Promise<void>] {
  const [state, setState] = useState<ApiState<T>>({
    data: null,
    loading: false,
    error: null,
  });
  
  const fetchData = async (): Promise<void> => {
    setState({ data: null, loading: true, error: null });
    
    try {
      const response: AxiosResponse<T> = await apiClient(url, config);
      setState({ data: response.data, loading: false, error: null });
    } catch (error) {
      setState({ 
        data: null, 
        loading: false, 
        error: error as AxiosError 
      });
    }
  };
  
  useEffect(() => {
    if (config.method === 'get' || !config.method) {
      fetchData();
    }
  }, [url]);
  
  return [state, fetchData];
}
```

## Component Design Best Practices

### Component Types

1. **Presentational Components**
   - Focus on UI and styling
   - Receive data via props
   - Don't connect to state management
   - Highly reusable

2. **Container Components**
   - Connect to state management
   - Pass data to presentational components
   - Handle business logic
   - Less reusable

3. **Layout Components**
   - Define page structure
   - Handle responsive behavior
   - Don't contain business logic

4. **Page Components**
   - Map to routes
   - Compose other components
   - May connect to global state

### Component Composition

```jsx
// Poor composition - complex component
function PortfolioItem({ item, onDelete, onEdit, onBuy, onSell }) {
  // Complex component with too many responsibilities
  return (
    <div className="portfolio-item">
      <h3>{item.name}</h3>
      <div className="details">
        <span>Price: ${item.price}</span>
        <span>Quantity: {item.quantity}</span>
        <span>Value: ${item.value}</span>
      </div>
      <div className="actions">
        <button onClick={() => onBuy(item)}>Buy</button>
        <button onClick={() => onSell(item)}>Sell</button>
        <button onClick={() => onEdit(item)}>Edit</button>
        <button onClick={() => onDelete(item)}>Delete</button>
      </div>
    </div>
  );
}

// Better composition - smaller, focused components
function PortfolioItemDetails({ item }) {
  return (
    <div className="details">
      <span>Price: ${item.price}</span>
      <span>Quantity: {item.quantity}</span>
      <span>Value: ${item.value}</span>
    </div>
  );
}

function PortfolioItemActions({ item, onBuy, onSell, onEdit, onDelete }) {
  return (
    <div className="actions">
      <button onClick={() => onBuy(item)}>Buy</button>
      <button onClick={() => onSell(item)}>Sell</button>
      <button onClick={() => onEdit(item)}>Edit</button>
      <button onClick={() => onDelete(item)}>Delete</button>
    </div>
  );
}

function PortfolioItem({ item, ...actionProps }) {
  return (
    <div className="portfolio-item">
      <h3>{item.name}</h3>
      <PortfolioItemDetails item={item} />
      <PortfolioItemActions item={item} {...actionProps} />
    </div>
  );
}
```

### Best Practices

1. **Follow Single Responsibility Principle**
   - Each component should do one thing well
   - Split large components into smaller ones

2. **Use TypeScript for Props and State**
   - Define interfaces for component props
   - Use generics for reusable components

3. **Memoize Components When Needed**
   - Use React.memo for pure components
   - Use useMemo and useCallback for expensive calculations and event handlers

4. **Keep Components Pure When Possible**
   - Avoid side effects in render
   - Move side effects to useEffect

5. **Custom Hooks for Reusable Logic**
   - Extract complex logic into custom hooks
   - Separate business logic from UI

## Micro-Frontend Architecture

```
+--------------------------------------------------------------+
|                       Container App                          |
+--------------------------------------------------------------+
|                                                              |
| +------------------+  +------------------+  +---------------+|
| |   Portfolio MFE  |  |   Market MFE     |  |  Account MFE  ||
| +------------------+  +------------------+  +---------------+|
|                                                              |
| +----------------------------------------------------------+ |
| |                   Shared Components                       | |
| +----------------------------------------------------------+ |
|                                                              |
| +----------------------------------------------------------+ |
| |                   Shared State                            | |
| +----------------------------------------------------------+ |
|                                                              |
+--------------------------------------------------------------+
```

### When to Use Micro-Frontends

- Large applications with distinct functional areas
- Teams working independently
- Different release cycles for application sections
- Scalability concerns

### Implementation Approaches

1. **Module Federation (Webpack 5)**
   - Load remote modules at runtime
   - Share dependencies
   - Enable true independent deployment

2. **Web Components**
   - Use custom elements for encapsulation
   - Framework-agnostic approach
   - Less coupling between micro-frontends

3. **iFrame-based**
   - Complete isolation
   - Simplest to implement
   - Limited interaction between micro-frontends

### Example with Module Federation

```typescript
// webpack.config.js for container app
const { ModuleFederationPlugin } = require('webpack').container;

module.exports = {
  // ...webpack config
  plugins: [
    new ModuleFederationPlugin({
      name: 'container',
      remotes: {
        portfolio: 'portfolio@http://localhost:3001/remoteEntry.js',
        market: 'market@http://localhost:3002/remoteEntry.js',
        account: 'account@http://localhost:3003/remoteEntry.js',
      },
      shared: {
        react: { singleton: true },
        'react-dom': { singleton: true },
        recoil: { singleton: true },
      },
    }),
  ],
};
```

### Sharing State Between Micro-Frontends

```typescript
// shared-state.ts in container app
import { atom } from 'recoil';

export const userProfileAtom = atom({
  key: 'userProfileState',
  default: null,
});

export const themeAtom = atom({
  key: 'themeState',
  default: 'light',
});
```

## Example Application Walkthrough

```
Portfolio Page Flow:
+----------------+     +----------------+     +----------------+
| Portfolio Grid | --> | Context Menu   | --> | Trade Modal    |
+----------------+     +----------------+     +----------------+
        |                                           |
        v                                           v
+----------------+     +----------------+     +----------------+
| Filter Controls| <-- | Portfolio Data | <-- | API Service    |
+----------------+     +----------------+     +----------------+
```

### Portfolio Feature Implementation

```typescript
// portfolio/components/portfolio-grid.tsx
import { useMemo } from 'react';
import { AgGridReact } from 'ag-grid-react';
import { useRecoilValue } from 'recoil';
import { filteredPortfolioSelector } from '../state/selectors';
import { usePortfolioActions } from '../hooks/use-portfolio-actions';

export const PortfolioGrid: React.FC = () => {
  const portfolioItems = useRecoilValue(filteredPortfolioSelector);
  const { onBuy, onSell, onShowDetails } = usePortfolioActions();
  
  const columnDefs = useMemo(() => [
    { field: 'symbol', headerName: 'Symbol' },
    { field: 'name', headerName: 'Name' },
    { field: 'quantity', headerName: 'Quantity' },
    { field: 'price', headerName: 'Current Price', valueFormatter: params => `$${params.value}` },
    { field: 'value', headerName: 'Total Value', valueFormatter: params => `$${params.value}` },
    {
      headerName: 'Actions',
      cellRenderer: params => (
        <div className="action-buttons">
          <button onClick={() => onBuy(params.data)}>Buy</button>
          <button onClick={() => onSell(params.data)}>Sell</button>
          <button onClick={() => onShowDetails(params.data)}>Details</button>
        </div>
      )
    }
  ], [onBuy, onSell, onShowDetails]);
  
  return (
    <div className="portfolio-grid">
      <AgGridReact
        rowData={portfolioItems}
        columnDefs={columnDefs}
        pagination={true}
        paginationPageSize={10}
        domLayout="autoHeight"
      />
    </div>
  );
};
```

### Market Feature Implementation

```typescript
// market/state/atoms.ts
import { atom } from 'recoil';
import { MarketStock } from '../types';

export const marketStocksAtom = atom<MarketStock[]>({
  key: 'marketStocksState',
  default: [],
});

export const marketFilterAtom = atom<string>({
  key: 'marketFilterState',
  default: '',
});

// market/state/selectors.ts
import { selector } from 'recoil';
import { marketStocksAtom, marketFilterAtom } from './atoms';

export const filteredMarketStocksSelector = selector({
  key: 'filteredMarketStocksState',
  get: ({ get }) => {
    const stocks = get(marketStocksAtom);
    const filter = get(marketFilterAtom).toLowerCase();
    
    if (!filter) return stocks;
    
    return stocks.filter(stock => 
      stock.symbol.toLowerCase().includes(filter) || 
      stock.name.toLowerCase().includes(filter)
    );
  }
});
```

### Modal Component Implementation

```typescript
// components/common/modal.tsx
import React from 'react';
import { createPortal } from 'react-dom';

interface ModalProps {
  isOpen: boolean;
  onClose: () => void;
  title: string;
  children: React.ReactNode;
}

export const Modal: React.FC<ModalProps> = ({ isOpen, onClose, title, children }) => {
  if (!isOpen) return null;
  
  return createPortal(
    <div className="modal-overlay" onClick={onClose}>
      <div className="modal-content" onClick={e => e.stopPropagation()}>
        <div className="modal-header">
          <h2>{title}</h2>
          <button className="close-button" onClick={onClose}>&times;</button>
        </div>
        <div className="modal-body">
          {children}
        </div>
      </div>
    </div>,
    document.body
  );
};
```

## Conclusion

This guide covered building a React SPA with TypeScript and Recoil, covering key differences from Angular, state management approaches, component design, and micro-frontend architecture. By following these best practices, you can create maintainable, scalable React applications.

For further learning:
- Official React documentation: https://reactjs.org/docs/getting-started.html
- Recoil documentation: https://recoiljs.org/docs/introduction/installation
- TypeScript documentation: https://www.typescriptlang.org/docs/
