# Parse Markdown / Markdown 解析器

A simple way to convert markdown files to HTML in your browser.  
一個在瀏覽器中將 markdown 檔案轉換為 HTML 的簡單方法。

## What it does / 它能做什麼

-   ✅ Turn markdown into HTML / 將 markdown 轉換為 HTML
-   ✅ Support tables and strikethrough text / 支援表格和刪除線
-   ✅ Highlight code with colors / 為程式碼添加顏色高亮
-   ✅ Works in any browser / 在任何瀏覽器中都能運作
-   ✅ Only requires minimal setup / 僅需少量設置

## How to use the main function / 如何使用主函數

The easiest way is to use the `main()` function. Just change these settings:  
最簡單的方法是使用 `main()` 函數。只需要更改這些設定：

### Step 1: Copy the HTML file / 步驟 1：複製 HTML 檔案

Copy the script part from the project's index.html to your own project:  
複製專案內的 index.html 的 script 部分到自己的專案：

**Important: Place the script at the bottom of your body tag**  
**重要：將 script 放在 body 標籤內的最下方**

```html
<script type="module">
    import { unified } from "https://esm.sh/unified@11?bundle";
    import remarkParse from "https://esm.sh/remark-parse@11?bundle";
    import remarkRehype from "https://esm.sh/remark-rehype@11?bundle";
    import rehypeStringify from "https://esm.sh/rehype-stringify@10?bundle";
    import grayMatter from "https://esm.sh/gray-matter@4.0.3";

    import remarkGfm from "https://esm.sh/remark-gfm@4.0.1";
    import rehypeShiki from "https://esm.sh/@shikijs/rehype@3.4.2";

    const defaultShikiLangs = [
        "javascript",
        "typescript",
        "css",
        "html",
        "json",
        "markdown",
        "scss",
        "jsx",
        "tsx",
        "vue",
    ];

    async function parseMarkdown({
        remarkParseOptions,
        remarkPlugins = [],
        remarkRehypeOptions,
        rehypePlugins = [],
        source = "",
    }) {
        const { content, data: frontmatter } = grayMatter(source);
        const file = await unified()
            .use(remarkParse, remarkParseOptions)
            .use(remarkPlugins)
            .use(remarkRehype, remarkRehypeOptions)
            .use(rehypePlugins)
            .use(rehypeStringify)
            .process(content);
        return { content: file, frontmatter };
    }

    async function useMarkdown({
        selector = "article",
        mdUrl = "",
        theme = "one-light",
        langs = defaultShikiLangs,
        remarkParseOptions,
        remarkPlugins = [],
        remarkRehypeOptions,
        rehypePlugins = [],
    }) {
        const response = await fetch(mdUrl);
        const data = await response.blob();
        const source = await data.text();
        const { content } = await parseMarkdown({
            remarkPlugins: [remarkGfm, ...remarkPlugins],
            rehypePlugins: [
                [
                    rehypeShiki,
                    {
                        theme,
                        langs,
                    },
                ],
                ...rehypePlugins,
            ],
            remarkParseOptions,
            remarkRehypeOptions,
            source,
        });
        const article = document.querySelector(selector);
        article.innerHTML = content.value;
    }

    async function main() {
        try {
            await useMarkdown({
                mdUrl: "markdowns/vue3_composition_api_guide.md", // markdown 文件路徑
                selector: "article", // css 選擇器 決定 markdown 的顯示位置
                theme: "one-light", // code block 的主題色 參考 https://shiki.matsu.io/themes
                langs: defaultShikiLangs, // 支援的程式語言 參考 https://shiki.matsu.io/languages
            });
        } catch (error) {
            console.error("error: ", error?.message ?? error);
        }
    }
    main();
</script>
```

### Step 2: Change the settings / 步驟 2：更改設定

In the `main()` function, you can change:  
在 `main()` 函數中，你可以更改：

```javascript
async function main() {
    try {
        await useMarkdown({
            mdUrl: "your-file.md", // Your markdown file name / 你的 markdown 檔案名稱
            selector: "article", // Where to show the content / 內容顯示位置
            theme: "github-light", // Color theme / 顏色主題
        });
    } catch (error) {
        console.error("Error:", error);
    }
}
```

### Step 3: Add your markdown file / 步驟 3：添加你的 markdown 檔案

Create a markdown file (like `my-article.md`) in the same folder:  
在同一個資料夾中創建一個 markdown 檔案（如 `my-article.md`）：

````markdown
# My First Article

This is **bold** text and this is _italic_ text.

## Code Example

```javascript
console.log("Hello World!");
```
````

That's it! Open your `index.html` in a browser and you'll see your markdown converted to HTML.  
就這樣！在瀏覽器中開啟你的 `index.html`，你就會看到 markdown 轉換成 HTML。

## Common settings you can change / 常用設定

### Different themes / 不同主題

```javascript
theme: "github-dark"; // Dark theme / 深色主題
theme: "dracula"; // Purple theme / 紫色主題
theme: "nord"; // Blue theme / 藍色主題
```

For more themes, check: https://shiki.matsu.io/themes  
更多主題可以參考：https://shiki.matsu.io/themes

### Different file locations / 不同檔案位置

```javascript
mdUrl: "articles/my-post.md"; // File in a subfolder / 子資料夾中的檔案
mdUrl: "blog/2024/article.md"; // Nested folders / 嵌套資料夾
```

### Show content in different places / 在不同位置顯示內容

```javascript
selector: "#content"; // Show in element with id="content"
selector: ".markdown"; // Show in element with class="markdown"
selector: "main"; // Show in <main> tag
```

### Supported programming languages / 支援的程式語言

You can choose which programming languages to highlight:  
你可以選擇要高亮顯示的程式語言：

```javascript
// Use default languages / 使用預設語言
langs: defaultShikiLangs;

// Or choose specific languages / 或選擇特定語言
langs: ["javascript", "python", "css", "html"];

// Add more languages / 添加更多語言
langs: ["javascript", "typescript", "python", "go", "rust", "php"];
```

**Default supported languages / 預設支援的語言:**

-   JavaScript
-   TypeScript
-   CSS
-   HTML
-   JSON
-   Markdown
-   SCSS
-   JSX
-   TSX
-   Vue

**Popular additional languages / 其他熱門語言:**

-   Python
-   Go
-   Rust
-   PHP
-   Java
-   C++
-   C#

For more available languages, check: https://shiki.matsu.io/languages  
更多可用語言請參考：https://shiki.matsu.io/languages

## Troubleshooting / 問題排解

**Problem: Nothing shows up / 問題：什麼都沒顯示**

1. Check the file name is correct / 檢查檔案名稱是否正確
2. Make sure the markdown file path is correct / 確保 markdown 檔案路徑正確
3. Open browser console (F12) to see errors / 開啟瀏覽器控制台 (F12) 查看錯誤

**Problem: Code has no colors / 問題：程式碼沒有顏色**

Make sure you have the theme setting:  
確保你有主題設定：

```javascript
theme: "github-light"; // Add this line / 添加這一行
```

## Adding information to your markdown / 在 markdown 中添加資訊

You can add information at the top of your markdown file:  
你可以在 markdown 檔案頂部添加資訊：

```markdown
---
title: "My Article"
date: "2024-01-01"
---

# Article content starts here

Your markdown content...
```

**Access this information in JavaScript / 在 JavaScript 中取得這些資訊:**

You can get the frontmatter data from `parseMarkdown`'s return value:  
你可以從 `parseMarkdown` 的回傳值中取得 frontmatter 資料：

```javascript
const result = await parseMarkdown({ source: markdownText });

// Get frontmatter data / 取得 frontmatter 資料
console.log(result.frontmatter.title); // "My Article"
console.log(result.frontmatter.date); // "2024-01-01"

// Get HTML content / 取得 HTML 內容
console.log(result.content.value); // HTML output
```

## Advanced usage (optional) / 進階用法（可選）

For more advanced customization, you can refer to these libraries:  
如需更進階的自訂功能，可以參考這些函式庫：

-   **unified** - Main processing engine / 主要處理引擎: https://unifiedjs.com/
-   **shiki** - Syntax highlighting / 語法高亮: https://shiki.matsu.io/

```javascript
// Example: Direct function usage / 範例：直接使用函數
const result = await parseMarkdown({ source: markdownText });
await useMarkdown({ mdUrl: "file.md", selector: "article" });
```
