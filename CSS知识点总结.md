# CSS知识点总结
### 参考自[HTML中文网](https://www.html.cn/book/css/)

### 1.样式优先级

> 内联样式 >内部样式 >外部样式> 浏览器默认样式
#### 注意：如果外部样式放在内部样式的后面，则外部样式将覆盖内部样式

### 2.background

> background : \[background-color\] | \[background-image\] | \[background-position\]\[/background-size] | [background-repeat] | [background-attachment] | [background-clip] | [background-origin],...

属性|定义
:-|:-
background-color|背景颜色
background-image|背景图片
background-position|定位背景图像的位置，使用百分比、像素值或‘left top’、‘left center’等值
background-size|CSS3以前，背景图像大小由图像的实际大小决定。CSS3可以指定背景图片的大小，在不同的环境中指定背景图片的大小，可以指定像素值、百分比大小（指定的百分比大小是相对于父元素的宽度和高度的百分比的大小），还可以是选项值：cover会保持图像的纵横比并将图像缩放成将完全覆盖背景定位区域的最小大小；contain会保持图像的纵横比并将图像缩放成将适合背景定位区域的最大大小
background-repeat|repeat: 背景图像将向垂直和水平方向重复。这是默认;repeat-x	: 只有水平位置会重复背景图;repeat-y: 只有垂直位置会重复背景图像;no-repeat: background-image不会重复
background-attachment|设置背景图像是否固定或者随着页面的其余部分滚动。scroll: 背景图片随页面的其余部分滚动。这是默认;fixed: 背景图像是固定的
background-clip|border-box:默认值，背景绘制在边框方框内（剪切成边框方框）;padding-box:背景绘制在衬距方框内（剪切成衬距方框）;content-box:背景绘制在内容方框内（剪切成内容方框）
background-origin|背景图片的定位区域（padding-box定位区域包含内边距、border-box定位区域包含内边距、边框、content-box定位区域仅包含内容区域）

### 3.渐变

#### 线性渐变
> background: linear-gradient(direction, color-stop1, color-stop2, ...);
#
> background: repeating-linear-gradient(direction, color-stop1, color-stop2, ...);
#### 径向渐变，可以指定渐变的中心、形状（圆形或椭圆形）、大小。默认情况下，渐变的中心是 center（表示在中心点），渐变的形状是 ellipse（表示椭圆形，circle 表示圆形），渐变的大小是 farthest-corner（表示到最远的角落closest-side、farthest-sid、closest-corner、farthest-corner）
> background: radial-gradient(center, shape size, start-color, ..., last-color);
#
> background: repeating-radial-gradient(center, shape size, start-color, ..., last-color);

#### 渐变角度设置

![](https://i.imgur.com/ZyVbTpB.png)

### 4.文本效果

- text-shadow
- box-shadow

		h1
		{
		//设置阴影的水平位移、阴影的垂直位移、模糊距离、阴影的扩展大小、阴影颜色、内/外阴影
		text-shadow: 5px 5px 5px 6px #FF0000 inset;
		box-shadow: 10px 10px 5px 8px #888888 outset;
		}
- text-overflow 指定应向用户如何显示溢出内容（ellipsis：显示省略号 clip：不显示什么，直接剪裁）
- word-wrap 强制文本换行而不是扩展到外边去（break-word）
- word-break 指定换行的规则，keep-all（会在连接符的地方断开）、break-all

#### 其他的文本属性

属性|描述
:-|:-
hanging-punctuation|规定标点字符是否位于线框之外。
punctuation-trim|规定是否对标点字符进行修剪。
text-align-last|设置如何对齐最后一行或紧挨着强制换行符之前的行。
text-emphasis|向元素的文本应用重点标记以及重点标记的前景色。
text-justify|规定当 text-align 设置为 "justify" 时所使用的对齐方法。
text-outline|规定文本的轮廓。	
text-overflow|规定当文本溢出包含元素时发生的事情。
text-shadow|向文本添加阴影。
text-wrap|规定文本的换行规则。
word-break|规定非中日韩文本的换行规则。
word-wrap|允许对长的不可分割的单词进行分割并换行到下一行。

### 5.字体

	@font-face
	{
	font-family: myFirstFont;//定义字体的名称
	src: url(sansation_bold.woff);//字体文件
	font-weight:bold;
	}
	div
	{
	font-family:myFirstFont;//引用自定义的字体
	}

#### @font-face规则定义

描述符|值|描述
:-|:-|:-
font-family|name|必需。规定字体的名称。
src|URL|必需。定义字体文件的 URL。
font-stretch|normal、condensed、ultra-condensed、extra-condensed、semi-condensed、expanded、semi-expanded、extra-expanded、ultra-expanded|可选。定义如何拉伸字体。默认是 "normal"。
font-style|normal、italic、oblique|可选。定义字体的样式。默认是 "normal"。
font-weight|normal、bold、100~900|可选。定义字体的粗细。默认是 "normal"。
unicode-range|unicode-range|可选。定义字体支持的 UNICODE 字符范围。默认是"U+0-10FFFF"。

### 6.2D/3D转换

> transform
#
> transform-origin: x-axis y-axis z-axis; 规定转换元素的位置（x: left|center|right|length|%; y: top|center|bottom|length|%; z: length）

#### 2D

- translate(X, Y) 根据左(X轴)和顶部(Y轴)位置给定的参数，从当前元素位置移动。

- rotate(X deg) 顺时针旋转X度

- scale(X, Y) 元素增加或减少的大小，取决于宽度（X轴）和高度（Y轴）的参数。scale（2,3）转变宽度为原来的大小的2倍，和其原始大小3倍的高度。

- skew(<angle> [,<angle>]) 包含两个参数值，分别表示X轴和Y轴倾斜的角度，如果第二个参数为空，则默认为0，参数为负表示向相反方向倾斜。

- matrix(宽度伸缩，y轴歪斜， x轴歪斜，高度伸缩，x轴平移，y轴平移)方法将2D变换方法合并成一个。matrix 方法有六个参数，包含旋转，缩放，移动（平移）和倾斜功能

- translateX、translateY、scaleX、scaleY、skewX、skewY

#### 3D

- transform-style属性指定嵌套元素是怎样在三维空间中呈现(使用此属性必须先使用 transform 属性)可选值：

		flat		表示所有子元素在2D平面呈现（默认）。
		preserve-3d	表示所有子元素在3D空间中呈现。

- perspective 设置从何处查看一个元素的角度。

		number	元素距离视图的距离，以像素计。
		none	默认值。与 0 相同。不设置透视。

- perspective-orgin: x-axis y-axis; 设置一个3D元素的基数位置。(x-axis: left|center|right|length|%; y-axis: top|center|bottom|length|%)

- backface-visibility 属性定义当元素不面向屏幕时是否可见(visible|hidden)。

##### 3D转换方法

函数|描述
:-|:-
matrix3d(n,n,n,n,n,n,n,n,n,n,n,n,n,n,n,n)|定义 3D 转换，使用 16 个值的 4x4 矩阵。
translate3d(x,y,z)|定义 3D 转化。
translateX(x)|定义 3D 转化，仅使用用于 X 轴的值。
translateY(y)|定义 3D 转化，仅使用用于 Y 轴的值。
translateZ(z)|定义 3D 转化，仅使用用于 Z 轴的值。
scale3d(x,y,z)|定义 3D 缩放转换。
scaleX(x)|定义 3D 缩放转换，通过给定一个 X 轴的值。
scaleY(y)|定义 3D 缩放转换，通过给定一个 Y 轴的值。
scaleZ(z)|定义 3D 缩放转换，通过给定一个 Z 轴的值。
rotate3d(x,y,z,angle)|定义 3D 旋转。
rotateX(angle)|定义沿 X 轴的 3D 旋转。
rotateY(angle)|定义沿 Y 轴的 3D 旋转。
rotateZ(angle)|定义沿 Z 轴的 3D 旋转。

### 7.过渡（元素从一种样式逐渐改变为另一种的效果）

- 需要指定要添加效果的CSS属性
- 需要指定效果的持续时间。
- 注意：要添加多个样式的变换效果，添加的属性由逗号分隔。eg：transition: width 2s, height 2s, transform 2s;

属性|描述
:-|:-
transition|简写属性，用于在一个属性中设置四个过渡属性。
transition-property|规定应用过渡的 CSS 属性的名称。(none、all、property[相应的属性名])
transition-duration|定义过渡效果花费的时间。默认是 0。
transition-timing-function|规定过渡效果的时间曲线。默认是 "ease"。(linear: 规定以相同速度开始至结束的过渡效果（等于 cubic-bezier(0,0,1,1)）; ease: 规定慢速开始，然后变快，然后慢速结束的过渡效果（cubic-bezier(0.25,0.1,0.25,1)）; ease-in: 规定以慢速开始的过渡效果（等于 cubic-bezier(0.42,0,1,1)）; ease-out: 规定以慢速结束的过渡效果（等于 cubic-bezier(0,0,0.58,1)）; ease-in-out: 规定以慢速开始和结束的过渡效果（等于 cubic-bezier(0.42,0,0.58,1)）; cubic-bezier(n,n,n,n): 在 cubic-bezier 函数中定义自己的值。可能的值是 0 至 1 之间的数值）
transition-delay|规定过渡效果何时开始。默认是 0。

### 8.动画

	@keyframes myfirst
	{
	    0%   {background: red;}//0% 是动画的开始，100% 是动画的完成。
	    25%  {background: yellow;}
	    50%  {background: blue;}
	    100% {background: green;}
	}

属性|描述
:-|:-
@keyframes|规定动画。
animation|所有动画属性的简写属性，除了 animation-play-state 属性。
animation-name|规定 @keyframes 动画的名称。
animation-duration|规定动画完成一个周期所花费的秒或毫秒。默认是 0。
animation-timing-function|规定动画的速度曲线。默认是 "ease"。
animation-fill-mode|规定当动画不播放时（当动画完成时，或当动画有一个延迟未开始播放时），要应用到元素的样式。	
animation-delay|规定动画何时开始。默认是 0。
animation-iteration-count|规定动画被播放的次数。默认是 1。	
animation-direction|规定动画是否在下一周期逆向地播放。默认是 "normal"。
animation-play-state|规定动画是否正在运行或暂停。默认是 "running"。

### 9.多列

属性|描述
:-|:-
column-count|指定元素应该被分割的列数。
column-fill|	指定如何填充列
column-gap	|指定列与列之间的间隙
column-rule	|所有 column-rule-* 属性的简写
column-rule-color|	指定两列间边框的颜色
column-rule-style|	指定两列间边框的样式
column-rule-width|	指定两列间边框的厚度
column-span|	指定元素要跨越多少列
column-width|	指定列的宽度
columns	设置| column-width 和 column-count 的简写

### 10.用户界面特性

属性	|说明
:-|:-	
appearance|允许您使一个元素的外观像一个标准的用户界面元素
box-sizing|允许你以适应区域而用某种方式定义某些元素
icon|为创作者提供了将元素设置为图标等价物的能力。
nav-down|指定在何处使用箭头向下导航键时进行导航
nav-index|指定一个元素的Tab的顺序
nav-left|指定在何处使用左侧的箭头导航键进行导航
nav-right|指定在何处使用右侧的箭头导航键进行导航
nav-up|指定在何处使用箭头向上导航键时进行导航
outline-offset|外轮廓修饰并绘制超出边框的边缘
resize|指定一个元素是否是由用户调整大小
outline|绘制外轮廓
outline-offset|属性对轮廓进行偏移，并在超出边框边缘的位置绘制轮廓

### 11.Flex定位

属性|描述
:-|:-
display|指定 HTML 元素盒子类型。
flex-direction|指定了弹性容器中子元素的排列方式
justify-content|设置弹性盒子元素在主轴（横轴）方向上的对齐方式。
align-items|设置弹性盒子元素在侧轴（纵轴）方向上的对齐方式。
flex-wrap|设置弹性盒子的子元素超出父容器时是否换行。
align-content|修改 flex-wrap 属性的行为，类似 align-items, 但不是设置子元素对齐，而是设置行对齐
flex-flow|flex-direction 和 flex-wrap 的简写
order|设置弹性盒子的子元素排列顺序。
align-self|在弹性子元素上使用。覆盖容器的 align-items 属性。
flex|设置弹性盒子的子元素如何分配空间。

### 12.@media

#### 可检测的目标

- viewport(视窗) 的宽度与高度
- 设备的宽度与高度
- 朝向 (智能手机横屏，竖屏) 。
- 分辨率

#### 使用

		@media [not|only mediatype| all] and|not|only (media feature) {
		    CSS-Code;
		}
#
		<link rel="stylesheet" media="mediatype and|not|only (expressions)" href="print.css">

- not: not是用来排除掉某些特定的设备的，比如 @media not print（非打印设备）。

- only: 用来定某种特别的媒体类型。对于支持Media Queries的移动设备来说，如果存在only关键字，移动设备的Web浏览器会忽略only关键字并直接根据后面的表达式应用样式文件。对于不支持Media Queries的设备但能够读取Media Type类型的Web浏览器，遇到only关键字时会忽略这个样式文件。

- all: 所有设备，这个应该经常看到。

#### 多媒体类型

值	|描述
:-|:-
all|用于所有多媒体类型设备
print|用于打印机
screen|用于电脑屏幕，平板，智能手机等。
speech|用于屏幕阅读器

#### 常见的媒体功能

值	|描述
:-|:-
device-height|定义输出设备的屏幕可见高度。
device-width|定义输出设备的屏幕可见宽度。
height|定义输出设备中的页面可见区域高度。
max-height|定义输出设备中的页面最大可见区域高度。
width|定义输出设备中的页面可见区域宽度。
max-width|定义输出设备中的页面最大可见区域宽度。
#### 实例
	@media screen and (min-width: 480px) {
	    body {
	        background-color: lightgreen;
	    }
	}
	@media screen and (min-width: 480px) {
	    #leftsidebar {width: 200px; float: left;}
	    #main {margin-left:216px;}
	}


### 其他

- filter 属性用为元素添加可视效果 (例如：模糊与饱和度) 
