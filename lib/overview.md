# 概述

标签（空格分隔）： Pascal解释器

---

## Pascal程序

```Pascal
program Main; { Program入口 }
   var b, x, y : real; { 变量声明 }
   var z : integer;

   procedure AlphaA; { 过程声明，无参数 }
      var b : integer;

      procedure Beta(c : integer); { 过程声明，一个参数 }
         var y : integer;

         procedure Gamma(c, d : integer; a: integer); { 过程声明，多个参数 }
            var x : integer;
         begin { Gamma }
            x := a + b + c + x + y + z; { 赋值语句 }
         end;  { Gamma }

      begin { Beta }
      end;  { Beta }

   begin { AlphaA }
   end;  { AlphaA }

   procedure AlphaB(a, b : integer);
      var c : real;
   begin { AlphaB }
      c := a + b;
      y := -a + 1.2;
      z := 1;
   end;  { AlphaB }

begin { Main }
  x := 1.2;
  AlphaB(1 + 2, 3); { 过程调用 }
  begin { 复合语句 }
    y := 2.3;
  end;
end.  { Main }
```

## Pascal解释器

* 词法分析：根据词法规则将源代码文本转换为Token流
* 语法分析：根据语法规则将Token流转换为抽象语法树
* 语义分析：遍历抽象语法树，进行静态检查，如变量声明、过程声明
* 解释执行：遍历抽象语法树，在执行上下文中解释执行树节点

![Pascal解释器流程图](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-pascal-interpreter/interpreter-process.png)
