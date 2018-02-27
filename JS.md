##### JavaScript内核系列
>http://hzjavaeyer.group.iteye.com/group/wiki/2271-JavaScript-Core

##### 函数作用域
JavaScript的作用域为**词法作用域**，所谓词法作用域是说，其作用域为在定义时(词法分析时)就确定下来的，而并非在执行时确定;
作用域中的变量作用域为函数体内有效，而无块作用域；JavaScript的函数是在局部作用域内运行的，在局部作用域内运行的函数体可以访问其外层的(可能是全局作用域)的变量和函数；

示例:
```
var str = "global";  
function scopeTest(){  
    alert(str);  
    var str = "local";  
    alert(str);  
}     
scopeTest();    //undefined  local
```
上面代码运行的结果为: undefined  local 而非：global  local，因为在函数scopeTest的定义中，预先访问了未声明的变量str，然后才对str变量进行初始化，所以第一个print(str)会返回undifined错误。那为什么函数这个时候不去访问外部的str变量呢？这是因为，在词法分析结束后，构造作用域链的时候，会将函数内定义的var变量放入该链，因此str在整个函数scopeTest内都是可见的(从函数体的第一行到最后一行)，由于str变量本身是未定义的，程序顺序执行，到第一行就会返回未定义，第二行为str赋值，所以第三行的print(str)将返回”local”
