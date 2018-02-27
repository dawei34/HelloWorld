##### siblings()
jQuery的siblings函数用来查找同胞元素(注：不包括自身，需要返回自身则要用.andSelf())
```
 <div><span>Hello</span></div>  
 <p class="selected">Hello Again</p>  
 <p>And Again</p>  
  
$("div").siblings();   //result: <p class="selected">Hello Again</p> <p>And Again</p> 
$("div").siblings(".selected");   //result: <p class="selected">Hello Again</p>
$("div").siblings().andSelf();   //result: <div><span>Hello</span></div> <p class="selected">Hello Again</p> <p>And Again</p>  
```
