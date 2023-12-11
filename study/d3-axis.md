## d3 轴

轴组件为位置标尺提供人类可读的参考标记。它适用于大多数刻度类型，包括线性、对数、波段和时间刻度。

对选定的 SVG 容器（通常是单个 G 元素）调用轴组件将填充轴。轴在原点处渲染。若要更改轴相对于图表的位置，请在包含元素上指定一个变换属性。

```js
const gx = svg
  .append("g")
  .attr("transform", `translate(0,${height - marginBottom})`)
  .call(d3.axisBottom(x));
```

如果比例已更改，请再次调用轴组件进行更新。对于平滑动画，可以在过渡时调用它。

```js
gx.transition().duration(750).call(d3.axisBottom(x));
```

轴创建的元素被视为其公共 API 的一部分。您可以应用外部样式表或修改生成的轴元素来自定义轴外观。一个轴由一个类“domain”的路径元素组成，表示刻度域的范围，后面是类“tick”(刻度)的转换 g 元素，表示刻度的每个刻度。每个刻度都有一个用于绘制刻度线的线元素和一个用于刻度标签的文本元素。例如，这里是一个典型的底部定向轴：
轴的方向是固定的；若要更改方向，请删除旧轴并创建新轴。

```js
<g fill="none" font-size="10" font-family="sans-serif" text-anchor="middle">
  <path class="domain" stroke="currentColor" d="M0.5,6V0.5H880.5V6"></path>
  <g class="tick" opacity="1" transform="translate(0.5,0)">
    <line stroke="currentColor" y2="6"></line>
    <text fill="currentColor" y="9" dy="0.71em">
      0.0
    </text>
  </g>
  <g class="tick" opacity="1" transform="translate(176.5,0)">
    <line stroke="currentColor" y2="6"></line>
    <text fill="currentColor" y="9" dy="0.71em">
      0.2
    </text>
  </g>
</g>
```

### axisTop(scale)、axisBottom(scale)、axisLeft(scale)、axisRight(scale) 文本元素在上/下/左/右

- [source](https://github.com/d3/d3-axis/blob/main/src/axis.js)

- 要显式设置刻度值，请使用 axis.tickValues。要显式设定刻度格式，请使用 axis.tickFormat。要直接生成刻度值，可使用 scale.ticks。

### axis(context)

将轴渲染到给定的上下文，该上下文可以是 SVG 容器（SVG 或 G 元素）的选择，也可以是相应的转换。

```js
svg
  .append("g")
  .attr("transform", `translate(0,${height - marginBottom})`)
  .call(d3.axisBottom(x));
```

### axis.scale(scale)

如果指定了比例，则设置比例并返回轴。如果未指定比例，则返回当前比例。

```js
const xAxis = d3.axisBottom().scale(x);
```

### axis.ticks(...arguments)

设置渲染轴时将传递给 scale.ticks 和 scale.tickFormat 的参数，并返回轴生成器。

参数的含义取决于轴的刻度类型：最常见的是，参数是刻度数的建议计数（或时间刻度的时间间隔），以及自定义刻度值格式的可选格式说明符。

如果缩放不实现 scale.tick，则此方法没有效果，如带和点缩放。

```js
axis.ticks(20, "s"); // 要以线性比例生成具有 SI 前缀格式的二十个刻度
// other case
axis.ticks(d3.timeMinute.every(15)); // 要使用时间刻度每十五分钟生成一次刻度
axis.ticks(10); // axis.tickArguments的一个方便函数,等同于axis.tickArguments([10]);
```

### axis.tickArguments(arguments)

如果指定了实参，则设置渲染轴时将传递给 scale.ticks 和 scale.tickFormat 的实参，并返回轴生成器。

参数的含义取决于轴的刻度类型：最常见的是，参数是刻度数的建议计数（或时间刻度的时间间隔），以及自定义刻度值格式的可选格式说明符。

如果指定了参数，则如果 scale 不能实现 scale.ticks，则此方法无效，就像带和点比例一样。

```js
axis.tickArguments(); // 如果未指定参数，则返回当前 tick 参数，默认为空数组，即return []
axis.tickArguments([20, "s"]); // 例如，要以线性比例生成具有 SI 前缀格式的二十个刻度
axis.tickArguments([d3.timeMinute.every(15)]);
```

### axis.tickValues(values)

如果指定了可迭代的值，则指定的值将用于刻度，而不是刻度的自动刻度生成器。例如，要以特定值生成记号，请执行以下操作：

显式刻度值优先于 axis.tickArguments 设置的刻度参数。但是，如果未设置刻度格式，则任何刻度参数仍将传递给刻度的 tickFormat 函数。

如果值为空，则清除以前设置的任何显式刻度值，并返回到刻度的刻度生成器。如果未指定值，则返回当前刻度值，默认值为 null

```js
const axis = d3.axisBottom(x).tickValues([1, 2, 3, 5, 8, 13, 21]);
```

### axis.tickFormat(format)

如果指定了格式，则设置刻度格式函数并返回轴。例如，要显示以逗号分组为千的整数：

```js
axis.tickFormat(d3.format(",.0f"));
axis.ticks(10, ",f"); // 更常见的情况是，将格式说明符传递给 axis.ticks，其优点是根据刻度间隔自动设置格式精度
```

### axis.tickSize(size)

```js
const axis = d3.axisBottom(x).tickSize(0); // 如果指定了大小，则将内部和外部刻度大小设置为指定值并返回轴。
axis.tickSize(); // 0 如果未指定大小，则返回当前内部刻度大小，默认为6。
```

### axis.tickSizeInner(size)

内部刻度大小控制刻度线的长度，从轴的原始位置偏移。

```js
const axis = d3.axisBottom(x).tickSizeInner(0); // 如果指定了大小，则将内部刻度大小设置为指定值并返回轴。
axis.tickSizeInner(); // 0 如果未指定大小，则返回当前内部刻度大小，默认为6。
```

### axis.tickSizeOuter(size)

外部刻度大小控制域路径的方形末端的长度，从轴的原始位置偏移。因此，“外部刻度”实际上不是刻度，而是域路径的一部分，其位置由相关刻度的域范围决定。因此，外部记号可能与第一个或最后一个内部记号重叠。外部刻度大小为 0 将抑制域路径的方形端点，而不是生成一条直线。

```js
const axis = d3.axisBottom(x).tickSizeOuter(0); // 如果指定了大小，则将外部刻度大小设置为指定值并返回轴。
axis.tickSizeOuter(); // 0 如果未指定大小，则返回当前内部刻度大小，默认为6。
```

### axis.tickPadding(padding)

```js
const axis = d3.axisBottom(x).tickPadding(0); // 设置指定值和返回轴
axis.tickPadding(); // 0 没指定默认返回3
```

### axis.offset(offset)

在 devicePixelRatio 大于 1 的设备上，像素偏移默认为 0，否则为 0.5。此默认像素偏移可确保低分辨率设备上的边缘清晰。

```js
const axis = d3.axisBottom(x).offset(0); // If offset is specified, sets the pixel offset to the specified value in pixels and returns the axis.
axis.offset(); // 0 If offset is not specified, returns the current pixel offset.
```
