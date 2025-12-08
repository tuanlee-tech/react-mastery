# React Component Lifecycle - Tài liệu chuyên sâu

## 📋 Executive Summary

Tài liệu này cung cấp kiến thức toàn diện về React Component Lifecycle, từ cơ bản đến nâng cao, bao gồm:

### 🎯 Nội dung chính:

**I. Kiến trúc Render (2 Phase):**
- **Render Phase:** Tính toán Virtual DOM, có thể bị gián đoạn
- **Commit Phase:** Ghi thay đổi vào DOM thật, chạy effects

**II-IV. Lifecycle Hooks:** Mount → Update → Unmount với đầy đủ hooks React 18-19

**V-VI. Class Components:** Legacy lifecycle và migration guide sang hooks

**VII. Best Practices:** Chọn đúng effect, dependencies, cleanup, tránh infinite loops

**VIII. Debugging:** React DevTools, console logging, Strict Mode

**IX. Concurrent Rendering (React 18+):**
- Cơ chế render thông minh: tạm dừng, tiếp tục, hủy, ưu tiên
- useTransition, useDeferredValue, Suspense
- So sánh Blocking vs Concurrent với timeline visualization

**X. Performance Optimization:** Memoization, lazy loading, concurrent features

### 🎓 Ai nên đọc:
- React developers (beginner → advanced)
- Frontend engineers muốn hiểu sâu về lifecycle
- Teams cần tài liệu reference chuẩn
- Developers migration từ Class → Hooks hoặc React 17 → 18

### ⏱️ Thời gian đọc: ~45-60 phút (đọc đầy đủ)

---

## Tổng quan

React Component Lifecycle mô tả toàn bộ quá trình từ khi component được khởi tạo, cập nhật, cho đến khi bị loại bỏ khỏi UI. Hiểu rõ lifecycle giúp developer kiểm soát tốt hơn luồng dữ liệu, tối ưu performance và tránh các lỗi phổ biến.

---

## I. Kiến trúc Render của React

React hoạt động theo mô hình **hai giai đoạn (two-phase architecture)**, và từ React 18+ có thêm khả năng **Concurrent Rendering** để tối ưu trải nghiệm người dùng.

### 1. Render Phase (Reconciliation Phase)

**Mục đích:** Tính toán những thay đổi cần thiết mà không chạm vào DOM thật.

**Hoạt động:**
- Xác định xem component có cần re-render hay không
- Tính toán Virtual DOM mới
- So sánh (diffing) với Virtual DOM hiện tại
- Lập danh sách các thay đổi cần commit

**Đặc điểm quan trọng:**
- **Không** tác động trực tiếp lên DOM thật
- Có thể chạy **nhiều lần** hoặc bị gián đoạn (với Concurrent Rendering trong React 18+)
- Hoàn toàn **thuần túy** (pure) - không side effects
- Trong chế độ Concurrent, React có thể **tạm dừng, tiếp tục, hủy bỏ** quá trình render để ưu tiên các tác vụ quan trọng hơn

**Các hooks chạy trong Render Phase:**
- State management: `useState`, `useReducer`
- Memoization: `useMemo`, `useCallback`
- References: `useRef`
- Context: `useContext`
- React 18+: `useTransition`, `useDeferredValue`, `useId`, `useSyncExternalStore`
- React 19: `useOptimistic`, `useActionState`, `use`

> ⚠️ **Lưu ý:** Tuyệt đối KHÔNG thực hiện side effects hoặc truy cập DOM trong render phase.

---

### 2. Commit Phase

**Mục đích:** Áp dụng thay đổi vào DOM thật và thực thi side effects.

**Hoạt động:**
1. Ghi (commit) các thay đổi vào DOM thực tế
2. Thực thi các effect hooks theo thứ tự ưu tiên

**Đặc điểm quan trọng:**
- Chạy **một lần duy nhất** cho mỗi update cycle
- **Không thể bị hủy** (atomic operation)
- Là thời điểm duy nhất an toàn để truy cập DOM thật

**Thứ tự thực thi effects:**

```
DOM được cập nhật
    ↓
useInsertionEffect (đồng bộ, sớm nhất)
    ↓
useLayoutEffect (đồng bộ, trước paint)
    ↓
Browser paint
    ↓
useEffect (bất đồng bộ, sau paint)
```

---

## II. Lifecycle trong Function Components

### Mount (Khởi tạo)

Xảy ra khi component được render lần đầu tiên.

```javascript
function MyComponent() {
  // 1. Render Phase
  const [state, setState] = useState(initialValue);
  const memoValue = useMemo(() => computation(), [deps]);
  const ref = useRef(null);
  
  // 2. Commit Phase - Effects
  useInsertionEffect(() => {
    // Chạy đầu tiên, dùng cho CSS-in-JS
    return () => {/* cleanup */};
  }, []);
  
  useLayoutEffect(() => {
    // Chạy đồng bộ, trước paint
    // Đo lường DOM, animations
    return () => {/* cleanup */};
  }, []);
  
  useEffect(() => {
    // Chạy bất đồng bộ, sau paint
    // Data fetching, subscriptions
    return () => {/* cleanup */};
  }, []);
  
  return <div ref={ref}>...</div>;
}
```

**Luồng thực thi:**
```
1. Khởi tạo state, refs, memo values
2. Render JSX
3. Commit changes vào DOM
4. useInsertionEffect()
5. useLayoutEffect()
6. Browser paint màn hình
7. useEffect()
```

---

### Update (Cập nhật)

Xảy ra khi props, state hoặc context thay đổi.

```javascript
useEffect(() => {
  // Setup mới
  const subscription = subscribe();
  
  return () => {
    // Cleanup được gọi TRƯỚC effect tiếp theo
    subscription.unsubscribe();
  };
}, [dependency]);
```

**Luồng thực thi:**
```
1. Re-render (tính toán lại)
2. Commit changes vào DOM
3. Cleanup useInsertionEffect cũ → useInsertionEffect mới
4. Cleanup useLayoutEffect cũ → useLayoutEffect mới
5. Browser paint
6. Cleanup useEffect cũ → useEffect mới
```

---

### Unmount (Hủy bỏ)

Xảy ra khi component bị loại bỏ khỏi DOM.

```javascript
useEffect(() => {
  const timer = setInterval(() => {}, 1000);
  
  return () => {
    clearInterval(timer); // Cleanup khi unmount
  };
}, []);
```

**Thứ tự cleanup:**
```
1. Cleanup useInsertionEffect
2. Cleanup useLayoutEffect
3. Cleanup useEffect
```

---

## III. So sánh các Effect Hooks

### useInsertionEffect

**Thời điểm:** Đồng bộ, **sớm nhất**, ngay sau DOM mutation.

**Mục đích:** Inject styles động (CSS-in-JS libraries).

```javascript
useInsertionEffect(() => {
  // Chèn <style> tags trước khi layout được tính
  document.head.appendChild(styleElement);
}, []);
```

**Use case:** Emotion, styled-components, CSS Modules động.

---

### useLayoutEffect

**Thời điểm:** Đồng bộ, sau DOM update, **trước browser paint**.

**Mục đích:** Đo lường và thay đổi layout trước khi user nhìn thấy.

```javascript
useLayoutEffect(() => {
  const height = divRef.current.offsetHeight;
  if (height > 500) {
    divRef.current.style.maxHeight = '500px';
  }
}, [data]);
```

**Use case:** Đo DOM, scroll animations, tooltips positioning.

⚠️ **Chú ý:** Block browser paint, có thể gây giật lag nếu tính toán nặng.

---

### useEffect

**Thời điểm:** Bất đồng bộ, **sau browser paint**.

**Mục đích:** Side effects không ảnh hưởng visual (data fetching, subscriptions).

```javascript
useEffect(() => {
  fetch('/api/data')
    .then(res => res.json())
    .then(setData);
}, []);
```

**Use case:** API calls, event listeners, analytics tracking.

✅ **Ưu điểm:** Không block UI, tốt cho performance.

---

## IV. Hooks Lifecycle Reference (React 18-19)

### Hooks trong Render Phase

| Hook | Version | Mục đích |
|------|---------|----------|
| `useState` | 16.8+ | Quản lý local state |
| `useReducer` | 16.8+ | State phức tạp với reducer pattern |
| `useMemo` | 16.8+ | Memoize expensive computations |
| `useCallback` | 16.8+ | Memoize function references |
| `useRef` | 16.8+ | Persistent mutable values |
| `useContext` | 16.8+ | Consume React Context |
| `useId` | 18+ | Generate unique IDs (SSR-safe) |
| `useSyncExternalStore` | 18+ | Subscribe to external stores |
| `useTransition` | 18+ | Non-blocking state updates |
| `useDeferredValue` | 18+ | Defer re-renders for values |
| `useOptimistic` | 19+ | Optimistic UI updates |
| `useActionState` | 19+ | Form actions state management |
| `use` | 19+ | Unwrap Promises/Context |

### Hooks trong Commit Phase

| Hook | Timing | Use Case |
|------|--------|----------|
| `useInsertionEffect` | Sync, pre-layout | CSS-in-JS injection |
| `useLayoutEffect` | Sync, pre-paint | DOM measurements, animations |
| `useEffect` | Async, post-paint | Data fetching, subscriptions |

---

## V. Class Components Lifecycle (Legacy)

### Mount Lifecycle

```
constructor()
    ↓
static getDerivedStateFromProps(props, state)
    ↓
render()
    ↓
componentDidMount()
```

### Update Lifecycle

```
static getDerivedStateFromProps(props, state)
    ↓
shouldComponentUpdate(nextProps, nextState)
    ↓
render()
    ↓
getSnapshotBeforeUpdate(prevProps, prevState)
    ↓
componentDidUpdate(prevProps, prevState, snapshot)
```

### Unmount Lifecycle

```
componentWillUnmount()
```

---

## VI. Migration Guide: Class → Hooks

| Class Method | Hook Equivalent |
|--------------|-----------------|
| `constructor` | `useState` initialization |
| `componentDidMount` | `useEffect(() => {...}, [])` |
| `componentDidUpdate` | `useEffect(() => {...}, [deps])` |
| `componentWillUnmount` | `useEffect` cleanup function |
| `shouldComponentUpdate` | `React.memo()`, `useMemo` |
| `getDerivedStateFromProps` | Compute during render |
| `getSnapshotBeforeUpdate` | `useLayoutEffect` |

**Ví dụ migration:**

```javascript
// Class Component
class Example extends React.Component {
  componentDidMount() {
    document.title = this.props.name;
  }
  
  componentDidUpdate(prevProps) {
    if (prevProps.name !== this.props.name) {
      document.title = this.props.name;
    }
  }
  
  componentWillUnmount() {
    document.title = 'React App';
  }
}

// Function Component
function Example({ name }) {
  useEffect(() => {
    document.title = name;
    return () => {
      document.title = 'React App';
    };
  }, [name]);
}
```

---

## VII. Best Practices

### 1. Chọn đúng Effect Hook

```javascript
// ✅ useEffect cho data fetching
useEffect(() => {
  fetchData().then(setData);
}, []);

// ✅ useLayoutEffect cho DOM measurements
useLayoutEffect(() => {
  const rect = ref.current.getBoundingClientRect();
  setDimensions(rect);
}, []);

// ✅ useInsertionEffect cho CSS injection (rare)
useInsertionEffect(() => {
  injectGlobalStyles();
}, []);
```

### 2. Dependencies Array

```javascript
// ❌ Thiếu dependencies
useEffect(() => {
  console.log(count); // Stale closure
}, []);

// ✅ Đầy đủ dependencies
useEffect(() => {
  console.log(count);
}, [count]);

// ✅ Hoặc dùng callback form
const [count, setCount] = useState(0);
useEffect(() => {
  setCount(c => c + 1); // Không cần count trong deps
}, []);
```

### 3. Cleanup Functions

```javascript
// ✅ Luôn cleanup subscriptions
useEffect(() => {
  const sub = dataSource.subscribe(handleChange);
  return () => sub.unsubscribe();
}, [dataSource]);

// ✅ Clear timers
useEffect(() => {
  const timer = setTimeout(() => setShow(true), 1000);
  return () => clearTimeout(timer);
}, []);
```

### 4. Tránh Infinite Loops

```javascript
// ❌ Infinite loop
useEffect(() => {
  setCount(count + 1); // Triggers re-render → effect runs again
}, [count]);

// ✅ Điều kiện rõ ràng
useEffect(() => {
  if (count < 10) {
    setCount(count + 1);
  }
}, [count]);
```

---

## VIII. Debugging Lifecycle Issues

### 1. React DevTools

- Profiler tab: Xem component re-render
- Components tab: Inspect hooks state

### 2. Console Logging

```javascript
useEffect(() => {
  console.log('Effect ran', { prop1, prop2 });
  return () => console.log('Cleanup ran');
}, [prop1, prop2]);
```

### 3. Strict Mode

React Strict Mode chạy effects **hai lần** trong development để phát hiện bugs:

```javascript
// Development: mount → unmount → mount
// Production: mount only

useEffect(() => {
  console.log('Mount'); // Logs twice in dev
  return () => console.log('Unmount');
}, []);
```

---

## IX. Concurrent Rendering (React 18+)

### Tổng quan

**Concurrent Rendering** là cơ chế render mới trong React 18+ cho phép React quản lý việc render một cách thông minh hơn: tạm dừng, tiếp tục, hủy bỏ, và ưu tiên lại công việc để UI luôn mượt mà và phản hồi nhanh.

> **Định nghĩa ngắn gọn:** Concurrent Rendering = React render theo kiểu "đa luồng giả lập" với khả năng ưu tiên công việc quan trọng, không còn render đồng bộ kiểu "khóa cứng" UI như trước React 18.

---

### 1. So sánh: Legacy vs Concurrent Rendering

#### React trước 18: Blocking Render (Render đồng bộ)

**Cách hoạt động:**
- Khi React bắt đầu render → chạy một mạch từ đầu đến cuối
- **Không thể** tạm dừng
- **Không thể** hủy bỏ
- **Không thể** ưu tiên lại
- Nếu component phức tạp → UI bị lag, không scroll được, không nhận input

**Vấn đề:**
```javascript
// React 17: User types → UI freezes
function SearchList({ items }) {
  const [query, setQuery] = useState('');
  
  const filtered = items.filter(item => 
    item.name.includes(query) // Nếu items lớn → UI đơ
  );
  
  return (
    <>
      <input 
        value={query} 
        onChange={e => setQuery(e.target.value)} 
        // ❌ Input bị lag khi filter chạy
      />
      <List items={filtered} />
    </>
  );
}
```

**Timeline React 17:**
```
User types "a"
    ↓
React starts rendering (BLOCKING)
    ↓
Filter 100,000 items (takes 500ms)
    ↓
[UI FROZEN - không scroll, không click được]
    ↓
Render complete → UI updates
    ↓
User can type next character
```

---

#### React 18+: Concurrent Rendering

**Cách hoạt động:**
- React có thể **tạm dừng** render để xử lý tác vụ ưu tiên cao hơn
- React có thể **tiếp tục** render đã tạm dừng
- React có thể **hủy bỏ** render cũ nếu có dữ liệu mới
- React có thể **ưu tiên** các tác vụ (typing > filtering > rendering list)
- Chia nhỏ công việc lớn thành nhiều phần để không block main thread

**Kích hoạt Concurrent Mode:**
```javascript
// ✅ React 18: Concurrent mode
import { createRoot } from 'react-dom/client';

const root = createRoot(document.getElementById('root'));
root.render(<App />);
```

```javascript
// ❌ Legacy mode (blocking)
import ReactDOM from 'react-dom';

ReactDOM.render(<App />, document.getElementById('root'));
```

**Timeline React 18:**
```
User types "a"
    ↓
React updates input IMMEDIATELY (high priority)
    ↓
React starts filtering in BACKGROUND (low priority)
    ↓
User types "b" 
    ↓
React CANCELS old filter, starts new filter for "ab"
    ↓
[UI RESPONSIVE - có thể scroll, click]
    ↓
Filter complete → Update list
```

---

### 2. Tại sao cần Concurrent Rendering?

**Mục tiêu chính:** Cải thiện UX bằng cách ưu tiên các tương tác quan trọng.

**Các tình huống được cải thiện:**

| Tình huống | React 17 (Blocking) | React 18 (Concurrent) |
|------------|---------------------|----------------------|
| Typing vào input + render list lớn | Input bị lag, UI đơ | Input mượt, list render background |
| Scroll + update data | Scroll bị giật | Scroll mượt, update sau |
| Multiple state updates | Re-render nhiều lần | Batch updates thông minh |
| Loading heavy component | Toàn bộ UI chờ | Hiện phần sẵn sàng trước |

---

### 3. Tính năng dựa trên Concurrent Rendering

| Tính năng | Mô tả | Use Case |
|-----------|-------|----------|
| **useTransition** | Đánh dấu state update là "không gấp" | Search, filtering, tab switching |
| **useDeferredValue** | Trì hoãn render của giá trị cụ thể | Debounce tự động, expensive renders |
| **Suspense** | Chờ async data mà không block UI | Code splitting, data fetching |
| **Automatic Batching** | Gom nhiều setState thành 1 render | Performance optimization |
| **Selective Hydration** | Ưu tiên hydrate phần đang tương tác | SSR performance |
| **startTransition** | Mark updates as non-urgent | Navigation, filters |

---

### 4. useTransition - Concurrent Updates

**Concept:** Chia updates thành 2 loại:
- **Urgent updates:** typing, clicking, hovering (update ngay)
- **Transition updates:** filtering, searching, rendering list (có thể delay)

**Syntax:**
```javascript
const [isPending, startTransition] = useTransition();
```

**Ví dụ thực tế:**

```javascript
function SearchApp() {
  const [input, setInput] = useState('');
  const [filteredList, setFilteredList] = useState(bigList);
  const [isPending, startTransition] = useTransition();
  
  function handleChange(e) {
    const value = e.target.value;
    
    // ✅ URGENT: Update input ngay lập tức
    setInput(value);
    
    // ✅ NON-URGENT: Filter có thể đợi
    startTransition(() => {
      // React có thể:
      // - Tạm dừng render này nếu user gõ tiếp
      // - Hủy render này nếu có value mới
      // - Chia nhỏ render để không block UI
      const filtered = bigList.filter(item => 
        item.name.toLowerCase().includes(value.toLowerCase())
      );
      setFilteredList(filtered);
    });
  }
  
  return (
    <>
      <input 
        value={input} 
        onChange={handleChange}
        // ✅ Input luôn mượt, không bao giờ bị lag
      />
      
      {isPending && <Spinner />}
      
      <List items={filteredList} />
    </>
  );
}
```

**Lợi ích:**
- Input cập nhật **tức thì** (< 16ms)
- Filtering chạy **background** (có thể bị interrupt)
- UI **không bao giờ bị đóng băng**
- `isPending` cho feedback visual

---

### 5. useDeferredValue - Debounce tự động

**Concept:** Trì hoãn việc render của một giá trị để ưu tiên các tác vụ khác.

```javascript
function SearchResults({ query }) {
  // ✅ deferredQuery sẽ "chậm" hơn query
  const deferredQuery = useDeferredValue(query);
  
  // Expensive computation chỉ chạy khi deferredQuery thay đổi
  const results = useMemo(() => 
    expensiveSearch(deferredQuery)
  , [deferredQuery]);
  
  return (
    <div className={query !== deferredQuery ? 'dimmed' : ''}>
      <List items={results} />
    </div>
  );
}
```

**So sánh với useTransition:**

```javascript
// useTransition: Kiểm soát CẢ quá trình update
startTransition(() => {
  setValue(newValue);
});

// useDeferredValue: Chỉ defer 1 giá trị cụ thể
const deferredValue = useDeferredValue(value);
```

---

### 6. Cách Concurrent Rendering hoạt động (Chi tiết kỹ thuật)

**Lưu ý quan trọng:**
> Concurrent Rendering **KHÔNG PHẢI** multi-threading (đa luồng thật). JavaScript vẫn chạy single-threaded. React sử dụng **cooperative scheduling** trên main thread.

**Cơ chế:**

1. **Time Slicing:** Chia công việc lớn thành chunks nhỏ (~5ms mỗi chunk)
2. **Priority Queue:** Mỗi update có độ ưu tiên khác nhau
3. **Work Loop:** React check main thread sau mỗi chunk
4. **Yielding:** Nếu có tác vụ ưu tiên cao hơn → yield (nhường quyền)

**Priority Levels:**
```
Immediate Priority  → Discrete user input (click, keypress)
User-blocking       → Continuous input (scroll, hover)
Normal Priority     → Network responses, transitions
Low Priority        → Analytics, logging
Idle Priority       → Background work
```

**Ví dụ minh họa:**

```javascript
// React 18 internally (simplified)
function workLoop() {
  while (workInProgress && !shouldYield()) {
    workInProgress = performUnitOfWork(workInProgress);
  }
  
  if (workInProgress) {
    // Còn việc nhưng cần yield → schedule tiếp
    return scheduleCallback(workLoop);
  }
}

function shouldYield() {
  return (
    currentTime >= deadline ||     // Hết thời gian slice
    hasHigherPriorityWork()        // Có việc quan trọng hơn
  );
}
```

---

### 7. Automatic Batching

React 18 tự động gom nhiều state updates thành 1 render, **kể cả trong promises, setTimeout, native event handlers**.

```javascript
// React 17: Re-render 2 lần
setTimeout(() => {
  setCount(c => c + 1);  // Render lần 1
  setFlag(f => !f);      // Render lần 2
}, 1000);

// React 18: Re-render 1 lần (batched)
setTimeout(() => {
  setCount(c => c + 1);
  setFlag(f => !f);
  // ✅ React tự động batch → chỉ 1 render
}, 1000);
```

**Opt-out nếu cần:**
```javascript
import { flushSync } from 'react-dom';

flushSync(() => {
  setCount(c => c + 1);  // Render ngay
});
setFlag(f => !f);        // Render riêng
```

---

### 8. Suspense trong Concurrent Mode

**Data Fetching:**
```javascript
// Library hỗ trợ Suspense (e.g., React Query, SWR, Relay)
function ProfilePage() {
  return (
    <Suspense fallback={<Spinner />}>
      <ProfileDetails />
      <Suspense fallback={<PostsSkeleton />}>
        <ProfilePosts />
      </Suspense>
    </Suspense>
  );
}
```

**Code Splitting:**
```javascript
const HeavyChart = lazy(() => import('./HeavyChart'));

function Dashboard() {
  return (
    <Suspense fallback={<ChartSkeleton />}>
      <HeavyChart data={data} />
    </Suspense>
  );
}
```

---

### 9. Best Practices với Concurrent Rendering

#### ✅ Nên làm:

```javascript
// 1. Wrap expensive updates trong transition
startTransition(() => {
  setSearchResults(expensiveFilter(query));
});

// 2. Dùng useDeferredValue cho derived state nặng
const deferredQuery = useDeferredValue(query);
const results = useMemo(() => search(deferredQuery), [deferredQuery]);

// 3. Sử dụng Suspense cho async operations
<Suspense fallback={<Loading />}>
  <AsyncComponent />
</Suspense>

// 4. Memoize để tránh re-render không cần thiết
const MemoChild = memo(Child);
```

#### ❌ Không nên:

```javascript
// ❌ Wrap tất cả updates trong transition
startTransition(() => {
  setInput(value); // Input nên urgent!
});

// ❌ Quá nhiều nested Suspense
<Suspense>
  <Suspense>
    <Suspense>
      <Suspense>
        <Component /> {/* Quá phức tạp */}
      </Suspense>
    </Suspense>
  </Suspense>
</Suspense>

// ❌ Forget cleanup trong concurrent effects
useEffect(() => {
  fetchData(); // ❌ Có thể bị call nhiều lần
  
  return () => controller.abort(); // ✅ Cần cleanup
}, []);
```

---

### 10. Timeline Comparison

**React 17 (Blocking):**
```
User Action (t=0ms)
    ↓
━━━━━━━━━━━ React Render (500ms, BLOCKING) ━━━━━━━━━━━
    ↓
UI Update (t=500ms)
    ↓
User can interact again

Timeline: [========== BLOCKED ==========]|
          0ms                        500ms
```

**React 18 (Concurrent):**
```
User Action (t=0ms)
    ↓
Urgent Update (t=5ms) ✅
    ↓
[═══]  [═══]  [═══]  [═══]  Non-urgent work (chunked)
 5ms   25ms   50ms   75ms
  ↑      ↑      ↑      ↑
  └──────┴──────┴──────┴─── User can interact anytime

Timeline: [quick][gap][quick][gap][quick]
          0ms   5ms  25ms  50ms  75ms
          ✅    ✅    ✅    ✅    ✅  UI responsive
```

---

### 11. Kết luận Concurrent Rendering

**Tóm tắt:**
- **Concurrent Rendering** = Render thông minh với khả năng tạm dừng, tiếp tục, hủy bỏ, ưu tiên
- **Mục tiêu:** UI luôn mượt mà, không bao giờ bị đóng băng
- **Không phải:** Multi-threading (vẫn single-threaded JavaScript)
- **Là:** Cooperative scheduling với priority queue

**Khi nào dùng:**
- Search/filter với dataset lớn → `useTransition`
- Expensive derived values → `useDeferredValue`
- Async data/code splitting → `Suspense`
- Complex animations + updates → Concurrent features

**Migration từ React 17:**
```javascript
// Bước 1: Upgrade React 18+
npm install react@18 react-dom@18

// Bước 2: Dùng createRoot
import { createRoot } from 'react-dom/client';
createRoot(root).render(<App />);

// Bước 3: Thêm transitions cho expensive updates
startTransition(() => { /* expensive work */ });

// Bước 4: Test kỹ với React Strict Mode
<StrictMode><App /></StrictMode>
```

---

## XI. Kết luận

### 1. Memoization

```javascript
// Tránh re-render không cần thiết
const MemoizedChild = React.memo(Child);

// Memoize expensive calculations
const expensiveValue = useMemo(() => {
  return computeExpensiveValue(a, b);
}, [a, b]);

// Memoize callbacks
const handleClick = useCallback(() => {
  doSomething(a, b);
}, [a, b]);
```

### 2. Lazy Loading

```javascript
const HeavyComponent = React.lazy(() => import('./Heavy'));

function App() {
  return (
    <Suspense fallback={<Loading />}>
      <HeavyComponent />
    </Suspense>
  );
}
```

### 3. Concurrent Features (React 18+)

```javascript
// useTransition: Non-blocking updates
const [isPending, startTransition] = useTransition();

function handleClick() {
  startTransition(() => {
    setTab('posts'); // Low priority
  });
}

// useDeferredValue: Defer expensive renders
const deferredQuery = useDeferredValue(query);
```

---

## X. Kết luận

React Lifecycle trong Function Components xoay quanh:

1. **Render Phase:** Tính toán (pure, có thể bị gián đoạn)
2. **Commit Phase:** Áp dụng thay đổi (atomic, một lần)
3. **Effects:** Side effects theo thứ tự priority

**Key Takeaways:**
- Hiểu sự khác biệt giữa render và commit phase
- Chọn đúng effect hook cho đúng use case
- Luôn cleanup để tránh memory leaks
- Tối ưu bằng memoization và concurrent features

**Tài nguyên học thêm:**
- [React Docs - Lifecycle](https://react.dev/learn/lifecycle-of-reactive-effects)
- [React Docs - useEffect](https://react.dev/reference/react/useEffect)
- [React 18 Concurrent Features](https://react.dev/blog/2022/03/29/react-v18)