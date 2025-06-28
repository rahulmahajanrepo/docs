# Props and TypeScript

In this section, we'll explore how to effectively use TypeScript with React props, covering various patterns and techniques for creating type-safe and reusable components.

## Typing Props in React Components

TypeScript enhances React by allowing you to define explicit types for component props, leading to better developer experience and fewer runtime errors.

### Basic Prop Types

```tsx
// Basic prop types example
interface UserProfileProps {
  name: string;                 // String prop
  age: number;                  // Number prop
  isActive: boolean;            // Boolean prop
  roles: string[];              // Array of strings
  address: {                    // Object prop
    street: string;
    city: string;
    zipCode: string;
  };
}

const UserProfile: React.FC<UserProfileProps> = ({ 
  name, 
  age, 
  isActive, 
  roles, 
  address 
}) => {
  return (
    <div>
      <h2>{name} ({age})</h2>
      <p>Status: {isActive ? 'Active' : 'Inactive'}</p>
      <p>
        Roles: {roles.join(', ')}
      </p>
      <address>
        {address.street}, {address.city}, {address.zipCode}
      </address>
    </div>
  );
};
```

## Required vs Optional Props

TypeScript allows you to distinguish between required and optional props, making your component API clear and explicit.

### Using Optional Props

```tsx
interface ButtonProps {
  label: string;          // Required prop
  onClick: () => void;    // Required prop
  variant?: string;       // Optional prop (notice the ?)
  disabled?: boolean;     // Optional prop
}

const Button: React.FC<ButtonProps> = ({ 
  label, 
  onClick, 
  variant = 'primary',   // Default value for optional prop
  disabled = false       // Default value for optional prop
}) => {
  return (
    <button
      onClick={onClick}
      className={`button ${variant}`}
      disabled={disabled}
    >
      {label}
    </button>
  );
};

// Usage examples:
// <Button label="Click me" onClick={() => console.log('Clicked!')} />
// <Button label="Submit" onClick={handleSubmit} variant="success" />
// <Button label="Delete" onClick={handleDelete} variant="danger" disabled={true} />
```

## Default Props

There are multiple ways to specify default props in TypeScript React components.

### Method 1: Default Parameter Values (Recommended)

```tsx
interface CounterProps {
  initialCount?: number;
  step?: number;
}

const Counter: React.FC<CounterProps> = ({
  initialCount = 0,  // Default parameter value
  step = 1           // Default parameter value
}) => {
  const [count, setCount] = React.useState(initialCount);
  
  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count - step)}>Decrease</button>
      <button onClick={() => setCount(count + step)}>Increase</button>
    </div>
  );
};
```

### Method 2: Explicit defaultProps (Legacy)

```tsx
interface ProgressBarProps {
  percent: number;
  width?: number;
  height?: number;
  color?: string;
}

const ProgressBar: React.FC<ProgressBarProps> = ({ 
  percent, 
  width, 
  height, 
  color 
}) => {
  return (
    <div 
      style={{ 
        width: `${width}px`,
        height: `${height}px`,
        backgroundColor: '#e0e0e0'
      }}
    >
      <div
        style={{
          width: `${percent}%`,
          height: '100%',
          backgroundColor: color
        }}
      />
    </div>
  );
};

ProgressBar.defaultProps = {
  width: 100,
  height: 10,
  color: 'blue'
};
```

## Children Props

React's `children` prop allows components to accept and render nested content, enabling component composition.

### Basic Children Example

```tsx
interface CardProps {
  title: string;
  children: React.ReactNode;  // Type for children
}

const Card: React.FC<CardProps> = ({ title, children }) => {
  return (
    <div className="card">
      <div className="card-header">
        <h2>{title}</h2>
      </div>
      <div className="card-body">
        {children}
      </div>
    </div>
  );
};

// Usage:
// <Card title="Welcome">
//   <p>This is some content inside the card.</p>
//   <button>Click me</button>
// </Card>
```

### Optional Children

```tsx
interface CollapsibleProps {
  header: React.ReactNode;
  children?: React.ReactNode;  // Optional children
  defaultExpanded?: boolean;
}

const Collapsible: React.FC<CollapsibleProps> = ({ 
  header, 
  children, 
  defaultExpanded = false 
}) => {
  const [isExpanded, setIsExpanded] = React.useState(defaultExpanded);
  
  return (
    <div className="collapsible">
      <div 
        className="collapsible-header"
        onClick={() => setIsExpanded(!isExpanded)}
      >
        {header}
        <span>{isExpanded ? '▲' : '▼'}</span>
      </div>
      
      {isExpanded && children && (
        <div className="collapsible-content">
          {children}
        </div>
      )}
    </div>
  );
};
```

## Advanced Prop Types

TypeScript offers advanced typing capabilities for more complex component interfaces.

### Union Types

```tsx
// Union type for component variants
type AlertVariant = 'info' | 'success' | 'warning' | 'error';

interface AlertProps {
  message: string;
  variant: AlertVariant;  // Must be one of the predefined values
  onClose?: () => void;
}

const Alert: React.FC<AlertProps> = ({ message, variant, onClose }) => {
  return (
    <div className={`alert alert-${variant}`}>
      <p>{message}</p>
      {onClose && (
        <button onClick={onClose} className="alert-close">
          ×
        </button>
      )}
    </div>
  );
};

// Usage:
// <Alert message="Operation successful" variant="success" />
// <Alert message="Something went wrong" variant="error" onClose={handleClose} />

// This would cause a TypeScript error:
// <Alert message="Hello" variant="primary" />
```

### Function Props with Parameters

```tsx
interface InputProps {
  value: string;
  onChange: (value: string) => void;  // Function that takes a string parameter
  onFocus?: () => void;               // Optional function with no parameters
  onBlur?: (event: React.FocusEvent<HTMLInputElement>) => void;  // With event parameter
}

const Input: React.FC<InputProps> = ({ value, onChange, onFocus, onBlur }) => {
  const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    onChange(e.target.value);
  };
  
  return (
    <input
      type="text"
      value={value}
      onChange={handleChange}
      onFocus={onFocus}
      onBlur={onBlur}
    />
  );
};
```

### Intersection Types

```tsx
// Base props that all form controls share
interface BaseFormControlProps {
  id: string;
  label: string;
  required?: boolean;
  disabled?: boolean;
}

// Specific props for text input
interface TextInputProps {
  value: string;
  onChange: (value: string) => void;
  placeholder?: string;
  type?: 'text' | 'email' | 'password';
}

// Combined props using intersection
type FormTextInputProps = BaseFormControlProps & TextInputProps;

const FormTextInput: React.FC<FormTextInputProps> = ({
  id,
  label,
  required = false,
  disabled = false,
  value,
  onChange,
  placeholder,
  type = 'text'
}) => {
  return (
    <div className="form-control">
      <label htmlFor={id}>
        {label}
        {required && <span className="required">*</span>}
      </label>
      <input
        id={id}
        type={type}
        value={value}
        placeholder={placeholder}
        disabled={disabled}
        required={required}
        onChange={(e) => onChange(e.target.value)}
      />
    </div>
  );
};
```

## Generic Components

Generic components allow you to create highly reusable components that work with different types while maintaining type safety.

### Simple Generic Component

```tsx
interface SelectProps<T> {
  options: T[];
  value: T | null;
  onChange: (value: T) => void;
  getLabel: (option: T) => string;
  getValue: (option: T) => string | number;
}

// Generic functional component
function Select<T>({ 
  options, 
  value, 
  onChange, 
  getLabel, 
  getValue 
}: SelectProps<T>) {
  const handleChange = (e: React.ChangeEvent<HTMLSelectElement>) => {
    const selectedOption = options.find(
      option => getValue(option).toString() === e.target.value
    );
    
    if (selectedOption) {
      onChange(selectedOption);
    }
  };
  
  return (
    <select 
      value={value ? getValue(value).toString() : ''}
      onChange={handleChange}
    >
      <option value="">-- Select --</option>
      {options.map(option => (
        <option 
          key={getValue(option).toString()} 
          value={getValue(option).toString()}
        >
          {getLabel(option)}
        </option>
      ))}
    </select>
  );
}

// Usage example:
interface User {
  id: number;
  name: string;
  email: string;
}

// In a component:
const users: User[] = [
  { id: 1, name: 'Alice', email: 'alice@example.com' },
  { id: 2, name: 'Bob', email: 'bob@example.com' },
  { id: 3, name: 'Charlie', email: 'charlie@example.com' },
];

const [selectedUser, setSelectedUser] = React.useState<User | null>(null);

// Using the generic component with User type
<Select<User>
  options={users}
  value={selectedUser}
  onChange={setSelectedUser}
  getLabel={(user) => user.name}
  getValue={(user) => user.id}
/>
```

### Generic List Component

```tsx
interface ListProps<T> {
  items: T[];
  renderItem: (item: T, index: number) => React.ReactNode;
  keyExtractor: (item: T, index: number) => string | number;
  emptyMessage?: string;
}

function List<T>({ 
  items, 
  renderItem, 
  keyExtractor, 
  emptyMessage = "No items to display" 
}: ListProps<T>) {
  if (items.length === 0) {
    return <p>{emptyMessage}</p>;
  }
  
  return (
    <ul>
      {items.map((item, index) => (
        <li key={keyExtractor(item, index)}>
          {renderItem(item, index)}
        </li>
      ))}
    </ul>
  );
}

// Usage example:
interface Product {
  id: string;
  name: string;
  price: number;
}

const products: Product[] = [
  { id: 'p1', name: 'Keyboard', price: 49.99 },
  { id: 'p2', name: 'Mouse', price: 29.99 },
  { id: 'p3', name: 'Monitor', price: 199.99 },
];

// Using the generic component with Product type
<List<Product>
  items={products}
  keyExtractor={(product) => product.id}
  renderItem={(product) => (
    <div>
      <strong>{product.name}</strong>
      <span>${product.price.toFixed(2)}</span>
    </div>
  )}
/>
```

## Example: Creating a Reusable Card Component

Let's build a versatile Card component with TypeScript:

```tsx
// src/components/Card/Card.tsx

import React from 'react';
import './Card.css';

export interface CardProps {
  /**
   * Card title
   */
  title?: React.ReactNode;
  
  /**
   * Card subtitle
   */
  subtitle?: React.ReactNode;
  
  /**
   * Card main content
   */
  children?: React.ReactNode;
  
  /**
   * Card footer content
   */
  footer?: React.ReactNode;
  
  /**
   * Width of the card
   */
  width?: number | string;
  
  /**
   * CSS class name for additional styling
   */
  className?: string;
  
  /**
   * Elevation level (shadow depth)
   */
  elevation?: 0 | 1 | 2 | 3 | 4;
  
  /**
   * Whether to show a border
   */
  bordered?: boolean;
  
  /**
   * Callback when the card is clicked
   */
  onClick?: (event: React.MouseEvent<HTMLDivElement>) => void;
}

export const Card: React.FC<CardProps> = ({
  title,
  subtitle,
  children,
  footer,
  width,
  className = '',
  elevation = 1,
  bordered = false,
  onClick,
}) => {
  const cardStyle: React.CSSProperties = {
    width: width,
  };
  
  const classNames = [
    'card',
    `card-elevation-${elevation}`,
    bordered ? 'card-bordered' : '',
    className,
    onClick ? 'card-clickable' : ''
  ].filter(Boolean).join(' ');
  
  return (
    <div 
      className={classNames} 
      style={cardStyle} 
      onClick={onClick}
      role={onClick ? 'button' : undefined}
      tabIndex={onClick ? 0 : undefined}
    >
      {(title || subtitle) && (
        <div className="card-header">
          {title && <div className="card-title">{title}</div>}
          {subtitle && <div className="card-subtitle">{subtitle}</div>}
        </div>
      )}
      
      {children && <div className="card-content">{children}</div>}
      
      {footer && <div className="card-footer">{footer}</div>}
    </div>
  );
};
```

### CSS for the Card Component

```css
/* src/components/Card/Card.css */
.card {
  background: white;
  border-radius: 8px;
  overflow: hidden;
  transition: box-shadow 0.3s ease, transform 0.2s ease;
  margin-bottom: 16px;
}

.card-elevation-0 {
  box-shadow: none;
}

.card-elevation-1 {
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.card-elevation-2 {
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

.card-elevation-3 {
  box-shadow: 0 6px 16px rgba(0, 0, 0, 0.1);
}

.card-elevation-4 {
  box-shadow: 0 12px 24px rgba(0, 0, 0, 0.1);
}

.card-bordered {
  border: 1px solid #e0e0e0;
}

.card-clickable {
  cursor: pointer;
}

.card-clickable:hover {
  transform: translateY(-2px);
}

.card-header {
  padding: 16px;
  border-bottom: 1px solid #f0f0f0;
}

.card-title {
  font-size: 18px;
  font-weight: 500;
  margin-bottom: 4px;
}

.card-subtitle {
  font-size: 14px;
  color: #666;
}

.card-content {
  padding: 16px;
}

.card-footer {
  padding: 12px 16px;
  border-top: 1px solid #f0f0f0;
  background-color: #fafafa;
}
```

### Card Component Index File

```tsx
// src/components/Card/index.ts
export * from './Card';
export { Card as default } from './Card';
```

## Unit Testing the Card Component

```tsx
// src/components/Card/Card.test.tsx

import React from 'react';
import { render, screen, fireEvent } from '@testing-library/react';
import { Card } from './Card';

describe('Card Component', () => {
  // Test 1: Renders children
  test('renders children correctly', () => {
    render(
      <Card>
        <p data-testid="card-content">Card Content</p>
      </Card>
    );
    
    expect(screen.getByTestId('card-content')).toBeInTheDocument();
    expect(screen.getByTestId('card-content')).toHaveTextContent('Card Content');
  });
  
  // Test 2: Renders title and subtitle
  test('renders title and subtitle', () => {
    render(
      <Card 
        title="Card Title" 
        subtitle="Card Subtitle"
      >
        Content
      </Card>
    );
    
    expect(screen.getByText('Card Title')).toBeInTheDocument();
    expect(screen.getByText('Card Subtitle')).toBeInTheDocument();
  });
  
  // Test 3: Renders footer
  test('renders footer when provided', () => {
    render(
      <Card 
        footer={<button>Action</button>}
      >
        Content
      </Card>
    );
    
    expect(screen.getByRole('button')).toBeInTheDocument();
    expect(screen.getByRole('button')).toHaveTextContent('Action');
  });
  
  // Test 4: Applies custom width
  test('applies custom width style', () => {
    render(
      <Card width={300}>Content</Card>
    );
    
    const cardElement = screen.getByText('Content').closest('.card');
    expect(cardElement).toHaveStyle({ width: 300 });
  });
  
  // Test 5: Applies proper elevation class
  test('applies correct elevation class', () => {
    const { rerender } = render(<Card elevation={2}>Content</Card>);
    
    let cardElement = screen.getByText('Content').closest('.card');
    expect(cardElement).toHaveClass('card-elevation-2');
    
    rerender(<Card elevation={4}>Content</Card>);
    cardElement = screen.getByText('Content').closest('.card');
    expect(cardElement).toHaveClass('card-elevation-4');
  });
  
  // Test 6: Applies bordered class
  test('applies bordered class when bordered is true', () => {
    render(<Card bordered>Content</Card>);
    
    const cardElement = screen.getByText('Content').closest('.card');
    expect(cardElement).toHaveClass('card-bordered');
  });
  
  // Test 7: Handles click events
  test('calls onClick handler when clicked', () => {
    const handleClick = jest.fn();
    render(<Card onClick={handleClick}>Clickable Card</Card>);
    
    const cardElement = screen.getByText('Clickable Card').closest('.card');
    fireEvent.click(cardElement!);
    
    expect(handleClick).toHaveBeenCalledTimes(1);
  });
  
  // Test 8: Is keyboard accessible when clickable
  test('is keyboard accessible when onClick is provided', () => {
    const handleClick = jest.fn();
    render(<Card onClick={handleClick}>Accessible Card</Card>);
    
    const cardElement = screen.getByText('Accessible Card').closest('.card');
    expect(cardElement).toHaveAttribute('tabIndex', '0');
    expect(cardElement).toHaveAttribute('role', 'button');
    
    // Test keyboard accessibility
    fireEvent.keyDown(cardElement!, { key: 'Enter' });
    expect(handleClick).toHaveBeenCalled();
  });
});
```

## Example Usage of the Card Component

```tsx
// Example usage of the Card component
import React from 'react';
import { Card } from './components/Card';

const ExamplePage: React.FC = () => {
  return (
    <div style={{ maxWidth: 800, margin: '0 auto', padding: 20 }}>
      {/* Basic card */}
      <Card title="Basic Card">
        <p>This is a basic card with just a title and content.</p>
      </Card>
      
      {/* Card with subtitle and footer */}
      <Card 
        title="Feature Card" 
        subtitle="A more advanced example" 
        footer={
          <div style={{ display: 'flex', justifyContent: 'flex-end' }}>
            <button>Learn More</button>
          </div>
        }
      >
        <p>This card demonstrates more features like a subtitle and footer.</p>
      </Card>
      
      {/* Different elevation and bordered */}
      <Card 
        title="Elevated Card" 
        elevation={3}
        bordered
      >
        <p>This card has a higher elevation (shadow) and a border.</p>
      </Card>
      
      {/* Clickable card */}
      <Card 
        title="Clickable Card"
        onClick={() => alert('Card clicked!')}
      >
        <p>Click on this card to trigger an action.</p>
      </Card>
      
      {/* Custom width */}
      <Card 
        title="Custom Width"
        width="50%"
      >
        <p>This card has a custom width set to 50%.</p>
      </Card>
      
      {/* Card with custom components inside */}
      <Card title="User Profile">
        <div style={{ display: 'flex', alignItems: 'center' }}>
          <div style={{ 
            width: 80, 
            height: 80, 
            borderRadius: '50%', 
            backgroundColor: '#e0e0e0', 
            marginRight: 16 
          }} />
          <div>
            <h3 style={{ margin: '0 0 8px' }}>Jane Doe</h3>
            <p style={{ margin: 0 }}>Software Engineer</p>
            <p style={{ margin: '4px 0 0', color: '#666' }}>
              jane.doe@example.com
            </p>
          </div>
        </div>
      </Card>
    </div>
  );
};
```

## Conclusion

TypeScript provides powerful tools for defining prop types in React components, helping you create more maintainable and robust applications. By leveraging interfaces, generics, and other TypeScript features, you can create reusable components with clear, self-documenting APIs.

In this section, we explored:

- Basic prop typing
- Required vs optional props
- Default props
- Children props
- Advanced type patterns like union and intersection types
- Generic components
- A practical example with a Card component

Using these techniques will help you build a more type-safe React application, catching errors during development rather than at runtime.
