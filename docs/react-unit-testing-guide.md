# Comprehensive Guide to Unit Testing in React Applications

## Table of Contents
- [Introduction to React Unit Testing](#introduction-to-react-unit-testing)
- [Testing Tools and Libraries](#testing-tools-and-libraries)
- [Writing Effective Unit Tests](#writing-effective-unit-tests)
- [Testing React Components](#testing-react-components)
- [Mocking Dependencies](#mocking-dependencies)
- [Testing Hooks](#testing-hooks)
- [Testing Context and Redux](#testing-context-and-redux)
- [Example: Stock Portfolio Application](#example-stock-portfolio-application)
- [Advanced Testing Techniques](#advanced-testing-techniques)
- [Testing Best Practices](#testing-best-practices)
- [Conclusion](#conclusion)

## Introduction to React Unit Testing

Unit testing is a crucial aspect of developing reliable and maintainable React applications. It involves testing individual components or functions in isolation to ensure they work as expected. By implementing a comprehensive test suite, you can:

- Identify bugs early in the development cycle
- Ensure code changes don't break existing functionality
- Document expected component behavior
- Provide confidence when refactoring code
- Improve overall code quality

For React applications, unit testing typically focuses on testing:
- Component rendering
- Component state changes
- Event handling
- Custom hooks
- Utility functions
- API interactions

## Testing Tools and Libraries

### Core Testing Tools

1. **Jest**:
   - A JavaScript testing framework maintained by Facebook
   - Provides test runner, assertion library, and mocking utilities
   - Built-in code coverage reporting
   - Snapshot testing capabilities

2. **React Testing Library**:
   - Encourages testing components as users would interact with them
   - Focuses on DOM nodes rather than component instances
   - Promotes accessibility and best practices
   - Provides utilities for querying elements, firing events, and more

3. **Testing Library/User-Event**:
   - Simulates real user interactions more accurately than fireEvent
   - Provides high-level APIs for common user actions like clicking, typing, etc.

### Additional Testing Tools

- **Mock Service Worker (MSW)**: Intercepts network requests for API testing without having to mock fetch/axios directly. It works at the network level, providing a more realistic testing environment.
- **Jest Axe**: Tests for accessibility issues in your components, helping ensure your application is usable by people with disabilities.
- **Testing Library/Jest-DOM**: Extends Jest with custom DOM element matchers like `toBeInTheDocument()`, `toHaveClass()`, making assertions more readable and expressive.
- **Cypress**: For end-to-end testing that complements unit tests by testing complete user flows in a real browser environment.

## Writing Effective Unit Tests

### Anatomy of a Unit Test

A well-structured unit test typically follows the "Arrange-Act-Assert" pattern:

1. **Arrange**: Set up the test environment and component props
2. **Act**: Perform the action to be tested (render, click, etc.)
3. **Assert**: Verify the expected outcome

Example:

```javascript
test('should display username when logged in', () => {
  // Arrange
  const username = 'JohnDoe';
  render(<UserProfile username={username} isLoggedIn={true} />);
  
  // Act
  // (In this case, rendering is the action)
  
  // Assert
  expect(screen.getByText(username)).toBeInTheDocument();
});
```

In this example:
- **Arrange**: We set up the test by defining the username and rendering the UserProfile component with props
- **Act**: The act of rendering the component with these props is our action
- **Assert**: We verify that the username appears in the document when isLoggedIn is true

### Test Structure

```javascript
// Import required libraries
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import Component from './Component';

// Optional setup/teardown logic
beforeEach(() => {
  // Setup code runs before each test
  // Useful for initializing shared variables or mocking services
  jest.clearAllMocks();
  localStorage.clear();
});

afterEach(() => {
  // Cleanup code runs after each test
  // Useful for cleaning up side effects
  document.body.innerHTML = '';
});

// Test suite
describe('Component', () => {
  // Individual test cases
  test('renders correctly', () => {
    // Test implementation
  });
  
  test('handles user interaction', async () => {
    // Test implementation
  });
});
```

This structure:
- Uses `beforeEach` to set up a clean state before each test runs
- Groups related tests with `describe` blocks to organize tests logically
- Uses clear, descriptive test names that explain what's being tested
- Separates test cases to focus on individual behaviors

## Testing React Components

### Basic Component Rendering

```javascript
test('renders the component with the correct text', () => {
  render(<Button text="Click me" />);
  expect(screen.getByRole('button')).toHaveTextContent('Click me');
});
```

Explanation:
- The `render` function mounts the component into a virtual DOM for testing
- `screen` is an object that provides access to the rendered component
- `getByRole('button')` finds a button element in the rendered output
- `toHaveTextContent('Click me')` checks if the button has the correct text

By using `getByRole`, we're also testing that the component has the correct semantic HTML, which is better for accessibility. This is preferable to using class selectors or test IDs when possible.

### Testing User Interactions

```javascript
test('calls onClick when button is clicked', async () => {
  // Create a mock function to track if it gets called
  const handleClick = jest.fn();
  
  // Render the component with the mock function as the onClick prop
  render(<Button onClick={handleClick} text="Click me" />);
  
  // Simulate a user clicking the button
  await userEvent.click(screen.getByRole('button'));
  
  // Verify the mock function was called exactly once
  expect(handleClick).toHaveBeenCalledTimes(1);
});
```

Explanation:
- `jest.fn()` creates a mock function that tracks its calls
- `userEvent.click()` is an async function that simulates a real user click more accurately than fireEvent
- It includes focus and blur events that would occur in real browser interactions
- We wait for the click operation to complete using `await`
- We verify the mock function was called exactly once with `toHaveBeenCalledTimes(1)`

The `userEvent` library is preferred over `fireEvent` because it more closely resembles how real users interact with your application. For example, when a user clicks a button, they first move their mouse over it, then press the mouse button down, and finally release it. `userEvent` simulates these sequences of events.

### Querying Elements

React Testing Library provides several query methods:

1. **getBy...**: Finds one element or throws an error
   - `getByText`, `getByRole`, `getByLabelText`, `getByTestId`, etc.

2. **queryBy...**: Returns null instead of throwing when no element is found
   - Useful for asserting that something is not present

3. **findBy...**: Returns a Promise which resolves when an element is found
   - Used with async operations

4. **getAllBy...**, **queryAllBy...**, **findAllBy...**: Return multiple elements

Examples of when to use each:

```javascript
// When you expect an element to be in the document:
test('displays the welcome message', () => {
  render(<Header username="John" />);
  
  // getByText throws if not found, making the test fail
  const welcomeMessage = screen.getByText(/welcome, john/i);
  expect(welcomeMessage).toBeInTheDocument();
});

// When you want to verify an element is NOT present:
test('does not display the admin panel for regular users', () => {
  render(<Dashboard userRole="user" />);
  
  // queryByText returns null if not found (doesn't throw)
  const adminPanel = screen.queryByText(/admin panel/i);
  expect(adminPanel).not.toBeInTheDocument();
});

// When working with async operations:
test('displays data after it loads', async () => {
  render(<DataFetcher />);
  
  // findByText returns a promise that resolves when the element appears
  // It will retry until the element is found or timeout is reached
  const loadedData = await screen.findByText(/data loaded/i);
  expect(loadedData).toBeInTheDocument();
});

// When working with multiple elements:
test('renders a list of items', () => {
  const items = ['Apple', 'Banana', 'Cherry'];
  render(<List items={items} />);
  
  // getAllByRole returns an array of elements
  const listItems = screen.getAllByRole('listitem');
  expect(listItems).toHaveLength(3);
  expect(listItems[0]).toHaveTextContent('Apple');
});
```

The hierarchy of preference for querying is:
1. Queries accessible to everyone (role, label text, placeholder)
2. Semantic queries (text content, form elements)
3. Test IDs (as a last resort)

This approach emphasizes testing from the user's perspective and encourages accessible component design.

### Testing Asynchronous Code

```javascript
test('loads and displays user data', async () => {
  // Mock API response
  jest.spyOn(api, 'fetchUserData').mockResolvedValue({
    name: 'John Doe',
    email: 'john@example.com'
  });
  
  render(<UserProfile userId="123" />);
  
  // Wait for data to be displayed - this is crucial for async operations
  expect(await screen.findByText('John Doe')).toBeInTheDocument();
  expect(screen.getByText('john@example.com')).toBeInTheDocument();
});
```

Detailed explanation:
- `jest.spyOn` creates a spy on the `fetchUserData` method of the `api` object
- `mockResolvedValue` replaces the implementation with a function that returns a resolved Promise with our test data
- `render(<UserProfile userId="123" />)` triggers the component to render, which would call our API in its useEffect hook
- `await screen.findByText('John Doe')` waits for the text to appear in the document, which happens after the API call resolves
- Once the first assertion passes, we can use `getByText` for subsequent checks because we know the data is already loaded

Additional approaches for testing async behavior:

```javascript
// Using waitFor when multiple elements need to be checked
test('displays all user information after loading', async () => {
  jest.spyOn(api, 'fetchUserData').mockResolvedValue({
    name: 'John Doe',
    email: 'john@example.com',
    role: 'Admin'
  });
  
  render(<UserProfile userId="123" />);
  
  // Wait for all elements to be present
  await waitFor(() => {
    expect(screen.getByText('John Doe')).toBeInTheDocument();
    expect(screen.getByText('john@example.com')).toBeInTheDocument();
    expect(screen.getByText('Admin')).toBeInTheDocument();
  });
});

// Testing loading states
test('shows loading indicator while fetching data', async () => {
  // Create a promise that won't resolve immediately
  let resolvePromise;
  const promise = new Promise(resolve => {
    resolvePromise = resolve;
  });
  
  jest.spyOn(api, 'fetchUserData').mockImplementation(() => promise);
  
  render(<UserProfile userId="123" />);
  
  // Verify loading state is shown
  expect(screen.getByText('Loading...')).toBeInTheDocument();
  
  // Resolve the promise to complete the API call
  resolvePromise({
    name: 'John Doe',
    email: 'john@example.com'
  });
  
  // Verify loading is replaced with data
  await waitFor(() => {
    expect(screen.queryByText('Loading...')).not.toBeInTheDocument();
    expect(screen.getByText('John Doe')).toBeInTheDocument();
  });
});
```

These techniques allow you to test various aspects of asynchronous behavior, including loading states, success states, and error states.

## Mocking Dependencies

### Mocking Functions

```javascript
test('calls API when form is submitted', async () => {
  // Create a mock function to track calls
  const mockSubmit = jest.fn();
  
  // Replace the real submitForm implementation with our mock
  jest.spyOn(api, 'submitForm').mockImplementation(mockSubmit);
  
  render(<ContactForm />);
  
  // Fill out the form
  await userEvent.type(screen.getByLabelText(/name/i), 'John Doe');
  await userEvent.type(screen.getByLabelText(/email/i), 'john@example.com');
  
  // Submit the form
  await userEvent.click(screen.getByRole('button', { name: /submit/i }));
  
  // Verify our mock function was called with the correct data
  expect(mockSubmit).toHaveBeenCalledWith({
    name: 'John Doe',
    email: 'john@example.com'
  });
});
```

Detailed explanation:
- `jest.fn()` creates a simple mock function that allows us to track calls to it
- `jest.spyOn(api, 'submitForm')` creates a spy on the submitForm method while keeping the original method
- `mockImplementation(mockSubmit)` replaces the original implementation with our mock function
- `userEvent.type()` simulates a user typing into form fields, which is more realistic than directly setting values
- After clicking the submit button, we verify that our mock function was called with the expected form data

This approach allows us to test that the form correctly collects user input and passes it to the API, without actually making API calls during tests.

### Mocking Modules

Create a `__mocks__` folder next to the module you want to mock or use Jest's `jest.mock()`:

```javascript
// Inline mocking
jest.mock('../api', () => ({
  fetchUserData: jest.fn(),
  submitForm: jest.fn(),
}));

// Or in a __mocks__ folder structure
// Then in your test:
import { fetchUserData } from '../api';
fetchUserData.mockResolvedValue({ name: 'John' });
```

When to use each approach:

1. **Inline mocking with `jest.mock()`**:
   - Good for simple, test-specific mocks
   - The mock is defined directly in your test file
   - You can customize the mock for each test
   
   ```javascript
   // Example of complex inline mocking
   jest.mock('../api', () => {
     // Create mock functions
     const fetchUserData = jest.fn();
     const submitForm = jest.fn();
     
     // Return mock object
     return {
       fetchUserData,
       submitForm,
       // Add default implementation for commonly used methods
       isAuthenticated: () => true,
     };
   });
   ```

2. **`__mocks__` folder**:
   - Better for complex mocks used across multiple test files
   - Create a file with the same name as the module in a `__mocks__` folder next to the real module
   - Jest will automatically use this mock when the module is imported
   
   ```javascript
   // __mocks__/api.js
   const fetchUserData = jest.fn();
   const submitForm = jest.fn();
   
   // Set default implementations
   fetchUserData.mockResolvedValue({ name: 'Default User' });
   submitForm.mockResolvedValue({ success: true });
   
   module.exports = {
     fetchUserData,
     submitForm,
     isAuthenticated: () => true
   };
   ```

3. **Mocking specific module implementations**:
   
   ```javascript
   // In your test file
   import { fetchUserData, submitForm } from '../api';
   
   beforeEach(() => {
     // Reset mocks before each test
     jest.clearAllMocks();
     
     // Set up test-specific implementations
     fetchUserData.mockResolvedValueOnce({ 
       name: 'John', 
       email: 'john@example.com' 
     });
   });
   
   test('displays user data', async () => {
     render(<UserProfile userId="123" />);
     await screen.findByText('John');
     expect(fetchUserData).toHaveBeenCalledWith('123');
   });
   ```

These approaches give you flexibility in how you mock dependencies, depending on the complexity and reusability needs of your tests.

### Mocking Context Providers

```javascript
const mockValue = {
  user: { id: '123', name: 'John' },
  logout: jest.fn()
};

test('displays user information from context', () => {
  render(
    <UserContext.Provider value={mockValue}>
      <UserProfile />
    </UserContext.Provider>
  );
  
  expect(screen.getByText('John')).toBeInTheDocument();
});
```

Detailed explanation:
- We create a mock context value with the data structure our component expects
- We wrap the component under test in the context provider with our mock value
- This allows us to test how the component uses the context data without needing the actual context implementation
- Including `logout: jest.fn()` lets us track if/when the logout function is called

For more complex scenarios, you might want to create a reusable wrapper:

```javascript
// Create a custom render function for components that use context
function renderWithUserContext(ui, contextValue = mockValue) {
  return render(
    <UserContext.Provider value={contextValue}>
      {ui}
    </UserContext.Provider>
  );
}

test('displays user name from context', () => {
  renderWithUserContext(<UserProfile />);
  expect(screen.getByText('John')).toBeInTheDocument();
});

test('handles logout when button is clicked', async () => {
  const customMockValue = {
    user: { id: '123', name: 'John' },
    logout: jest.fn()
  };
  
  renderWithUserContext(<UserProfile />, customMockValue);
  
  await userEvent.click(screen.getByRole('button', { name: /logout/i }));
  
  expect(customMockValue.logout).toHaveBeenCalledTimes(1);
});
```

This approach makes it easy to test components that consume context without needing to set up the real context providers or their dependencies.

## Testing Hooks

Use `@testing-library/react-hooks` for testing custom hooks:

```javascript
import { renderHook, act } from '@testing-library/react-hooks';
import useCounter from './useCounter';

test('increments counter', () => {
  // Render the hook in a test environment
  const { result } = renderHook(() => useCounter());
  
  // Initial state check
  expect(result.current.count).toBe(0);
  
  // Use act() for any action that changes state
  act(() => {
    result.current.increment();
  });
  
  // Verify the state was updated correctly
  expect(result.current.count).toBe(1);
});
```

Detailed explanation:
- `renderHook()` creates a test component that uses our hook and gives us access to the hook's return values
- `result.current` contains the current values returned by the hook
- `act()` ensures that all state updates and side effects are processed before continuing
- This is crucial because React batches state updates
- Without `act()`, you might make assertions before React has updated the state

Testing hooks with dependencies:

```javascript
test('fetches data on mount', async () => {
  // Mock API
  const mockData = { id: 1, name: 'Test Item' };
  jest.spyOn(api, 'fetchData').mockResolvedValue(mockData);
  
  // Render hook with initial arguments
  const { result, waitForNextUpdate } = renderHook(() => useDataFetcher('items'));
  
  // Initial state
  expect(result.current.data).toBe(null);
  expect(result.current.loading).toBe(true);
  
  // Wait for the async operation to complete
  await waitForNextUpdate();
  
  // Check the updated state
  expect(result.current.data).toEqual(mockData);
  expect(result.current.loading).toBe(false);
  expect(api.fetchData).toHaveBeenCalledWith('items');
});

test('handles error states', async () => {
  // Mock API failure
  jest.spyOn(api, 'fetchData').mockRejectedValue(new Error('Network error'));
  
  // Render hook
  const { result, waitForNextUpdate } = renderHook(() => useDataFetcher('items'));
  
  // Wait for the async operation to fail
  await waitForNextUpdate();
  
  // Check error handling
  expect(result.current.error).toBe('Network error');
  expect(result.current.loading).toBe(false);
});
```

Additional examples for testing hooks with props changes:

```javascript
test('updates when props change', async () => {
  // Start with initial props
  const { result, rerender, waitForNextUpdate } = renderHook(
    (props) => useDataFetcher(props.id), 
    { initialProps: { id: '1' } }
  );
  
  // Wait for the first fetch to complete
  await waitForNextUpdate();
  
  // Trigger a rerender with new props
  rerender({ id: '2' });
  
  // This should trigger another fetch
  expect(result.current.loading).toBe(true);
  
  // Wait for the second fetch
  await waitForNextUpdate();
  
  // Verify the API was called with the new ID
  expect(api.fetchData).toHaveBeenCalledWith('2');
});
```

The `renderHook` function is powerful for testing hooks in isolation, and `act` ensures that all updates are processed properly before assertions.

## Testing Context and Redux

### Testing Context

```javascript
import { render, screen } from '@testing-library/react';
import { ThemeContext, ThemeProvider } from './ThemeContext';
import ThemeDisplay from './ThemeDisplay';

test('displays current theme and toggles theme', async () => {
  // Render the component with the actual context provider
  render(
    <ThemeProvider>
      <ThemeDisplay />
    </ThemeProvider>
  );
  
  // Check initial theme (assuming it starts with light theme)
  expect(screen.getByText(/light theme/i)).toBeInTheDocument();
  
  // Find and click the toggle button
  await userEvent.click(screen.getByRole('button', { name: /toggle theme/i }));
  
  // Verify the theme changed
  expect(screen.getByText(/dark theme/i)).toBeInTheDocument();
});
```

Detailed explanation:
- Instead of mocking the context, we're using the real `ThemeProvider` from our application
- This tests both the context and the component that uses it
- This is closer to an integration test than a pure unit test
- It verifies that both the component correctly reads from context and that the context updates correctly

When to test with real vs. mocked context:
- Use real context providers when testing the provider functionality itself or for integration tests
- Use mocked context for pure unit tests of components that consume context

### Testing Redux Connected Components

```javascript
import { render, screen } from '@testing-library/react';
import { Provider } from 'react-redux';
import configureStore from 'redux-mock-store';
import UserProfile from './UserProfile';

// Create a mock store factory
const mockStore = configureStore([]);

test('displays user data from Redux store', () => {
  // Create a store with initial state
  const store = mockStore({
    user: {
      data: { name: 'John', email: 'john@example.com' },
      loading: false
    }
  });
  
  // Render with Redux Provider and our mock store
  render(
    <Provider store={store}>
      <UserProfile />
    </Provider>
  );
  
  // Verify component renders with data from store
  expect(screen.getByText('John')).toBeInTheDocument();
  expect(screen.getByText('john@example.com')).toBeInTheDocument();
});
```

Detailed explanation:
- `configureStore([])` creates a mock store factory (the empty array would normally contain middleware)
- `mockStore({ user: ... })` creates a store instance with our test data
- We wrap our component in the Redux `Provider` with our mock store
- This lets us test that the component correctly reads data from the Redux store

Testing Redux actions:

```javascript
test('dispatches action when button is clicked', async () => {
  // Create store and track dispatched actions
  const store = mockStore({
    user: {
      data: { name: 'John', email: 'john@example.com' },
      loading: false
    }
  });
  
  render(
    <Provider store={store}>
      <UserProfile />
    </Provider>
  );
  
  // Click the logout button
  await userEvent.click(screen.getByRole('button', { name: /logout/i }));
  
  // Check that the correct action was dispatched
  const actions = store.getActions();
  expect(actions).toEqual([
    { type: 'user/logout' }
  ]);
});
```

This tests that the component dispatches the correct actions when interacted with, without needing to test the reducer logic (which should be tested separately).

## Example: Stock Portfolio Application

Let's build a comprehensive example of testing a Stock Portfolio application with the following features:
- Display user's stock portfolio
- Right-click functionality to sell stocks
- Modal popup for adding more stocks

### Application Structure

```
/src
  /components
    /StockPortfolio
      StockPortfolio.jsx
      StockPortfolio.test.jsx
    /StockItem
      StockItem.jsx
      StockItem.test.jsx
    /AddStockModal
      AddStockModal.jsx
      AddStockModal.test.jsx
  /hooks
    useStockPortfolio.js
    useStockPortfolio.test.js
  /context
    StockContext.js
    StockContext.test.js
  /utils
    stockCalculations.js
    stockCalculations.test.js
  /api
    stockApi.js
    stockApi.test.js
```

This structure follows a modular approach where:
- Each component has its own test file directly alongside it
- Shared logic is extracted into hooks, context, and utility functions
- Each piece has dedicated tests for its specific functionality

### Component Implementation

```jsx
// StockPortfolio.jsx
import React, { useContext, useState } from 'react';
import { StockContext } from '../../context/StockContext';
import StockItem from '../StockItem/StockItem';
import AddStockModal from '../AddStockModal/AddStockModal';

const StockPortfolio = () => {
  const { stocks } = useContext(StockContext);
  const [isModalOpen, setIsModalOpen] = useState(false);
  const [contextMenuPos, setContextMenuPos] = useState({ x: 0, y: 0 });
  const [selectedStock, setSelectedStock] = useState(null);
  const [showContextMenu, setShowContextMenu] = useState(false);

  const handleRightClick = (e, stock) => {
    e.preventDefault();
    setSelectedStock(stock);
    setContextMenuPos({ x: e.clientX, y: e.clientY });
    setShowContextMenu(true);
  };

  const handleSellStock = () => {
    if (selectedStock) {
      // Call to context method to sell stock
      const { sellStock } = useContext(StockContext);
      sellStock(selectedStock.id);
      setShowContextMenu(false);
    }
  };

  const closeContextMenu = () => {
    setShowContextMenu(false);
  };

  const openAddModal = () => {
    setIsModalOpen(true);
  };

  const closeAddModal = () => {
    setIsModalOpen(false);
  };

  // Close context menu when clicking elsewhere
  React.useEffect(() => {
    const handleClickOutside = () => setShowContextMenu(false);
    document.addEventListener('click', handleClickOutside);
    return () => document.removeEventListener('click', handleClickOutside);
  }, []);

  const totalValue = stocks.reduce((total, stock) => 
    total + (stock.quantity * stock.currentPrice), 0);

  return (
    <div className="stock-portfolio" data-testid="stock-portfolio">
      <h1>Your Stock Portfolio</h1>
      <p>Total Value: ${totalValue.toFixed(2)}</p>
      
      <button onClick={openAddModal}>Add New Stock</button>
      
      <div className="stock-list">
        {stocks.length === 0 ? (
          <p>No stocks in your portfolio yet.</p>
        ) : (
          stocks.map(stock => (
            <StockItem 
              key={stock.id}
              stock={stock}
              onRightClick={(e) => handleRightClick(e, stock)}
            />
          ))
        )}
      </div>
      
      {showContextMenu && (
        <div 
          className="context-menu"
          style={{ top: contextMenuPos.y, left: contextMenuPos.x }}
          data-testid="context-menu"
        >
          <button onClick={handleSellStock}>Sell Stock</button>
          <button onClick={closeContextMenu}>Cancel</button>
        </div>
      )}
      
      {isModalOpen && (
        <AddStockModal onClose={closeAddModal} />
      )}
    </div>
  );
};

export default StockPortfolio;
```

This main component:
- Uses context to access the stock data and actions
- Manages state for the modal and context menu
- Calculates the total portfolio value
- Handles right-click interactions
- Conditionally renders the list of stocks, context menu, and modal

### Unit Tests for the Stock Portfolio Component

```jsx
// StockPortfolio.test.jsx
import React from 'react';
import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import StockPortfolio from './StockPortfolio';
import { StockContext } from '../../context/StockContext';

// Mock the context
const mockSellStock = jest.fn();
const mockAddStock = jest.fn();

const mockStocks = [
  { id: '1', symbol: 'AAPL', name: 'Apple Inc.', quantity: 10, currentPrice: 150.25 },
  { id: '2', symbol: 'MSFT', name: 'Microsoft', quantity: 5, currentPrice: 290.75 }
];

// Custom render function to provide context
const customRender = (ui, { providerProps = {}, ...renderOptions } = {}) => {
  return render(
    <StockContext.Provider 
      value={{ 
        stocks: mockStocks, 
        sellStock: mockSellStock,
        addStock: mockAddStock,
        ...providerProps 
      }}
    >
      {ui}
    </StockContext.Provider>,
    renderOptions
  );
};

describe('StockPortfolio Component', () => {
  beforeEach(() => {
    jest.clearAllMocks();
  });

  test('renders the portfolio with correct total value', () => {
    customRender(<StockPortfolio />);
    
    // Total value = (10 * 150.25) + (5 * 290.75) = 1502.5 + 1453.75 = 2956.25
    expect(screen.getByText('Total Value: $2956.25')).toBeInTheDocument();
  });

  test('renders stock items for each stock', () => {
    customRender(<StockPortfolio />);
    
    expect(screen.getByText('Apple Inc.')).toBeInTheDocument();
    expect(screen.getByText('Microsoft')).toBeInTheDocument();
    expect(screen.getByText('AAPL - 10 shares')).toBeInTheDocument();
    expect(screen.getByText('MSFT - 5 shares')).toBeInTheDocument();
  });

  test('displays message when no stocks available', () => {
    customRender(<StockPortfolio />, { 
      providerProps: { stocks: [] } 
    });
    
    expect(screen.getByText('No stocks in your portfolio yet.')).toBeInTheDocument();
  });

  test('shows context menu on right click of a stock item', async () => {
    const user = userEvent.setup();
    customRender(<StockPortfolio />);
    
    const stockItem = screen.getByText('Apple Inc.').closest('[data-testid="stock-item"]');
    
    // Simulate right click
    await user.pointer({ 
      keys: '[MouseRight]', 
      target: stockItem 
    });
    
    expect(screen.getByTestId('context-menu')).toBeInTheDocument();
    expect(screen.getByRole('button', { name: /sell stock/i })).toBeInTheDocument();
  });

  test('calls sellStock when Sell Stock is clicked in context menu', async () => {
    const user = userEvent.setup();
    customRender(<StockPortfolio />);
    
    // Right click the Apple stock
    const stockItem = screen.getByText('Apple Inc.').closest('[data-testid="stock-item"]');
    await user.pointer({ 
      keys: '[MouseRight]', 
      target: stockItem 
    });
    
    // Click the Sell Stock button
    await user.click(screen.getByRole('button', { name: /sell stock/i }));
    
    // Verify the sell function was called with the correct stock ID
    expect(mockSellStock).toHaveBeenCalledWith('1');
  });

  test('opens add stock modal when Add New Stock button is clicked', async () => {
    const user = userEvent.setup();
    customRender(<StockPortfolio />);
    
    await user.click(screen.getByRole('button', { name: /add new stock/i }));
    
    expect(screen.getByTestId('add-stock-modal')).toBeInTheDocument();
  });

  test('closes add stock modal when close button is clicked', async () => {
    const user = userEvent.setup();
    customRender(<StockPortfolio />);
    
    // Open the modal
    await user.click(screen.getByRole('button', { name: /add new stock/i }));
    expect(screen.getByTestId('add-stock-modal')).toBeInTheDocument();
    
    // Close the modal
    await user.click(screen.getByRole('button', { name: /cancel/i }));
    
    // Verify modal is closed
    expect(screen.queryByTestId('add-stock-modal')).not.toBeInTheDocument();
  });
});
```

Detailed explanation of each test:

1. **Total Value Calculation Test**:
   - Verifies that the component correctly sums up the value of all stocks
   - Each stock's value is quantity × currentPrice
   - Tests both the calculation logic and the formatting of the result

2. **Stock Items Rendering Test**:
   - Verifies that each stock in the mock data is rendered correctly
   - Checks for both the company name and the stock details format

3. **Empty State Test**:
   - Tests the conditional rendering logic when there are no stocks
   - Uses custom provider props to override the default stocks with an empty array

4. **Context Menu Right-Click Test**:
   - Uses userEvent's pointer API to simulate a right-click, which is more complex than a regular click
   - Verifies that the context menu appears in the correct state
   - Uses data-testid for elements that don't have semantic roles

5. **Sell Stock Functionality Test**:
   - Tests the complete user flow: right-click → context menu → click sell
   - Verifies that the context action calls the correct function with the right parameter

6. **Modal Opening Test**:
   - Verifies that clicking the "Add New Stock" button opens the modal
   - Tests conditional rendering of the modal component

7. **Modal Closing Test**:
   - Tests the complete flow of opening and then closing the modal
   - Uses queryByTestId instead of getByTestId when checking for absence

The `customRender` function simplifies providing the context to each test and makes the tests more maintainable.

### Testing the Stock Item Component

```jsx
// StockItem.test.jsx
import React from 'react';
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import StockItem from './StockItem';

describe('StockItem Component', () => {
  const mockStock = {
    id: '1',
    symbol: 'AAPL',
    name: 'Apple Inc.',
    quantity: 10,
    currentPrice: 150.25,
    purchasePrice: 140.50
  };
  
  const mockRightClick = jest.fn();
  
  test('renders stock information correctly', () => {
    render(<StockItem stock={mockStock} onRightClick={mockRightClick} />);
    
    expect(screen.getByText('Apple Inc.')).toBeInTheDocument();
    expect(screen.getByText('AAPL - 10 shares')).toBeInTheDocument();
    expect(screen.getByText('Current: $150.25')).toBeInTheDocument();
    expect(screen.getByText('Total Value: $1,502.50')).toBeInTheDocument();
  });
  
  test('displays profit information', () => {
    render(<StockItem stock={mockStock} onRightClick={mockRightClick} />);
    
    // Profit = (currentPrice - purchasePrice) * quantity = (150.25 - 140.50) * 10 = 97.50
    expect(screen.getByText('Profit: +$97.50 (+7.0%)')).toBeInTheDocument();
    // Green color for positive profit
    expect(screen.getByTestId('profit-indicator')).toHaveClass('profit-positive');
  });
  
  test('displays loss information when stock is down', () => {
    const lossStock = {
      ...mockStock,
      currentPrice: 130.25
    };
    
    render(<StockItem stock={lossStock} onRightClick={mockRightClick} />);
    
    // Loss = (currentPrice - purchasePrice) * quantity = (130.25 - 140.50) * 10 = -102.50
    expect(screen.getByText('Loss: -$102.50 (-7.3%)')).toBeInTheDocument();
    // Red color for negative profit
    expect(screen.getByTestId('profit-indicator')).toHaveClass('profit-negative');
  });
  
  test('calls onRightClick handler when right-clicked', async () => {
    const user = userEvent.setup();
    render(<StockItem stock={mockStock} onRightClick={mockRightClick} />);
    
    const stockElement = screen.getByTestId('stock-item');
    await user.pointer({ 
      keys: '[MouseRight]', 
      target: stockElement 
    });
    
    expect(mockRightClick).toHaveBeenCalledTimes(1);
  });
});
```

Detailed explanation of each test:

1. **Basic Rendering Test**:
   - Verifies that all stock details are correctly displayed
   - Tests the formatting of various pieces of information

2. **Profit Calculation Test**:
   - Tests the component's calculation of profit when current price > purchase price
   - Verifies both the numeric calculation and the percentage calculation
   - Also tests that the correct CSS class is applied for styling

3. **Loss Calculation Test**:
   - Creates a modified version of the stock with a lower current price
   - Tests the calculation and display of loss values
   - Verifies that the negative profit class is applied

4. **Right-Click Handler Test**:
   - Uses userEvent's pointer API to simulate a right-click action
   - Verifies that the onRightClick callback is called exactly once

These tests ensure that the StockItem component correctly:
- Displays stock information
- Calculates and displays profit/loss correctly
- Handles user interactions (right-click)
- Applies the appropriate styling based on profit/loss status

### Testing the Add Stock Modal

```jsx
// AddStockModal.test.jsx
import React from 'react';
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import AddStockModal from './AddStockModal';
import { StockContext } from '../../context/StockContext';

const mockAddStock = jest.fn();
const mockOnClose = jest.fn();

const customRender = (ui, { providerProps = {}, ...renderOptions } = {}) => {
  return render(
    <StockContext.Provider 
      value={{ 
        addStock: mockAddStock,
        ...providerProps 
      }}
    >
      {ui}
    </StockContext.Provider>,
    renderOptions
  );
};

describe('AddStockModal Component', () => {
  beforeEach(() => {
    jest.clearAllMocks();
  });
  
  test('renders the form with input fields', () => {
    customRender(<AddStockModal onClose={mockOnClose} />);
    
    expect(screen.getByTestId('add-stock-modal')).toBeInTheDocument();
    expect(screen.getByLabelText(/stock symbol/i)).toBeInTheDocument();
    expect(screen.getByLabelText(/quantity/i)).toBeInTheDocument();
    expect(screen.getByLabelText(/purchase price/i)).toBeInTheDocument();
    expect(screen.getByRole('button', { name: /add stock/i })).toBeInTheDocument();
    expect(screen.getByRole('button', { name: /cancel/i })).toBeInTheDocument();
  });
  
  test('validates form fields on submission', async () => {
    const user = userEvent.setup();
    customRender(<AddStockModal onClose={mockOnClose} />);
    
    // Click submit without filling fields
    await user.click(screen.getByRole('button', { name: /add stock/i }));
    
    // Check validation messages
    expect(screen.getByText(/stock symbol is required/i)).toBeInTheDocument();
    expect(screen.getByText(/quantity is required/i)).toBeInTheDocument();
    expect(screen.getByText(/purchase price is required/i)).toBeInTheDocument();
    
    // Verify addStock was not called
    expect(mockAddStock).not.toHaveBeenCalled();
  });
  
  test('calls addStock with form data when valid form is submitted', async () => {
    const user = userEvent.setup();
    customRender(<AddStockModal onClose={mockOnClose} />);
    
    // Fill out the form
    await user.type(screen.getByLabelText(/stock symbol/i), 'AAPL');
    await user.type(screen.getByLabelText(/quantity/i), '10');
    await user.type(screen.getByLabelText(/purchase price/i), '150.25');
    
    // Submit the form
    await user.click(screen.getByRole('button', { name: /add stock/i }));
    
    // Verify addStock was called with correct data
    expect(mockAddStock).toHaveBeenCalledWith({
      symbol: 'AAPL',
      quantity: 10,
      purchasePrice: 150.25
    });
    
    // Verify modal was closed
    expect(mockOnClose).toHaveBeenCalled();
  });
  
  test('calls onClose when Cancel button is clicked', async () => {
    const user = userEvent.setup();
    customRender(<AddStockModal onClose={mockOnClose} />);
    
    await user.click(screen.getByRole('button', { name: /cancel/i }));
    
    expect(mockOnClose).toHaveBeenCalled();
  });
});
```

Detailed explanation of each test:

1. **Form Rendering Test**:
   - Verifies that the modal contains all necessary form elements
   - Uses both `getByLabelText` (which is better for accessibility) and `getByRole`
   - This indirectly tests that form inputs have proper labels

2. **Form Validation Test**:
   - Tests the form's validation logic by submitting an empty form
   - Verifies that appropriate error messages are displayed
   - Ensures the API call isn't made with invalid data

3. **Form Submission Test**:
   - Tests the complete form submission flow:
     - Filling out all form fields with valid data
     - Submitting the form
     - Verifying the expected API call with the correct parameters
     - Checking that the modal is closed after successful submission
   - This test ensures that form values are correctly collected and processed

4. **Modal Cancellation Test**:
   - Verifies that clicking the Cancel button calls the onClose callback
   - This ensures users can easily exit the modal without submitting

These tests cover the key functionality of the modal component:
- Rendering the correct UI elements
- Form validation
- Form submission with data transformation (string to number for numeric fields)
- User interaction with the cancel button

## Advanced Testing Techniques

### Testing Custom Hooks

```javascript
// useStockPortfolio.test.js
import { renderHook, act } from '@testing-library/react-hooks';
import useStockPortfolio from './useStockPortfolio';

// Mock API
jest.mock('../api/stockApi', () => ({
  fetchStocks: jest.fn(),
  addStock: jest.fn(),
  sellStock: jest.fn(),
}));

import { fetchStocks, addStock, sellStock } from '../api/stockApi';

describe('useStockPortfolio Hook', () => {
  beforeEach(() => {
    jest.clearAllMocks();
    fetchStocks.mockResolvedValue([
      { id: '1', symbol: 'AAPL', name: 'Apple Inc.', quantity: 10, currentPrice: 150.25 }
    ]);
  });
  
  test('initializes with empty stocks array and loading state', () => {
    const { result } = renderHook(() => useStockPortfolio());
    
    expect(result.current.stocks).toEqual([]);
    expect(result.current.loading).toBe(true);
    expect(result.current.error).toBe(null);
  });
  
  test('fetches stocks on initialization', async () => {
    const { result, waitForNextUpdate } = renderHook(() => useStockPortfolio());
    
    await waitForNextUpdate();
    
    expect(fetchStocks).toHaveBeenCalledTimes(1);
    expect(result.current.stocks).toEqual([
      { id: '1', symbol: 'AAPL', name: 'Apple Inc.', quantity: 10, currentPrice: 150.25 }
    ]);
    expect(result.current.loading).toBe(false);
  });
  
  test('handles error during stocks fetch', async () => {
    fetchStocks.mockRejectedValue(new Error('Failed to fetch'));
    
    const { result, waitForNextUpdate } = renderHook(() => useStockPortfolio());
    
    await waitForNextUpdate();
    
    expect(result.current.loading).toBe(false);
    expect(result.current.error).toBe('Failed to fetch stocks');
    expect(result.current.stocks).toEqual([]);
  });
  
  test('adds a stock successfully', async () => {
    const newStock = { symbol: 'MSFT', quantity: 5, purchasePrice: 290.75 };
    addStock.mockResolvedValue({
      id: '2',
      ...newStock,
      name: 'Microsoft',
      currentPrice: 290.75
    });
    
    const { result, waitForNextUpdate } = renderHook(() => useStockPortfolio());
    
    await waitForNextUpdate(); // Wait for initial fetch
    
    act(() => {
      result.current.addStockToPortfolio(newStock);
    });
    
    await waitForNextUpdate(); // Wait for addStock to complete
    
    expect(addStock).toHaveBeenCalledWith(newStock);
    expect(result.current.stocks).toHaveLength(2);
    expect(result.current.stocks[1].symbol).toBe('MSFT');
  });
  
  test('sells a stock successfully', async () => {
    sellStock.mockResolvedValue({ success: true });
    
    const { result, waitForNextUpdate } = renderHook(() => useStockPortfolio());
    
    await waitForNextUpdate(); // Wait for initial fetch
    
    act(() => {
      result.current.sellStockFromPortfolio('1');
    });
    
    await waitForNextUpdate(); // Wait for sellStock to complete
    
    expect(sellStock).toHaveBeenCalledWith('1');
    expect(result.current.stocks).toHaveLength(0);
  });
});
```

Detailed explanation of hook tests:

1. **Initial State Test**:
   - Verifies that the hook initializes with the expected default values
   - This is important because components using the hook will initially render with these values

2. **Data Fetching Test**:
   - Tests that the hook automatically fetches data when mounted
   - Uses `waitForNextUpdate` to wait for the asynchronous operation to complete
   - Checks both that the API was called and that the state was updated correctly

3. **Error Handling Test**:
   - Tests the hook's behavior when the API call fails
   - Mocks a rejected promise to simulate a network error
   - Verifies that the hook correctly updates loading and error states

4. **Add Stock Test**:
   - Tests the function that adds a new stock to the portfolio
   - Waits for the initial load, then calls the function
   - Verifies that:
     - The API was called with the correct parameters
     - The state was updated to include the new stock

5. **Sell Stock Test**:
   - Tests the function that removes a stock from the portfolio
   - Waits for the initial load, then calls the function
   - Verifies that:
     - The API was called with the correct stock ID
     - The stock was removed from the state

These tests ensure that the hook correctly:
- Manages loading and error states
- Fetches initial data
- Handles API interactions for adding and selling stocks
- Updates its state based on API responses

### Snapshot Testing

```javascript
import { render } from '@testing-library/react';
import StockItem from './StockItem';

test('matches snapshot', () => {
  const stock = {
    id: '1',
    symbol: 'AAPL',
    name: 'Apple Inc.',
    quantity: 10,
    currentPrice: 150.25
  };
  
  const { container } = render(<StockItem stock={stock} onRightClick={() => {}} />);
  expect(container).toMatchSnapshot();
});
```

Snapshot testing captures the rendered output of a component and saves it as a reference. In subsequent test runs, the component's output is compared to the saved snapshot.

Benefits of snapshot testing:
- Quickly detect unintended changes in component output
- Provide a visual reference for how a component should render
- Easy to update when intentional changes are made

Best practices:
- Keep snapshots small and focused
- Review snapshot diffs carefully before updating
- Don't rely solely on snapshots; they should complement, not replace, behavior testing
- Use `toMatchInlineSnapshot()` for smaller components to keep the snapshot in the test file

Example with inline snapshot:

```javascript
test('matches inline snapshot', () => {
  const stock = {
    id: '1',
    symbol: 'AAPL',
    name: 'Apple Inc.',
    quantity: 10,
    currentPrice: 150.25
  };
  
  const { container } = render(<StockItem stock={stock} onRightClick={() => {}} />);
  expect(container).toMatchInlineSnapshot(`
    <div>
      <div class="stock-item" data-testid="stock-item">
        <h3>Apple Inc.</h3>
        <p>AAPL - 10 shares</p>
        <p>Current: $150.25</p>
        <p>Total Value: $1,502.50</p>
      </div>
    </div>
  `);
});
```

When to use snapshots:
- For stable components that don't change frequently
- For visual components where the structure is important
- As a complement to behavior tests, not a replacement

### Testing Accessibility

```javascript
import { render } from '@testing-library/react';
import { axe, toHaveNoViolations } from 'jest-axe';
import StockPortfolio from './StockPortfolio';

expect.extend(toHaveNoViolations);

test('has no accessibility violations', async () => {
  const { container } = render(<StockPortfolio />);
  const results = await axe(container);
  expect(results).toHaveNoViolations();
});
```

Detailed explanation:
- `jest-axe` is a Jest matcher that integrates with the axe-core accessibility testing engine
- `expect.extend(toHaveNoViolations)` adds a custom matcher to Jest
- The test renders the component, then uses axe to analyze the DOM for accessibility issues
- If any violations are found, the test will fail with detailed information about the issues

Benefits of accessibility testing:
- Ensures components are usable by people with disabilities
- Identifies issues with color contrast, screen reader compatibility, keyboard navigation, etc.
- Helps comply with accessibility standards like WCAG 2.1

Additional testing for specific accessibility concerns:

```javascript
test('form elements have associated labels', () => {
  render(<AddStockModal onClose={() => {}} />);
  
  // Get all form inputs
  const inputs = screen.getAllByRole('textbox');
  
  // Check that each input has an associated label
  inputs.forEach(input => {
    const inputId = input.getAttribute('id');
    const associatedLabel = screen.getByLabelText(input.getAttribute('aria-label') || '');
    expect(associatedLabel).toBeInTheDocument();
  });
});

test('can navigate form with keyboard', async () => {
  const user = userEvent.setup();
  render(<AddStockModal onClose={() => {}} />);
  
  // Start by focusing the first element
  const firstInput = screen.getByLabelText(/stock symbol/i);
  firstInput.focus();
  expect(document.activeElement).toBe(firstInput);
  
  // Tab to the next field
  await user.tab();
  expect(document.activeElement).toBe(screen.getByLabelText(/quantity/i));
  
  // Tab to the third field
  await user.tab();
  expect(document.activeElement).toBe(screen.getByLabelText(/purchase price/i));
  
  // Tab to the Add Stock button
  await user.tab();
  expect(document.activeElement).toBe(screen.getByRole('button', { name: /add stock/i }));
  
  // Tab to the Cancel button
  await user.tab();
  expect(document.activeElement).toBe(screen.getByRole('button', { name: /cancel/i }));
});
```

These tests help ensure your application is accessible to all users, including those with disabilities who may use assistive technologies like screen readers or keyboard navigation.

## Testing Best Practices

1. **Test behavior, not implementation details**:
   - Focus on what the component does, not how it does it
   - Avoid testing component state directly
   - Use user-centric queries (getByRole, getByLabelText) instead of testId when possible

   ```javascript
   // Good: Testing behavior
   test('increments counter when button is clicked', async () => {
     render(<Counter initialCount={0} />);
     
     expect(screen.getByText('Count: 0')).toBeInTheDocument();
     
     await userEvent.click(screen.getByRole('button', { name: /increment/i }));
     
     expect(screen.getByText('Count: 1')).toBeInTheDocument();
   });
   
   // Avoid: Testing implementation details
   test('updates count state when button is clicked', () => {
     const { result } = renderHook(() => useState(0));
     const [count, setCount] = result.current;
     
     act(() => {
       setCount(count + 1);
     });
     
     expect(result.current[0]).toBe(1);
   });
   ```

2. **Write maintainable tests**:
   - Keep tests simple and focused
   - Use setup and teardown functions for common logic
   - Use descriptive test names

   ```javascript
   // Use descriptive test names that form complete sentences
   test('displays loading indicator while fetching data', async () => {
     // ...
   });
   
   // Extract common setup code
   function renderComponent(props = {}) {
     return render(<MyComponent {...defaultProps} {...props} />);
   }
   
   test('shows error message when API call fails', async () => {
     mockApiFailure();
     renderComponent();
     expect(await screen.findByText(/error/i)).toBeInTheDocument();
   });
   ```

3. **Leverage Testing Library's best practices**:
   - Query elements in order of accessibility: role, label, text, etc.
   - Use userEvent over fireEvent for more realistic user interactions
   - Prefer waitFor or findBy over arbitrary timeouts

   ```javascript
   // Prefer (in order of preference):
   screen.getByRole('button', { name: /submit/i });
   screen.getByLabelText(/email/i);
   screen.getByPlaceholderText(/enter email/i);
   screen.getByText(/forgot password/i);
   
   // Use only when necessary:
   screen.getByTestId('submit-button');
   ```

4. **Test coverage guidelines**:
   - Components: Rendering, user interactions, different states
   - Hooks: Initialization, state changes, side effects
   - Utils: Edge cases, error handling
   - Aim for 80%+ coverage but prioritize quality over quantity

   ```javascript
   // Test different states of a component
   test('renders empty state', () => {
     render(<TodoList items={[]} />);
     expect(screen.getByText(/no items/i)).toBeInTheDocument();
   });
   
   test('renders with items', () => {
     render(<TodoList items={['Buy milk', 'Walk dog']} />);
     expect(screen.getByText('Buy milk')).toBeInTheDocument();
     expect(screen.getByText('Walk dog')).toBeInTheDocument();
   });
   
   test('renders error state', () => {
     render(<TodoList error="Failed to load" />);
     expect(screen.getByText(/failed to load/i)).toBeInTheDocument();
   });
   ```

5. **Follow the testing pyramid**:
   - Unit tests: High quantity, test individual units
   - Integration tests: Medium quantity, test combinations of units
   - End-to-end tests: Lower quantity, test complete user flows

   ```
   // Unit test for a utility function
   test('formatCurrency formats number correctly', () => {
     expect(formatCurrency(1234.56)).toBe('$1,234.56');
     expect(formatCurrency(0)).toBe('$0.00');
     expect(formatCurrency(-10)).toBe('-$10.00');
   });
   
   // Integration test for connected components
   test('submitting form saves data and shows success message', async () => {
     render(
       <Provider store={store}>
         <UserForm />
       </Provider>
     );
     
     // Fill and submit form...
     
     // Check store was updated and success message shown
   });
   ```

6. **Mock wisely**:
   - Only mock what's necessary (external dependencies, API calls)
   - Use realistic mock data
   - Consider using MSW for API mocking instead of mocking fetch/axios

   ```javascript
   // Mock specific function
   jest.spyOn(api, 'fetchData').mockResolvedValue(mockData);
   
   // Using MSW for API mocking
   import { setupServer } from 'msw/node';
   import { rest } from 'msw';
   
   const server = setupServer(
     rest.get('/api/users', (req, res, ctx) => {
       return res(ctx.json([{ id: 1, name: 'John' }]));
     })
   );
   
   beforeAll(() => server.listen());
   afterEach(() => server.resetHandlers());
   afterAll(() => server.close());
   ```

7. **Test for edge cases**:
   - Loading states
   - Error states
   - Empty states
   - Boundaries and limits

   ```javascript
   test('handles maximum character limit in text field', async () => {
     render(<TextField maxLength={10} />);
     
     const input = screen.getByRole('textbox');
     await userEvent.type(input, '12345678901');
     
     expect(input).toHaveValue('1234567890');
   });
   
   test('displays appropriate message for empty search results', async () => {
     mockSearchResults([]);
     render(<SearchResults query="nonexistent" />);
     
     expect(await screen.findByText(/no results found/i)).toBeInTheDocument();
   });
   ```

Each of these best practices helps create a robust, maintainable test suite that gives you confidence in your application's behavior without becoming brittle or difficult to maintain.

## Conclusion

Unit testing React applications is essential for building reliable, maintainable software. By following the practices outlined in this guide, you can create tests that:

- Act as documentation for your components
- Catch bugs before they reach production
- Provide confidence when making changes
- Improve the overall quality of your codebase

Remember that the primary goal of testing is not to achieve 100% coverage, but to ensure your application works correctly for users. Focus on testing critical paths and user interactions first, then expand your test suite as needed.

For the stock portfolio application example, we've demonstrated how to test various aspects of React components, including:
- Rendering and display of data
- User interactions (clicks, right-clicks, form submissions)
- Context integration
- Modal dialogs
- Error handling and form validation

By applying these testing principles to your own React applications, you can build more robust and maintainable software that meets user needs.
