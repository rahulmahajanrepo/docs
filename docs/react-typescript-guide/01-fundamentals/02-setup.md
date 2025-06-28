# Setting Up a React TypeScript Project

This guide covers different approaches to setting up a React project with TypeScript, providing you with a solid foundation for development.

## Create React App with TypeScript

Create React App (CRA) offers a comfortable starting point for React beginners and experienced developers alike. It sets up a new React project with a good default configuration.

### Setting Up a New Project

```bash
# Create a new React project with TypeScript template
npx create-react-app my-app --template typescript

# Navigate to project directory
cd my-app

# Start the development server
npm start
```

### Project Structure

After initialization, your CRA TypeScript project will have the following structure:

```
my-app/
├── node_modules/
├── public/
│   ├── favicon.ico
│   ├── index.html
│   └── manifest.json
├── src/
│   ├── App.css
│   ├── App.test.tsx
│   ├── App.tsx
│   ├── index.css
│   ├── index.tsx
│   ├── logo.svg
│   ├── react-app-env.d.ts
│   ├── reportWebVitals.ts
│   └── setupTests.ts
├── .gitignore
├── package.json
├── README.md
├── tsconfig.json
└── package-lock.json
```

### Key TypeScript Configuration

The `tsconfig.json` file contains TypeScript compiler options. Here's a breakdown of important settings:

```json
{
  "compilerOptions": {
    "target": "es5",          // Compile to ES5 for broader browser compatibility
    "lib": [                  // Include these libraries in compilation
      "dom",
      "dom.iterable",
      "esnext"
    ],
    "allowJs": true,          // Allow JavaScript files to be compiled
    "skipLibCheck": true,     // Skip type checking of all declaration files
    "esModuleInterop": true,  // Enable interoperability between CommonJS and ES Modules
    "allowSyntheticDefaultImports": true,  // Allow default imports from modules with no default export
    "strict": true,           // Enable all strict type-checking options
    "forceConsistentCasingInFileNames": true,  // Ensure consistent file naming
    "noFallthroughCasesInSwitch": true,  // Report errors for fallthrough cases in switch statements
    "module": "esnext",       // Use ESNext module system
    "moduleResolution": "node",  // Use Node.js module resolution strategy
    "resolveJsonModule": true,  // Allow importing JSON modules
    "isolatedModules": true,   // Warn if code can't be correctly analyzed in isolation
    "noEmit": true,           // Do not emit outputs (Babel will handle this)
    "jsx": "react-jsx"        // Support JSX for React 17+ (no need to import React)
  },
  "include": [
    "src"                     // Which files to include in compilation
  ]
}
```

### Adding Type Checking to Scripts

Enhance your development workflow by adding type checking scripts to `package.json`:

```json
{
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject",
    "typecheck": "tsc --noEmit",
    "typecheck:watch": "tsc --noEmit --watch"
  }
}
```

## Vite with TypeScript

Vite offers significantly faster development and build times compared to CRA.

### Setting Up a New Project

```bash
# Create a new Vite project with React and TypeScript
npm create vite@latest my-vite-app -- --template react-ts

# Navigate to project directory
cd my-vite-app

# Install dependencies
npm install

# Start the development server
npm run dev
```

### Project Structure

A Vite project with TypeScript has this initial structure:

```
my-vite-app/
├── node_modules/
├── public/
│   └── favicon.ico
├── src/
│   ├── App.css
│   ├── App.tsx
│   ├── index.css
│   ├── main.tsx
│   └── vite-env.d.ts
├── .gitignore
├── index.html
├── package.json
├── tsconfig.json
├── tsconfig.node.json
└── vite.config.ts
```

### TypeScript Configuration

Vite uses two TypeScript configuration files:

**tsconfig.json** (for application code):
```json
{
  "compilerOptions": {
    "target": "ESNext",
    "useDefineForClassFields": true,
    "lib": ["DOM", "DOM.Iterable", "ESNext"],
    "allowJs": false,
    "skipLibCheck": true,
    "esModuleInterop": false,
    "allowSyntheticDefaultImports": true,
    "strict": true,
    "forceConsistentCasingInFileNames": true,
    "module": "ESNext",
    "moduleResolution": "Node",
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true,
    "jsx": "react-jsx"
  },
  "include": ["src"],
  "references": [{ "path": "./tsconfig.node.json" }]
}
```

**tsconfig.node.json** (for Vite configuration):
```json
{
  "compilerOptions": {
    "composite": true,
    "module": "ESNext",
    "moduleResolution": "Node",
    "allowSyntheticDefaultImports": true
  },
  "include": ["vite.config.ts"]
}
```

### Vite Configuration

The `vite.config.ts` file configures your project:

```typescript
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [react()],
  resolve: {
    alias: {
      '@': '/src', // Set up path aliases
    },
  },
});
```

## Customizing Your Setup

### Path Aliases

Path aliases make imports cleaner and more manageable:

**For CRA (tsconfig.json):**
```json
{
  "compilerOptions": {
    // ...other settings
    "baseUrl": "src",
    "paths": {
      "@components/*": ["components/*"],
      "@hooks/*": ["hooks/*"],
      "@utils/*": ["utils/*"]
    }
  }
}
```

**For Vite (vite.config.ts):**
```typescript
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
import path from 'path';

export default defineConfig({
  plugins: [react()],
  resolve: {
    alias: {
      '@': path.resolve(__dirname, './src'),
      '@components': path.resolve(__dirname, './src/components'),
      '@hooks': path.resolve(__dirname, './src/hooks'),
      '@utils': path.resolve(__dirname, './src/utils')
    },
  },
});
```

### Adding ESLint and Prettier

Enhance code quality with ESLint and Prettier:

```bash
# For CRA project
npm install --save-dev eslint-config-prettier prettier

# For Vite project
npm install --save-dev eslint eslint-plugin-react eslint-plugin-react-hooks @typescript-eslint/eslint-plugin @typescript-eslint/parser eslint-config-prettier prettier
```

**ESLint Configuration (.eslintrc.json):**
```json
{
  "extends": [
    "react-app", 
    "react-app/jest", 
    "prettier"
  ],
  "rules": {
    "react/jsx-uses-react": "off",
    "react/react-in-jsx-scope": "off"
  }
}
```

**Prettier Configuration (.prettierrc):**
```json
{
  "semi": true,
  "tabWidth": 2,
  "printWidth": 100,
  "singleQuote": true,
  "trailingComma": "es5",
  "jsxBracketSameLine": false
}
```

## Testing the Project Setup

Let's write some tests to verify our project setup is working correctly:

```tsx
// src/setupTests.ts
import '@testing-library/jest-dom';
```

```tsx
// src/App.test.tsx
import { render, screen } from '@testing-library/react';
import App from './App';

describe('App Component', () => {
  test('renders without crashing', () => {
    render(<App />);
    expect(screen.getByRole('heading')).toBeInTheDocument();
  });
  
  test('contains the welcome text', () => {
    render(<App />);
    const welcomeElement = screen.getByText(/Welcome to React/i);
    expect(welcomeElement).toBeInTheDocument();
  });
});
```

```tsx
// src/App.tsx (modified for test)
import React from 'react';
import './App.css';

function App() {
  return (
    <div className="App">
      <header className="App-header">
        <h1>Welcome to React</h1>
        <p>
          Edit <code>src/App.tsx</code> and save to reload.
        </p>
      </header>
    </div>
  );
}

export default App;
```

Run the tests with:

```bash
npm test
```

### Creating a Custom Component and Testing It

Let's create a simple component to further validate our setup:

```tsx
// src/components/Greeting.tsx
import React from 'react';

interface GreetingProps {
  name: string;
}

export const Greeting: React.FC<GreetingProps> = ({ name }) => {
  return <h2 data-testid="greeting">Hello, {name}!</h2>;
};
```

```tsx
// src/components/Greeting.test.tsx
import { render, screen } from '@testing-library/react';
import { Greeting } from './Greeting';

describe('Greeting Component', () => {
  test('renders greeting with name', () => {
    render(<Greeting name="TypeScript" />);
    const greetingElement = screen.getByTestId('greeting');
    expect(greetingElement).toHaveTextContent('Hello, TypeScript!');
  });
});
```

## Project Organization Best Practices

A well-organized project structure is crucial for maintainability:

```
src/
├── assets/             # Static files like images, fonts, etc.
├── components/         # Shared components
│   ├── Button/
│   │   ├── Button.tsx
│   │   ├── Button.test.tsx
│   │   ├── Button.module.css
│   │   └── index.ts
│   └── ...
├── hooks/              # Custom React hooks
├── pages/              # Page components
│   ├── Home/
│   ├── About/
│   └── ...
├── services/           # API services
├── types/              # TypeScript type definitions
├── utils/              # Utility functions
├── App.tsx             # Root component
└── index.tsx           # Entry point
```

This organization keeps related files together and makes it easier to find what you're looking for.

## Conclusion

You now have a solid foundation for starting a React project with TypeScript. In the next section, we'll dive deeper into creating components with JSX and TypeScript.
