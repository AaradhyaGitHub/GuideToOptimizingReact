# React Component Rendering: A Detailed Explanation

## Starting Point: App.jsx

React starts rendering component functions, beginning with `App.jsx`. Why App? It's the first and only file being highlighted to run in the `main.jsx`. This is our entry point into the React application.

## Initial Setup Phase

The functions and state are all set, but importantly, they're not executed yet - they're just being set up. This is a crucial distinction in understanding React's rendering process.

```jsx
// Setting up state and functions, not executing them yet
function App() {
  const [count, setCount] = useState(0);
  // ... more setup code
}
```

## Component Types and Distinction

There's a mix of native and custom Components in React applications. They're typically distinguished by their first letter:
- Native components: Start with lowercase (e.g., `div`, `p`, `span`)
- Custom components: Start with uppercase (e.g., `Header`, `Counter`)

## Custom Component Rendering Flow

When React encounters a custom component like `<Header />`, it:
1. Jumps to the Header file
2. Runs that file's code
3. The rest of the code in App won't render until Header completes its execution

```jsx
// Example of component flow
<App>
  <Header /> {/* React processes this completely first */}
  {/* Other components wait */}
</App>
```

After JSX of Header is returned by React, this part of the component tree is complete. At the top level, we have App → Header. Since Header doesn't have any children, it marks the end of that branch.

## Counter Component Branch

React also processes another branch with the Counter Component. This Component receives a prop called `initialCount`:

```jsx
<Counter initialCount={someValue} />
```

## Props Flow Understanding

The flow of props follows this pattern:
1. Component receives prop name
2. Prop name receives value
3. Value is passed to prop which is passed to Component

Back at Counter, the same principle applies - everything gets created but NOT executed initially.

## Deeper Component Tree

The Counter component's JSX has more complex structure with three children:
1. IconButton
2. CounterOutput
3. IconButton

The two IconButton components each render out an Icon child, extending that part of the tree further. The CounterOutput doesn't render any custom Components, so it stops there.

## Component Tree Visualization

```
App
├── Header (no children)
└── Counter
    ├── IconButton
    │   └── Icon
    ├── CounterOutput (no children)
    └── IconButton
        └── Icon
```

Understanding that React builds this tree of components is crucial for:
- Debugging rendering issues
- Understanding data flow
- Optimizing component performance
- Managing component lifecycles

This hierarchical structure ensures organized rendering and maintainable code structure. React processes each branch completely before moving to siblings, ensuring predictable rendering patterns.

Remember: The key is understanding that components are set up first, then executed in a specific order based on their position in the component tree.

## Optimizing React with that understanding of Component and Component execution? 
Our Demo App currently has a place where we can optimize 
We have a input field in App.jsx and on every key stroke, we are updating the state 
This re-renders App so every Component that's getting rendered by App.

## Solution 1: Memo Function 
- React has a built in function that prevents unnecessary execution of components
- Import `memo` function and wrap the entire function in in it and store it in a const and return that const 
- What it does: It looks at the props in the component function and whenever the Component fucntion would re-render again from the prop change, memo function looks at the old prop and the new prop. 
- If the prop values are exactly same, (in memory), the component function does not execute again.
- Makes sense. Why re-ender the Component if the state didn't change. But if the internal state changes, it triggers the component function. 
- Memo should only be wrapped as high on the component tree as possible
- If wrapped around all function, checking the props draws out more unnecary performance price. Use it with care, not necessary for smaller parts, only use when absolutely necessary 

## Solution 1: Even more powerful method -> Component Composition 
- Currently, the input changes state on the App component on every key stroke 


