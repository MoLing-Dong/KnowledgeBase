# 一、关于 CSS 居中的那些事

居中布局是 CSS 中非常常见的需求，无论是行内元素还是块级元素，都有多种方式可以实现水平和垂直居中。本文将从基础到高级，详细介绍各种居中方案，并探讨 2024 年最新的 CSS 属性。

## 1.1 行内元素居中

行内元素的居中实现通常通过 `text-align` 属性完成，针对水平和垂直两个维度进行处理。

### 1.1.1 水平居中

对于行内元素（如 `<span>` 或 `<img>`），使用 `text-align` 可以让它们在父容器中水平居中。

#### 1.1.1.1 DOM 结构

```html
<div class="parent">
  <span class="child">content</span>
</div>
```

#### 1.1.1.2 CSS 实现

```css
.parent {
  background-color: red;
  text-align: center; /* 行内元素水平居中 */
}
```

### 1.1.2 垂直居中

当处理单行文本时，`line-height` 是一种简单有效的垂直居中方式。需要注意的是，这种方式仅适用于单行文本。

#### 1.1.2.1 CSS 实现

```css
.parent {
  height: 200px;
  line-height: 200px; /* 行高设置为容器高度 */
  background-color: red;
}
```

---

## 2.1 块级元素居中

块级元素的居中通常需要通过设置 `margin`、`position` 或者使用更现代的布局方式如 `flex` 和 `grid` 实现。

### 2.1.1 水平居中

最经典的块级元素水平居中方法是 `margin: 0 auto`，其作用是让元素在父容器的宽度范围内自动居中。

#### 2.1.1.1 DOM 结构

```html
<div class="parent">
  <div class="child"></div>
</div>
```

#### 2.1.1.2 CSS 实现

```css
.parent {
  background-color: red;
}
.child {
  width: 100px;
  margin: 0 auto; /* 水平居中 */
  background-color: blue;
}
```

### 2.1.2 水平垂直居中

#### 2.1.2.1 方案 1：定位 + `margin`

通过绝对定位结合 `margin` 来实现元素的水平和垂直居中。这种方式适用于知道元素具体宽高的情况。

```css
.parent {
  position: relative;
  height: 200px;
  background-color: red;
}
.child {
  width: 100px;
  height: 100px;
  position: absolute;
  left: 50%;
  top: 50%;
  margin-left: -50px;
  margin-top: -50px;
  background-color: blue;
}
```

#### 2.1.2.2 方案 2：定位 + `transform`

这是通过 `position: absolute` 和 `transform: translate` 实现居中的简洁方式，它不需要知道元素的具体尺寸。

```css
.parent {
  position: relative;
  height: 200px;
  background-color: red;
}
.child {
  width: 100px;
  height: 100px;
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
  background-color: blue;
}
```

#### 2.1.2.3 方案 3：`flex` 布局

`flexbox` 是一种强大的布局工具，通过设置 `align-items` 和 `justify-content`，可以轻松实现水平和垂直居中。

```css
.parent {
  display: flex;
  align-items: center; /* 垂直居中 */
  justify-content: center; /* 水平居中 */
  height: 200px;
  background-color: red;
}
.child {
  width: 100px;
  height: 100px;
  background-color: blue;
}
```

#### 2.1.2.4 方案 4：`grid` 布局

类似于 `flexbox`，`grid` 布局也可以通过 `place-items` 实现元素的水平和垂直居中。

```css
.parent {
  display: grid;
  place-items: center; /* 水平垂直居中 */
  height: 200px;
  background-color: red;
}
.child {
  width: 100px;
  height: 100px;
  background-color: blue;
}
```

#### 2.1.2.5 方案 5：使用 2024 年最新属性 `align-content`

2024 年引入的 `align-content: center` 属性可以更加简洁地实现垂直居中，并且与 `justify-content` 一起使用时，水平垂直居中会更加方便。

```css
.parent {
  display: flex;
  align-content: center; /* 垂直居中 */
  justify-content: center; /* 水平居中 */
  height: 200px;
  background-color: red;
}
.child {
  width: 100px;
  height: 100px;
  background-color: blue;
}
```

---

## 3.1 其他有趣的居中方式

除了常规的居中方案，CSS 中还有一些特殊的居中实现方法，适用于特定场景。

### 3.1.1 利用 `padding` 实现居中

通过设置 `padding` 和 `background-clip: content-box`，可以将元素按需居中。该方法适合需要一定的内边距时使用。

```css
.parent {
  padding: 20px;
  background-color: red;
}
.child {
  height: 200px;
  background-color: blue;
  background-clip: content-box;
}
```

### 3.1.2 使用伪元素居中

通过为父元素添加伪元素 `::before`，结合 `vertical-align` 和 `inline-block`，可以实现元素居中。

```css
.parent {
  height: 200px;
  text-align: center; /* 水平居中 */
  background-color: red;
}
.child {
  display: inline-block; /* 行内元素变为块级 */
  vertical-align: middle;
  background-color: blue;
}
.parent::before {
  content: "";
  display: inline-block;
  height: 100%;
  vertical-align: middle;
}
```

---

## 4.1 总结

通过上述方法，我们可以根据不同场景，选择合适的居中方案：

- **行内元素的水平居中**：`text-align: center`
- **单行文本的垂直居中**：`line-height`
- **块级元素的水平居中**：`margin: 0 auto`
- **水平垂直居中**：
  - `position: absolute` + `transform: translate`
  - `flex` 布局
  - `grid` 布局
  - **2024 年引入的 `align-content: center`**

随着 CSS 规范的发展，居中布局的实现方式变得越来越简洁和高效。特别是 `flex` 和 `grid` 布局几乎能满足大部分的居中需求。未来，`align-content: center` 等新属性将进一步简化垂直居中的实现，让开发更加轻松。
