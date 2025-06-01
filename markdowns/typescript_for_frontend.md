---
category: frontend
tags:
    - TypeScript
    - 程式設計
title: TypeScript 快速上手：強化前端程式碼的安全與可讀性
description: TypeScript 為 JavaScript 帶來了型別系統，大幅提升了程式碼的可維護性。本文將帶你從基礎開始，逐步掌握 TypeScript 的核心概念，並學會如何在前端專案中有效運用。
language: zh-TW
date: 2024-02-17T13:02:00Z
image: /typescript_for_frontend.png
---

# TypeScript 前端開發完全指南

## 前言

TypeScript 是 JavaScript 的超集，它為 JavaScript 添加了靜態型別系統，使我們能夠在開發時就發現潛在的錯誤，並提供更好的開發體驗。隨著前端應用的複雜度不斷提高，TypeScript 已成為現代前端開發的重要工具。

### 為什麼選擇 TypeScript？

- **提早發現錯誤**：在編譯階段就能發現型別相關的錯誤，而非等到執行時
- **更好的開發工具支援**：代碼提示、自動補全、重構工具更加智能
- **更易於維護**：型別定義可以作為文檔，使程式碼更易理解
- **更安全的重構**：改變介面時，編譯器會指出所有需要更新的地方

### TypeScript 在前端生態中的地位

目前，幾乎所有主流的前端框架都對 TypeScript 提供了一流的支援：

- React 官方支援 TypeScript，並提供了完整的型別定義
- Vue 3 使用 TypeScript 重寫，提供了更好的 TypeScript 整合
- Angular 本身就是用 TypeScript 開發的
- Next.js、Nuxt、Svelte 等主流框架也都支援 TypeScript

## 開始使用 TypeScript

### 環境設置

**安裝 TypeScript**

```bash
# 全局安裝
npm install -g typescript

# 專案內安裝
npm install --save-dev typescript
```

**創建 TypeScript 配置文件**

```bash
tsc --init
```

這會生成一個 `tsconfig.json` 文件，你可以根據需要調整配置：

```json
{
    "compilerOptions": {
        "target": "es2016",
        "module": "commonjs",
        "strict": true,
        "esModuleInterop": true,
        "skipLibCheck": true,
        "forceConsistentCasingInFileNames": true,
        "outDir": "./dist",
        "rootDir": "./src"
    },
    "include": ["src/**/*"],
    "exclude": ["node_modules"]
}
```

## TypeScript 基礎

### 1. 基本型別

TypeScript 提供了多種基本型別，涵蓋了 JavaScript 中的所有原始型別：

```typescript
// 基本型別定義
let name: string = "John";
let age: number = 30;
let isActive: boolean = true;
let notSure: any = 4; // 盡量避免使用 any
let nothing: null = null;
let notDefined: undefined = undefined;

// 陣列
let numbers: number[] = [1, 2, 3];
// 或使用泛型語法
let strings: Array<string> = ["a", "b", "c"];

// 元組 - 固定長度和型別的陣列
let tuple: [string, number] = ["age", 25];

// 列舉
enum Color {
    Red,
    Green,
    Blue,
}
let color: Color = Color.Red;

// void - 通常用於沒有返回值的函數
function log(message: string): void {
    console.log(message);
}

// never - 表示永遠不會有返回值的函數（如拋出錯誤或無限循環）
function throwError(message: string): never {
    throw new Error(message);
}
```

### 2. 介面（Interface）

介面是 TypeScript 中定義對象形狀的強大方式：

```typescript
// 定義物件結構
interface User {
    id: number;
    name: string;
    email: string;
    age?: number; // 可選屬性
    readonly createdAt: Date; // 唯讀屬性
}

// 使用介面
const user: User = {
    id: 1,
    name: "John",
    email: "john@example.com",
    createdAt: new Date(),
};

// 擴展介面
interface Employee extends User {
    department: string;
    salary: number;
}

// 介面可以定義函數型別
interface SearchFunction {
    (source: string, subString: string): boolean;
}

// 實現介面
const mySearch: SearchFunction = function (src, sub) {
    return src.search(sub) > -1;
};
```

### 3. 型別別名（Type Alias）

型別別名提供了另一種方式來定義型別：

```typescript
// 定義型別別名
type Point = {
    x: number;
    y: number;
};

// 使用型別別名
const point: Point = { x: 10, y: 20 };

// 聯合型別
type ID = string | number;
let id: ID = 123;
id = "abc"; // 合法

// 型別別名也可以是泛型
type Container<T> = { value: T };
const numberContainer: Container<number> = { value: 123 };
```

**Type vs Interface：如何選擇？**

- 介面可以被合併和擴展，適合定義公共 API
- 型別別名可以表示基本型別、聯合型別、元組等，更加靈活
- 一般來說，如果你不確定使用哪個，先使用介面，需要表示更複雜的型別時再使用型別別名

### 4. 函數型別

TypeScript 對函數參數和返回值提供了強大的型別檢查：

```typescript
// 函數參數和返回值型別
function add(a: number, b: number): number {
    return a + b;
}

// 可選參數
function buildName(firstName: string, lastName?: string): string {
    return lastName ? `${firstName} ${lastName}` : firstName;
}

// 默認參數
function greet(name: string, greeting: string = "Hello"): string {
    return `${greeting}, ${name}!`;
}

// 剩餘參數
function sum(...numbers: number[]): number {
    return numbers.reduce((total, n) => total + n, 0);
}

// 函數重載
function getLength(value: string): number;
function getLength(value: string[]): number;
function getLength(value: string | string[]): number {
    return Array.isArray(value) ? value.length : value.length;
}
```

## 進階型別

### 1. 泛型（Generics）

泛型允許你創建可重用的組件，同時保持型別安全：

```typescript
// 泛型函數
function identity<T>(arg: T): T {
    return arg;
}
const num = identity<number>(123); // 明確指定型別
const str = identity("hello"); // 型別推斷

// 泛型介面
interface GenericResponse<T> {
    data: T;
    status: number;
    message: string;
}

// 使用泛型介面
const userResponse: GenericResponse<User> = {
    data: { id: 1, name: "John", email: "john@example.com", createdAt: new Date() },
    status: 200,
    message: "Success",
};

// 泛型類
class Queue<T> {
    private items: T[] = [];

    enqueue(item: T): void {
        this.items.push(item);
    }

    dequeue(): T | undefined {
        return this.items.shift();
    }
}

// 泛型約束
interface HasLength {
    length: number;
}

function logLength<T extends HasLength>(arg: T): number {
    console.log(arg.length);
    return arg.length;
}

logLength("hello"); // 5
logLength([1, 2, 3]); // 3
// logLength(123);       // 錯誤：number 沒有 length 屬性
```

### 2. 聯合型別與交叉型別

```typescript
// 聯合型別 - 可以是多種型別之一
type StringOrNumber = string | number;

function processValue(value: StringOrNumber) {
    if (typeof value === "string") {
        return value.toUpperCase();
    } else {
        return value.toFixed(2);
    }
}

// 交叉型別 - 結合多個型別
type Person = {
    name: string;
    age: number;
};

type Employee = {
    employeeId: string;
    department: string;
};

type EmployeePerson = Person & Employee;

const employee: EmployeePerson = {
    name: "John",
    age: 30,
    employeeId: "E123",
    department: "Engineering",
};
```

### 3. 型別守衛與型別縮窄

```typescript
// 型別守衛
function isString(value: any): value is string {
    return typeof value === "string";
}

// 使用 typeof 進行型別縮窄
function printValue(value: string | number) {
    if (typeof value === "string") {
        console.log(value.toUpperCase());
    } else {
        console.log(value.toFixed(2));
    }
}

// 使用 instanceof 進行型別縮窄
class Bird {
    fly() {
        console.log("flying...");
    }
}

class Fish {
    swim() {
        console.log("swimming...");
    }
}

function move(animal: Bird | Fish) {
    if (animal instanceof Bird) {
        animal.fly();
    } else {
        animal.swim();
    }
}

// 使用屬性檢查進行型別縮窄
interface Circle {
    kind: "circle";
    radius: number;
}

interface Square {
    kind: "square";
    sideLength: number;
}

type Shape = Circle | Square;

function getArea(shape: Shape) {
    switch (shape.kind) {
        case "circle":
            return Math.PI * shape.radius ** 2;
        case "square":
            return shape.sideLength ** 2;
    }
}
```

### 4. TypeScript 4.x 和 5.x 新特性

```typescript
// 可選鏈操作符
const user = {
    address: {
        street: "Main St",
    },
};
const street = user?.address?.street; // 安全地訪問可能不存在的屬性

// 空值合併操作符
const name = user.name ?? "Anonymous"; // 如果 user.name 是 null 或 undefined，使用默認值

// 模板字面量型別
type EventName = "click" | "scroll" | "mousemove";
type EventHandler<T extends string> = `on${Capitalize<T>}`;
type EventHandlers = EventHandler<EventName>; // "onClick" | "onScroll" | "onMousemove"

// satisfies 操作符 (TypeScript 4.9+)
type RGB = [red: number, green: number, blue: number];
type Color = RGB | { hex: string };

const palette = {
    red: [255, 0, 0],
    green: "#00ff00",
    blue: [0, 0, 255],
} satisfies Record<string, Color>;

// TypeScript 5.0: 裝飾器
@logged
class Person {
    @validateName
    name: string;

    constructor(name: string) {
        this.name = name;
    }
}
```

## 在 React 中使用 TypeScript

### 1. 組件 Props 型別定義

```typescript
// 函數組件
interface ButtonProps {
    text: string;
    onClick: () => void;
    variant?: "primary" | "secondary";
    disabled?: boolean;
    children?: React.ReactNode;
}

const Button: React.FC<ButtonProps> = ({
    text,
    onClick,
    variant = "primary",
    disabled = false,
    children
}) => {
    return (
        <button
            className={`btn btn-${variant}`}
            onClick={onClick}
            disabled={disabled}
        >
            {text}
            {children}
        </button>
    );
};

// 使用組件
<Button
    text="Submit"
    onClick={() => console.log("Clicked!")}
    variant="primary"
>
    <span>🚀</span>
</Button>
```

### 2. 狀態管理

```typescript
// 使用 useState 的型別推斷
const [count, setCount] = useState<number>(0);

// 複雜狀態的型別定義
interface Todo {
    id: number;
    text: string;
    completed: boolean;
}

const [todos, setTodos] = useState<Todo[]>([]);

// 添加新的 Todo
const addTodo = (text: string) => {
    const newTodo: Todo = {
        id: Date.now(),
        text,
        completed: false,
    };
    setTodos([...todos, newTodo]);
};

// 使用 useReducer 的型別
type TodoAction = { type: "ADD"; text: string } | { type: "TOGGLE"; id: number } | { type: "REMOVE"; id: number };

function todoReducer(state: Todo[], action: TodoAction): Todo[] {
    switch (action.type) {
        case "ADD":
            return [...state, { id: Date.now(), text: action.text, completed: false }];
        case "TOGGLE":
            return state.map((todo) => (todo.id === action.id ? { ...todo, completed: !todo.completed } : todo));
        case "REMOVE":
            return state.filter((todo) => todo.id !== action.id);
        default:
            return state;
    }
}

const [todos, dispatch] = useReducer(todoReducer, []);
```

### 3. 事件處理

```typescript
// 按鈕點擊事件
const handleClick = (event: React.MouseEvent<HTMLButtonElement>) => {
    console.log("Button clicked", event.currentTarget.name);
};

// 表單提交事件
const handleSubmit = (event: React.FormEvent<HTMLFormElement>) => {
    event.preventDefault();
    const formData = new FormData(event.currentTarget);
    const email = formData.get("email") as string;
    console.log("Form submitted", email);
};

// 輸入事件
const handleChange = (event: React.ChangeEvent<HTMLInputElement>) => {
    const { name, value } = event.target;
    console.log(`${name}: ${value}`);
};
```

### 4. 使用 useRef 和 forwardRef

```typescript
// 使用 useRef
const inputRef = useRef<HTMLInputElement>(null);

// 聚焦輸入框
const focusInput = () => {
    inputRef.current?.focus();
};

// 使用 forwardRef
interface InputProps {
    label: string;
    value: string;
    onChange: (value: string) => void;
}

const Input = forwardRef<HTMLInputElement, InputProps>(
    ({ label, value, onChange }, ref) => {
        return (
            <div>
                <label>{label}</label>
                <input
                    ref={ref}
                    value={value}
                    onChange={(e) => onChange(e.target.value)}
                />
            </div>
        );
    }
);
```

## TypeScript 與前端生態系統

### 1. 與 Next.js 整合

Next.js 提供了出色的 TypeScript 支援。一些常見的型別定義：

```typescript
// 頁面組件 props
import { GetServerSideProps, NextPage } from 'next';

interface PostProps {
    post: {
        id: number;
        title: string;
        content: string;
    };
}

// 使用 GetServerSideProps 型別
export const getServerSideProps: GetServerSideProps<PostProps> = async (context) => {
    const { id } = context.params as { id: string };
    // 獲取數據...

    return {
        props: {
            post: {
                id: 1,
                title: "Hello World",
                content: "This is my first post"
            }
        }
    };
};

// 使用 NextPage 泛型
const PostPage: NextPage<PostProps> = ({ post }) => {
    return (
        <div>
            <h1>{post.title}</h1>
            <p>{post.content}</p>
        </div>
    );
};

export default PostPage;
```

### 2. 使用 Apollo Client 處理 GraphQL

```typescript
// 定義 Query 型別
import { gql, useQuery } from '@apollo/client';

const GET_USER = gql`
    query GetUser($id: ID!) {
        user(id: $id) {
            id
            name
            email
        }
    }
`;

// 從 GraphQL Schema 生成的型別
interface GetUserQueryVariables {
    id: string;
}

interface GetUserQueryResult {
    user: {
        id: string;
        name: string;
        email: string;
    };
}

// 使用生成的型別
function UserProfile({ userId }: { userId: string }) {
    const { loading, error, data } = useQuery<GetUserQueryResult, GetUserQueryVariables>(
        GET_USER,
        { variables: { id: userId } }
    );

    if (loading) return <p>Loading...</p>;
    if (error) return <p>Error: {error.message}</p>;

    return (
        <div>
            <h1>{data?.user.name}</h1>
            <p>{data?.user.email}</p>
        </div>
    );
}
```

## TypeScript 最佳實踐

### 1. 型別推斷

利用 TypeScript 的型別推斷能力，可以減少冗餘的型別標註：

```typescript
// 不必要的型別標註
const name: string = "John"; // TypeScript 可以推斷這是一個字符串

// 必要的型別標註
function parseCoordinate(coordinate: string): { x: number; y: number } {
    const [x, y] = coordinate.split(",").map(Number);
    return { x, y };
}

// 使用 const 斷言
const colors = ["red", "green", "blue"] as const;
type Color = (typeof colors)[number]; // "red" | "green" | "blue"
```

### 2. 型別定義檔案

為第三方庫創建或擴展型別定義：

```typescript
// 在 src/types/module-name.d.ts 中
declare module "untyped-module" {
    export function doSomething(value: string): number;

    export interface Options {
        timeout: number;
        retry: boolean;
    }

    export default function (options?: Options): void;
}
```

### 3. 型別安全

避免使用 `any`，優先使用 `unknown` 和型別守衛：

```typescript
// 不好的做法
function processData(data: any) {
    data.doSomething(); // 不安全，沒有型別檢查
}

// 好的做法
function processData(data: unknown) {
    if (typeof data === "object" && data !== null && "doSomething" in data) {
        (data as { doSomething: () => void }).doSomething();
    }
}

// 更好的做法 - 使用型別守衛
interface DataWithMethod {
    doSomething: () => void;
}

function isDataWithMethod(data: unknown): data is DataWithMethod {
    return (
        typeof data === "object" &&
        data !== null &&
        "doSomething" in data &&
        typeof (data as any).doSomething === "function"
    );
}

function processData(data: unknown) {
    if (isDataWithMethod(data)) {
        data.doSomething(); // 安全，有型別檢查
    }
}
```

## 高級 TypeScript 技巧

### 1. 映射型別

```typescript
// 將所有屬性設為可選
type Partial<T> = {
    [P in keyof T]?: T[P];
};

// 將所有屬性設為唯讀
type Readonly<T> = {
    readonly [P in keyof T]: T[P];
};

// 自定義映射型別 - 創建表單值型別
type FormValues<T> = {
    [K in keyof T]: {
        value: T[K];
        error?: string;
        touched: boolean;
    };
};

interface UserForm {
    name: string;
    email: string;
    age: number;
}

const userForm: FormValues<UserForm> = {
    name: { value: "", error: undefined, touched: false },
    email: { value: "", error: undefined, touched: false },
    age: { value: 0, error: undefined, touched: false },
};
```

### 2. 條件型別

```typescript
// 基本條件型別
type TypeName<T> = T extends string
    ? "string"
    : T extends number
      ? "number"
      : T extends boolean
        ? "boolean"
        : T extends undefined
          ? "undefined"
          : T extends Function
            ? "function"
            : "object";

// 從聯合型別中排除某個型別
type Exclude<T, U> = T extends U ? never : T;
type T0 = Exclude<"a" | "b" | "c", "a">; // "b" | "c"

// 從聯合型別中提取某個型別
type Extract<T, U> = T extends U ? T : never;
type T1 = Extract<"a" | "b" | "c", "a" | "f">; // "a"

// 從函數型別中獲取返回型別
type ReturnType<T extends (...args: any) => any> = T extends (...args: any) => infer R ? R : any;
```

### 3. 實用工具型別

TypeScript 內置了許多實用的工具型別：

```typescript
// 常用工具型別
interface Todo {
    title: string;
    description: string;
    completed: boolean;
}

// 所有屬性都變為可選
type PartialTodo = Partial<Todo>;
// { title?: string; description?: string; completed?: boolean; }

// 所有屬性都變為必需
type RequiredTodo = Required<Todo>;
// { title: string; description: string; completed: boolean; }

// 所有屬性都變為唯讀
type ReadonlyTodo = Readonly<Todo>;
// { readonly title: string; readonly description: string; readonly completed: boolean; }

// 從一個型別中選取部分屬性
type TodoPreview = Pick<Todo, "title" | "completed">;
// { title: string; completed: boolean; }

// 從一個型別中排除部分屬性
type TodoInfo = Omit<Todo, "completed">;
// { title: string; description: string; }

// 對象中屬性的值的型別
type TodoValues = Record<keyof Todo, string>;
// { title: string; description: string; completed: string; }
```

## 常見陷阱與解決方案

### 1. 過度使用型別

- **問題**：過於複雜的型別定義會降低代碼可讀性
- **解決方案**：拆分複雜型別，使用型別別名提高可讀性

```typescript
// 過於複雜的型別定義
type UserResponseData = {
    data: {
        user: {
            profile: {
                details: {
                    name: string;
                    settings: {
                        theme: "light" | "dark";
                        notifications: boolean;
                    };
                };
            };
        };
    };
};

// 更好的方式：拆分型別定義
type Theme = "light" | "dark";

interface UserSettings {
    theme: Theme;
    notifications: boolean;
}

interface UserDetails {
    name: string;
    settings: UserSettings;
}

interface UserProfile {
    details: UserDetails;
}

interface User {
    profile: UserProfile;
}

interface UserResponse {
    data: {
        user: User;
    };
}
```

### 2. 型別定義不完整

- **問題**：不完整的型別定義可能導致運行時錯誤
- **解決方案**：思考邊界情況，使用聯合型別和可選屬性

```typescript
// 不完整的型別定義
interface ApiResponse {
    data: User[];
}

// 更完整的型別定義
interface ApiResponse {
    data: User[] | null;
    error?: {
        message: string;
        code: number;
    };
    meta: {
        total: number;
        page: number;
        limit: number;
    };
}
```

### 3. 效能考量

- **問題**：過深的型別巢狀和複雜的條件型別可能導致編譯變慢
- **解決方案**：使用型別別名，避免過度复杂的型別計算

```typescript
// 可能導致編譯變慢的型別定義
type DeepNested<T> = {
    [K in keyof T]: T[K] extends object ? DeepNested<T[K]> : T[K] extends Array<infer U> ? Array<DeepNested<U>> : T[K];
};

// 更高效的型別定義
type DeepNestedLevel1<T> = {
    [K in keyof T]: T[K] extends object ? any : T[K];
};

type DeepNestedLevel2<T> = {
    [K in keyof T]: T[K] extends object ? DeepNestedLevel1<T[K]> : T[K];
};

// 根據需要定義更多層級
```

## 結語

TypeScript 為前端開發帶來了更強的型別安全性和更好的開發體驗。通過合理使用 TypeScript 的特性，我們可以寫出更可靠、更易維護的程式碼。隨著前端應用的複雜度不斷提高，TypeScript 的重要性也將持續增長。

在實際項目中，建議逐步採用 TypeScript，從關鍵組件和核心邏輯開始，逐漸擴展到整個代碼庫。記住，TypeScript 是一個工具，其目標是幫助我們寫出更好的代碼，而不是讓開發變得更複雜。

## 參考資源

- [TypeScript 官方文檔](https://www.typescriptlang.org/docs/)
- [TypeScript 中文手冊](https://typescript.bootcss.com/)
- [React TypeScript Cheatsheet](https://react-typescript-cheatsheet.netlify.app/)
- [TypeScript Deep Dive](https://basarat.gitbook.io/typescript/)
- [Effective TypeScript: 62 Specific Ways to Improve Your TypeScript](https://effectivetypescript.com/)
