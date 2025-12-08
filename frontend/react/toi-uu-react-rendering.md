# Cẩm Nang Hiểu Và Tối Ưu React Rendering

## 📚 MỤC LỤC

1. [React Rendering Lifecycle - Nền Tảng Cần Hiểu](#1-react-rendering-lifecycle)
2. [Re-render Không Phải Là Xấu](#2-re-render-không-phải-là-xấu)
3. [Khi Nào React Re-render](#3-khi-nào-react-re-render)
4. [Reference Identity - Gốc Rễ Của Vấn Đề](#4-reference-identity)
5. [Style={{}} - Trường Hợp Đặc Biệt](#5-style---trường-hợp-đặc-biệt)
6. [Chiến Lược Tối Ưu](#6-chiến-lược-tối-ưu)
7. [Class Component - Legacy Optimization](#7-class-component)
8. [Debugging & Profiling](#8-debugging--profiling)
9. [Case Studies](#9-case-studies)
10. [Tổng Kết](#10-tổng-kết)

---

## 1. React Rendering Lifecycle - Nền Tảng Cần Hiểu
### [👉 Nên xem qua React Render Tutorial → tại đây](https://www.youtube.com/playlist?list=PLC3y8-rFHvwg7czgqpQIBEAHn8D6l530t)

### 1.1. Rendering vs Committing - Hai Giai Đoạn Khác Nhau

React hoạt động theo **3 phases**:

```
Trigger → Render Phase → Commit Phase → Browser Paint
```

#### **Phase 1: Trigger (Kích hoạt)**
- State changes
- Props changes  
- Parent re-render
- Context changes

#### **Phase 2: Render Phase** ⚡ (Diễn ra trong bộ nhớ - KHÔNG tốn kém)
```javascript
// React gọi lại component function
function MyComponent(props) {
  // Code này chạy lại → "Re-render"
  const [count] = useState(0);
  return <div>{count}</div>; // Tạo React Element (Virtual DOM)
}
```

**Điều quan trọng:** 
- React tạo ra cây Virtual DOM mới
- So sánh với cây Virtual DOM cũ (Reconciliation)
- Tìm ra **diff** (những gì thực sự thay đổi)
- **CHỈ LÀ TÍNH TOÁN TRONG BỘ NHỚ** - rất nhanh!

#### **Phase 3: Commit Phase** 🎨 (Cập nhật DOM thật - CÓ THỂ tốn kém)
```javascript
// React chỉ update những gì thay đổi
<div>0</div> → <div>1</div>
// Chỉ update text node "1", KHÔNG tạo lại <div>
```

**Điều quan trọng:**
- Chỉ diễn ra nếu có **thay đổi thực sự** sau Render Phase
- React chỉ patch những phần DOM cần thiết
- Browser re-paint chỉ những vùng thay đổi

### 1.2. Minh Họa Với Ví Dụ Cụ Thể

```javascript
function Counter() {
  const [count, setCount] = useState(0);
  console.log("Render phase"); // Log mỗi lần re-render
  
  return (
    <div>
      <h1>{count}</h1>
      <button onClick={() => setCount(count + 1)}>+1</button>
    </div>
  );
}
```

**Khi click button:**

```
1. Trigger: setCount(1) được gọi
2. Render Phase: 
   - Counter() chạy lại
   - console.log("Render phase") → in ra
   - Return <div><h1>1</h1>...</div>
   
3. Reconciliation:
   - So sánh: <h1>0</h1> vs <h1>1</h1>
   - Phát hiện: text node thay đổi "0" → "1"
   
4. Commit Phase:
   - Chỉ update DOM: textContent của <h1> = "1"
   - KHÔNG tạo lại <div>, <h1>, <button>
   
5. Browser Paint:
   - Chỉ vẽ lại vùng <h1>
```

---

## 2. Re-render Không Phải Là Xấu

### 2.1. Hiểu Đúng Về "Re-render"

❌ **Quan niệm sai:**
> "Re-render = chậm = tốn performance = phải tránh"

✅ **Sự thật:**
> "Re-render = React tính toán xem có gì thay đổi không"

### 2.2. Khi Nào Re-render Là Cần Thiết?

**✅ Re-render CÓ CẬP NHẬT DOM** (Necessary & Good):
```javascript
function TodoList() {
  const [todos, setTodos] = useState([]);
  
  const addTodo = (text) => {
    setTodos([...todos, { id: Date.now(), text }]);
    // ✅ Re-render cần thiết: UI phải hiển thị todo mới
  };
  
  return <ul>{todos.map(todo => <li key={todo.id}>{todo.text}</li>)}</ul>;
}
```
**Kết quả:** DOM được cập nhật → User thấy todo mới → **Hoàn hảo!**

---

**⚠️ Re-render KHÔNG CẬP NHẬT DOM** (Unnecessary - Lãng phí):
```javascript
const ExpensiveChild = React.memo(({ data }) => {
  console.log("Child rendered");
  return <div>{data.name}</div>;
});

function Parent() {
  const [count, setCount] = useState(0);
  
  return (
    <>
      <ExpensiveChild data={{ name: "John" }} /> {/* Object mới mỗi lần! */}
      <button onClick={() => setCount(count + 1)}>Count: {count}</button>
    </>
  );
}
```

**Khi click button:**
```
1. Parent re-render (cần thiết - count thay đổi)
2. ExpensiveChild nhận prop mới: data={{ name: "John" }}
3. React.memo so sánh: 
   - prevProps.data !== nextProps.data (reference khác!)
   - → ExpensiveChild re-render
4. Reconciliation:
   - <div>John</div> vs <div>John</div> → KHÔNG CÓ GÌ THAY ĐỔI
5. Commit Phase: BỊ BỎ QUA (không có gì để commit)
```

**Vấn đề:** ExpensiveChild chạy Render Phase nhưng không tạo ra giá trị gì → **Lãng phí!**

### 2.3. So Sánh Chi Tiết

| Scenario                                              | Render Phase | Commit Phase | Đánh Giá                         |
| ----------------------------------------------------- | ------------ | ------------ | -------------------------------- |
| State thay đổi → UI thay đổi                          | ✅ Chạy       | ✅ Update DOM | ✅ **Necessary** - Cần thiết      |
| Props khác reference nhưng giống value → UI không đổi | ⚠️ Chạy       | ❌ Bỏ qua     | ⚠️ **Unnecessary** - Lãng phí CPU |
| Parent render → Child render dù props không đổi       | ⚠️ Chạy       | ❌ Bỏ qua     | ⚠️ **Unnecessary** - Lãng phí CPU |
| Context thay đổi → Consumer sử dụng phần không đổi    | ⚠️ Chạy       | ❌ Bỏ qua     | ⚠️ **Unnecessary** - Lãng phí CPU |

### 2.4. Khi Nào Cần Lo Lắng?

**KHÔNG cần lo lắng khi:**
- Component đơn giản (< 100 elements)
- Re-render < 16ms (60 FPS)
- User không cảm thấy lag

**CẦN tối ưu khi:**
- Component phức tạp (lists lớn, heavy calculations)
- Re-render > 16ms → UI giật lag
- React DevTools Profiler hiển thị màu đỏ/vàng
- Có nhiều unnecessary re-renders liên tục

**Nguyên tắc vàng:**
> **"Premature optimization is the root of all evil"** - Donald Knuth
> 
> Viết code rõ ràng trước, measure performance sau, optimize chỉ khi cần.

---

## 3. Khi Nào React Re-render?

### 3.1. State Changes

```javascript
function Counter() {
  const [count, setCount] = useState(0);
  
  const increment = () => {
    setCount(count + 1); // ✅ Trigger re-render
  };
  
  const noChange = () => {
    setCount(0); // React so sánh: 0 === 0 → ❌ KHÔNG re-render
  };
  
  return <button onClick={increment}>{count}</button>;
}
```

**Cơ chế:**
- React dùng `Object.is(newValue, oldValue)` để so sánh
- Giống nhau → Bail out (không re-render)
- Khác nhau → Re-render

### 3.2. Props Changes

```javascript
function Child({ name }) {
  return <div>{name}</div>;
}

function Parent() {
  const [name, setName] = useState("John");
  
  return <Child name={name} />; // name thay đổi → Child re-render
}
```

### 3.3. Parent Re-render (Default Behavior)

```javascript
function Parent() {
  const [count, setCount] = useState(0);
  
  return (
    <>
      <Child /> {/* ⚠️ LUÔN re-render khi Parent render */}
      <button onClick={() => setCount(count + 1)}>{count}</button>
    </>
  );
}

function Child() {
  console.log("Child rendered");
  return <div>I am child</div>;
}
```

**Giải thích:**
- React **KHÔNG TỰ ĐỘNG** biết Child có cần re-render hay không
- Behavior mặc định: An toàn hơn là render → kiểm tra → bỏ qua commit
- Developer phải **chủ động** báo cho React: "Child này không cần re-render"

### 3.4. Context Changes

```javascript
const ThemeContext = React.createContext();

function App() {
  const [theme, setTheme] = useState("light");
  const [user, setUser] = useState("John");
  
  return (
    <ThemeContext.Provider value={{ theme, user }}>
      <Toolbar />
    </ThemeContext.Provider>
  );
}

function Toolbar() {
  const { theme } = useContext(ThemeContext);
  // ⚠️ Re-render cả khi CHỈ user thay đổi
  return <div className={theme}>Toolbar</div>;
}
```

**Vấn đề:** Context không có cơ chế "partial subscribe" - subscribe value → re-render khi BẤT KỲ phần nào của value thay đổi.

---

## 4. Reference Identity - Gốc Rễ Của Vấn Đề

### 4.1. JavaScript Reference Types - Ôn Lại Kiến Thức Nền

```javascript
// Primitive types - So sánh bằng VALUE
const a = 1;
const b = 1;
console.log(a === b); // true

// Reference types - So sánh bằng REFERENCE (địa chỉ bộ nhớ)
const obj1 = { name: "John" };
const obj2 = { name: "John" };
console.log(obj1 === obj2); // false - Hai object khác nhau trong bộ nhớ

const obj3 = obj1;
console.log(obj1 === obj3); // true - Cùng trỏ tới một object
```

### 4.2. React.memo - Cơ Chế Hoạt Động

```javascript
const Child = React.memo(({ data }) => {
  return <div>{data.name}</div>;
});

// React.memo thực hiện shallow comparison:
function arePropsEqual(prevProps, nextProps) {
  return Object.is(prevProps.data, nextProps.data); // So sánh reference!
}
```

**Ví dụ chi tiết:**

```javascript
function Parent() {
  const [count, setCount] = useState(0);
  
  // ❌ Object literal mới được tạo MỖI LẦN Parent render
  const data = { name: "John" };
  
  return (
    <>
      <Child data={data} />
      <button onClick={() => setCount(count + 1)}>{count}</button>
    </>
  );
}
```

**Bước chạy khi click button:**

```javascript
// Lần render 1:
const data_v1 = { name: "John" }; // Address: 0x001
<Child data={data_v1} />

// Lần render 2 (sau khi click):
const data_v2 = { name: "John" }; // Address: 0x002 (MỚI!)
<Child data={data_v2} />

// React.memo so sánh:
0x001 === 0x002 // false → Re-render Child
```

### 4.3. Các Trường Hợp Phá Vỡ Reference Identity

#### **A. Inline Functions**

**❌ Vấn đề:**
```javascript
function Parent() {
  return (
    <Child 
      onClick={() => console.log("Click")} // Function mới mỗi lần
      onSubmit={function() { console.log("Submit"); }} // Function mới
    />
  );
}
```

**✅ Giải pháp:**
```javascript
function Parent() {
  const handleClick = useCallback(() => {
    console.log("Click");
  }, []); // Reference ổn định
  
  return <Child onClick={handleClick} />;
}
```

**Khi nào dùng `useCallback`:**
```javascript
// ✅ CẦN: Child được memoized
const MemoChild = React.memo(Child);
<MemoChild onClick={handleClick} />

// ✅ CẦN: Function trong dependency array
useEffect(() => {
  handleSomething();
}, [handleSomething]); // Nếu không wrap, effect chạy mỗi render

// ❌ KHÔNG CẦN: DOM element thông thường
<button onClick={() => console.log("Click")}>Click</button>

- React không memoize DOM element (`như <button>, <div>…`), 
và React không nhận prop function để so sánh như khi truyền xuống component con.

- Dù bạn tạo function mới mỗi lần render (`() => console.log(...)`), 
React không phải re-render gì thêm, vì DOM element không dùng cơ chế so sánh props như component con memoized.

- 🧠 Điều quan trọng:
`onClick={() => ...}` không gây re-render lại bất cứ component nào khác.
Function mới chỉ dùng một lần cho chính element đó → `không gây tốn kém gì đáng kể`

// ❌ KHÔNG CẦN: Child không memoized
<RegularChild onClick={() => console.log("Click")} />

- `RegularChild` luôn `re-render` khi `parent re-render`.
- Dùng `useCallback` ở `parent` hay không thì nó vẫn `re-render lại`, 
vì `RegularChild` không bọc `React.memo` để so sánh props.

👉 Điều này nghĩa là:

Dù bạn dùng `useCallback`, function truyền xuống vẫn được tạo lại hoặc dù là function cũ, component con vẫn render lại.

Vì child không `memoized` → `useCallback` mất tác dụng, chỉ làm code phức tạp thêm.

```

#### **B. Inline Objects & Arrays**

**❌ Vấn đề:**
```javascript
function Parent() {
  return (
    <>
      <Child data={{ id: 1, name: "John" }} /> {/* Object mới */}
      <Child items={[1, 2, 3]} /> {/* Array mới */}
      <Child config={{ timeout: 1000, retries: 3 }} /> {/* Config mới */}
    </>
  );
}
```

**✅ Giải pháp 1: useMemo cho dynamic values**
```javascript
function Parent({ userId }) {
  const data = useMemo(() => ({ 
    id: userId, 
    name: "John" 
  }), [userId]); // Chỉ tạo mới khi userId thay đổi
  
  const items = useMemo(() => [1, 2, 3], []); // Không bao giờ thay đổi
  
  return <Child data={data} items={items} />;
}
```

**✅ Giải pháp 2: Constant bên ngoài cho static values**
```javascript
// ✅ TỐT NHẤT cho static values
const STATIC_ITEMS = [1, 2, 3];
const STATIC_CONFIG = { timeout: 1000, retries: 3 };

function Parent() {
  return (
    <Child 
      items={STATIC_ITEMS} 
      config={STATIC_CONFIG}
    />
  );
}
```

#### **C. Context Value**

**❌ Vấn đề:**
```javascript
function Provider({ children }) {
  const [user, setUser] = useState({ name: "John" });
  const [theme, setTheme] = useState("dark");
  
  // ❌ Object mới mỗi lần Provider render (dù user/theme không đổi)
  return (
    <MyContext.Provider value={{ user, theme, setTheme }}>
      {children}
    </MyContext.Provider>
  );
}
```

**✅ Giải pháp 1: Memoize context value**
```javascript
function Provider({ children }) {
  const [user, setUser] = useState({ name: "John" });
  const [theme, setTheme] = useState("dark");
  
  const value = useMemo(
    () => ({ user, theme, setTheme }),
    [user, theme] // Chỉ tạo object mới khi dependencies thay đổi
  );
  
  return (
    <MyContext.Provider value={value}>
      {children}
    </MyContext.Provider>
  );
}
```

**✅ Giải pháp 2: Tách Context (Khuyên dùng)**
```javascript
// Tách thành nhiều Context nhỏ
const UserContext = React.createContext();
const ThemeContext = React.createContext();

function Provider({ children }) {
  const [user, setUser] = useState({ name: "John" });
  const [theme, setTheme] = useState("dark");
  
  return (
    <UserContext.Provider value={user}>
      <ThemeContext.Provider value={{ theme, setTheme }}>
        {children}
      </ThemeContext.Provider>
    </UserContext.Provider>
  );
}

// Component chỉ subscribe phần cần thiết
function Toolbar() {
  const { theme } = useContext(ThemeContext); // ✅ Chỉ re-render khi theme đổi
  return <div>{theme}</div>;
}

function UserProfile() {
  const user = useContext(UserContext); // ✅ Chỉ re-render khi user đổi
  return <div>{user.name}</div>;
}
```

#### **D. Children Props**

**❌ Vấn đề:**
```javascript
function Parent() {
  const [count, setCount] = useState(0);
  
  return (
    <Wrapper>
      <ExpensiveChild /> {/* JSX Element mới được tạo mỗi lần Parent render */}
    </Wrapper>
  );
}
```

**Giải thích:**
```javascript
// JSX được compile thành:
React.createElement(
  Wrapper,
  null,
  React.createElement(ExpensiveChild) // ← Object mới mỗi lần
)

// Tương đương:
<Wrapper children={<ExpensiveChild />} />
// children là prop → object mới → Wrapper re-render → ExpensiveChild re-render
```

**✅ Giải pháp: Lift children up**
```javascript
// ✅ Tạo children ở component cao hơn (không bị re-render)
function App() {
  return (
    <Parent>
      <ExpensiveChild /> {/* Chỉ tạo một lần */}
    </Parent>
  );
}

function Parent({ children }) {
  const [count, setCount] = useState(0);
  // count thay đổi → Parent re-render
  // Nhưng children KHÔNG phải object mới → ExpensiveChild KHÔNG re-render
  return (
    <>
      <div>Count: {count}</div>
      {children}
    </>
  );
}
```

---

## 5. Style={{}} - Trường Hợp Đặc Biệt

**Bạn nên dùng `style={{}}` một cách tự nhiên trong hầu hết trường hợp!**

Lý do:
- Inline style được truyền trực tiếp cho **DOM element** (không phải React component)
- DOM element **KHÔNG BAO GIỜ** re-render vì reference thay đổi
- Chỉ cần lo lắng khi truyền style cho **memoized React component**

### 5.2. Hiểu Sự Khác Biệt: DOM Element vs React Component

#### **Case 1: Inline Style cho DOM Element ✅ (AN TOÀN)**

```javascript
function MyComponent() {
  const [count, setCount] = useState(0);
  
  return (
    <div>
      {/* ✅ HOÀN TOÀN OK - Không gây re-render thừa */}
      <button 
        style={{ backgroundColor: 'blue', padding: '10px' }}
        onClick={() => setCount(count + 1)}
      >
        Count: {count}
      </button>
      
      {/* ✅ HOÀN TOÀN OK */}
      <div style={{ margin: '20px', fontSize: '16px' }}>
        Content
      </div>
    </div>
  );
}
```

**Tại sao an toàn?**

```javascript
// Mỗi lần MyComponent render:
// 1. React tạo React Element:
{
  type: 'button',
  props: {
    style: { backgroundColor: 'blue', padding: '10px' }, // Object mới
    onClick: ...
  }
}

// 2. React Reconciliation so sánh:
// Phát hiện: type vẫn là 'button' (DOM element)
// → Không re-render <button>, CHỈ so sánh props

// 3. So sánh style:
// prevProps.style = { backgroundColor: 'blue', padding: '10px' }
// nextProps.style = { backgroundColor: 'blue', padding: '10px' }
// Reference khác NHƯNG React duyệt từng key:
//   backgroundColor: 'blue' === 'blue' ✓
//   padding: '10px' === '10px' ✓
// → KHÔNG CÓ GÌ THAY ĐỔI

// 4. Commit Phase: BỊ BỎ QUA
```

**Kết luận:** DOM element tự xử lý việc so sánh style properties - không có vấn đề performance!

---

#### **Case 2: Style cho Memoized Component ⚠️ (CẦN CHÚ Ý)**

```javascript
// Component con được memoized
const Card = React.memo(({ title, style }) => {
  console.log('Card rendered:', title);
  return (
    <div style={style}>
      {title}
    </div>
  );
});

function Parent() {
  const [count, setCount] = useState(0);
  
  return (
    <div>
      {/* ⚠️ VẤN ĐỀ: Card re-render mỗi lần Parent render */}
      <Card 
        title="My Card" 
        style={{ padding: '20px', border: '1px solid gray' }}
      />
      
      <button onClick={() => setCount(count + 1)}>
        Count: {count}
      </button>
    </div>
  );
}
```

**Tại sao có vấn đề?**

```javascript
// Lần render 1:
const style_v1 = { padding: '20px', border: '1px solid gray' }; // 0x001
<Card style={style_v1} />

// Lần render 2 (sau khi click button):
const style_v2 = { padding: '20px', border: '1px solid gray' }; // 0x002 (MỚI!)
<Card style={style_v2} />

// React.memo so sánh:
prevProps.style === nextProps.style
// 0x001 === 0x002 → false
// → Card buộc phải re-render
```

**✅ Giải pháp:**

```javascript
function Parent() {
  const [count, setCount] = useState(0);
  
  // Solution 1: useMemo
  const cardStyle = useMemo(() => ({
    padding: '20px',
    border: '1px solid gray'
  }), []); // Reference ổn định
  
  return (
    <div>
      <Card title="My Card" style={cardStyle} />
      <button onClick={() => setCount(count + 1)}>Count: {count}</button>
    </div>
  );
}

// Solution 2: Constant bên ngoài (TỐT NHẤT cho static values)
const CARD_STYLE = { padding: '20px', border: '1px solid gray' };

function Parent() {
  const [count, setCount] = useState(0);
  
  return (
    <div>
      <Card title="My Card" style={CARD_STYLE} />
      <button onClick={() => setCount(count + 1)}>Count: {count}</button>
    </div>
  );
}
```

### 5.3. Decision Tree: Khi Nào Lo Lắng Về style={{}}?

```
Bạn đang truyền style={{}} vào đâu?
  │
  ├─ DOM element (<div>, <button>, <span>, v.v.)
  │    └─ ✅ LUÔN OK - Không cần lo lắng
  │
  └─ React Component (<Card>, <Button>, v.v.)
       │
       ├─ Component có được wrap với React.memo?
       │    ├─ KHÔNG → ✅ OK - Component sẽ re-render anyway
       │    └─ CÓ → Continue
       │
       └─ Style có giá trị dynamic (phụ thuộc state/props)?
            ├─ CÓ → Dùng useMemo với dependencies
            └─ KHÔNG → Đưa ra constant bên ngoài component
```

### 5.4. Ví Dụ Thực Tế

#### **Ví Dụ 1: Button Component Library**

```javascript
// ❌ BAD: Unnecessary memoization overhead
function MyForm() {
  const buttonStyle = useMemo(() => ({
    padding: '10px 20px',
    backgroundColor: 'blue'
  }), []); // LÃNG PHÍ - button là DOM element
  
  return (
    <button style={buttonStyle}>Submit</button>
  );
}

// ✅ GOOD: Simple và clear
function MyForm() {
  return (
    <button style={{ padding: '10px 20px', backgroundColor: 'blue' }}>
      Submit
    </button>
  );
}
```

#### **Ví Dụ 2: Dynamic Styles**

```javascript
const Card = React.memo(({ isActive, children }) => {
  console.log('Card rendered');
  return <div>{children}</div>;
});

// ❌ BAD: Card re-render mỗi lần
function Parent() {
  const [isActive, setIsActive] = useState(false);
  
  return (
    <Card 
      style={{ 
        backgroundColor: isActive ? 'blue' : 'gray',
        padding: '20px'
      }}
    >
      Content
    </Card>
  );
}

// ✅ GOOD: Memoize với dependencies
function Parent() {
  const [isActive, setIsActive] = useState(false);
  
  const cardStyle = useMemo(() => ({
    backgroundColor: isActive ? 'blue' : 'gray',
    padding: '20px'
  }), [isActive]); // Chỉ tạo object mới khi isActive thay đổi
  
  return <Card style={cardStyle}>Content</Card>;
}

// ✅ BETTER: Dùng className (khuyên dùng cho styles phức tạp)
function Parent() {
  const [isActive, setIsActive] = useState(false);
  
  return (
    <Card className={isActive ? 'card-active' : 'card-inactive'}>
      Content
    </Card>
  );
}
```

#### **Ví Dụ 3: List Items**

```javascript
const ListItem = React.memo(({ item }) => {
  console.log('ListItem rendered:', item.id);
  return (
    <div style={{ padding: '10px', borderBottom: '1px solid #eee' }}>
      {item.name}
    </div>
  );
});

// ⚠️ VẤN ĐỀ: Mỗi item re-render khi list thay đổi
function List({ items }) {
  return (
    <div>
      {items.map(item => (
        <ListItem key={item.id} item={item} />
      ))}
    </div>
  );
}

// ✅ FIX 1: Constant style
const LIST_ITEM_STYLE = {
  padding: '10px',
  borderBottom: '1px solid #eee'
};

const ListItem = React.memo(({ item }) => {
  return (
    <div style={LIST_ITEM_STYLE}>
      {item.name}
    </div>
  );
});

// ✅ FIX 2: CSS class (TỐT NHẤT)
const ListItem = React.memo(({ item }) => {
  return (
    <div className="list-item">
      {item.name}
    </div>
  );
});
```

### 5.5. Inline Style vs CSS Classes

#### **Khi Nào Dùng Inline Style?**

```javascript
// ✅ 1. Dynamic values từ state/props
function ProgressBar({ percent }) {
  return (
    <div 
      style={{ 
        width: `${percent}%`,  // Dynamic
        backgroundColor: 'blue' 
      }}
    />
  );
}

// ✅ 2. Styles phụ thuộc vào JavaScript calculations
function DraggableBox({ x, y }) {
  return (
    <div 
      style={{ 
        transform: `translate(${x}px, ${y}px)`,
        position: 'absolute'
      }}
    />
  );
}

// ✅ 3. Component library cần customizable
function Button({ size = 'medium', color = 'blue', children }) {
  const sizeMap = {
    small: { padding: '5px 10px', fontSize: '12px' },
    medium: { padding: '10px 20px', fontSize: '14px' },
    large: { padding: '15px 30px', fontSize: '16px' }
  };
  
  return (
    <button 
      style={{ 
        ...sizeMap[size],
        backgroundColor: color 
      }}
    >
      {children}
    </button>
  );
}
```

#### **Khi Nào Dùng CSS Classes?**

```javascript
// ✅ 1. Styles tĩnh, không thay đổi
function Card({ children }) {
  return <div className="card">{children}</div>;
}

// ✅ 2. Pseudo-classes/elements (:hover, :before, :after)
// Inline style KHÔNG SUPPORT
function Button({ children }) {
  return (
    <button className="btn-primary"> {/* :hover trong CSS */}
      {children}
    </button>
  );
}

// ✅ 3. Media queries
// Inline style KHÔNG SUPPORT
function ResponsiveGrid({ children }) {
  return <div className="grid">{children}</div>; // @media trong CSS
}

// ✅ 4. Complex animations
function AnimatedBox() {
  return <div className="fade-in">{/* @keyframes trong CSS */}</div>;
}
```

### 5.6. Best Practices Summary

#### **Golden Rules**

```javascript
// Rule 1: DOM element → Inline style thoải mái
<div style={{ padding: '20px' }}>Content</div> // ✅

// Rule 2: Memoized component + static style → Constant
const STYLE = { padding: '20px' };
<MemoComponent style={STYLE} /> // ✅

// Rule 3: Memoized component + dynamic style → useMemo
const style = useMemo(() => ({ 
  padding: isActive ? '20px' : '10px' 
}), [isActive]);
<MemoComponent style={style} /> // ✅

// Rule 4: Complex styles → CSS classes
<div className="complex-card">Content</div> // ✅
```

#### **Checklist**

**Khi viết `style={{}}`:**

- [ ] Đây có phải DOM element? → OK, dùng thoải mái
- [ ] Đây có phải React component được memo? → Cần stable reference
- [ ] Style có giá trị static? → Đưa ra constant
- [ ] Style có giá trị dynamic? → useMemo với dependencies
- [ ] Style có cần :hover, media queries? → Dùng CSS class thay thế

### 5.7. Kết Luận về Style={{}}

**"Có nên dùng `style={{}}` không?"**

**✅ CÓ - trong 95% trường hợp!**

Đừng lo lắng quá mức về inline styles. React và browser đã tối ưu rất tốt. Chỉ cần chú ý:

1. **DOM elements**: Dùng `style={{}}` tự nhiên - không có vấn đề gì
2. **Memoized components**: Đảm bảo style object có stable reference
3. **Measure first**: Chỉ optimize khi có vấn đề performance thực sự

**Mental Model:**

```
Inline style={{}} trên DOM element 
  → React so sánh từng property 
  → Chỉ update properties thay đổi 
  → Performance tốt ✅

Inline style={{}} trên memoized component 
  → React.memo so sánh reference 
  → Reference luôn khác 
  → Component re-render thừa ⚠️
  → Cần useMemo hoặc constant
```

---

## 6. Chiến Lược Tối Ưu - Từ Nguyên Tắc Đến Thực Hành

### 6.1. Nguyên Tắc Thiết Kế

#### **1. State Colocation (Đặt State Gần Nơi Dùng)**

**❌ Anti-pattern: State ở level cao**
```javascript
function App() {
  const [formData, setFormData] = useState({ name: "", email: "" });
  const [uiState, setUiState] = useState({ isModalOpen: false });
  
  // formData thay đổi → TOÀN BỘ App re-render
  return (
    <>
      <Header /> {/* Không liên quan nhưng bị re-render */}
      <Sidebar /> {/* Không liên quan nhưng bị re-render */}
      <Form data={formData} onChange={setFormData} />
      <Footer /> {/* Không liên quan nhưng bị re-render */}
    </>
  );
}
```

**✅ Best practice: State ở level thấp**
```javascript
function App() {
  return (
    <>
      <Header /> {/* Không bao giờ re-render */}
      <Sidebar /> {/* Không bao giờ re-render */}
      <Form /> {/* State được giữ bên trong */}
      <Footer /> {/* Không bao giờ re-render */}
    </>
  );
}

function Form() {
  // State chỉ ảnh hưởng Form và children của nó
  const [formData, setFormData] = useState({ name: "", email: "" });
  return <form>...</form>;
}
```

#### **2. Component Composition (Sử Dụng Children/Slots)**

**❌ Cách thông thường:**
```javascript
function Layout() {
  const [theme, setTheme] = useState("light");
  
  // theme thay đổi → Layout re-render → TẤT CẢ children re-render
  return (
    <div className={theme}>
      <Header />
      <Sidebar />
      <Content />
      <Footer />
    </div>
  );
}
```

**✅ Composition pattern:**
```javascript
function App() {
  // Children được tạo TẠI ĐÂY - không bị ảnh hưởng bởi Layout
  return (
    <Layout>
      <Header />
      <Sidebar />
      <Content />
      <Footer />
    </Layout>
  );
}

function Layout({ children }) {
  const [theme, setTheme] = useState("light");
  // theme thay đổi → chỉ Layout re-render
  // children là prop không đổi → KHÔNG re-render
  return <div className={theme}>{children}</div>;
}
```

### 6.2. Function Component Optimization

#### **useCallback - Khi Nào Dùng**

```javascript
function SearchList({ items }) {
  const [query, setQuery] = useState("");
  
  // ❌ KHÔNG CẦN: không truyền xuống memoized component
  const handleChange = useCallback((e) => {
    setQuery(e.target.value);
  }, []); // Lãng phí - thêm memory để lưu callback
  
  return <input onChange={handleChange} />; // DOM element không care
}
```

```javascript
function SearchList({ items }) {
  const [query, setQuery] = useState("");
  
  // ✅ CẦN: truyền xuống memoized component
  const handleSearch = useCallback((term) => {
    // Logic phức tạp
    console.log("Searching:", term);
  }, []);
  
  return <MemoizedSearchBar onSearch={handleSearch} />;
}

const MemoizedSearchBar = React.memo(SearchBar);
```

#### **useMemo - Khi Nào Dùng**

```javascript
function ProductList({ products }) {
  // ❌ KHÔNG CẦN: Tính toán đơn giản
  const count = useMemo(() => products.length, [products]); // Overhead > benefit
  
  // ✅ CẦN: Tính toán phức tạp
  const expensiveData = useMemo(() => {
    return products
      .filter(p => p.price > 100)
      .map(p => ({ ...p, discount: p.price * 0.1 }))
      .sort((a, b) => b.price - a.price);
  }, [products]); // Re-calculate chỉ khi products thay đổi
  
  // ✅ CẦN: Truyền xuống memoized component
  const config = useMemo(() => ({ 
    theme: "dark", 
    locale: "en" 
  }), []); // Object ổn định
  
  return <MemoizedChild config={config} />;
}
```

#### **React.memo - Khi Nào Dùng**

```javascript
// ❌ KHÔNG CẦN: Component đơn giản, render nhanh
function SimpleText({ text }) {
  return <span>{text}</span>;
}
// React.memo ở đây chỉ thêm overhead

// ✅ CẦN: Component phức tạp, render chậm
const HeavyChart = React.memo(function HeavyChart({ data }) {
  // Render 1000+ SVG elements
  return <svg>...</svg>;
});

// ✅ CẦN: Component trong list lớn
const ListItem = React.memo(function ListItem({ item }) {
  return <div>{item.name}</div>;
});

function List({ items }) {
  return items.map(item => <ListItem key={item.id} item={item} />);
}
```

### 6.3. Context Optimization

#### **Strategy 1: Tách Context Theo Tần Suất Thay Đổi**

```javascript
// ❌ BAD: Everything in one context
const AppContext = React.createContext();

function Provider({ children }) {
  const [user, setUser] = useState(null); // Thay đổi ít
  const [theme, setTheme] = useState("light"); // Thay đổi ít
  const [notifications, setNotifications] = useState([]); // Thay đổi NHIỀU
  
  return (
    <AppContext.Provider value={{ user, theme, notifications }}>
      {children}
    </AppContext.Provider>
  );
}

// Mỗi notification mới → TẤT CẢ consumers re-render
```

```javascript
// ✅ GOOD: Tách context theo tần suất
const UserContext = React.createContext();
const ThemeContext = React.createContext();
const NotificationsContext = React.createContext();

function Providers({ children }) {
  const [user, setUser] = useState(null);
  const [theme, setTheme] = useState("light");
  const [notifications, setNotifications] = useState([]);
  
  return (
    <UserContext.Provider value={user}>
      <ThemeContext.Provider value={theme}>
        <NotificationsContext.Provider value={notifications}>
          {children}
        </NotificationsContext.Provider>
      </ThemeContext.Provider>
    </UserContext.Provider>
  );
}

// Component chỉ subscribe phần cần thiết
function Header() {
  const theme = useContext(ThemeContext); // Chỉ re-render khi theme đổi
  return <header className={theme}>...</header>;
}
```

#### **Strategy 2: Context Selector Pattern**

```javascript
// Custom hook với selector
function useAppContext(selector) {
  const context = useContext(AppContext);
  return useMemo(() => selector(context), [context, selector]);
}

// Sử dụng
function UserName() {
  const name = useAppContext(ctx => ctx.user.name); // Chỉ subscribe name
  return <div>{name}</div>;
}
```

**Hoặc dùng thư viện:**
```javascript
// Zustand - built-in selector
const useStore = create((set) => ({
  user: { name: "John" },
  theme: "light",
}));

function UserName() {
  const name = useStore(state => state.user.name); // ✅ Chỉ re-render khi name thay đổi
  return <div>{name}</div>;
}
```

### 6.4. List Optimization

#### **Virtualization**

```javascript
// ❌ BAD: Render 10,000 items
function List({ items }) {
  return (
    <div>
      {items.map(item => <Item key={item.id} item={item} />)}
    </div>
  );
}
```

```javascript
// ✅ GOOD: Chỉ render visible items
import { FixedSizeList } from 'react-window';

function List({ items }) {
  return (
    <FixedSizeList
      height={600}
      itemCount={items.length}
      itemSize={50}
      width="100%"
    >
      {({ index, style }) => (
        <div style={style}>
          <Item item={items[index]} />
        </div>
      )}
    </FixedSizeList>
  );
}
```

### 6.5. Workflow Tối Ưu

```
1. Viết code rõ ràng, dễ hiểu
         ↓
2. Measure với React DevTools Profiler
         ↓
3. Xác định bottleneck:
   - Component nào render lâu?
   - Component nào render thường xuyên?
   - Component nào unnecessary re-render?
         ↓
4. Apply optimization:
   - State colocation
   - Component composition
   - React.memo + stable props
   - Context splitting
   - Virtualization (cho lists)
         ↓
5. Measure lại → Verify improvement
```

---

## 7. Class Component - Legacy Optimization

### 7.1. PureComponent vs Component

```javascript
// ❌ Component - Luôn re-render khi parent render
class RegularComponent extends React.Component {
  render() {
    return <div>{this.props.name}</div>;
  }
}

// ✅ PureComponent - Shallow compare props & state
class OptimizedComponent extends React.PureComponent {
  render() {
    return <div>{this.props.name}</div>;
  }
}
```

**Cơ chế PureComponent:**
```javascript
// React tự động thực hiện:
shouldComponentUpdate(nextProps, nextState) {
  return (
    !shallowEqual(this.props, nextProps) ||
    !shallowEqual(this.state, nextState)
  );
}
```

### 7.2. shouldComponentUpdate - Custom Logic

```javascript
class SmartComponent extends React.Component {
  shouldComponentUpdate(nextProps, nextState) {
    // ✅ Chỉ re-render khi id hoặc name thay đổi
    return (
      this.props.id !== nextProps.id ||
      this.props.name !== nextProps.name
    );
  }
  
  render() {
    return <div>{this.props.name}</div>;
  }
}
```

### 7.3. Stable Method References

#### **✅ Solution: Class Fields (Khuyên dùng)**

```javascript
class Parent extends React.Component {
  // ✅ Function tạo 1 lần duy nhất
  handleClick = (id) => {
    console.log("Clicked:", id);
  }
  
  render() {
    return (
      <div>
        <Child onClick={this.handleClick} />
      </div>
    );
  }
}
```

### 7.4. Stable Object/Array Props

```javascript
// ✅ GOOD: Constant bên ngoài class
const STATIC_DATA = { id: 1, name: "John" };
const STATIC_ITEMS = [1, 2, 3];

class Parent extends React.Component {
  render() {
    return (
      <Child 
        data={STATIC_DATA}
        items={STATIC_ITEMS}
      />
    );
  }
}
```

---

## 8. Debugging & Profiling - Công Cụ Thực Chiến

### 8.1. React DevTools Profiler

```javascript
import React, { Profiler, useState } from "react";

function onRenderCallback(
  id, // "App"
  phase, // "mount" hoặc "update"
  actualDuration, // thời gian component render thật
  baseDuration,
  startTime,
  commitTime,
  interactions
) {
  console.log(`${id} – ${phase} – took ${actualDuration.toFixed(2)}ms`);
}

function ExpensiveComponent() {
  // mô phỏng component rất nặng
  let total = 0;
  for (let i = 0; i < 10_000; i++) total += i;

  return <div>Expensive Component: {total}</div>;
}

export default function App() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <button onClick={() => setCount((c) => c + 1)}>Re-render App</button>

      <Profiler id="App" onRender={onRenderCallback}>
        <ExpensiveComponent />
        <p>Count: {count}</p>
      </Profiler>
    </div>
  );
}



// LOG: id="App"

// Khi mount lần đầu
// App – mount – took 0.60ms

// Sau khi re-render
// App – update – took 0.20ms
```

### 8.2. Custom Hook để Detect Re-renders

```javascript
function useWhyDidYouUpdate(name, props) {
  const previousProps = useRef();
  
  useEffect(() => {
    if (previousProps.current) {
      const allKeys = Object.keys({ ...previousProps.current, ...props });
      const changedProps = {};
      
      allKeys.forEach(key => {
        if (previousProps.current[key] !== props[key]) {
          changedProps[key] = {
            from: previousProps.current[key],
            to: props[key]
          };
        }
      });
      
      if (Object.keys(changedProps).length) {
         console.log(`[why-did-you-update] ${name}`, JSON.stringify(changedProps));
      }
    }
    
    previousProps.current = props;
  });
}
```
```jsx
import { useState } from "react";

function Child({ count, user }) {
  useWhyDidYouUpdate("Child", { count, user });

  return (
    <div>
      <p>Count: {count}</p>
      <p>User: {user.name}</p>
    </div>
  );
}
export default function App() {
  const [count, setCount] = useState(0);
  const [user, setUser] = useState({ name: "Alice" });

  return (
    <div>
      <button onClick={() => setCount(count + 1)}>Increase Count</button>
      <button onClick={() => setUser({ name: "Alice" })}>
        Re-set User (same value but new object)
      </button>

      <Child count={count} user={user} />
    </div>
  );
}
```

### Console log ví dụ

Mỗi lần props thay đổi, console sẽ log:

```
[why-did-you-update] Child {
  count: { from: 0, to: 1 }
}
```

Hoặc khi set lại object (dù nội dung giống nhau):

```
[why-did-you-update] Child {
  user: { from: {name: "Alice"}, to: {name: "Alice"} }
}
```

---

## 9. Case Studies - Ví Dụ Thực Tế

### Case Study 1: Todo List App - Before & After

#### **✅ Optimized Version**

```javascript
function TodoApp() {
  const [todos, setTodos] = useState([]);
  const [filter, setFilter] = useState('all');
  const [searchTerm, setSearchTerm] = useState('');
  
  // ✅ Stable functions
  const addTodo = useCallback((text) => {
    setTodos(prev => [...prev, { 
      id: Date.now(), 
      text, 
      completed: false 
    }]);
  }, []);
  
  const toggleTodo = useCallback((id) => {
    setTodos(prev => prev.map(t => 
      t.id === id ? { ...t, completed: !t.completed } : t
    ));
  }, []);
  
  // ✅ Memoize expensive calculation
  const filteredTodos = useMemo(() => {
    return todos
      .filter(todo => {
        if (filter === 'active') return !todo.completed;
        if (filter === 'completed') return todo.completed;
        return true;
      })
      .filter(todo => 
        todo.text.toLowerCase().includes(searchTerm.toLowerCase())
      );
  }, [todos, filter, searchTerm]);
  
  return (
    <div>
      <AddTodoForm onAdd={addTodo} />
      <FilterBar filter={filter} onChange={setFilter} />
      <SearchBar value={searchTerm} onChange={setSearchTerm} />
      <TodoList 
        todos={filteredTodos} 
        onToggle={toggleTodo} 
      />
    </div>
  );
}

const AddTodoForm = React.memo(({ onAdd }) => {
  const [text, setText] = useState('');
  
  const handleSubmit = (e) => {
    e.preventDefault();
    if (text.trim()) {
      onAdd(text);
      setText('');
    }
  };
  
  return (
    <form onSubmit={handleSubmit}>
      <input value={text} onChange={e => setText(e.target.value)} />
      <button type="submit">Add</button>
    </form>
  );
});

const TodoItem = React.memo(({ todo, onToggle }) => {
  const handleClick = useCallback(() => {
    onToggle(todo.id);
  }, [todo.id, onToggle]);
  
  return (
    <li onClick={handleClick}>
      {todo.completed ? '✓' : '○'} {todo.text}
    </li>
  );
});
```

---

## 10. Tổng Kết - Mental Model

### 10.1. Hiểu Đúng Về React Rendering

```
╔═══════════════════════════════════════════════════════════╗
║  RENDERING ≠ DOM UPDATE                                   ║
║                                                           ║
║  Rendering là tính toán (rất nhanh)                       ║
║  DOM update là vẽ lại UI (có thể chậm)                    ║
║                                                           ║
║  React thông minh: chỉ update DOM khi cần thiết           ║
╚═══════════════════════════════════════════════════════════╝
```

### 10.2. Decision Tree: Khi Nào Optimize?

```
Start
  │
  ├─ User có cảm thấy lag?
  │    ├─ Không → STOP (Không cần optimize)
  │    └─ Có → Continue
  │
  ├─ React DevTools Profiler: Component render > 16ms?
  │    ├─ Không → STOP (Bottleneck ở đâu khác)
  │    └─ Có → Continue
  │
  ├─ Component có unnecessary re-render?
  │    ├─ Không → Optimize thuật toán trong component
  │    └─ Có → Continue
  │
  └─ Apply optimization:
       1. State colocation (Làm đầu tiên)
       2. Component composition
       3. Memoization (React.memo + stable props)
       4. Virtualization (cho lists)
```

### 10.3. Các Quy Tắc Vàng

1. **Đo Lường Trước Khi Tối Ưu**

   * Dùng React DevTools Profiler
   * Xác minh cải thiện sau khi tối ưu

2. **Ưu Tiên Kiến Trúc Hơn Là Tối Ưu Vi Mô**

   * Ưu Tiên `State colocation` hơn `useCallback`
   * Ưu Tiên `Composition` hơn `React.memo` mọi nơi

3. **Memoization Có Chi Phí**

   * Tốn bộ nhớ
   * Tốn chi phí so sánh
   * Chỉ dùng khi lợi ích nhiều hơn chi phí

4. **Reference Identity Là Quan Trọng**

   * Objects/Arrays/Functions: So sánh theo reference
   * Primitives: So sánh theo value
   * Giữ reference ổn định cho các component được memo

5. **Re-render Không Phải Lúc Nào Cũng Xấu**

   * React được thiết kế để render rất nhanh
   * Chỉ tối ưu khi thật sự có vấn đề

6. **Style={{}} Thường Là Ổn**

   * Với DOM elements: Dùng bình thường
   * Với memoized components: Cần reference ổn định
   * Đo lường trước khi lo lắng

---

## 11. Checklist Cuối Cùng

### Pre-Optimization
- [ ] Đã đo performance với React DevTools Profiler?
- [ ] Đã xác định component nào là bottleneck?
- [ ] User có thực sự cảm thấy chậm không?

### Architecture
- [ ] State đã ở level thấp nhất có thể?
- [ ] Có thể dùng composition thay prop drilling?
- [ ] Context đã được tách hợp lý?
- [ ] Static values đã được đặt bên ngoài component?

### Memoization
- [ ] Chỉ memo components phức tạp/chậm?
- [ ] Props truyền vào memoized component đã stable?
- [ ] useCallback chỉ dùng khi thực sự cần?
- [ ] useMemo chỉ cho expensive calculations?
- [ ] Style props cho memoized components đã stable?

### Special Cases
- [ ] Lists lớn đã dùng virtualization?
- [ ] Context value đã được memoized?
- [ ] Forms có thể dùng uncontrolled?

---

**Kết luận cuối cùng:** 

- React rendering là một quá trình đã được tối ưu rất tốt. 
- Hầu hết ứng dụng không cần optimization đặc biệt. 
- Hãy tập trung vào việc viết code rõ ràng, dễ maintain, và chỉ optimize khi có bằng chứng cụ thể về vấn đề performance.
- Đừng lo lắng quá mức về `style={{}}` hay inline functions - chúng thường không phải là vấn đề thực sự!