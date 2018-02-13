##### 引用类型
```
Boolean对象
 var bFalseObj = new Boolean(false)
 var vresult = bFalseObj && ture;   //返回 ture
```
在这段代码中，用 false 值创建 Boolean 对象。然后用这个值与原始值 true 进行 AND 操作。在 Boolean 运算中，false 和 true 进行 AND 操作的结果是 false。不过，在这行代码中，计算的是 bFalseObj，而不是它的值 false。
在 Boolean 表达式中，所有对象都会被自动转换为 true，所以 bFalseObj 的值是 true。然后 true 再与 true 进行 AND 操作，结果为 true
##### 对象
```
```
##### 对象作用域
```
```
