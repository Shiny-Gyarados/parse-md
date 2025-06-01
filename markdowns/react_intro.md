---
category: frontend
tags:
    - React
    - 入門基礎
title: React 入門不再迷惘：三步驟帶你上手核心概念
description: 你是否對 React 感到好奇，卻苦惱於該如何正式啟動學習之路？身為前端工程師兼職涯諮詢師，我常遇到同學問我：「React 到底該怎麼入門？」為了幫大家減少摸索的時間，我整理出三個循序漸進的關鍵步驟，帶你更輕鬆地掌握 React 核心概念，真正把理論應用在實際專案中。
language: zh-TW
date: 2024-10-11T14:27:00Z
image: /react_intro.png
---

## 前言

React 已經成為前端開發的主流技術之一，無論是求職面試還是實際專案開發，掌握 React 都成為前端工程師的基本需求。但對於初學者來說，面對龐大的 React 生態系統和不斷更新的概念，很容易感到迷失。本文將提供三個循序漸進的步驟，帶你有系統地理解和掌握 React 的核心概念，建立穩固的學習基礎。

## 步驟一：理解核心概念

在開始寫 React 程式碼之前，先了解幾個基礎但關鍵的概念，會幫助你更順暢地進入 React 開發世界。

### 元件（Component）思維

React 的核心理念是將 UI 拆分成獨立、可重用的元件。每個元件負責自己的渲染邏輯和狀態管理。

```jsx
// 一個簡單的函式元件
function Greeting(props) {
    return <h1>你好，{props.name}！</h1>;
}

// 使用元件
<Greeting name="小明" />;
```

這種元件化的思維讓程式碼更加模組化、可維護，並促進了團隊合作開發效率。

### JSX 語法

JSX 是 JavaScript 的語法擴展，允許你在 JavaScript 中寫 HTML 結構，這看起來很奇特，但實際使用後會發現它讓元件結構變得更直觀。

```jsx
// JSX 語法範例
function Card() {
    return (
        <div className="card">
            <h2>卡片標題</h2>
            <p>這是卡片內容</p>
            <button onClick={() => alert("點擊了！")}>點我</button>
        </div>
    );
}
```

注意 JSX 中的一些特性：

- 使用 `className` 而非 `class`（因為 `class` 是 JavaScript 保留字）
- 事件處理使用駝峰命名（如 `onClick` 而非 `onclick`）
- 可以直接在標籤中嵌入 JavaScript 表達式，使用 `{}`

### 單向數據流

React 採用「單向數據流」的理念：數據從父元件向下流向子元件。這種模式讓應用的數據流動變得可預測，更容易追蹤數據變化的來源。

```jsx
function Parent() {
    const [count, setCount] = useState(0);

    return (
        <div>
            <p>計數: {count}</p>
            <Child count={count} onIncrement={() => setCount(count + 1)} />
        </div>
    );
}

function Child({ count, onIncrement }) {
    return <button onClick={onIncrement}>增加計數（當前值：{count}）</button>;
}
```

## 步驟二：掌握狀態管理

React 的魅力在於它對應用狀態的優雅處理方式。隨著你的 React 技能提升，你需要熟悉不同層次的狀態管理方法。

### useState：元件級狀態

`useState` 是 React Hooks 中最基本且常用的一個，用於在函式元件中添加本地狀態：

```jsx
import { useState } from "react";

function Counter() {
    // 宣告一個名為 count 的狀態變數，初始值為 0
    const [count, setCount] = useState(0);

    return (
        <div>
            <p>你點擊了 {count} 次</p>
            <button onClick={() => setCount(count + 1)}>點擊增加</button>
            <button onClick={() => setCount(0)}>重置</button>
        </div>
    );
}
```

要記住 `useState` 的幾個重點：

- 狀態更新是非同步的
- 永遠使用 setter 函數（如 `setCount`）來更新狀態，不要直接修改
- 若新狀態依賴於舊狀態，應使用函數形式更新：`setCount(prevCount => prevCount + 1)`

### useEffect：處理副作用

在 React 元件中，除了渲染 UI 外，我們還需要執行一些「副作用」操作，例如資料獲取、手動操作 DOM、訂閱事件等。`useEffect` Hook 讓我們能在函式元件中執行這些操作：

```jsx
import { useState, useEffect } from "react";

function UserProfile({ userId }) {
    const [user, setUser] = useState(null);
    const [loading, setLoading] = useState(true);

    useEffect(() => {
        // 當 userId 改變時，重新獲取使用者資料
        setLoading(true);

        fetch(`https://api.example.com/users/${userId}`)
            .then((response) => response.json())
            .then((data) => {
                setUser(data);
                setLoading(false);
            })
            .catch((error) => {
                console.error("獲取資料失敗:", error);
                setLoading(false);
            });

        // 清除函數，在元件卸載或 effect 重新執行前調用
        return () => {
            // 可執行清理操作，如取消請求、取消訂閱等
        };
    }, [userId]); // 依賴陣列，只有當 userId 變化時才重新執行

    if (loading) return <p>載入中...</p>;
    if (!user) return <p>找不到使用者</p>;

    return (
        <div>
            <h2>{user.name}</h2>
            <p>Email: {user.email}</p>
            <p>生日: {user.birthdate}</p>
        </div>
    );
}
```

### 進階狀態管理

當應用變得複雜，可能多個元件需要共享狀態時，就需要考慮更強大的狀態管理解決方案：

1. **Context API**：React 內建的跨元件狀態共享方案

    ```jsx
    // 創建 Context
    const ThemeContext = React.createContext("light");

    // 提供 Context
    function App() {
        const [theme, setTheme] = useState("light");

        return (
            <ThemeContext.Provider value={{ theme, setTheme }}>
                <Header />
                <MainContent />
                <Footer />
            </ThemeContext.Provider>
        );
    }

    // 消費 Context
    function ThemedButton() {
        const { theme, setTheme } = useContext(ThemeContext);

        return (
            <button
                onClick={() => setTheme(theme === "light" ? "dark" : "light")}
                style={{ background: theme === "light" ? "#fff" : "#333", color: theme === "light" ? "#333" : "#fff" }}
            >
                切換主題
            </button>
        );
    }
    ```

2. **狀態管理庫**：針對更複雜的應用，可以考慮使用 Redux、MobX 或 Zustand 等專業狀態管理庫。

## 步驟三：構建實戰專案

理論知識的累積必須結合實踐才能真正掌握。循序漸進地完成以下專案練習，將幫助你全面應用 React 知識。

### 專案一：待辦清單應用

這是一個經典的入門專案，能夠幫助你應用基本的狀態管理、條件渲染和列表渲染概念：

```jsx
import { useState } from "react";

function TodoApp() {
    const [todos, setTodos] = useState([]);
    const [input, setInput] = useState("");

    const addTodo = () => {
        if (!input.trim()) return;
        setTodos([...todos, { id: Date.now(), text: input, completed: false }]);
        setInput("");
    };

    const toggleTodo = (id) => {
        setTodos(todos.map((todo) => (todo.id === id ? { ...todo, completed: !todo.completed } : todo)));
    };

    const deleteTodo = (id) => {
        setTodos(todos.filter((todo) => todo.id !== id));
    };

    return (
        <div>
            <h1>待辦清單</h1>
            <div>
                <input value={input} onChange={(e) => setInput(e.target.value)} placeholder="新增待辦事項" />
                <button onClick={addTodo}>新增</button>
            </div>
            <ul>
                {todos.map((todo) => (
                    <li key={todo.id} style={{ textDecoration: todo.completed ? "line-through" : "none" }}>
                        <span onClick={() => toggleTodo(todo.id)}>{todo.text}</span>
                        <button onClick={() => deleteTodo(todo.id)}>刪除</button>
                    </li>
                ))}
            </ul>
            <p>剩餘項目: {todos.filter((todo) => !todo.completed).length}</p>
        </div>
    );
}
```

### 專案二：天氣預報應用

進階一點的練習，加入 API 調用、非同步操作和複雜交互：

```jsx
import { useState, useEffect } from "react";

function WeatherApp() {
    const [location, setLocation] = useState("");
    const [weather, setWeather] = useState(null);
    const [loading, setLoading] = useState(false);
    const [error, setError] = useState(null);

    const searchWeather = async () => {
        if (!location.trim()) return;

        try {
            setLoading(true);
            setError(null);

            // 注意：這裡需要使用真實的天氣 API，以下為示例
            const response = await fetch(`https://api.example.com/weather?location=${location}`);
            if (!response.ok) throw new Error("無法獲取天氣資料");

            const data = await response.json();
            setWeather(data);
        } catch (err) {
            setError(err.message);
        } finally {
            setLoading(false);
        }
    };

    return (
        <div>
            <h1>天氣預報</h1>
            <div>
                <input value={location} onChange={(e) => setLocation(e.target.value)} placeholder="輸入城市名稱" />
                <button onClick={searchWeather} disabled={loading}>
                    查詢
                </button>
            </div>

            {loading && <p>載入中...</p>}
            {error && <p className="error">{error}</p>}

            {weather && (
                <div className="weather-info">
                    <h2>{weather.location}</h2>
                    <p>溫度: {weather.temperature}°C</p>
                    <p>天氣: {weather.conditions}</p>
                    <p>濕度: {weather.humidity}%</p>
                </div>
            )}
        </div>
    );
}
```

### 專案三：整合路由的電子商城

更進階的專案，整合 React Router 實現多頁面應用：

```jsx
import { useState } from "react";
import { BrowserRouter, Routes, Route, Link, useParams } from "react-router-dom";

// 假設的產品數據
const products = [
    { id: 1, name: "智慧手錶", price: 2999, description: "全天候健康監測，智能提醒" },
    { id: 2, name: "無線耳機", price: 1599, description: "主動降噪，長效續航" },
    { id: 3, name: "高效能筆電", price: 35999, description: "輕薄設計，強勁效能" },
];

function App() {
    const [cart, setCart] = useState([]);

    const addToCart = (product) => {
        setCart([...cart, product]);
    };

    return (
        <BrowserRouter>
            <div>
                <nav>
                    <Link to="/">首頁</Link> |<Link to="/products">產品列表</Link> |
                    <Link to="/cart">購物車 ({cart.length})</Link>
                </nav>

                <Routes>
                    <Route path="/" element={<Home />} />
                    <Route path="/products" element={<ProductList />} />
                    <Route path="/products/:id" element={<ProductDetail addToCart={addToCart} />} />
                    <Route path="/cart" element={<Cart cart={cart} />} />
                </Routes>
            </div>
        </BrowserRouter>
    );
}

function Home() {
    return (
        <div>
            <h1>歡迎來到我們的線上商城</h1>
            <p>瀏覽最新產品並享受優惠價格！</p>
            <Link to="/products">開始購物</Link>
        </div>
    );
}

function ProductList() {
    return (
        <div>
            <h2>產品列表</h2>
            <div className="product-grid">
                {products.map((product) => (
                    <div key={product.id} className="product-card">
                        <h3>{product.name}</h3>
                        <p>${product.price}</p>
                        <Link to={`/products/${product.id}`}>查看詳情</Link>
                    </div>
                ))}
            </div>
        </div>
    );
}

function ProductDetail({ addToCart }) {
    const { id } = useParams();
    const product = products.find((p) => p.id === parseInt(id));

    if (!product) return <p>產品不存在</p>;

    return (
        <div>
            <h2>{product.name}</h2>
            <p>價格: ${product.price}</p>
            <p>{product.description}</p>
            <button onClick={() => addToCart(product)}>加入購物車</button>
            <Link to="/products">返回列表</Link>
        </div>
    );
}

function Cart({ cart }) {
    const total = cart.reduce((sum, item) => sum + item.price, 0);

    return (
        <div>
            <h2>購物車</h2>
            {cart.length === 0 ? (
                <p>購物車是空的</p>
            ) : (
                <>
                    <ul>
                        {cart.map((item, index) => (
                            <li key={index}>
                                {item.name} - ${item.price}
                            </li>
                        ))}
                    </ul>
                    <p>總計: ${total}</p>
                    <button>結帳</button>
                </>
            )}
        </div>
    );
}
```

## 進階學習資源

完成上述三個步驟後，你已經具備了 React 開發的基本能力。如果想進一步精進自己的 React 技術，以下資源值得參考：

1. **官方文件**：React 官方文檔是最權威的學習資源，包含了基礎教程和進階指南。
2. **React Query / SWR**：學習更高效的數據獲取方式。
3. **Redux Toolkit / Zustand**：進階狀態管理解決方案。
4. **Next.js / Remix**：基於 React 的框架，提供更完整的應用開發體驗。
5. **React Testing Library**：學習如何測試 React 應用。
6. **TypeScript**：將靜態類型添加到 React 應用，提高代碼質量和開發體驗。

## 結語

React 學習之路雖然初期可能會感到困惑，但透過本文提供的三步驟學習策略，你可以有條理地掌握 React 的核心概念並建立實際開發經驗。記住，成為一名熟練的 React 開發者需要不斷練習和持續學習，當你遇到挑戰時，不要氣餒，這是成長過程中的自然部分。

希望這篇指南能幫助你更有信心地踏上 React 之旅。如果你在學習過程中遇到任何問題，歡迎在下方留言或參考我們的其他學習資源。祝你在前端開發的道路上順利！
