---
category: frontend
tags:
    - Vue3
    - 框架學習
title: Vue 3 新手指南：快速掌握 Composition API 與關鍵特色
description: Vue 3 的 Composition API 為開發者帶來了更靈活的程式碼組織方式。本文將帶你深入了解 Composition API 的核心概念，並透過實際範例展示如何運用這些新特性來提升開發效率。
language: zh-TW
date: 2024-06-29T10:31:00Z
image: /vue3_composition_api_guide.png
---

## 前言

Vue 3 推出的 Composition API，讓開發者能以更彈性的方式組織與重用邏輯。這種新的編寫方式解決了 Vue 2 中 Options API 的諸多限制，特別是在大型應用程式中程式碼組織與邏輯重用的問題。

本文將帶你全面了解 Composition API 的核心概念，並透過實例說明如何在實際專案中充分發揮其優勢。無論你是 Vue 新手還是有經驗的開發者，都能從中獲得實用的技巧與深入的理解。

## 為什麼需要 Composition API？

在深入學習之前，讓我們先了解為什麼 Vue 3 要引入 Composition API：

1. **更好的程式碼組織**：不再按照 Options（data、methods、computed）分類，而是按照功能邏輯組織
2. **更強的邏輯重用能力**：無需使用 mixins 或複雜的高階元件，直接抽離可重用邏輯
3. **更好的型別推導**：對 TypeScript 支援更友好
4. **更小的打包體積**：支援 Tree-shaking，僅打包使用的功能

## Composition API 基礎

### 1. setup 函式與 <script setup>

`setup` 是 Composition API 的核心入口。你可以選擇使用函式形式或更簡潔的 `<script setup>` 語法。

#### 函式形式的 setup：

```vue
<script>
export default {
    setup() {
        const count = ref(0);

        function increment() {
            count.value++;
        }

        // 必須明確返回想要在模板中使用的項目
        return {
            count,
            increment,
        };
    },
};
</script>
```

#### 使用 <script setup>（推薦）：

```vue
<script setup>
import { ref } from 'vue';

// 在 <script setup> 中宣告的變數和函式會自動暴露給模板使用
const count = ref(0);

function increment() {
  count.value++;
}

// 不需要 return 語句
</script>

<template>
  <div>
    <p>目前計數: {{ count }}</p>
    <button @click="increment">增加</button>
  </div>
</template>
```

`<script setup>` 語法更簡潔，也是 Vue 3 中推薦的做法。它不僅減少了樣板程式碼，同時也優化了編譯產出結果。

### 2. ref 與 reactive：響應式數據的基石

Vue 3 提供了兩個主要的響應式 API：`ref` 和 `reactive`。理解它們的區別與使用場景非常重要。

#### ref：適用於任何類型的數據

```vue
<script setup>
import { ref } from "vue";

// 基本類型
const count = ref(0);
const name = ref("Hex School");
const isActive = ref(true);

// 複雜類型
const user = ref({ id: 1, name: "Hex" });
const items = ref([1, 2, 3]);

// 訪問或修改時需要使用 .value
console.log(count.value); // 0
count.value++; // 1

// 物件內部也是響應式的
user.value.name = "Vue School"; // 會觸發視圖更新
</script>
```

#### reactive：只適用於物件類型

```vue
<script setup>
import { reactive } from "vue";

// 物件類型
const user = reactive({ id: 1, name: "Hex" });
const items = reactive([1, 2, 3]);

// 直接訪問或修改，不需要 .value
console.log(user.name); // 'Hex'
user.name = "Vue School"; // 會觸發視圖更新

// 但不能重新賦值整個物件！這會丟失響應性
user = reactive({ id: 2, name: "New User" }); // ❌ 錯誤做法
</script>
```

#### 什麼時候用 ref，什麼時候用 reactive？

- 使用 `ref` 當：

    - 需要處理基本類型（字串、數字、布林值）
    - 需要將響應式物件作為參數傳遞或從函式返回
    - 希望保持一致的使用模式

- 使用 `reactive` 當：
    - 只處理物件類型且不需要重新賦值
    - 希望避免 `.value` 的使用
    - 處理深度嵌套的物件

### 3. computed 與 watch：響應式的計算與監聽

#### computed：計算屬性

```vue
<script setup>
import { ref, computed } from "vue";

const price = ref(100);
const quantity = ref(2);

// 基本使用
const total = computed(() => price.value * quantity.value);

// 帶有 getter 和 setter
const totalWithTax = computed({
    get() {
        return (price.value * quantity.value * 1.1).toFixed(2);
    },
    set(newValue) {
        price.value = (newValue / quantity.value / 1.1).toFixed(2);
    },
});

// 使用
console.log(total.value); // 200
totalWithTax.value = 330; // 會修改 price
</script>
```

#### watch：監聽變化

```vue
<script setup>
import { ref, watch } from "vue";

const searchQuery = ref("");
const searchResults = ref([]);
const isLoading = ref(false);

// 基本監聽
watch(searchQuery, (newQuery, oldQuery) => {
    console.log(`查詢從 "${oldQuery}" 變成了 "${newQuery}"`);
});

// 立即執行 + 深度監聽
watch(
    searchQuery,
    async (newQuery) => {
        if (newQuery.length === 0) {
            searchResults.value = [];
            return;
        }

        try {
            isLoading.value = true;
            // 假設有一個 API 函式
            const results = await api.search(newQuery);
            searchResults.value = results;
        } catch (error) {
            console.error(error);
        } finally {
            isLoading.value = false;
        }
    },
    { immediate: true, deep: true }
);

// 監聽多個數據源
const firstName = ref("John");
const lastName = ref("Doe");

watch([firstName, lastName], ([newFirst, newLast], [oldFirst, oldLast]) => {
    console.log(`姓名從 ${oldFirst} ${oldLast} 變成了 ${newFirst} ${newLast}`);
});
</script>
```

#### watchEffect：自動追蹤依賴

```vue
<script setup>
import { ref, watchEffect } from "vue";

const id = ref(1);
const user = ref(null);

// watchEffect 會自動追蹤內部使用的響應式數據
watchEffect(async () => {
    user.value = await fetchUser(id.value);
    console.log(`獲取到 ID 為 ${id.value} 的用戶: ${user.value.name}`);
    // 當 id.value 變化時，此函式會重新執行
});
</script>
```

## 進階應用

### 1. 自訂 Hook（Composable Functions）：邏輯重用的藝術

Composable 是 Vue 3 最強大的特性之一，它讓我們能夠輕鬆地抽離和重用邏輯。以下是一些實用例子：

#### 基本的計數器 Hook

```vue
<script setup>
// useCounter.js
import { ref } from "vue";

export function useCounter(initialValue = 0, step = 1) {
    const count = ref(initialValue);

    function increment() {
        count.value += step;
    }

    function decrement() {
        count.value -= step;
    }

    function reset() {
        count.value = initialValue;
    }

    return {
        count,
        increment,
        decrement,
        reset,
    };
}

// 在元件中使用
import { useCounter } from "./useCounter";

// 可以在多個元件中重用
const { count, increment, decrement, reset } = useCounter(10, 2);
</script>
```

#### 表單處理 Hook

```vue
<script setup>
// useForm.js
import { reactive, computed } from "vue";

export function useForm(initialValues = {}, validationRules = {}) {
    const formData = reactive({ ...initialValues });
    const formErrors = reactive({});
    const isValid = computed(() => Object.keys(formErrors).length === 0);

    function validate() {
        // 清空之前的錯誤
        Object.keys(formErrors).forEach((key) => delete formErrors[key]);

        // 進行驗證
        for (const [field, rules] of Object.entries(validationRules)) {
            for (const [ruleName, ruleValue] of Object.entries(rules)) {
                if (ruleName === "required" && ruleValue && !formData[field]) {
                    formErrors[field] = "此欄位為必填";
                    break;
                }

                if (ruleName === "minLength" && formData[field].length <script ruleValue) {
                    formErrors[field] = `此欄位最少需要 ${ruleValue} 個字元`;
                    break;
                }

                // 可以加入更多驗證規則...
            }
        }

        return isValid.value;
    }

    function resetForm() {
        Object.keys(formData).forEach((key) => {
            formData[key] = initialValues[key] || "";
        });
        Object.keys(formErrors).forEach((key) => delete formErrors[key]);
    }

    return {
        formData,
        formErrors,
        isValid,
        validate,
        resetForm,
    };
}

// 在元件中使用
import { useForm } from "./useForm";

const { formData, formErrors, isValid, validate, resetForm } = useForm(
    { username: "", password: "" },
    {
        username: { required: true, minLength: 3 },
        password: { required: true, minLength: 6 },
    }
);

function onSubmit() {
    if (validate()) {
        // 表單驗證通過，可以提交
        console.log("表單提交", formData);
    }
}
</script>
```

#### API 請求的 Hook

```vue
<script setup>
// useFetch.js
import { ref, computed } from "vue";

export function useFetch(initialUrl, options = {}) {
    const url = ref(initialUrl);
    const data = ref(null);
    const error = ref(null);
    const isLoading = ref(false);

    const hasData = computed(() => !!data.value);
    const hasError = computed(() => !!error.value);

    async function execute(newUrl = url.value) {
        url.value = newUrl;
        data.value = null;
        error.value = null;
        isLoading.value = true;

        try {
            const response = await fetch(url.value, options);
            if (!response.ok) {
                throw new Error(`HTTP error ${response.status}`);
            }
            data.value = await response.json();
        } catch (err) {
            error.value = err;
        } finally {
            isLoading.value = false;
        }
    }

    // 可選：立即執行
    if (options.immediate !== false) {
        execute();
    }

    return {
        url,
        data,
        error,
        isLoading,
        hasData,
        hasError,
        execute,
    };
}

// 在元件中使用
import { useFetch } from "./useFetch";

const { data: users, error, isLoading, execute: fetchUsers } = useFetch("https://jsonplaceholder.typicode.com/users");

// 重新獲取資料
function refreshData() {
    fetchUsers();
}

// 請求不同的 URL
function fetchUserDetails(userId) {
    fetchUsers(`https://jsonplaceholder.typicode.com/users/${userId}`);
}
</script>
```

### 2. 與 Options API 比較

了解 Composition API 和 Options API 的區別，有助於決定何時使用哪種方式：

#### Options API（Vue 2 的主要方式）

```vue
<script>
export default {
    data() {
        return {
            count: 0,
            user: { name: "Hex" },
        };
    },
    computed: {
        doubleCount() {
            return this.count * 2;
        },
    },
    methods: {
        increment() {
            this.count++;
        },
    },
    watch: {
        count(newValue, oldValue) {
            console.log(`Count 從 ${oldValue} 變成了 ${newValue}`);
        },
    },
    created() {
        console.log("元件已建立");
    },
};
</script>
```

#### 相同功能的 Composition API

```vue
<script>
import { ref, computed, watch, onCreated } from "vue";

export default {
    setup() {
        // data
        const count = ref(0);
        const user = ref({ name: "Hex" });

        // computed
        const doubleCount = computed(() => count.value * 2);

        // methods
        function increment() {
            count.value++;
        }

        // watch
        watch(count, (newValue, oldValue) => {
            console.log(`Count 從 ${oldValue} 變成了 ${newValue}`);
        });

        // lifecycle
        onCreated(() => {
            console.log("元件已建立");
        });

        // 返回給模板使用
        return {
            count,
            user,
            doubleCount,
            increment,
        };
    },
};
</script>
```

#### 優缺點比較

| 特性            | Options API                         | Composition API                 |
| --------------- | ----------------------------------- | ------------------------------- |
| 代碼組織        | 按選項類型（data/methods/computed） | 按邏輯功能（用戶管理/購物車）   |
| 邏輯重用        | Mixins（容易衝突和難以追蹤）        | Composables（更清晰且更少衝突） |
| TypeScript 支援 | 一般                                | 卓越                            |
| 學習曲線        | 對初學者友好                        | 需要理解響應式原理              |
| 適用場景        | 小型項目和簡單元件                  | 大型應用和複雜邏輯              |

### 3. 生命週期鉤子

Composition API 提供了生命週期鉤子函式，對應 Options API 中的生命週期鉤子：

```vue
<script>
import {
    onBeforeMount,
    onMounted,
    onBeforeUpdate,
    onUpdated,
    onBeforeUnmount,
    onUnmounted,
    onErrorCaptured,
} from "vue";

export default {
    setup() {
        // 在元件實例創建之後，DOM 渲染之前
        onBeforeMount(() => {
            console.log("元件即將掛載");
        });

        // 在元件掛載到 DOM 後
        onMounted(() => {
            console.log("元件已掛載");
            // 適合進行 DOM 操作、API 調用等
        });

        // 在數據變化導致虛擬 DOM 重新渲染之前
        onBeforeUpdate(() => {
            console.log("元件即將更新");
        });

        // 在虛擬 DOM 重新渲染且更新到真實 DOM 後
        onUpdated(() => {
            console.log("元件已更新");
        });

        // 在元件即將被卸載之前
        onBeforeUnmount(() => {
            console.log("元件即將卸載");
            // 適合清理定時器、解除事件監聽等
        });

        // 在元件被卸載後
        onUnmounted(() => {
            console.log("元件已卸載");
        });

        // 捕獲子元件錯誤
        onErrorCaptured((err, instance, info) => {
            console.error("捕獲到錯誤:", err, instance, info);
            return false; // 防止錯誤繼續傳播
        });

        return {};
    },
};
</script>
```

## 常見陷阱與解決方案

在使用 Composition API 時，有一些常見的陷阱需要注意：

### 1. 忘記使用 .value

```vue
<script setup>
const count = ref(0);

// 錯誤：直接使用 ref 而沒有 .value
function increment() {
    count++; // 無效，不會更新視圖
}

// 正確：使用 .value
function increment() {
    count.value++; // 正確，會更新視圖
}
</script>
```

### 2. 解構丟失響應性

```vue
<script setup>
const user = reactive({ name: "Hex", age: 30 });

// 錯誤：解構會丟失響應性
const { name, age } = user;
function updateAge() {
    age++; // 無效，不會更新視圖
}

// 正確：使用 toRefs 保持響應性
import { toRefs } from "vue";
const { name, age } = toRefs(user);
function updateAge() {
    age.value++; // 正確，會更新視圖
}

// 或者使用計算屬性
const name = computed(() => user.name);
const age = computed({
    get: () => user.age,
    set: (value) => (user.age = value),
});
</script>
```

### 3. 過度巢狀

```vue
<script>
// 不好的做法：過度巢狀
setup() {
  const state = reactive({});

  function feature1() {
    const featureState = reactive({});

    function subFeature() {
      // 更多巢狀...
    }

    return { /* ... */ };
  }

  return { /* ... */ };
}

// 好的做法：抽離成獨立的 composable
function useFeature1() {
  const featureState = reactive({});
  // 實現邏輯...
  return { /* ... */ };
}

function useSubFeature() {
  // 實現邏輯...
  return { /* ... */ };
}

setup() {
  const { /* ... */ } = useFeature1();
  return { /* ... */ };
}
</script>
```

### 4. 未善用 composable 抽離重用邏輯

```vue
<script>
// 不好的做法：在多個元件中重複相似的邏輯
setup() {
  const searchQuery = ref('');
  const results = ref([]);
  const isLoading = ref(false);
  const error = ref(null);

  async function search() {
    isLoading.value = true;
    try {
      results.value = await api.search(searchQuery.value);
    } catch (err) {
      error.value = err;
    } finally {
      isLoading.value = false;
    }
  }

  return { searchQuery, results, isLoading, error, search };
}

// 好的做法：抽離成 useSearch composable
function useSearch() {
  const searchQuery = ref('');
  const results = ref([]);
  const isLoading = ref(false);
  const error = ref(null);

  async function search() {
    isLoading.value = true;
    try {
      results.value = await api.search(searchQuery.value);
    } catch (err) {
      error.value = err;
    } finally {
      isLoading.value = false;
    }
  }

  return { searchQuery, results, isLoading, error, search };
}

// 在多個元件中重用
setup() {
  const { searchQuery, results, isLoading, error, search } = useSearch();
  return { searchQuery, results, isLoading, error, search };
}
</script>
```

## 結語

Composition API 為 Vue 開發帶來了革命性的變化，它讓程式碼組織更靈活，邏輯重用更簡單，並提供了更好的型別支援。雖然學習曲線可能比 Options API 更陡峭，但掌握它能讓你寫出更可維護、更模組化的 Vue 應用。

隨著對 Composition API 的深入學習，你會發現它特別適合處理複雜的狀態管理和業務邏輯。強烈建議在開始一個新的 Vue 3 專案時，優先考慮使用 Composition API，尤其是當專案預計會隨時間成長和擴展。

最後，記住 Vue 3 同時支援 Options API 和 Composition API，你可以根據需要混合使用它們，或者逐步將現有的 Options API 代碼遷移到 Composition API。

## 參考資源

- [Vue 3 官方文件](https://vuejs.org/guide/introduction.html)
- [Composition API 文檔](https://vuejs.org/guide/extras/composition-api-faq.html)
- [Vue 3 Composables 集合](https://vueuse.org/)
- [Vue 3 生態系統探索](https://github.com/vuejs/awesome-vue)
- [Vue Mastery 課程](https://www.vuemastery.com/courses/)
