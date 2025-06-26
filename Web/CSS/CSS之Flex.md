# CSS Flexbox 布局详解

## Flexbox 布局

Flexbox（Flexible Box Layout）是 CSS3 提供的一种用于简化复杂布局的布局模型。Flexbox 提供了一种更加灵活和高效的方式来排列元素，并且可以很轻松地实现各种响应式布局。以下是对 Flexbox 布局的详细指导，包括属性的使用和相关代码示例。

## 练习平台

<!-- https://flexboxfroggy.com/ -->

这里推荐一个在线练习平台，可以练习 Flexbox 布局。
[https://flexboxfroggy.com/]

### 1. **基本概念**

Flexbox 布局的核心思想是让容器和子项根据可用空间自动调整大小。通过设定一个父容器为 flex 容器，容器内的子项就会按规则进行排列和布局。

- **容器**：父元素，设置 `display: flex` 或 `display: inline-flex` 的元素。
- **项目**：容器内部的子元素，每一个项目都可以通过 Flexbox 特性进行布局调整。

### 2. **Flexbox 容器属性**

以下属性用于定义 Flexbox 容器的行为：

#### 2.1 **`display: flex`**

设置容器为 flex 容器。

```css
.container {
  display: flex;
}
```

#### 2.2 **`flex-direction`**

定义主轴的方向，即项目排列的方向。默认值是 `row`。

- `row`：水平从左到右排列（默认）。
- `row-reverse`：水平从右到左排列。
- `column`：垂直从上到下排列。
- `column-reverse`：垂直从下到上排列。

```css
.container {
  display: flex;
  flex-direction: row; /* 水平排列 */
}
```

#### 2.3 **`justify-content`**

定义项目在主轴（水平或垂直方向）上的对齐方式。

- `flex-start`：从主轴的起点对齐（默认）。
- `flex-end`：从主轴的终点对齐。
- `center`：在主轴上居中对齐。
- `space-between`：项目之间均匀分布，首尾项目靠边。
- `space-around`：项目之间均匀分布，首尾项目有一半的间隔。

```css
.container {
  display: flex;
  justify-content: center; /* 项目在主轴上居中 */
}
```

#### 2.4 **`align-items`**

定义项目在交叉轴（垂直于主轴的方向）上的对齐方式。

- `flex-start`：从交叉轴的起点对齐。
- `flex-end`：从交叉轴的终点对齐。
- `center`：在交叉轴上居中对齐。
- `stretch`：项目在交叉轴上拉伸以填充容器（默认）。
- `baseline`：项目的基线对齐。

```css
.container {
  display: flex;
  align-items: center; /* 在交叉轴上居中对齐 */
}
```

#### 2.5 **`flex-wrap`**

控制项目是否在一行中排列，还是在多行中换行排列。

- `nowrap`：不换行，所有项目挤在一行内（默认）。
- `wrap`：当项目超出容器宽度时换行。
- `wrap-reverse`：与 `wrap` 相同，但项目按反向换行。

```css
.container {
  display: flex;
  flex-wrap: wrap; /* 项目换行排列 */
}
```

#### 2.6 **`align-content`**

当项目存在多行时，控制这些行在交叉轴上的对齐方式。

- `flex-start`：与交叉轴的起点对齐。
- `flex-end`：与交叉轴的终点对齐。
- `center`：在交叉轴上居中。
- `space-between`：行之间均匀分布，首行和尾行靠边。
- `space-around`：行之间均匀分布，首行和尾行有一半的间隔。
- `stretch`：行将会伸展以占满交叉轴的剩余空间（默认）。

```css
.container {
  display: flex;
  align-content: space-between; /* 行之间均匀分布 */
}
```

### 3. **Flexbox 项目属性**

以下属性用于定义 Flexbox 项目（子元素）如何在 Flex 容器中表现：

#### 3.1 **`flex-grow`**

定义项目如何根据容器中的剩余空间进行扩展，默认值为 `0`。

- 如果值为 `1`，项目将根据剩余空间进行扩展。
- 如果所有项目的 `flex-grow` 都为 `1`，那么它们将等比例扩展。

```css
.item {
  flex-grow: 1; /* 项目扩展填充容器 */
}
```

#### 3.2 **`flex-shrink`**

定义项目如何在容器空间不足时进行收缩，默认值为 `1`。

- 如果值为 `0`，项目不会收缩。

```css
.item {
  flex-shrink: 1; /* 项目会根据容器收缩 */
}
```

#### 3.3 **`flex-basis`**

定义项目的初始大小，可以是固定的长度值（如 `px`）或百分比。

```css
.item {
  flex-basis: 200px; /* 设置初始宽度为200px */
}
```

#### 3.4 **`order`**

定义项目的排列顺序，默认值为 `0`。项目将根据其 `order` 值进行排列，值越小，项目越靠前。

```css
.item1 {
  order: 1; /* 项目排序为第一 */
}
.item2 {
  order: 2; /* 项目排序为第二 */
}
```

#### 3.5 **`align-self`**

允许单独设置某个项目在交叉轴上的对齐方式，覆盖 `align-items` 的值。

- `auto`：继承 `align-items` 的值（默认）。
- `flex-start`：项目与交叉轴的起点对齐。
- `flex-end`：项目与交叉轴的终点对齐。
- `center`：项目在交叉轴上居中对齐。
- `baseline`：项目的基线对齐。
- `stretch`：项目在交叉轴上拉伸。

```css
.item {
  align-self: flex-start; /* 单个项目对齐交叉轴的起点 */
}
```

### 4. **Flexbox 布局示例**

#### 4.1 基本 Flexbox 布局

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Flexbox Layout</title>
    <style>
      .container {
        display: flex;
        justify-content: space-around; /* 项目均匀分布 */
        align-items: center; /* 项目在交叉轴上居中 */
        height: 100vh; /* 高度占满整个视口 */
      }
      .item {
        width: 100px;
        height: 100px;
        background-color: lightblue;
        text-align: center;
        line-height: 100px;
        font-size: 20px;
      }
    </style>
  </head>
  <body>
    <div class="container">
      <div class="item">1</div>
      <div class="item">2</div>
      <div class="item">3</div>
    </div>
  </body>
</html>
```

#### 4.2 多行 Flexbox 布局

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Flexbox Multi-line Layout</title>
    <style>
      .container {
        display: flex;
        flex-wrap: wrap; /* 项目自动换行 */
        gap: 10px; /* 项目之间的间距 */
      }
      .item {
        width: 200px;
        height: 200px;
        background-color: lightcoral;
        text-align: center;
        line-height: 200px;
        font-size: 20px;
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

### 总结

Flexbox 布局提供了一种简单灵活的方式来实现复杂的页面布局。它特别适合用于创建响应式设计和在各种屏幕尺寸下动态调整项目的排列方式。通过充分理解和使用 Flexbox 布局，可以大大提高开发效率并实现更加灵活的布局设计。
