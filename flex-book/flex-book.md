## 注意事项

#### 行内元素和块级元素都可以用

#### 设置为`flex`布局之后，子元素的`float`、`clear`和`vertical-align`属性将失效。

#### 注意加前缀



## 基本概念

主轴、交叉轴

主轴起点、终点

交叉轴起点、终点

单个项目占据的主轴空间、交叉轴空间



## 容器的属性

六个

主轴方向

轴线排不下如何换行

以上两个属性的简写

主轴上面的对齐方式

交叉轴上的对齐方式

多根轴线的对齐方式



- flex-direction:  **row** | row-reverse | column | column-reverse

- flex-wrap: **nowrap** | wrap | wrap-reverse
- flex-flow: \<flex-direction\> \|| \<flex-wrap>
- justify-content:  **flex-start** | flex-end | center | space-between | space-around
- align-items:  flex-start | flex-end | center | baseline | **stretch**
- align-content:  flex-start | flex-end | center | space-between | space-around | **stretch**



## 项目的属性

项目的排列顺序

项目的放大比例

项目的缩小比例

项目占据的主轴空间

以上三个属性的简写 flex 后两个可以省略 **推荐使用**

单个属性的对齐方式



- `order`: \<integer> 数字越小越靠前
- `flex-grow`:  \<integer> 0
- `flex-shrink`: \<number> 1
- `flex-basis`: \<length> | auto
- `flex`
- `align-self`: **auto** | flex-start | flex-end | center | baseline | stretch





