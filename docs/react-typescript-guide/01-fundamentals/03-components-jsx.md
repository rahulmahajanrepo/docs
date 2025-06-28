# Components and JSX in React with TypeScript

This section explores the fundamentals of creating and working with React components using TypeScript and JSX.

## Understanding JSX

JSX (JavaScript XML) allows you to write HTML-like code within JavaScript. When combined with TypeScript, it provides a powerful way to build type-safe UI components.

### Basic JSX Syntax

```tsx
// Basic JSX example
const element = <h1>Hello, world!</h1>;

// JSX with expressions
const name = "TypeScript";
const greeting = <h1>Hello, {name}!</h1>;

// JSX with attributes
const link = <a href="https://reactjs.org" target="_blank">React Website</a>;

// JSX for multiline elements
const container = (
  <div>
    <h1>Title</h1>
    <p>Paragraph</p>
  </div>
);
```

### TypeScript with JSX

TypeScript adds type checking to JSX, helping catch errors before runtime:

```tsx
// Using TypeScript with JSX
const user: { name: string; age: number } = {
  name: "Alice",
  age: 30
};

const userProfile = (
  <div>
    <h2>{user.name}</h2>
    <p>Age: {user.age}</p>
    {/* This would cause a TypeScript error: */}
    {/* <p>Location: {user.location}</p> */}
  </div>
);
```

## Import Syntax in React with TypeScript

Understanding import syntax is crucial for working with React components. JavaScript offers two main types of imports: default imports and named imports.

### Default vs Named Imports

```tsx
// Default import (without curly braces)
import React from 'react';

// Named import (with curly braces)
import { useState } from 'react';

// Combined import (both default and named)
import React, { Component, useState } from 'react';
```

#### Default Imports

Default imports are used when a module exports a single primary value:

- A module can have only one default export
- When importing a default export, you can choose any name for the import
- Often used for the main functionality of a module

```tsx
// Module with default export (Button.tsx)
const Button = ({ children }) => <button>{children}</button>;
export default Button;

// Importing the default export - you can use any name
import MyButton from './Button';
import CustomButton from './Button'; // Same component, different import name
```

#### Named Imports

Named imports allow you to import specific exports from a module:

- A module can have multiple named exports
- You must use the exact names as exported (or use alias with `as`)
- Enclosed in curly braces `{ }`

```tsx
// Module with named exports (utils.tsx)
export const formatDate = (date) => { /* formatting logic */ };
export const calculateTotal = (items) => { /* calculation logic */ };

// Importing named exports
import { formatDate, calculateTotal } from './utils';

// Using alias for named imports
import { formatDate as formatMyDate } from './utils';
```

#### Benefits of Named Imports

1. **Tree-shaking friendly**: Bundlers can eliminate unused code
2. **Explicit dependencies**: Clearly shows which parts of a module you're using
3. **Code organization**: Encourages breaking down code into focused, reusable functions

```tsx
// Only imports what you need, helping reduce bundle size
import { useState, useEffect } from 'react';

// Instead of importing everything
import React from 'react';
// And then using React.useState, React.useEffect
```

#### Common React Import Patterns

```tsx
// Standard React import (prior to React 17)
import React from 'react';

// Importing hooks
import { useState, useEffect, useRef } from 'react';

// Importing component types
import { FC, ReactNode, ComponentProps } from 'react';

// Component file with both default and named exports
import Button, { ButtonProps } from './components/Button';
```

### Export Syntax

Export syntax mirrors import syntax with default and named exports:

```tsx
// Named exports
export const Button = () => <button>Click me</button>;
export interface ButtonProps { /* props definition */ }

// Default export
const Card = () => <div className="card">Card content</div>;
export default Card;

// Re-exporting in index files (barrel exports)
export { Button } from './Button';
export { Card as default } from './Card';
export type { ButtonProps } from './Button';
```

### Exporting and Importing Types

TypeScript provides specific syntax for exporting and importing types:

```tsx
// In Button.tsx
export interface ButtonProps {
  variant?: 'primary' | 'secondary';
  size?: 'small' | 'medium' | 'large';
  children: React.ReactNode;
}

export const Button = ({ variant, size, children }: ButtonProps) => {
  // Component implementation
};
```

```tsx
// In index.ts
// Re-export the type explicitly with 'export type'
export type { ButtonProps } from './Button';
export { Button } from './Button';
```

```tsx
// In another file using the component
// Using the 'import type' syntax
import type { ButtonProps } from './components/Button';
import { Button } from './components/Button';

// Use ButtonProps for type annotations
const props: ButtonProps = {
  variant: 'primary',
  children: 'Click me'
};
```

#### Understanding `export type` and `import type`

The syntax `export type { ButtonProps } from './Button';` in React with TypeScript is used to re-export a type definition from another module. Here's a breakdown:

- **`export type`**: Specifies that you're exporting a type, not a value. This is crucial in TypeScript, as types are used for compile-time checking and do not exist at runtime.
- **`{ ButtonProps }`**: This is the named export of the type ButtonProps. It means you're exporting a type named ButtonProps that was defined in the file ./Button.
- **`from './Button'`**: This specifies the source module from which the type is being imported and re-exported.

#### Should you import it as type as well?

Yes, when you import ButtonProps from this re-export, you should also use `import type`:

```tsx
import type { ButtonProps } from './components/Button';
```

This is because you're dealing with a type definition, not a value.

#### Benefits of explicit type exports and imports:

1. **Clarity**: It makes it clear that you're dealing with types, not values, improving code readability and maintainability.
2. **Performance**: It helps TypeScript optimize compilation by knowing which imports are types and can be removed from the output JavaScript.
3. **Avoid runtime errors**: It prevents accidental imports of values that are meant to be types only.
4. **Tree-shaking**: Bundlers can better eliminate unused code since type imports are guaranteed to have no runtime impact.

#### Best practices:

- Use `export type` when exporting interfaces, types, and type aliases
- Use `import type` when importing types for use as type annotations
- For component index files, clearly separate type exports from component exports

```tsx
// Good practice in index.ts
// Types first, then components
export type { ButtonProps, ButtonSize } from './Button';
export type { CardProps } from './Card';

export { Button } from './Button';
export { Card } from './Card';
```

## Functional Components

Functional components are the preferred way to write React components. They're JavaScript functions that return JSX.

### Basic Functional Component

```tsx
// Basic functional component
import React from 'react';

const Greeting = () => {
  return <h1>Hello, World!</h1>;
};

export default Greeting;
```

### Functional Component with Props

```tsx
// Function component with TypeScript props
import React from 'react';

// Define the props interface
interface GreetingProps {
  name: string;
  count?: number;
}

// Use React.FC type with generics (optional)
const Greeting: React.FC<GreetingProps> = ({ name, count = 1 }) => {
  return (
    <div>
      <h1>
        {Array(count)
          .fill(`Hello, ${name}!`)
          .join(' ')}
      </h1>
    </div>
  );
};

export default Greeting;

// Usage
// <Greeting name="TypeScript" />
// <Greeting name="TypeScript" count={3} />

```

### Functional Component without React.FC

```tsx
// Modern approach without using React.FC
import React from 'react';

interface GreetingProps {
  name: string;
  count?: number;
}

// Simply use the props interface directly
function Greeting({ name, count = 1 }: GreetingProps) {
  return (
    <div>
      <h1>
        {Array(count)
          .fill(`Hello, ${name}!`)
          .join(' ')}
      </h1>
    </div>
  );
}

export default Greeting;
```

### React.FC vs Direct Function Components

There are two common approaches to typing functional components in React with TypeScript:

1. Using the `React.FC` (or `React.FunctionComponent`) generic type
2. Defining a function with explicit prop types without using `React.FC`

Let's compare these approaches:

#### Comparison Table: React.FC vs Direct Function Components

| Aspect | With React.FC | Without React.FC |
|--------|---------------|------------------|
| Children prop | Implicitly includes `children?: ReactNode` | Must explicitly define children in props interface if needed |
| Return type | Explicitly typed as `JSX.Element` | Inferred by TypeScript (more flexible) |
| displayName, defaultProps, etc. | TypeScript aware | TypeScript aware but requires manual type assertion |
| Typing complexity | Simpler for beginners | More explicit but slightly more verbose |
| Type inference | Sometimes restricts inference | Better for complex type inference |
| Generic components | Works but can be verbose | Easier to work with generic props |
| Forward refs | Less intuitive typing | More natural typing |
| Performance | No difference | No difference |

#### Advantages of Using React.FC

1. **Automatically provides children type**: Includes `children?: React.ReactNode` in the props type without explicit definition
2. **Explicit return type**: Makes it clear the function returns JSX
3. **Auto-completion**: IDEs can provide better auto-completion for React-specific features
4. **Beginner-friendly**: More intuitive for developers new to TypeScript with React

```tsx
// With React.FC, children prop is automatically included
const Card: React.FC<{ title: string }> = ({ title, children }) => {
  return (
    <div className="card">
      <h2>{title}</h2>
      <div>{children}</div> {/* No need to declare children in props */}
    </div>
  );
};
```

#### Advantages of Not Using React.FC

1. **More flexible return types**: Better supports components that might return null, arrays, or strings
2. **Better support for generics**: Simpler syntax for components with generic props
3. **More precise typing**: You declare exactly what your component accepts
4. **No implicit children**: Forces you to explicitly define the children prop if you need it
5. **React team's current preference**: The React team and many libraries are moving away from using `React.FC`

```tsx
// Without React.FC you must define children explicitly
interface CardProps {
  title: string;
  children?: React.ReactNode; // Explicitly define children
}

function Card({ title, children }: CardProps) {
  return (
    <div className="card">
      <h2>{title}</h2>
      <div>{children}</div>
    </div>
  );
}

// Generic components are more straightforward
function List<T>({ items, renderItem }: { 
  items: T[]; 
  renderItem: (item: T) => React.ReactNode;
}) {
  return (
    <ul>
      {items.map((item, index) => (
        <li key={index}>{renderItem(item)}</li>
      ))}
    </ul>
  );
}
```

### Best Practices

Based on current community trends and React team recommendations:

1. **Prefer direct function components over React.FC** for most use cases
   ```tsx
   // Prefer this
   function Button({ onClick, children }: ButtonProps) {
     return <button onClick={onClick}>{children}</button>;
   }
   
   // Over this
   const Button: React.FC<ButtonProps> = ({ onClick, children }) => {
     return <button onClick={onClick}>{children}</button>;
   };
   ```

2. **Explicitly define children when needed**
   ```tsx
   interface LayoutProps {
     header: React.ReactNode;
     children: React.ReactNode; // Explicitly typed
     footer?: React.ReactNode;
   }
   ```

3. **Use function declarations for main components** for better debugging and clearer stack traces
   ```tsx
   // Function declaration - easier to debug
   function UserProfile({ user }: { user: User }) {
     return <div>{user.name}</div>;
   }
   
   // Rather than arrow function
   const UserProfile = ({ user }: { user: User }) => <div>{user.name}</div>;
   ```

4. **Prefer interfaces for prop types** over type aliases for better error messages and extendability
   ```tsx
   // Prefer this
   interface ButtonProps {
     variant?: 'primary' | 'secondary';
     size?: 'small' | 'medium' | 'large';
     onClick?: () => void;
     children: React.ReactNode;
   }
   
   // Over this
   type ButtonProps = {
     variant?: 'primary' | 'secondary';
     size?: 'small' | 'medium' | 'large';
     onClick?: () => void;
     children: React.ReactNode;
   };
   ```

5. **Choose consistency** within your codebase - use the same approach throughout your project

### Example: Component Definition Patterns

Here's an example showing different component definition patterns with and without React.FC:

```tsx
import React, { useState } from 'react';

// 1. Function declaration without React.FC (RECOMMENDED)
interface CounterProps {
  initialCount: number;
  step?: number;
}

function Counter({ initialCount, step = 1 }: CounterProps) {
  const [count, setCount] = useState(initialCount);
  
  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + step)}>Increment</button>
    </div>
  );
}

// 2. Arrow function without React.FC
const CounterArrow = ({ initialCount, step = 1 }: CounterProps) => {
  const [count, setCount] = useState(initialCount);
  
  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + step)}>Increment</button>
    </div>
  );
};

// 3. Function declaration with React.FC
const CounterWithFC: React.FC<CounterProps> = ({ initialCount, step = 1 }) => {
  const [count, setCount] = useState(initialCount);
  
  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + step)}>Increment</button>
    </div>
  );
};

// 4. Generic component without React.FC
interface ListProps<T> {
  items: T[];
  renderItem: (item: T) => React.ReactNode;
}

function List<T>({ items, renderItem }: ListProps<T>) {
  return (
    <ul>
      {items.map((item, index) => (
        <li key={index}>{renderItem(item)}</li>
      ))}
    </ul>
  );
}

// 5. Generic component with React.FC
// More verbose and complex
function GenericListWithFC<T>() {
  // Need to define as a function that returns React.FC due to TS limitations
  return React.forwardRef<HTMLUListElement, ListProps<T>>((props, ref) => {
    const { items, renderItem } = props;
    return (
      <ul ref={ref}>
        {items.map((item, index) => (
          <li key={index}>{renderItem(item)}</li>
        ))}
      </ul>
    );
  });
}
```

### Card Component Index File

```tsx
// src/components/Card/index.ts
export * from './Card';
export { Card as default } from './Card';
```

## Component Index Files Explained

Index files play a crucial role in organizing React components and making imports cleaner. Let's explore how they work and best practices for using them effectively.

### What is a Component Index File?

An index file (typically named `index.ts` or `index.tsx`) in a component directory serves as the public API for that component. It determines what gets exported from the component folder and how consumers can import it.

### Benefits of Using Index Files

1. **Cleaner imports**: Allows importing from the folder path rather than specific files
2. **Encapsulation**: Hides internal structure and implementation details
3. **API control**: Explicitly defines what's available to component consumers
4. **Simplified refactoring**: Change component internals without affecting imports elsewhere

### Common Index File Patterns

#### 1. Basic Re-export

This is the simplest pattern, just re-exporting the main component:

```tsx
// src/components/Button/index.ts
export { Button } from './Button';
```

Usage:
```tsx
import { Button } from 'components/Button';
```

#### 2. Default Export

Makes the component available as both a named and default export:

```tsx
// src/components/Avatar/index.ts
export { Avatar } from './Avatar';
export { Avatar as default } from './Avatar';
```

Usage:
```tsx
import Avatar from 'components/Avatar';
// or
import { Avatar } from 'components/Avatar';
```

#### 3. Bundled Component Family

For component families or compound components:

```tsx
// src/components/Menu/index.ts
export { Menu } from './Menu';
export { MenuItem } from './MenuItem';
export { MenuDivider } from './MenuDivider';
export { MenuHeader } from './MenuHeader';

// Optional default export of the main component
export { Menu as default } from './Menu';
```

Usage:
```tsx
import Menu, { MenuItem, MenuDivider } from 'components/Menu';
```

#### 4. Types and Constants Export

Export component along with related types and constants:

```tsx
// src/components/Table/index.ts
export { Table } from './Table';
export type { TableProps, TableColumn } from './Table.types';
export { SortDirection, TableSize } from './Table.constants';
```

Usage:
```tsx
import { Table, SortDirection } from 'components/Table';
import type { TableProps } from 'components/Table';
```

### Best Practices for Component Index Files

1. **Be explicit with exports**: Only export what's meant to be part of the public API
   ```tsx
   // Good: Explicit exports
   export { Button } from './Button';
   export type { ButtonProps } from './Button';
   
   // Avoid: Wildcard exports
   export * from './Button'; // Exports everything, including internals
   ```

2. **Maintain consistent export patterns** across your codebase
   ```tsx
   // Consistent export pattern for all components
   export { ComponentName } from './ComponentName';
   export { ComponentName as default } from './ComponentName';
   ```

3. **Consider barrel files** for logically grouped components
   ```tsx
   // src/components/index.ts (barrel file)
   export * from './Button';
   export * from './Card';
   export * from './Modal';
   ```

4. **Include TypeScript types** in your exports
   ```tsx
   export type { ButtonProps, ButtonSize, ButtonVariant } from './Button.types';
   ```

5. **Use index files for internal organization** as well
   ```
   Form/
     ├── components/
     │    ├── FormInput.tsx
     │    ├── FormCheckbox.tsx
     │    ├── FormSelect.tsx
     │    └── index.ts  // Internal barrel file
     ├── Form.tsx
     └── index.ts  // Main public API
   ```

6. **Keep index files simple** - they should primarily be re-export files, not contain logic

### Directory Structure with Index Files

A well-organized component with index files might look like this:

## Class Components

While functional components are now preferred, class components are still used in some codebases.

### Basic Class Component

```tsx
// Basic class component
import React, { Component } from 'react';

class Greeting extends Component {
  render() {
    return <h1>Hello, World!</h1>;
  }
}

export default Greeting;
```

### Class Component with Props

```tsx
// Class component with TypeScript props
import React, { Component } from 'react';

// Define props interface
interface GreetingProps {
  name: string;
  count?: number;
}

// Define state interface (if needed)
interface GreetingState {
  clicked: boolean;
}

class Greeting extends Component<GreetingProps, GreetingState> {
  // Default props
  static defaultProps = {
    count: 1
  };

  // Initialize state
  state: GreetingState = {
    clicked: false
  };

  handleClick = () => {
    this.setState({ clicked: true });
  };

  render() {
    const { name, count = 1 } = this.props;
    const { clicked } = this.state;
    
    return (
      <div>
        <h1>
          {Array(count)
            .fill(`Hello, ${name}!`)
            .join(' ')}
        </h1>
        <button onClick={this.handleClick}>
          {clicked ? 'Clicked!' : 'Click me'}
        </button>
      </div>
    );
  }
}

export default Greeting;
```

## TypeScript Interfaces for Props

TypeScript interfaces help define the shape of props, making components more predictable and easier to use.

### Basic Props Interface

```tsx
interface ButtonProps {
  text: string;           // Required string
  onClick: () => void;    // Required function with no parameters, returns void
  color?: string;         // Optional string
  disabled?: boolean;     // Optional boolean
}
```

### Props with Children

```tsx
interface CardProps {
  title: string;
  children: React.ReactNode; // Can accept any valid React children
}

const Card = ({ title, children }: CardProps) => {
  return (
    <div className="card">
      <h2>{title}</h2>
      <div>{children}</div>
    </div>
  );
};
```

### Props with Event Handlers

```tsx
interface InputFieldProps {
  label: string;
  value: string;
  onChange: (e: React.ChangeEvent<HTMLInputElement>) => void;
}

const InputField = ({ label, value, onChange }: InputFieldProps) => {
  return (
    <div>
      <label>{label}</label>
      <input type="text" value={value} onChange={onChange} />
    </div>
  );
};
```

### Props with Union Types

```tsx
// Using union types for more flexible props
interface ButtonProps {
  variant: 'primary' | 'secondary' | 'danger';
  size?: 'small' | 'medium' | 'large';
  onClick: () => void;
  children: React.ReactNode;
}

const Button = ({ 
  variant, 
  size = 'medium', 
  onClick, 
  children 
}: ButtonProps) => {
  return (
    <button 
      className={`btn btn-${variant} btn-${size}`}
      onClick={onClick}
    >
      {children}
    </button>
  );
};
```

## Example: Building a Button Component

Let's create a reusable Button component with TypeScript:

```tsx
// src/components/Button/Button.tsx
import React from 'react';
import './Button.css'; // Assume we have styles defined

export interface ButtonProps {
  /**
   * Button contents
   */
  children: React.ReactNode;
  
  /**
   * Button variation
   */
  variant?: 'primary' | 'secondary' | 'outlined' | 'text';
  
  /**
   * Button size
   */
  size?: 'small' | 'medium' | 'large';
  
  /**
   * Makes the button take the full width of its container
   */
  fullWidth?: boolean;
  
  /**
   * Is button disabled
   */
  disabled?: boolean;
  
  /**
   * Optional click handler
   */
  onClick?: (event: React.MouseEvent<HTMLButtonElement>) => void;
  
  /**
   * Optional className for additional styling
   */
  className?: string;
  
  /**
   * Optional type attribute
   */
  type?: 'button' | 'submit' | 'reset';
}

/**
 * Primary UI component for user interaction
 */
export const Button: React.FC<ButtonProps> = ({
  children,
  variant = 'primary',
  size = 'medium',
  fullWidth = false,
  disabled = false,
  onClick,
  className = '',
  type = 'button',
  ...restProps
}) => {
  const baseClass = 'button';
  
  const classNames = [
    baseClass,
    `${baseClass}--${variant}`,
    `${baseClass}--${size}`,
    fullWidth ? `${baseClass}--full-width` : '',
    className
  ].filter(Boolean).join(' ');
  
  return (
    <button
      type={type}
      className={classNames}
      disabled={disabled}
      onClick={onClick}
      {...restProps}
    >
      {children}
    </button>
  );
};
```

### CSS for the Button Component

```css
/* src/components/Button/Button.css */
.button {
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-family: 'Arial', sans-serif;
  font-weight: 600;
  transition: all 0.2s ease;
}

.button:disabled {
  cursor: not-allowed;
  opacity: 0.6;
}

/* Variants */
.button--primary {
  background-color: #0066cc;
  color: white;
}

.button--primary:hover:not(:disabled) {
  background-color: #0055aa;
}

.button--secondary {
  background-color: #6c757d;
  color: white;
}

.button--secondary:hover:not(:disabled) {
  background-color: #5a6268;
}

.button--outlined {
  background-color: transparent;
  border: 1px solid #0066cc;
  color: #0066cc;
}

.button--outlined:hover:not(:disabled) {
  background-color: rgba(0, 102, 204, 0.1);
}

.button--text {
  background-color: transparent;
  color: #0066cc;
  padding-left: 4px;
  padding-right: 4px;
}

.button--text:hover:not(:disabled) {
  background-color: rgba(0, 102, 204, 0.1);
}

/* Sizes */
.button--small {
  font-size: 12px;
  padding: 8px 12px;
}

.button--medium {
  font-size: 14px;
  padding: 10px 16px;
}

.button--large {
  font-size: 16px;
  padding: 12px 20px;
}

/* Full width */
.button--full-width {
  width: 100%;
}
```

### Button Component Index File

```tsx
// src/components/Button/index.ts
export * from './Button';
export { Button as default } from './Button';
```

## Unit Testing Components

Testing is crucial for maintaining component quality. Here's how to test our Button component:

```tsx
// src/components/Button/Button.test.tsx
import React from 'react';
import { render, screen, fireEvent } from '@testing-library/react';
import { Button } from './Button';

describe('Button Component', () => {
  // Test 1: Button renders with children
  test('renders button with children', () => {
    render(<Button>Click me</Button>);
    expect(screen.getByRole('button', { name: /click me/i })).toBeInTheDocument();
  });

  // Test 2: Button handles click events
  test('calls onClick handler when clicked', () => {
    const handleClick = jest.fn();
    render(<Button onClick={handleClick}>Click me</Button>);
    
    fireEvent.click(screen.getByRole('button'));
    expect(handleClick).toHaveBeenCalledTimes(1);
  });

  // Test 3: Button doesn't call onClick when disabled
  test('does not call onClick when disabled', () => {
    const handleClick = jest.fn();
    render(<Button onClick={handleClick} disabled>Click me</Button>);
    
    fireEvent.click(screen.getByRole('button'));
    expect(handleClick).not.toHaveBeenCalled();
  });

  // Test 4: Button has the correct CSS classes
  test('has correct CSS classes based on props', () => {
    const { rerender } = render(<Button>Default Button</Button>);
    expect(screen.getByRole('button')).toHaveClass('button button--primary button--medium');
    
    // Test secondary variant
    rerender(<Button variant="secondary">Secondary Button</Button>);
    expect(screen.getByRole('button')).toHaveClass('button button--secondary button--medium');
    
    // Test size
    rerender(<Button size="large">Large Button</Button>);
    expect(screen.getByRole('button')).toHaveClass('button button--primary button--large');
    
    // Test fullWidth
    rerender(<Button fullWidth>Full Width Button</Button>);
    expect(screen.getByRole('button')).toHaveClass('button button--primary button--medium button--full-width');
  });

  // Test 5: Button has the correct type attribute
  test('has correct type attribute', () => {
    const { rerender } = render(<Button>Default Button</Button>);
    expect(screen.getByRole('button')).toHaveAttribute('type', 'button');
    
    rerender(<Button type="submit">Submit Button</Button>);
    expect(screen.getByRole('button')).toHaveAttribute('type', 'submit');
  });
});
```

## Advanced Component Patterns

### Component with Internal State

```tsx
// Counter component with internal state
import React, { useState } from 'react';

interface CounterProps {
  initialCount?: number;
  step?: number;
}

export const Counter: React.FC<CounterProps> = ({
  initialCount = 0,
  step = 1
}) => {
  const [count, setCount] = useState(initialCount);
  
  const increment = () => setCount(count + step);
  const decrement = () => setCount(count - step);
  
  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={decrement}>Decrement</button>
      <button onClick={increment}>Increment</button>
    </div>
  );
};
```

### Component with Conditional Rendering

```tsx
// Component with conditional rendering
import React from 'react';

interface UserGreetingProps {
  user?: {
    name: string;
    isAuthenticated: boolean;
  };
}

export const UserGreeting: React.FC<UserGreetingProps> = ({ user }) => {
  // Early return pattern
  if (!user) {
    return <p>Please log in to continue.</p>;
  }
  
  return (
    <div>
      {user.isAuthenticated ? (
        <h1>Welcome back, {user.name}!</h1>
      ) : (
        <h1>Please verify your account, {user.name}.</h1>
      )}
    </div>
  );
};
```

### Component that Renders a List

```tsx
// Component that renders a list
import React from 'react';

interface ListItem {
  id: string | number;
  text: string;
}

interface ItemListProps {
  items: ListItem[];
  renderItem?: (item: ListItem) => React.ReactNode;
}

export const ItemList: React.FC<ItemListProps> = ({
  items,
  renderItem = (item) => <span>{item.text}</span>
}) => {
  if (items.length === 0) {
    return <p>No items found.</p>;
  }
  
  return (
    <ul>
      {items.map((item) => (
        <li key={item.id}>{renderItem(item)}</li>
      ))}
    </ul>
  );
};
```

## Testing Advanced Components

```tsx
// Testing the Counter component
import React from 'react';
import { render, screen, fireEvent } from '@testing-library/react';
import { Counter } from './Counter';

describe('Counter Component', () => {
  test('renders with initial count', () => {
    render(<Counter initialCount={5} />);
    expect(screen.getByText(/count: 5/i)).toBeInTheDocument();
  });
  
  test('increments count when increment button is clicked', () => {
    render(<Counter />);
    fireEvent.click(screen.getByText(/increment/i));
    expect(screen.getByText(/count: 1/i)).toBeInTheDocument();
  });
  
  test('decrements count when decrement button is clicked', () => {
    render(<Counter initialCount={5} />);
    fireEvent.click(screen.getByText(/decrement/i));
    expect(screen.getByText(/count: 4/i)).toBeInTheDocument();
  });
  
  test('respects custom step value', () => {
    render(<Counter initialCount={0} step={5} />);
    fireEvent.click(screen.getByText(/increment/i));
    expect(screen.getByText(/count: 5/i)).toBeInTheDocument();
    
    fireEvent.click(screen.getByText(/decrement/i));
    expect(screen.getByText(/count: 0/i)).toBeInTheDocument();
  });
});
```

## Conclusion

In this section, we've covered the fundamentals of creating functional and class components with JSX and TypeScript. We've seen how TypeScript provides type safety for props, learned how to structure components, and implemented unit tests to ensure our components work as expected.

In the next section, we'll dive deeper into props and explore more advanced TypeScript patterns for React components.
