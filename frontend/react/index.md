---
layout: doc
title: React
---

# ⚛️ React

JavaScript library để build user interfaces - maintained by Meta (Facebook).

## 📚 Overview

React là thư viện UI phổ biến nhất với component-based architecture. Docs này cover:

- **Hooks**: useState, useEffect, custom hooks
- **Patterns**: Composition, render props, HOC
- **Performance**: Memo, lazy loading, optimization
- **React 18/19**: Concurrent features, Suspense, Server Components

---

## 🎯 Quick Links

### 📘 [React Mastery - 30 Days Course](/frontend/react/react-mastery/)

**Khóa học React chuyên sâu từ Foundation → Senior Level**

Học từ A-Z trong 30 ngày với:
- ✅ 6 giai đoạn học có hệ thống
- ✅ React 18 & 19 features mới nhất
- ✅ Performance optimization chuyên sâu
- ✅ Hands-on exercises & projects

→ [Bắt đầu khóa học](/frontend/react/react-mastery/)

---

## 📖 Quick References

### [Hooks Guide](/frontend/react/hooks-guide)
Complete guide về React Hooks - useState, useEffect, useContext, useReducer, custom hooks.

### [State Management](/frontend/react/state-management)
Quản lý state với Context API, Zustand, Redux - chọn tool phù hợp.

### [Performance Optimization](/frontend/react/performance)
Tối ưu React apps - React.memo, useMemo, useCallback, lazy loading.

---

## 🚀 Getting Started

### Create React App (Vite)

```bash
# Create new project
npm create vite@latest my-react-app -- --template react

cd my-react-app
npm install
npm run dev
```

### Basic Component

```jsx
import { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>
        Increment
      </button>
    </div>
  );
}

export default Counter;
```

---

## 🎓 Learning Path

### Level 1: Fundamentals
- JSX syntax
- Components (functional & class)
- Props & state
- Event handling
- Conditional rendering
- Lists & keys

### Level 2: Hooks
- useState - Local state
- useEffect - Side effects
- useContext - Global state
- useReducer - Complex state
- Custom hooks

### Level 3: Advanced
- Performance optimization
- Code splitting
- Error boundaries
- Portals
- Refs

### Level 4: Ecosystem
- React Router - Navigation
- React Query - Data fetching
- Zustand/Redux - State management
- Testing Library - Testing

---

## 💡 Core Concepts

### Components

```jsx
// Functional Component (Recommended)
function Welcome({ name }) {
  return <h1>Hello, {name}</h1>;
}

// Props destructuring
function User({ name, age, email }) {
  return (
    <div>
      <h2>{name}</h2>
      <p>Age: {age}</p>
      <p>Email: {email}</p>
    </div>
  );
}
```

### State Management

```jsx
// useState - Simple state
const [count, setCount] = useState(0);

// useReducer - Complex state
const [state, dispatch] = useReducer(reducer, initialState);

// Context - Global state
const value = useContext(MyContext);
```

### Side Effects

```jsx
// useEffect - Run after render
useEffect(() => {
  // Side effect here
  return () => {
    // Cleanup
  };
}, [dependencies]);

// Examples:
// Data fetching
useEffect(() => {
  fetchData().then(setData);
}, []);

// Event listeners
useEffect(() => {
  window.addEventListener('resize', handleResize);
  return () => window.removeEventListener('resize', handleResize);
}, []);
```

---

## 🔥 Best Practices

### Component Structure

```jsx
// ✅ Good - Single responsibility
function TodoList({ todos }) {
  return (
    <ul>
      {todos.map(todo => (
        <TodoItem key={todo.id} todo={todo} />
      ))}
    </ul>
  );
}

function TodoItem({ todo }) {
  return <li>{todo.text}</li>;
}

// ❌ Bad - Too much responsibility
function TodoList({ todos }) {
  // Fetching, filtering, rendering all in one
}
```

### Hooks Rules

```jsx
// ✅ Good - Top level only
function MyComponent() {
  const [count, setCount] = useState(0);
  const data = useFetch('/api/data');
  
  return <div>{data}</div>;
}

// ❌ Bad - Conditional hooks
function MyComponent({ condition }) {
  if (condition) {
    const [count, setCount] = useState(0); // ❌ Error!
  }
  return <div>...</div>;
}
```

### Performance

```jsx
// ✅ Good - Memoize expensive calculations
const expensiveValue = useMemo(() => {
  return computeExpensiveValue(a, b);
}, [a, b]);

// ✅ Good - Memoize callbacks
const handleClick = useCallback(() => {
  doSomething(a, b);
}, [a, b]);

// ✅ Good - Memoize components
const MemoizedComponent = React.memo(MyComponent);
```

---

## 📚 Resources

### Official
- [React.dev](https://react.dev/) - Official docs (new)
- [React Beta Docs](https://beta.reactjs.org/) - Modern React docs

### Learning
- [React Mastery Course](/frontend/react/react-mastery/) - 30-day course
- [Hooks Guide](/frontend/react/hooks-guide) - Complete hooks reference
- [Patterns Guide](/frontend/react/state-management) - Design patterns

### Tools
- [React DevTools](https://react.dev/learn/react-developer-tools)
- [Vite](https://vitejs.dev/) - Build tool
- [Testing Library](https://testing-library.com/react)

---

## 🎯 Next Steps

**New to React?** → Start với fundamentals trong [React Mastery](/frontend/react/react-mastery/)

**Know basics?** → Deep dive vào [Hooks Guide](/frontend/react/hooks-guide)

**Ready for advanced?** → Learn [Performance Optimization](/frontend/react/performance)

**Want complete course?** → Follow [React Mastery 30 Days](/frontend/react/react-mastery/)

---

**Happy Reacting! ⚛️**

[← Frontend](/frontend/) | [React Mastery →](/frontend/react/react-mastery/)