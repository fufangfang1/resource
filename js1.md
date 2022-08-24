## js 基础

#### 类型转换

```js
    Number(null) 转化为 0，
    Number(undefined) 转化为 NaN.
```

#### 特例

```js
Boolean(NaN == NaN);
Boolean(NaN === NaN);
```

均为 false.

#### typeof 连续转化

```js
  typeof(typeof(undefined))。
```

解释：结果是 string。第一次判断类型的'undefined',输出是字符串，第二次判断字符串，输出是'string'.

#### 预解析

> 函数预解析过程

1. AO 对象 AO{}
2. 找形参和变量声明,将变量和形参名作为 AO 对象的属性名，值为 undefined
3. 将实参值和形参统一
4. 在函数体内找函数声明，值赋予函数体

```js
    // 举例:
    // 1.生成 ao 对象
    ao{
      // 2.找形参和变量声明，并赋值 undefined
      a:undefined
      b:undefined
      c:undefined
      d:undefined
      // 3.实参=形参
      // 3.题中没传实参，这步略过
      // 4.找函数体赋值给变量声明
      // a:function a(){}
      b:undefined
      c:function c(){}
      // 5.开始按顺序执行
      a:function a(){} 123
      b:undefined
      c:function c(){}
    }
```

#### 笔试题

````js
     function fn1(a, c) {
       console.log(a); //function a(){}
       var a = 123;
       console.log(a); //123
       console.log(c); //function c(){}
       function a() {}
       if (a == 8) {
         var d = 678;
       } //注意：在判断条件里面的变量也要提升，是变量都要提
       console.log(d); //undefined
       console.log(b); //undefined
       var b = function () {}; //函数表达式
       console.log(b); //function(){}
       function c() {}
     }
     fn1();
```
````
