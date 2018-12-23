# Knowledge points you should know about Canvas
---

### 1.1概念

#### \<canvas>是一个可以使用脚本(通常为JavaScript)来绘制图形的 HTML 元素.例如,它可以用于绘制图表、制作图片构图或者制作简单的(以及不那么简单的)动画

#### \<canvas> 最早由Apple引入WebKit，用于Mac OS X 的 Dashboard，随后被各个浏览器实现。如今，所有主流的浏览器都支持它

#### canvas的width/height属性值不能使用百分比赋值

#### canvas只支持一种原生的图形绘制：矩形

### 1.2绘制

#### 绘制矩形的几种方法：

- fillRect(x, y, width, height)绘制一个填充的矩形
- strokeRect(x, y, width, height)绘制一个矩形的边框
- clearRect(x, y, width, height)清除指定矩形区域，让清除部分完全透明

#### 绘制路径

2. beginPath()新建一条路径，生成之后，图形绘制命令被指向到路径上生成路径。
1. closePath()闭合路径之后图形绘制命令又重新指向到上下文中。
1. stroke()通过线条来绘制图形轮廓。
1. fill()通过填充路径的内容区域生成实心的图形。
2. moveTo(x, y)将笔触移动到指定的坐标x以及y上,当canvas初始化或者beginPath()调用后，你通常会使用moveTo()函数设置起点
3. lineTo(x, y)绘制一条从当前位置到指定x以及y位置的直线
4. arc(x, y, radius, startAngle, endAngle, anticlockwise)画一个以（x,y）为圆心的以radius为半径的圆弧（圆），从startAngle开始到endAngle结束，按照anticlockwise给定的方向（默认为顺时针）来生成。arc()函数中表示角的单位是弧度，不是角度。角度与弧度的js表达式:弧度=(Math.PI/180)*角度。
5. arcTo(x1, y1, x2, y2, radius)根据给定的控制点和半径画一段圆弧，再以直线连接两个控制点
6. quadraticCurveTo(cp1x, cp1y, x, y)绘制二次贝塞尔曲线，cp1x,cp1y为一个控制点，x,y为结束点。
7. bezierCurveTo(cp1x, cp1y, cp2x, cp2y, x, y)绘制三次贝塞尔曲线，cp1x,cp1y为控制点一，cp2x,cp2y为控制点二，x,y为结束点。
8. rect(x, y, width, height)绘制一个左上角坐标为（x,y），宽高为width以及height的矩形，当该方法执行的时候，moveTo()方法自动设置坐标参数（0,0）。也就是说，当前笔触自动重置回默认坐标。

**注意：1.当前路径为空，即调用beginPath()之后，或者canvas刚建的时候，第一条路径构造命令通常被视为是moveTo（），无论实际上是什么。出于这个原因，几乎总是要在设置路径之后专门指定你的起始位置。2.当你调用fill()函数时，所有没有闭合的形状都会自动闭合，所以你不需要调用closePath()函数。但是调用stroke()时不会自动闭合。**

### 1.3Path2D

#### Path2D()会返回一个新初始化的Path2D对象（可能将某一个路径作为变量——创建一个它的副本，或者将一个包含SVG path数据的字符串作为变量）

	new Path2D();     // 空的Path对象
	new Path2D(path); // 克隆Path对象
	new Path2D(d);    // 从SVG建立Path对象

**所有的路径方法比如moveTo, rect, arc或quadraticCurveTo等，如我们前面见过的，都可以在Path2D中使用。**

#### Path2D API 添加了 addPath作为将path结合起来的方法。当你想要从几个元素中来创建对象时，这将会很实用

#### 新的Path2D API有另一个强大的特点，就是使用SVG path data来初始化canvas上的路径。这将使你获取路径时可以以SVG或canvas的方式来重用它们。

### 1.4Color与Transparency

- fillStyle = color 设置图形的填充颜色。
- strokeStyle = color 设置图形轮廓的颜色。

**color 可以是表示 CSS 颜色值的字符串，渐变对象或者图案对象**

- globalAlpha = transparencyValue 这个属性影响到 canvas 里所有图形的透明度，有效的值范围是 0.0 （完全透明）到 1.0（完全不透明），默认是 1.0

### 1.5Line styles

- lineWidth = value 设置线条宽度。
- lineCap = type 设置线条末端样式。butt，round 和 square。默认是 butt
- lineJoin = type 设定线条与线条间接合处的样式。round, bevel 和 miter。默认是 miter
- miterLimit = value 限制当两条线相交时交接处最大长度；所谓交接处长度（斜接长度）是指线条交接处内角顶点到外角顶点的长度。
- getLineDash() 返回一个包含当前虚线样式，长度为非负偶数的数组。
- setLineDash(segments) 设置当前虚线样式。
- lineDashOffset = value 设置虚线样式的起始偏移量

### 1.6Gradients
- createLinearGradient(x1, y1, x2, y2) 方法接受 4 个参数，表示渐变的起点 (x1,y1) 与终点 (x2,y2)。
- createRadialGradient(x1, y1, r1, x2, y2, r2) 方法接受 6 个参数，前三个定义一个以 (x1,y1) 为原点，半径为 r1 的圆，后三个参数则定义另一个以 (x2,y2) 为原点，半径为 r2 的圆
- gradient.addColorStop(position, color) 方法接受 2 个参数，position 参数必须是一个 0.0 与 1.0 之间的数值，表示渐变中颜色所在的相对位置。例如，0.5 表示颜色会出现在正中间。color 参数必须是一个有效的 CSS 颜色值（如 #FFF， rgba(0,0,0,1)，等等）

### 1.7Patterns

- createPattern(image, type) 该方法接受两个参数。Image 可以是一个 Image 对象的引用，或者另一个 canvas 对象。Type 必须是下面的字符串值之一：repeat，repeat-x，repeat-y 和 no-repeat

### 1.8Shadows

- shadowOffsetX = float | shadowOffsetY = float 分别用来设定阴影在 X 和 Y 轴的延伸距离，它们是不受变换矩阵所影响的。负值表示阴影会往上或左延伸，正值则表示会往下或右延伸，它们默认都为 0。
- shadowBlur = float 用于设定阴影的模糊程度，其数值并不跟像素数量挂钩，也不受变换矩阵的影响，默认为 0。
- shadowColor = color 是标准的 CSS 颜色值，用于设定阴影颜色效果，默认是全透明的黑色

### 1.9Canvas 填充规则节

> 当我们用到 fill（或者 clip和isPointinPath ）你可以选择一个填充规则，该填充规则根据某处在路径的外面或者里面来决定该处是否被填充，这对于自己与自己路径相交或者路径被嵌套的时候是有用的。

##### 两个可能的值：

-  "nonzero": non-zero winding rule, 默认值.
-  "evenodd":  even-odd winding rule.

### 2.0绘制文本

- fillText(text, x, y [, maxWidth]) 在指定的(x,y)位置填充指定的文本，绘制的最大宽度是可选的.
- strokeText(text, x, y [, maxWidth]) 在指定的(x,y)位置绘制文本边框，绘制的最大宽度是可选的

- font = value 当前我们用来绘制文本的样式. 这个字符串使用和 CSS font 属性相同的语法. 默认的字体是 10px sans-serif。
- textAlign = value 文本对齐选项. 可选的值包括：start, end, left, right or center. 默认值是 start。
- textBaseline = value 基线对齐选项. 可选的值包括：top, hanging, middle, alphabetic, ideographic, bottom。默认值是 alphabetic。
- direction = value 文本方向。可能的值包括：ltr, rtl, inherit。默认值是 inherit
- measureText() 将返回一个 TextMetrics对象的宽度、所在像素，这些体现文本特性的属性

