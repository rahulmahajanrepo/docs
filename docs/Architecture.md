# Form Engine Architecture Document

## Overview

The Form Engine is a React-based application designed to allow users to create, preview, and manage dynamic forms through a JSON-based configuration. The application is structured around a playground interface with four main tabs:

1. **Form Builder**: A drag-and-drop interface for designing form structures
2. **Preview**: A live preview of the form based on the current configuration
3. **JSON Viewer**: A view and editor for the form configuration in JSON format
4. **Dependency Graph**: A visual representation of form section dependencies

## Core Technologies

- **React 18.2.0**: For building the UI components
- **TypeScript**: For type safety and improved developer experience
- **Recoil**: For state management
- **Material UI**: For consistent, modern UI components
- **@dnd-kit**: For drag-and-drop functionality

## Architecture Principles

1. **Modularity**: Components are organized into focused, single-responsibility units
2. **Separation of Concerns**: Clear boundaries between state, UI, and business logic
3. **Type Safety**: Comprehensive TypeScript interfaces for all data structures
4. **Extensibility**: Architecture allows for easy addition of new field types and adapters
5. **Testability**: Components designed to be easily testable

## Domain-Specific Language (DSL)

The form engine is built around a JSON-based Domain-Specific Language that defines form structure, fields, validations, and visibility conditions. The DSL is represented through TypeScript interfaces to ensure type safety:

```typescript
export interface FormConfig {
  adapterType: "mui" | "vanilla";
  sections: Section[];
}

export interface Section {
  id: string;
  objectName: string;
  title: string;
  integral: boolean;
  fields: FormField[];
  subsections: Section[];
  visibilityCondition?: VisibilityCondition;
}

export type FormField = TextField | NumericField | DateField | ...;
```

## Module Architecture

### 1. Form Builder Module

The Form Builder uses a three-panel layout:
- **Fields Area**: Shows available field types for dragging
- **Form Canvas**: Displays the form structure being built
- **Field Properties**: Edits properties of the selected field or section

Key components:
- `FormBuilder.tsx`: Main container with resizable panels
- `FieldsArea.tsx`: Displays field types available for dragging
- `FormCanvas.tsx`: Shows the form structure with sections and fields
- `Section.tsx`: Renders individual sections and their fields
- `Field.tsx`: Renders individual field items
- `FieldProperties.tsx`: Edits properties of selected items

### 2. Form Renderer Module

The Form Renderer displays the form to users and implements the adapter pattern to support multiple UI frameworks:

- **Adapter Interface**: `FormAdapter` defines the contract for renderers
- **Adapters**: `MuiAdapter` and `VanillaAdapter` provide concrete implementations
- **Factory**: `AdapterFactory` creates appropriate adapters based on configuration

Key components:
- `FormRenderer.tsx`: Main renderer with navigation between sections
- `FormSection.tsx`: Renders individual sections and their fields
- `adapters/FormAdapter.ts`: Interface for form field adapters
- `adapters/MuiAdapter.tsx`: Material UI implementation
- `adapters/VanillaAdapter.tsx`: Vanilla HTML implementation

### 3. State Management

Recoil is used for state management, with atoms and selectors organized by module:

- **Form Builder State**:
  - `formConfigState`: Main atom for the form configuration
  - `selectedItemIdState`: Tracks the currently selected item
  - `selectedItemSelector`: Retrieves the selected item from the form config

- **Form Renderer State**:
  - `fieldValueState`: Atom family for individual field values
  - `formRendererState`: Selector for the complete form data
  - `visibleSectionsState`: Calculates which sections should be visible

### 4. Validation Architecture

Validation occurs at multiple levels:
- **Field Level**: Validates individual field inputs
- **Section Level**: Validates entire sections
- **Form Level**: Validates the entire form
- **Dependency Level**: Validates that section dependencies are correct

## Design Patterns

### 1. Adapter Pattern

The Adapter Pattern is used to support multiple UI frameworks for rendering forms:

```typescript
// Interface (contract)
export interface FormAdapter {
  renderField(field: FormField, value: any, onChange: Function, error?: string): JSX.Element;
}

// Concrete implementations
export class MuiAdapter implements FormAdapter { ... }
export class VanillaAdapter implements FormAdapter { ... }
```

Benefits:
- Decouples form definition from rendering implementation
- Allows for multiple UI frameworks with the same form configuration
- Simplifies adding new UI frameworks in the future

### 2. Factory Pattern

The Factory Pattern creates appropriate adapters based on configuration:

```typescript
export class AdapterFactory {
  static createAdapter(type: string): FormAdapter {
    switch (type) {
      case 'mui': return new MuiAdapter();
      case 'vanilla': return new VanillaAdapter();
      default: return new MuiAdapter();
    }
  }
}
```

Benefits:
- Centralizes adapter creation logic
- Makes it easy to add new adapter types
- Provides a single point for adapter configuration

### 3. Directed Acyclic Graph (DAG)

Form section dependencies are modeled as a Directed Acyclic Graph:
- Sections can depend on earlier sections (directed edges)
- No circular dependencies are allowed (acyclic)

This approach:
- Ensures logical form flow
- Prevents circular reference issues
- Provides clear visualization of dependencies

## Benefits of the Architecture

1. **Flexibility**: The form engine can render forms with different UI frameworks without changing the form definition.

2. **Maintainability**: Modular design and separation of concerns make the codebase easier to maintain and extend.

3. **Performance**: State management with Recoil provides performance optimizations through fine-grained updates.

4. **User Experience**: The drag-and-drop interface and live preview make form creation intuitive and efficient.

5. **Type Safety**: TypeScript interfaces ensure consistency and prevent common errors.

## Conclusion

The Form Engine architecture provides a robust foundation for creating, managing, and rendering dynamic forms. Its modular design, use of design patterns, and type safety make it a maintainable and extensible solution. The separation between form definition and rendering allows for multiple UI implementations while keeping the form logic consistent.
