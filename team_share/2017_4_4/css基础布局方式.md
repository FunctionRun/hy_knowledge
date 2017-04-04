星期六, 25. 三月 2017 11:41下午 

##css 布局方式的总览

![](http://images2015.cnblogs.com/blog/724254/201703/724254-20170327000754815-1193449774.png)


######图片来源 cssConf大会时阿里的 **寒冬winter**

css 布局的方式分为的部分

### css 的布局方式
* 盒子内部的布局
	*  文本的布局
	* 盒子本身的布局
	
* 盒子之间的布局 visual formmatting
	* 脱离正常normal flow的盒子的布局
		* absolute 布局上下文下的布局
		* float布局上下文下的布局、
		
	* 正常normal flow下的盒子的布局
		* BFC ( block formatting context ) 布局上下文下的布局
		* IFC 布局上下文下的布局
		* FFC 布局上下文下的布局
		* table 布局上下文下的布局
		* css grid 布局上下文下的布局

####1. 盒子内部的布局
* 文本的布局
      * 有个line boxes包裹每行文字，这是看不见的；
      * line boxes 会取子元素最高的 line-height
      * 通过line-height 可以设置单行文本的水平的居中
* box 布局
![](http://images2015.cnblogs.com/blog/724254/201703/724254-20170327000811768-378385082.gif)


######图片的来源w3c
	
####2. 盒子之间的布局
* absolute 布局 (脱离文档流)
* float 布局 (脱离文档流)
* normal flow (正常文档流的布局)  (上下文)
	* bfc ( block formatting context )  块级文档上下文
		* 满足下方**任意一个条件**，会为子元素，创建一个新的 bfc的上下文
			  * 根元素 (body)
			  * float 元素不为 none
			  * overflow 不为 visible
			  * display 为inline-block,table-cell,table-caption
			  * postition 的值为 absolute , fixed
			  
	*  ifc (Inline formatting contexts ) 行内块级上下文
	*  ffc ( Flex Formatting Contexts )  伸缩格式化上下文
	*  table  ( table formatting context ) 表格布局上下文
	*  grid  ( grid formatting context ) 网格格式化上下文

####3. 需要的注意的地方
* bfc上下文下的布局
      *   float 布局的元素
      *   会被假装成行内元素进行布局,向浮动的方向挤，挤不开就会到第二行
      *   当与元素排在一行时，会先渲染 float的元素

            <div> 
                <span style = 'padding:10px'>  one  </span>
                <div  style = 'float:left' > left </div>
                <span>  two </span>
            </div>

       *  这个是效果图
![](http://images2015.cnblogs.com/blog/724254/201703/724254-20170328234718608-1596381035.png)


		* left 出现在了 one,two 的前方

#### 4. float布局 
* 浮动元素对周围元素造成的影响
	* 其他元素会环绕float的元素
	* 浮动元素可以设置外边距，不与周围元素的外边距重合
	* 生成块级框（display: block）
	* 浮动的元素顶部不能超过父元素的顶边
		* 可以设置 负的margin( margin: -10px) 来打破这条规则
		
* 浮动元素的重叠问题
	出现的原因： 浮动元素可以设置 负的margin值，
	* 行内元素与浮动元素的重叠
		行内元素的内容，背景，边框都在浮动元素之上
	
	* 块框与浮动元素的重叠
		块框的背景，边框 会在浮动元素下方，内容在浮动元素上方
		
	* 浮动与浮动重叠
		后面的覆盖前面的
		
* 父元素塌陷问题

		<div id = 'parent'> 
			<div  style = 'float:left' > one  </div>
		</div>
		
  	父元素没有高度，这样不能添加点击的事件，所以需要清楚浮动也可以给父元素设置高度 
	
		<div id = 'parent' style = 'height : 200px'> 
			<div  style = 'float:left' > one  </div>
		</div>
	
  * 清楚浮动的方式：
    1.添加一个子元素设置 clear : both

       	<div id = 'parent' style = 'height : 200px'> 
			<div  style = 'float:left' > one  </div>
		<div  style = 'clear:both' >  </div>
	</div>
	
    2.给父元素添加一个伪类
      html部分
		
     	<div id = 'parent' style = 'height : 200px'> 
			<div  style = 'float:left' > one  </div>
		</div>
                
	        
      css伪类
	       
	        .clearfix:before, .clearfix:after{
				content : “”;
				display : block;
				overflow:hidden;
		     }