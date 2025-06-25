# CSS Grid 布局详解

## 引言

CSS Grid 是一种基于网格的二维布局系统，它提供了比 Flexbox 更强大的布局能力，尤其适用于创建复杂的网页布局。CSS Grid 支持行（rows）和列（columns）的控制，可以在垂直和水平方向上同时对元素进行精确的排列。

### 1. **基本概念**

CSS Grid 的基本单元是网格容器（grid container）和网格项目（grid items）。通过定义网格容器的行和列，可以在其中按需放置子元素，并为每个项目定义布局。

- **网格容器（Grid Container）**：包含网格项目的父元素，定义了行和列的结构。
- **网格项目（Grid Items）**：网格容器内的直接子元素，按照网格布局的规则排列。

### 2. **Grid 容器属性**

#### 2.1 **`display: grid`**

将元素定义为网格容器。

```css
.container {
  display: grid;
}
```

#### 2.2 **`grid-template-columns` 和 `grid-template-rows`**

分别定义网格容器的列和行的数量与大小。可以使用固定值、百分比、`fr`（表示网格剩余空间的分配单位）等定义大小。

- `repeat()`：简化多列或多行的定义。
- `fr`：灵活单位，表示可用空间的比例。

```css
.container {
  display: grid;
  grid-template-columns: repeat(3, 1fr); /* 创建3列，每列平分可用空间 */
  grid-template-rows: 100px 200px; /* 第一行100px，第二行200px */
}
```

#### 2.3 **`gap`**

定义网格单元之间的间距，可以分别设置行间距（`row-gap`）和列间距（`column-gap`）。

```css
.container {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr;
  gap: 20px; /* 设置行和列之间的间距为20px */
}
```

#### 2.4 **`grid-template-areas`**

通过定义网格区域的名称，创建更加直观的布局。每个网格区域可以通过名称直接定位布局。

```css
.container {
  display: grid;
  grid-template-areas:
    "header header header"
    "sidebar content content"
    "footer footer footer"; /* 定义区域名称 */
  grid-template-columns: 1fr 3fr; /* 定义两列，第二列宽度为第一列的3倍 */
  grid-template-rows: auto 1fr auto; /* 定义三行 */
}
```

每个子元素通过 `grid-area` 来指定区域。

```css
.header {
  grid-area: header;
}
.sidebar {
  grid-area: sidebar;
}
.content {
  grid-area: content;
}
.footer {
  grid-area: footer;
}
```

### 3. **Grid 项目属性**

#### 3.1 **`grid-column` 和 `grid-row`**

定义网格项目所占据的列和行。可以使用 `grid-column-start` 和 `grid-column-end` 分别定义项目的起始和结束位置，也可以简写为 `grid-column: start / end`。

```css
.item {
  grid-column: 1 / 3; /* 占据第1列到第3列 */
  grid-row: 2 / 4; /* 占据第2行到第4行 */
}
```

#### 3.2 **`grid-area`**

定义项目所属的网格区域，配合 `grid-template-areas` 属性使用。

```css
.item {
  grid-area: header; /* 项目位于名为 header 的区域 */
}
```

#### 3.3 **`justify-self` 和 `align-self`**

控制单个项目在网格单元中的水平和垂直对齐方式。

- `justify-self`：控制项目的水平对齐方式。

  - `start`：对齐到左侧。
  - `end`：对齐到右侧。
  - `center`：居中对齐。
  - `stretch`：拉伸项目以填满单元格（默认）。

- `align-self`：控制项目的垂直对齐方式。
  - `start`：对齐到顶部。
  - `end`：对齐到底部。
  - `center`：居中对齐。
  - `stretch`：拉伸项目以填满单元格（默认）。

```css
.item {
  justify-self: center; /* 水平居中 */
  align-self: end; /* 垂直底部对齐 */
}
```

### 4. **常见 Grid 布局示例**

#### 4.1 简单网格布局

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Simple Grid Layout</title>
    <style>
      .container {
        display: grid;
        grid-template-columns: 1fr 1fr 1fr; /* 创建三列，每列平分空间 */
        gap: 10px; /* 每个单元格之间有10px的间距 */
      }
      .item {
        background-color: lightblue;
        padding: 20px;
        text-align: center;
      }
    </style>
  </head>
  <body>
    <div class="container">
      <div class="item">1</div>
      <div class="item">2</div>
      <div class="item">3</div>
      <div class="item">4</div>
      <div class="item">5</div>
      <div class="item">6</div>
    </div>
  </body>
</html>
```

#### 4.2 带区域的网格布局

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Grid Areas Layout</title>
    <style>
      .container {
        display: grid;
        grid-template-areas:
          "header header header"
          "sidebar content content"
          "footer footer footer";
        grid-template-columns: 1fr 2fr; /* 左侧为sidebar，右侧为content */
        gap: 10px;
      }
      .header {
        grid-area: header;
        background-color: lightcoral;
      }
      .sidebar {
        grid-area: sidebar;
        background-color: lightgreen;
      }
      .content {
        grid-area: content;
        background-color: lightblue;
      }
      .footer {
        grid-area: footer;
        background-color: lightgray;
      }
      .item {
        padding: 20px;
        text-align: center;
      }
    </style>
  </head>
  <body>
    <div class="container">
      <div class="header item">Header</div>
      <div class="sidebar item">Sidebar</div>
      <div class="content item">Content</div>
      <div class="footer item">Footer</div>
    </div>
  </body>
</html>
```

### 5. **Grid 与 Flexbox 的区别**

- **布局方向**：
  - **Flexbox**：适合于一维布局（单一的行或列布局）。
  - **Grid**：适合于二维布局（同时控制行和列）。
- **项目控制**：
  - **Flexbox**：每个子项目相对于其他子项目进行排列。
  - **Grid**：项目可以完全根据行和列的定义独立控制位置。

### 总结

CSS Grid 布局为复杂的页面布局提供了非常强大的功能。通过灵活的行列控制、网格区域划分以及单元格的对齐，开发者能够以更直观和简洁的方式实现复杂的网页布局。Grid 布局特别适合多维度、复杂的网页设计，与 Flexbox 相辅相成，是现代 CSS 布局技术的核心之一。
