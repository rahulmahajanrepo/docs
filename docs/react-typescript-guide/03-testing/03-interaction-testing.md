# Testing User Interactions

Testing user interactions is crucial for ensuring that your React components behave correctly when users interact with them. This section covers how to test user interactions using React Testing Library and TypeScript.

## Simulating User Events

React Testing Library provides utilities for simulating user events, such as clicks, typing, and form submissions. The `userEvent` library offers more realistic event simulation compared to `fireEvent`.

### Basic User Event Simulation

Let's start with a simple example of simulating a button click:

```tsx
// src/components/Counter/Counter.tsx
import React, { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}

export default Counter;
```

```tsx
// src/components/Counter/Counter.test.tsx
import React from 'react';
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import Counter from './Counter';

describe('Counter component', () => {
  test('increments count when button is clicked', async () => {
    render(<Counter />);
    const user = userEvent.setup();

    // Initial state check
    expect(screen.getByText('Count: 0')).toBeInTheDocument();

    // Simulate button click
    await user.click(screen.getByText('Increment'));

    // Check if count incremented
    expect(screen.getByText('Count: 1')).toBeInTheDocument();
  });
});
```

## Testing Form Interactions

Forms are a common use case for user interaction testing. Let's create a `LoginForm` component and test its interactions.

```tsx
// src/components/LoginForm/LoginForm.tsx
import React, { useState } from 'react';

interface LoginFormProps {
  onSubmit: (username: string, password: string) => void;
}

function LoginForm({ onSubmit }: LoginFormProps) {
  const [username, setUsername] = useState('');
  const [password, setPassword] = useState('');
  const [errors, setErrors] = useState<{ username?: string; password?: string }>({});

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    const newErrors: { username?: string; password?: string } = {};

    if (!username) {
      newErrors.username = 'Username is required';
    }

    if (!password) {
      newErrors.password = 'Password is required';
    }

    if (Object.keys(newErrors).length > 0) {
      setErrors(newErrors);
      return;
    }

    onSubmit(username, password);
  };

  return (
    <form onSubmit={handleSubmit}>
      <div>
        <label htmlFor="username">Username</label>
        <input
          id="username"
          type="text"
          value={username}
          onChange={(e) => setUsername(e.target.value)}
        />
        {errors.username && <p>{errors.username}</p>}
      </div>
      <div>
        <label htmlFor="password">Password</label>
        <input
          id="password"
          type="password"
          value={password}
          onChange={(e) => setPassword(e.target.value)}
        />
        {errors.password && <p>{errors.password}</p>}
      </div>
      <button type="submit">Log In</button>
    </form>
  );
}

export default LoginForm;
```

```tsx
// src/components/LoginForm/LoginForm.test.tsx
import React from 'react';
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import LoginForm from './LoginForm';

describe('LoginForm component', () => {
  test('displays validation errors when fields are empty', async () => {
    const user = userEvent.setup();
    const handleSubmit = jest.fn();

    render(<LoginForm onSubmit={handleSubmit} />);

    // Try to submit an empty form
    await user.click(screen.getByRole('button', { name: /log in/i }));

    // Check for validation errors
    expect(screen.getByText('Username is required')).toBeInTheDocument();
    expect(screen.getByText('Password is required')).toBeInTheDocument();
    expect(handleSubmit).not.toHaveBeenCalled();
  });

  test('calls onSubmit with username and password when form is valid', async () => {
    const user = userEvent.setup();
    const handleSubmit = jest.fn();

    render(<LoginForm onSubmit={handleSubmit} />);

    // Fill out the form
    await user.type(screen.getByLabelText(/username/i), 'testuser');
    await user.type(screen.getByLabelText(/password/i), 'password123');

    // Submit the form
    await user.click(screen.getByRole('button', { name: /log in/i }));

    // Check if onSubmit was called with correct values
    expect(handleSubmit).toHaveBeenCalledWith('testuser', 'password123');
  });

  test('clears errors once fields are valid', async () => {
    const user = userEvent.setup();
    const handleSubmit = jest.fn();
    
    render(<LoginForm onSubmit={handleSubmit} />);
    
    // Try to submit an empty form
    await user.click(screen.getByRole('button', { name: /log in/i }));
    
    // Check for validation errors
    expect(screen.getByText('Username is required')).toBeInTheDocument();
    
    // Fill out the username field
    await user.type(screen.getByLabelText(/username/i), 'testuser');
    
    // Error for username should be removed
    expect(screen.queryByText('Username is required')).not.toBeInTheDocument();
    
    // Password field still has an error
    expect(screen.getByText('Password is required')).toBeInTheDocument();
  });
});
```

## Testing Custom Events

Sometimes, you need to test custom events or more complex interactions. Let's create a `Slider` component and test its interactions.

```tsx
// src/components/Slider/Slider.tsx
import React, { useState } from 'react';

interface SliderProps {
  min: number;
  max: number;
  step: number;
  onChange: (value: number) => void;
}

function Slider({ min, max, step, onChange }: SliderProps) {
  const [value, setValue] = useState(min);

  const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    const newValue = Number(e.target.value);
    setValue(newValue);
    onChange(newValue);
  };

  return (
    <div>
      <input
        type="range"
        min={min}
        max={max}
        step={step}
        value={value}
        onChange={handleChange}
      />
      <p>Value: {value}</p>
    </div>
  );
}

export default Slider;
```

```tsx
// src/components/Slider/Slider.test.tsx
import React from 'react';
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import Slider from './Slider';

describe('Slider component', () => {
  test('renders with initial value', () => {
    render(<Slider min={0} max={100} step={1} onChange={jest.fn()} />);
    expect(screen.getByRole('slider')).toHaveValue('0');
  });

  test('calls onChange with new value when slider is moved', async () => {
    const user = userEvent.setup();
    const handleChange = jest.fn();

    render(<Slider min={0} max={100} step={1} onChange={handleChange} />);

    // Move the slider
    await user.type(screen.getByRole('slider'), '{arrowright}');

    // Check if onChange was called with correct value
    expect(handleChange).toHaveBeenCalledWith(1);
  });

  test('updates value display when slider is moved', async () => {
    const user = userEvent.setup();
    render(<Slider min={0} max={100} step={1} onChange={jest.fn()} />);

    // Move the slider
    await user.type(screen.getByRole('slider'), '{arrowright}');

    // Check if value display updated
    expect(screen.getByText('Value: 1')).toBeInTheDocument();
  });
});
```

## Testing Drag and Drop

Testing drag-and-drop interactions can be more complex. Let's create a `DraggableList` component and test its interactions.

```tsx
// src/components/DraggableList/DraggableList.tsx
import React, { useState } from 'react';
import { DragDropContext, Droppable, Draggable, DropResult } from 'react-beautiful-dnd';

interface DraggableListProps {
  items: string[];
  onDragEnd: (items: string[]) => void;
}

function DraggableList({ items, onDragEnd }: DraggableListProps) {
  const [currentItems, setCurrentItems] = useState(items);

  const handleDragEnd = (result: DropResult) => {
    if (!result.destination) {
      return;
    }

    const newItems = Array.from(currentItems);
    const [movedItem] = newItems.splice(result.source.index, 1);
    newItems.splice(result.destination.index, 0, movedItem);

    setCurrentItems(newItems);
    onDragEnd(newItems);
  };

  return (
    <DragDropContext onDragEnd={handleDragEnd}>
      <Droppable droppableId="droppable">
        {(provided) => (
          <ul {...provided.droppableProps} ref={provided.innerRef}>
            {currentItems.map((item, index) => (
              <Draggable key={item} draggableId={item} index={index}>
                {(provided) => (
                  <li
                    ref={provided.innerRef}
                    {...provided.draggableProps}
                    {...provided.dragHandleProps}
                  >
                    {item}
                  </li>
                )}
              </Draggable>
            ))}
            {provided.placeholder}
          </ul>
        )}
      </Droppable>
    </DragDropContext>
  );
}

export default DraggableList;
```

```tsx
// src/components/DraggableList/DraggableList.test.tsx
import React from 'react';
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import DraggableList from './DraggableList';

describe('DraggableList component', () => {
  const items = ['Item 1', 'Item 2', 'Item 3'];

  test('renders with initial items', () => {
    render(<DraggableList items={items} onDragEnd={jest.fn()} />);
    items.forEach((item) => {
      expect(screen.getByText(item)).toBeInTheDocument();
    });
  });

  test('calls onDragEnd with new order when item is dragged', async () => {
    const user = userEvent.setup();
    const handleDragEnd = jest.fn();

    render(<DraggableList items={items} onDragEnd={handleDragEnd} />);

    // Simulate drag and drop
    const [item1, item2] = screen.getAllByRole('listitem');
    await user.dragAndDrop(item1, item2);

    // Check if onDragEnd was called with new order
    expect(handleDragEnd).toHaveBeenCalledWith(['Item 2', 'Item 1', 'Item 3']);
  });
});
```

## Best Practices for Interaction Testing

1. **Test from the user's perspective**: Focus on what the user sees and does, not implementation details.
2. **Use `userEvent` for realistic interactions**: It simulates user interactions more accurately than `fireEvent`.
3. **Test all interaction paths**: Ensure you test all possible user interactions, including edge cases.
4. **Mock external dependencies**: Stub out API calls, browser APIs, etc., to keep tests fast and reliable.
5. **Keep tests isolated**: Ensure each test is independent and doesn't rely on the state of other tests.
6. **Use `screen` queries**: Prefer `screen.getByRole()` over destructuring queries from render.
7. **Test accessibility**: Ensure your components are accessible and testable by querying elements by their roles, labels, and other accessible attributes.

## Conclusion

Testing user interactions is essential for ensuring that your React components behave correctly when users interact with them. By using React Testing Library and `userEvent`, you can create realistic and reliable tests for your components.

In this section, we've covered:
- Simulating user events with `userEvent`
- Testing form interactions
- Testing custom events and complex interactions
- Testing drag-and-drop interactions
- Best practices for interaction testing

In the next section, we'll explore testing custom hooks, focusing on how to test hooks in isolation and within components.