# 如何執行

- cd quiz2
- npm install
- npm run dev

# 問題

## 節點資料轉換

透過 JavaScript 的 pass by reference 特性，將節點轉成樹狀結構

## 元件遞迴

一開始先將資料透過 v-for 渲染到畫面上，但後來發現可以用遞迴做，於是建立 TreeItem.vue，裡面包含迴圈及判斷迴圈的每一個值是否是物件，如果是物件則將值再帶入一次 TreeItem.vue

## 每個節點都要可以獨立開關

一開始想透過單純的 isOpen 變數判斷節點開關，但這樣會導致同一層節點同時開關，後來透過 `<details>` 及 `<summary>` 加上 css 完成。
