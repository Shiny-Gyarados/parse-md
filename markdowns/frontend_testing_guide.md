---
category: frontend
tags:
    - 測試框架
    - 品質保證
title: 掌握前端測試：從單元測試到端對端測試的完整攻略
description: 前端測試是確保應用程式品質的重要環節。本文將全面介紹前端測試的各個層面，從單元測試到端對端測試，幫助你建立完整的測試策略，提升專案品質。
language: zh-TW
date: 2024-02-07T06:16:00Z
image: /frontend_testing_guide.png
---

## 前言

前端測試是確保應用程式品質的重要環節。隨著前端應用變得越來越複雜，建立完善的測試策略變得尤為重要。本文將帶你了解前端測試的各個層面，從基礎的單元測試到複雜的端對端測試。

當我們投入大量時間開發一個功能時，我們希望它能穩定運行，不會因為未來的改動而意外損壞。測試就是我們的安全網，它能夠捕捉潛在問題、維護程式品質，並為重構提供信心。

在當今的前端開發環境中，測試已經從"奢侈品"變成了"必需品"，尤其是在團隊協作的大型專案中。本文將系統性地介紹前端測試的關鍵概念、工具和最佳實踐，幫助你建立有效的測試策略。

## 測試金字塔

測試金字塔是一個視覺化模型，描述了不同類型測試應有的比例關係。從底部到頂部依次為：單元測試、整合測試、端對端測試。

### 為什麼採用金字塔結構？

這種結構有明確的理由：

- **執行速度**：底層測試執行快，頂層測試執行慢
- **維護成本**：底層測試易於維護，頂層測試維護成本高
- **問題定位**：底層測試失敗時，問題定位精確；頂層測試失敗時，問題範圍較廣

因此，理想的測試分佈應該是：

- 單元測試（70%）：數量最多，覆蓋基礎功能
- 整合測試（20%）：驗證組件協作
- 端對端測試（10%）：檢驗關鍵用戶流程

### 1. 單元測試 (Unit Testing)

單元測試是測試金字塔的基礎，它專注於測試代碼的最小可測試單元（通常是一個函數或組件）。

#### 什麼是好的單元測試？

好的單元測試應該是：

- **快速**：執行時間通常在毫秒級
- **獨立**：不依賴其他測試或外部資源
- **可重複**：每次執行結果一致
- **自我驗證**：明確的通過/失敗標準
- **及時**：理想情況下與產品代碼同時編寫

#### 前端單元測試工具

最流行的前端單元測試工具包括：

- **Jest**：Facebook 開發的零配置測試框架，內置斷言、模擬和覆蓋率報告
- **Vitest**：專為 Vite 項目設計的測試框架，API 兼容 Jest 但速度更快
- **Mocha**：靈活的測試框架，需要配合斷言庫（如 Chai）使用

#### Jest 單元測試實戰

下面是使用 Jest 進行單元測試的實際例子：

```javascript
// utils/calculator.js
export function add(a, b) {
    return a + b;
}

export function subtract(a, b) {
    return a - b;
}

export function multiply(a, b) {
    return a * b;
}

export function divide(a, b) {
    if (b === 0) {
        throw new Error("Division by zero");
    }
    return a / b;
}
```

測試文件：

```javascript
// utils/calculator.test.js
import { add, subtract, multiply, divide } from "./calculator";

describe("Calculator functions", () => {
    // 基本功能測試
    test("adds 1 + 2 to equal 3", () => {
        expect(add(1, 2)).toBe(3);
    });

    test("subtracts 5 - 3 to equal 2", () => {
        expect(subtract(5, 3)).toBe(2);
    });

    test("multiplies 4 * 3 to equal 12", () => {
        expect(multiply(4, 3)).toBe(12);
    });

    test("divides 10 / 2 to equal 5", () => {
        expect(divide(10, 2)).toBe(5);
    });

    // 邊界情況測試
    test("handles zero properly in addition", () => {
        expect(add(0, 5)).toBe(5);
        expect(add(5, 0)).toBe(5);
        expect(add(0, 0)).toBe(0);
    });

    test("handles negative numbers in multiplication", () => {
        expect(multiply(-3, 4)).toBe(-12);
        expect(multiply(3, -4)).toBe(-12);
        expect(multiply(-3, -4)).toBe(12);
    });

    // 例外處理測試
    test("throws error when dividing by zero", () => {
        expect(() => divide(10, 0)).toThrow("Division by zero");
    });
});
```

#### 單元測試 React 組件

使用 React Testing Library 測試 React 組件是目前的最佳實踐：

```jsx
// components/Counter.jsx
import React, { useState } from "react";

function Counter() {
    const [count, setCount] = useState(0);

    return (
        <div>
            <h2 data-testid="count-display">Count: {count}</h2>
            <button data-testid="increment-button" onClick={() => setCount(count + 1)}>
                Increment
            </button>
            <button data-testid="decrement-button" onClick={() => setCount(count - 1)}>
                Decrement
            </button>
        </div>
    );
}

export default Counter;
```

測試檔案：

```jsx
// components/Counter.test.jsx
import React from "react";
import { render, screen, fireEvent } from "@testing-library/react";
import Counter from "./Counter";

describe("Counter component", () => {
    test("renders with initial count of 0", () => {
        render(<Counter />);
        expect(screen.getByTestId("count-display")).toHaveTextContent("Count: 0");
    });

    test("increments count when increment button is clicked", () => {
        render(<Counter />);
        const button = screen.getByTestId("increment-button");

        fireEvent.click(button);
        expect(screen.getByTestId("count-display")).toHaveTextContent("Count: 1");

        fireEvent.click(button);
        expect(screen.getByTestId("count-display")).toHaveTextContent("Count: 2");
    });

    test("decrements count when decrement button is clicked", () => {
        render(<Counter />);
        const button = screen.getByTestId("decrement-button");

        fireEvent.click(button);
        expect(screen.getByTestId("count-display")).toHaveTextContent("Count: -1");

        fireEvent.click(button);
        expect(screen.getByTestId("count-display")).toHaveTextContent("Count: -2");
    });

    test("both buttons work together correctly", () => {
        render(<Counter />);
        const incrementBtn = screen.getByTestId("increment-button");
        const decrementBtn = screen.getByTestId("decrement-button");

        fireEvent.click(incrementBtn); // 1
        fireEvent.click(incrementBtn); // 2
        fireEvent.click(decrementBtn); // 1

        expect(screen.getByTestId("count-display")).toHaveTextContent("Count: 1");
    });
});
```

#### 單元測試中的模擬 (Mocking)

模擬（Mocking）是單元測試中的重要技術，它允許你替換測試中的依賴項：

```javascript
// service/userService.js
import axios from "axios";

export async function fetchUser(id) {
    const response = await axios.get(`/api/users/${id}`);
    return response.data;
}
```

測試文件中模擬 axios：

```javascript
// service/userService.test.js
import axios from "axios";
import { fetchUser } from "./userService";

// 模擬 axios 模組
jest.mock("axios");

describe("userService", () => {
    test("fetchUser returns user data when API call succeeds", async () => {
        // 設置模擬回傳值
        const mockUser = { id: 1, name: "John Doe" };
        axios.get.mockResolvedValue({ data: mockUser });

        // 調用被測試函數
        const user = await fetchUser(1);

        // 斷言
        expect(axios.get).toHaveBeenCalledWith("/api/users/1");
        expect(user).toEqual(mockUser);
    });

    test("fetchUser throws error when API call fails", async () => {
        // 設置模擬錯誤
        const errorMessage = "Network Error";
        axios.get.mockRejectedValue(new Error(errorMessage));

        // 使用 async/await 搭配 expect-throws 模式測試例外
        await expect(fetchUser(1)).rejects.toThrow(errorMessage);
    });
});
```

#### 常見的單元測試模式

1. **AAA 模式 (Arrange-Act-Assert)**

```javascript
test("adding two numbers", () => {
    // Arrange (準備測試條件)
    const a = 5;
    const b = 10;

    // Act (執行被測試的操作)
    const result = add(a, b);

    // Assert (驗證結果)
    expect(result).toBe(15);
});
```

2. **Given-When-Then 模式**

```javascript
test("password validation", () => {
    // Given (給定條件)
    const validPassword = "StrongPass123!";
    const validator = new PasswordValidator();

    // When (當執行某操作)
    const result = validator.validate(validPassword);

    // Then (那麼應該得到特定結果)
    expect(result.isValid).toBe(true);
});
```

#### 單元測試最佳實踐

1. **測試行為，而非實現**

    - 專注於函數的輸入和輸出，而非實現細節
    - 避免測試私有方法或內部狀態

2. **安排測試優先級**

    - 首先測試核心功能和關鍵路徑
    - 然後測試邊界情況和例外處理
    - 最後考慮優化和覆蓋率提升

3. **遵循 F.I.R.S.T 原則**

    - Fast（快速）：測試應該快速執行
    - Independent（獨立）：測試之間不應有依賴
    - Repeatable（可重複）：環境不同結果也應一致
    - Self-validating（自我驗證）：無需人工判斷結果
    - Timely（及時）：理想情況下與產品代碼同時寫

4. **使用恰當的斷言**
    - 精確匹配使用 `toBe()`（嚴格相等 ===）
    - 對象比較使用 `toEqual()`（遞歸比較值）
    - 浮點數比較使用 `toBeCloseTo()`（避免精度問題）
    - 正則匹配使用 `toMatch()`

### 2. 整合測試 (Integration Testing)

- 測試多個單元之間的互動
- 確保組件間的正確協作
- 使用 React Testing Library 等工具

```javascript
// React Testing Library 範例
test("renders login form", () => {
    render(<LoginForm />);
    expect(screen.getByLabelText(/username/i)).toBeInTheDocument();
    expect(screen.getByLabelText(/password/i)).toBeInTheDocument();
});
```

### 3. 端對端測試 (E2E Testing)

- 模擬真實用戶行為
- 使用 Cypress 或 Playwright
- 測試完整的使用者流程

```javascript
// Cypress 範例
describe("Login Flow", () => {
    it("should login successfully", () => {
        cy.visit("/login");
        cy.get('[data-testid="username"]').type("testuser");
        cy.get('[data-testid="password"]').type("password123");
        cy.get('[data-testid="submit"]').click();
        cy.url().should("include", "/dashboard");
    });
});
```

## 測試策略

### 1. 測試覆蓋率

- 使用工具如 Istanbul 追蹤覆蓋率
- 設定合理的覆蓋率目標
- 關注關鍵業務邏輯的測試

### 2. 測試自動化

- 整合到 CI/CD 流程
- 設定自動化測試腳本
- 定期執行測試套件

### 3. 測試維護

- 保持測試程式碼的可讀性
- 定期更新測試案例
- 移除過時的測試

## 最佳實踐

### 1. 測試命名

- 使用描述性的測試名稱
- 遵循 Given-When-Then 模式
- 清楚表達測試目的

### 2. 測試隔離

- 避免測試間的依賴
- 使用適當的測試夾具
- 確保測試的可重複性

### 3. 效能考量

- 優化測試執行時間
- 使用適當的測試工具
- 平衡測試覆蓋率和執行效率

## 常見陷阱

1. 過度測試

    - 避免測試實現細節
    - 關注行為而非實現
    - 保持測試的維護性

2. 測試不穩定

    - 處理非同步操作
    - 避免時間依賴
    - 使用適當的等待機制

3. 測試複雜度
    - 保持測試簡單
    - 避免過度抽象
    - 使用清晰的測試結構

## 結語

前端測試是確保應用程式品質的重要工具。通過建立完善的測試策略，我們可以及早發現問題，提高開發效率，並確保更好的用戶體驗。

測試不應該被視為開發過程中的負擔，而是你的得力助手。好的測試不僅能為你的代碼提供保障，還能形成活的文檔，幫助新成員理解系統行為。隨著專案的發展，測試對於維護程式品質和支持重構變得越來越重要。

## 參考資源

- [Jest 官方文檔](https://jestjs.io/)
- [React Testing Library 文檔](https://testing-library.com/docs/react-testing-library/intro/)
- [Cypress 官方文檔](https://docs.cypress.io/)
- [Playwright 官方文檔](https://playwright.dev/)
- [Kent C. Dodds 的測試文章](https://kentcdodds.com/blog/write-tests)
- [JavaScript 測試最佳實踐](https://github.com/goldbergyoni/javascript-testing-best-practices)
