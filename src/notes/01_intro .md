# React Component Rendering: A Clear Guide

Let me explain how React renders components, making it easy to understand the flow and hierarchy.

## Initial Rendering Process

React begins its rendering journey with `App.jsx`. Why? Because it's specifically designated as the entry point in `main.jsx`.

### Setting Up vs Executing

```jsx
// Components and state are initially set up, not executed
function App() {
  const [count, setCount] = useState(0);
  // ... more setup
}
```

## Component Types

React works with two types of components:

**Native Components**
- Start with lowercase letters (e.g., `div`, `span`)
- Built into React

**Custom Components**
- Start with uppercase letters (e.g., `Header`, `Counter`)
- Created by developers

## Component Tree Building

### Header Component Example
```jsx
<App>
  <Header /> {/* React jumps to Header file */}
  {/* App pauses until Header completes */}
</App>
```

### Counter Component Flow
```jsx
<App>
  <Counter initialCount={count}>
    <IconButton><Icon /></IconButton>
    <CounterOutput />
    <IconButton><Icon /></IconButton>
  </Counter>
</App>
```

## Props Flow

1. Component receives prop name
2. Prop name receives value
3. Value flows down to component

For example:
```jsx
// Prop passing
<Counter initialCount={count} />
// Counter receives and uses the prop
function Counter({ initialCount }) {
  // ... component logic
}
```

## Component Tree Visualization

```
App
├── Header
└── Counter
    ├── IconButton
    │   └── Icon
    ├── CounterOutput
    └── IconButton
        └── Icon
```

Understanding this tree structure is fundamental to working with React effectively. Each component waits for its children to complete rendering before finalizing its own render process.

This hierarchical rendering ensures that your application's UI is built consistently and predictably, with data flowing from parent to child components through props.