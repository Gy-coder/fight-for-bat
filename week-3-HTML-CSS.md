## Week 3

> HTML、CSS 问题

1. HTML 标签有哪些，什么是语义化

   - HTML 标签有: a、button、div、span......

   * 语义化：

     1. 是什么: 语义化是一种标签的 HTML 的方法论
     2. 怎么做: 比如标签就用 h1-h6,章节用 section,边栏用 aside,导航用 nav
     3. 解决的问题: 明确了 HTML 的书写规范 增强了可读性
     4. 优点: 适合搜索引擎搜索，改善了代码可读性

2. 哪些块级，哪些行级，什么区别，怎么转换

   - 块级元素: div , h1---h6 , p , ul , ol , dl , table , form
   - 行内元素: span , a ,strong
   - 区别: 块级元素可以设置宽高，行内元素不可以、块级元素会占满一行，行内元素不会、块级元素可以设置 margin,padding,行内元素垂直方向的 margin,padding 不生效

   * 转换: display、

3. Doctype 是什么，有什么作用，用的时候有什么要注意的

   - 是什么: 它是 html5 标准网页声明,全称为 Document Type HyperText Mark-up Language。
   - 作用： 声明文档的解析类型(document.compatMode)，避免浏览器的怪异模式。
   - 注意:

     - 一定要在 HTML 文档添加 <!DOCTYPE> 声明，这样浏览器才能获知文档类型。

     - <!DOCTYPE> 声明必须是 HTML 文档的第一行，位于 <html> 标签之前。

     - <!DOCTYPE> 声明对大小写不敏感的

     - <!DOCTYPE> 声明没有结束标签

4. 标签的全局属性有哪些

   1. class
   2. contenteditable 标签内容是否可以编辑
   3. data-\* 一组自定义数据的属性 可以在 js 中使用 HTMLElement.dataset 访问
   4. draggable 标签元素是否可以拖动
   5. id 唯一标识符 会成为 js 的全局变量

5. `<input type="checkbox" checked=false >` 这个表单是勾选状态吗

   是勾选状态

   可以尝试`<input type="checkbox" checked=false id="x">` 这个表单是勾选状态吗

   打印 console.log(x.checked) -> true

6. `<input type="submit" value="提交">` 如何使用能实现点击自动提交
7. 如何给 radio 分组

   为同组的 radio 设置相同的 name

8. 聊一聊选择器优先级
   
   计算方法: !important > 权重高的 > 权重相同位置靠后的 > 来自继承的

   权重计算方法

   1. 千位: 声明在style标签中则该位置得1分

   2. 百位: 选择器中包含id选择器该位置得1分

   3. 十位: 选择器中包含类选择器 属性选择器 伪类 则该位置得1分

   4. 个位: 选择器中包含标签选择器 伪元素选择器 则该位置得1分


9. 说一下盒模型，两种盒模型的区别

   1. content-box: width 是 content 区域的宽度 ，真实的宽度 = content + padding + border
   2. border-box: width 是实际宽度，包含了 content,border,padding

10. 你对响应式设计的理解是什么，怎么实现
11. 浮动元素有哪些特性？和行级元素有什么关系？如何处理塌陷？

    - 浮动元素的特性：

      1. 浮动元素会脱离文档流，

12. BFC 是什么?触发条件和作用?
   
   1. BFC是什么？
   
       块级格式上下文
   
   2. 触发条件
   
      * 绝对定位元素 absolute和fixed
      * inline-block元素
      * 弹性元素 flex/inline-flex的直接子元素
      * 浮动元素
      * overflow不是visible的元素
   
   3. 作用
   
      * 清除浮动
      * 防止边距合并
   
   
13. display 有哪些值
14. 怎么实现单行多行超出自动省略号呢？多行呢?
15. 隐藏元素的方式
  
    1. display: none  // 不占位不可交互
    2. visibility=hidden // 占位不可交互
    3. opcity： 0 // 占位可交互
16. flex 布局用过吗？怎么理解主轴
17. flex 的三个属性分别代表什么意思？具体怎么计算？flex-basis 和 width 有什么区别？
   
   1. flex 三个属性代表grow / shrink / basis
   2. 第一步：各个子元素根据 flex-basis 的值，填充父元素 第二步： 根据填充后的剩余空间大小根据flex-grow和flex-shrink伸缩
   
18. flex: 1 是什么意思？ flex: auto 是什么意思？ 如何实现真正的等分？
   
   flex: 1 -> flex-grow: 1,flex-shrink: 1,flex-basis: 0%
   flex: auto -> flex-grow :1,flex-shrink: 1,flex-basis: auto
   
19. flex 布局里如何实现子项一个靠左，一个靠右
20. grid 布局用过吗？和 flex 有啥不一样？grid 怎么做布局？
21. 左右两边固定距离，中间自适应的方法
22. 哪些方法可以实现水平垂直居中
23. CSS 定位有哪几种？absolute 特点，sticky 特点，position:absolute 相对于哪个元素定位
    样式
24. transform 介绍一下
25. CSS 动画的实现方法&性能
26. link和import引入css的不同
   
    * link可以引入除css意外的东西，而imort不可以
    * link的兼容性比import更好
    * link可以和html同时加载，import只能在页面加载完毕后被加载
    * js可以通过DOM改变link引入的css，而import方式无法被改改变

