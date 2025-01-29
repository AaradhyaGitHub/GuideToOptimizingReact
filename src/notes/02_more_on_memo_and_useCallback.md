## **More on using `memo`**  

Currently, **incrementing the counter re-executes a lot of components**, including:  
✅ `IconButton`  
✅ `MinusIcon`  
✅ `CounterOutput`  
✅ `IconButton` (again)  
✅ `PlusIcon`  

Now, **`CounterOutput` re-executing makes sense** because we’re incrementing the counter, so the UI should reflect that change. No surprises there.  

**BUT!** The **Increment** and **Decrement** buttons don’t visually change.  
- They still say "Increment" and "Decrement."  
- Their icons don’t change.  
- Their actual function still works, but that function shouldn't force a re-render.  

So, why are they still re-rendering? 🤔  

### **Using `memo` to optimize re-renders**  
We can wrap `IconButton` with `memo`, which ensures it **doesn’t re-render unnecessarily** when its props don’t change.  
- Since `IconButton` renders an **icon component**, preventing `IconButton` from re-rendering also prevents its icon from re-rendering.  
- **This should, in theory, stop unnecessary executions of both components.**  

---

### **🚨 Before (No `memo`)**
```jsx
import { log } from "../../log.js";

export default function IconButton({ children, icon, ...props }) {
  log("<IconButton /> rendered", 2);

  const Icon = icon;
  return (
    <button {...props} className="button">
      <Icon className="button-icon" />
      <span className="button-text">{children}</span>
    </button>
  );
}
```
---

### **✅ After (With `memo`)**
```jsx
import { memo } from "react";
import { log } from "../../log.js";

const IconButton = memo(function IconButton({ children, icon, ...props }) {
  log("<IconButton /> rendered", 2);

  const Icon = icon;
  return (
    <button {...props} className="button">
      <Icon className="button-icon" />
      <span className="button-text">{children}</span>
    </button>
  );
});

export default IconButton;
```
---

### **Wait… `memo` still isn’t working? What’s going on?!** 🤨  

Even after wrapping `IconButton` with `memo`, it **still re-renders unnecessarily.** What gives?  

The issue **must be with the props** being passed to `IconButton`. Let’s analyze:  

- **`children` prop** → Used inside a `<span>`.  
  ```jsx
  <IconButton icon={MinusIcon} onClick={handleDecrement}>
    Decrement
  </IconButton>
  ```
  - The text `"Decrement"` doesn’t change when we click the button. ✅  
  - Since `children` is static, it **shouldn’t be causing re-renders**.  

- **`icon` prop** → Receives a pointer to a component.  
  ```jsx
  const Icon = icon;
  ```
  - This is just a reference to an existing component (`MinusIcon` or `PlusIcon`).  
  - Again, nothing is dynamically changing here. ✅  

So if `children` and `icon` aren’t causing unnecessary re-renders, **what’s left?**  

### **The Real Culprit: `onClick` Prop 🎯**  
Looking at the parent component (`Counter.jsx`), `onClick` is being set to `handleDecrement` and `handleIncrement`:  

```jsx
<IconButton icon={MinusIcon} onClick={handleDecrement}>
<IconButton icon={PlusIcon} onClick={handleIncrement}>
```

At first glance, these functions **look like they don’t change**, but there’s a hidden issue! 🚨  

---

### **🔍 The Hidden Problem: Function Recreation**  
Inside `Counter.jsx`, the event handlers are declared **inside the component function**:  

```jsx
export default function Counter({ initialCount }) {
  log("<Counter /> rendered", 1);
  
  const [counter, setCounter] = useState(initialCount);

  function handleDecrement() {
    setCounter((prevCounter) => prevCounter - 1);
  }

  function handleIncrement() {
    setCounter((prevCounter) => prevCounter + 1);
  }
}
```

Here’s the catch:  
- **Each time `Counter` re-renders, these functions are recreated in memory.**  
- Even though they do the exact same thing, **they’re technically new function objects**.  
- Since they are passed as props to `IconButton`, React sees them as **new values** and forces a re-render.  

---

### **🚀 The Fix: `useCallback`**  
To prevent function recreation, we use `useCallback`, which **memoizes the function reference** so it stays the same between renders:  

```jsx
import { useCallback } from "react";

const handleDecrement = useCallback(() => {
  setCounter((prevCounter) => prevCounter - 1);
}, []);

const handleIncrement = useCallback(() => {
  setCounter((prevCounter) => prevCounter + 1);
}, []);
```

### **Why does this work?**  
- `useCallback` ensures that as long as `Counter` doesn’t re-render with new dependencies, `handleDecrement` and `handleIncrement` **stay the same in memory.**  
- Since the function reference no longer changes, `IconButton` doesn’t see a new prop, and `memo` **finally works correctly!** 🎉  

---

### **🔥 Key Takeaways**  
✅ `memo` **only prevents re-renders when props don’t change**.  
✅ **Beware of function recreation inside components**—they cause unnecessary re-renders!  
✅ `useCallback` **preserves function references**, allowing `memo` to work correctly.  
✅ **State updater functions (`setCounter`) are already stable in React**, so they don’t need `useCallback`.  

Now, `IconButton` only re-renders when absolutely necessary, **boosting performance and efficiency.** 🚀