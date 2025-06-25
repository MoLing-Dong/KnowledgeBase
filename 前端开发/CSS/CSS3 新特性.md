# CSS3 新特性

## 引入

CSS3 引入了许多新特性，极大地丰富了网页的表现力，简化了布局和动画效果的实现。以下是一些 CSS3 的重要新特性及其使用示例：

### 1. 边框（Border）相关新特性

- `border-radius`：用于设置元素的圆角。
- `box-shadow`：为元素添加阴影效果。

#### 使用示例

```css
.box {
  width: 200px;
  height: 200px;
  background-color: lightblue;
  border-radius: 20px; /* 圆角 */
  box-shadow: 5px 5px 15px rgba(0, 0, 0, 0.5); /* 阴影 */
}
```

### 2. 渐变（Gradient）

- `linear-gradient`：线性渐变。
- `radial-gradient`：径向渐变。

#### 2.1使用示例

```css
.gradient-box {
  width: 300px;
  height: 300px;
  background: linear-gradient(to right, red, yellow); /* 线性渐变 */
}
```

### 3. 过渡（Transition）

- `transition`：用于设置元素状态变化时的过渡效果。

#### 3.1使用示例

```css
.transition-box {
  width: 100px;
  height: 100px;
  background-color: blue;
  transition: background-color 0.5s ease; /* 过渡效果 */
}
.transition-box:hover {
  background-color: green; /* 鼠标悬停时颜色变化 */
}
```

### 4. 动画（Animation）

- `@keyframes`：用于定义动画关键帧。
- `animation`：设置动画属性。

#### 4.1使用示例

```css
@keyframes move {
  from {
    left: 0;
  }
  to {
    left: 100px;
  }
}

.animated-box {
  position: relative;
  width: 100px;
  height: 100px;
  background-color: red;
  animation: move 2s infinite alternate; /* 动画效果 */
}
```

### 5. 弹性布局（Flexbox）

- `display: flex`：定义弹性容器，支持自动调整布局。
- `justify-content`、`align-items`：用于控制子项在主轴和交叉轴上的排列方式。

#### 5.1使用示例

```css
.flex-container {
  display: flex;
  justify-content: center; /* 主轴居中 */
  align-items: center; /* 交叉轴居中 */
}
```

### 6. 多列布局（Multi-column Layout）

- `column-count`：指定列数。
- `column-gap`：设置列之间的间距。

#### 6.1使用示例

```css
.multi-column {
  column-count: 3; /* 三列布局 */
  column-gap: 20px; /* 列间距 */
}
```

### 7. 媒体查询（Media Queries）

- `@media`：用于定义响应式设计规则，根据屏幕尺寸、分辨率等条件应用不同的样式。

#### 7.1使用示例

```css
@media (max-width: 600px) {
  .responsive-box {
    background-color: yellow;
  }
}
```

### 8. 文字阴影（Text Shadow）

- `text-shadow`：用于给文本添加阴影效果。

#### 8.1使用示例

```css
.text-shadow {
  font-size: 24px;
  text-shadow: 2px 2px 5px gray; /* 文本阴影 */
}
```

### 9. 背景相关新特性

- `background-size`：控制背景图片的尺寸。
- `background-clip`：指定背景的绘制区域。

#### 9.1使用示例

```css
.background-box {
  width: 300px;
  height: 200px;
  background: url("image.jpg") no-repeat center;
  background-size: cover; /* 背景图片覆盖整个容器 */
  background-clip: padding-box; /* 背景剪裁到内边距区域 */
}
```

### 10. 网格布局（Grid Layout）

- `display: grid`：定义网格布局。
- `grid-template-columns` 和 `grid-template-rows`：定义网格的列和行。

#### 10.1使用示例

```css
.grid-container {
  display: grid;
  grid-template-columns: 100px 100px 100px; /* 三列网格 */
  grid-template-rows: 100px 100px; /* 两行网格 */
  gap: 10px; /* 网格之间的间距 */
}
```

### 总结

CSS3 的新特性使得网页设计更加简单和直观，尤其在响应式布局、动画和过渡效果等方面。通过使用这些特性，开发者可以构建更具交互性和吸引力的网页。
