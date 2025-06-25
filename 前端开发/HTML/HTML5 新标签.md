# HTML5 新标签

以下是 HTML5 新标签的详细描述，包含使用方法、主要属性和代码示例：

## 1. 语义化标签

这些标签帮助更好地定义网页的结构，提高网页的可读性和可访问性：

### - `<header>`

**使用方法**：通常放置在页面的顶部，包含网站的标题和导航链接。

**主要属性**：

- `role`：定义该元素的角色（例如，`banner`）。

**代码示例**：

```html
<header role="banner">
  <h1>我的网站</h1>
  <nav>
    <ul>
      <li><a href="#home">首页</a></li>
      <li><a href="#about">关于</a></li>
    </ul>
  </nav>
</header>
```

### - `<main>`

**使用方法**：作为页面的主要内容容器，排除其他部分。

**主要属性**：

- `role`：定义该元素的角色（例如，`main`）。

**代码示例**：

```html
<main role="main">
  <h1>主要内容</h1>
  <p>这里是网站的主要内容部分。</p>
</main>
```

### - `<footer>`

**使用方法**：定义页面或部分的底部内容。

**主要属性**：

- `role`：定义该元素的角色（例如，`contentinfo`）。

**代码示例**：

```html
<footer role="contentinfo">
  <p>© 2024 我的公司</p>
</footer>
```

### - `<nav>`

**使用方法**：用于定义网站的导航部分，包含导航链接。

**主要属性**：

- `role`：定义该元素的角色（例如，`navigation`）。

**代码示例**：

```html
<nav role="navigation">
  <ul>
    <li><a href="#home">首页</a></li>
    <li><a href="#services">服务</a></li>
  </ul>
</nav>
```

### - `<section>`

**使用方法**：用来划分页面中的不同内容块，通常带有标题。

**主要属性**：

- `id`：为该部分定义唯一标识符。

**代码示例**：

```html
<section id="about">
  <h2>关于我们</h2>
  <p>这是关于我们公司的描述。</p>
</section>
```

### - `<article>`

**使用方法**：表示独立的内容，如文章或博客。

**主要属性**：

- `lang`：定义内容的语言。

**代码示例**：

```html
<article lang="zh">
  <h2>一篇有趣的文章</h2>
  <p>这是文章的内容。</p>
</article>
```

### - `<aside>`

**使用方法**：用于侧边栏或附加信息。

**主要属性**：

- `role`：定义该元素的角色（例如，`complementary`）。

**代码示例**：

```html
<aside role="complementary">
  <h2>相关信息</h2>
  <p>一些额外的内容。</p>
</aside>
```

## 2. 多媒体标签

HTML5 增加了对音频和视频的原生支持：

### - `<audio>`

**使用方法**：嵌入音频文件。

**主要属性**：

- `src`：音频文件的 URL。
- `controls`：显示播放控件。

**代码示例**：

```html
<audio src="audio.mp3" controls>您的浏览器不支持音频元素。</audio>
```

### - `<video>`

**使用方法**：嵌入视频文件。

**主要属性**：

- `src`：视频文件的 URL。
- `controls`：显示播放控件。
- `width`、`height`：定义视频的宽度和高度。

**代码示例**：

```html
<video src="video.mp4" controls width="600" height="400">
  您的浏览器不支持视频元素。
</video>
```

### - `<source>`

**使用方法**：为音频或视频元素提供多个文件来源。

**主要属性**：

- `src`：媒体文件的 URL。
- `type`：媒体文件的 MIME 类型。

**代码示例**：

```html
<video controls>
  <source src="video.webm" type="video/webm" />
  <source src="video.mp4" type="video/mp4" />
  您的浏览器不支持视频元素。
</video>
```

### - `<track>`

**使用方法**：为视频或音频元素添加字幕或文本轨道。

**主要属性**：

- `kind`：定义轨道类型（如 `subtitles`、`captions`）。
- `src`：轨道文件的 URL。

**代码示例**：

```html
<video controls>
  <source src="video.mp4" type="video/mp4" />
  <track src="subtitles_en.vtt" kind="subtitles" srclang="en" label="English" />
  您的浏览器不支持视频元素。
</video>
```

## 3. 表单相关标签

HTML5 增强了表单的功能，引入了多个新元素和属性：

### - `<datalist>`

**使用方法**：提供预定义的选项供用户选择。

**主要属性**：

- `id`：为`<input>`元素提供的选项列表标识符。

**代码示例**：

```html
<input list="browsers" name="browser" id="browser" />
<datalist id="browsers">
  <option value="Chrome"></option>
  <option value="Firefox"></option>
  <option value="Safari"></option>
</datalist>
```

### - `<keygen>`

**使用方法**：用于生成公钥的表单字段（已被弃用）。

### - `<output>`

**使用方法**：显示计算结果或用户操作的结果。

**主要属性**：

- `for`：指定与之关联的输入元素。

**代码示例**：

```html
<form oninput="result.value=parseInt(a.value)+parseInt(b.value)">
  <input type="range" id="a" value="50" />+
  <input type="number" id="b" value="50" />
  <output name="result" for="a b">100</output>
</form>
```

### - `<progress>`

**使用方法**：显示任务进度，表示任务的进度，通常用于进度条，例如文件上传进度、下载进度等

**主要属性**：

- `value`：当前进度值。
- `max`：进度的最大值。

**代码示例**：

```html
<progress value="70" max="100">70%</progress>
```

### - `<meter>`

**使用方法**：表示范围内的度量值，表示在一个已知范围内的数值，比如表示分数、投票结果、带宽使用量等。

**主要属性**：

- `value`：当前值。
- `min`、`max`：值的范围。

**代码示例**：

```html
<meter value="0.6" min="0" max="1">60%</meter>
```

## 4. 图形和脚本标签

为了支持更现代的网页应用和功能，HTML5 引入了一些绘图和脚本的标签：

### - `<canvas>`

**使用方法**：用于绘制图像和动画，通过 JavaScript 控制。

**主要属性**：

- `width`：画布的宽度。
- `height`：画布的高度。

**代码示例**：

```html
<canvas
  id="myCanvas"
  width="200"
  height="100"
  style="border:1px solid #000000;"
></canvas>
<script>
  var canvas = document.getElementById("myCanvas");
  var ctx = canvas.getContext("2d");
  ctx.fillStyle = "#FF0000";
  ctx.fillRect(20, 20, 150, 50);
</script>
```

### - `<svg>`

**使用方法**：用于定义矢量图形（Scalable Vector Graphics）。

**代码示例**：

```html
<svg width="100" height="100">
  <circle cx="50" cy="50" r="40" stroke="black" stroke-width="2" fill="red" />
</svg>
```

### - `<mark>`

**使用方法**：表示高亮显示或突出的文本。

**代码示例**：

```html
<p>这是一些 <mark>高亮显示</mark> 的文本。</p>
```

### - `<time>`

**使用方法**：用于表示具体时间或日期。

**主要属性**：

- `datetime`：时间的机器可读格式。

**代码示例**：

```html
<time datetime="2024-09-22">2024年9月22日</time>
```

## 5. 新功能标签

### - `<figure>`

**使用方法**：表示独立的图示、照片、代码或其他可自包含的内容。

**代码示例**：

```html
<figure>
  <img src="image.jpg" alt="示例图像" />
  <figcaption>这是一幅示例图像的说明。</figcaption>
</figure>
```

### - `<figcaption>`

**使用方法**：为`<figure>`元素提供图示的标题或说明。

**代码示例** ：

```html
<figure>
  <img src="image.jpg" alt="示例图像" />
  <figcaption>这是一幅示例图像的说明。</figcaption>
</figure>
```

### - `<details>`

**使用方法**：用于显示或隐藏额外的内容。

**代码示例**：

```html
<details>
  <summary>点击查看详情</summary>
  <p>这里是隐藏的详细内容。</p>
</details>
```

### - `<summary>`

**使用方法**：为`<details>`元素定义一个可点击的标题。

**代码示例**：

```html
<details>
  <summary>更多信息</summary>
  <p>这是一些额外的信息。</p>
</details>
```

### - `<embed>`

**使用方法**：用于嵌入外部资源，如插件、视频、音频等。

**代码示例**：

```html
<embed src="external-content.swf" width="300" height="200" />
```

### - `<wbr>`

**使用方法**：建议在指定位置进行换行。

**代码示例**：

```html
<p>
  这是一个长单词：supercalifragilisticexpialidocious<wbr />，在此处建议换行。
</p>
```

这些新标签增强了 HTML5 的语义化功能，使得网页结构更清晰、功能更丰富，也减少了对额外插件的依赖。
