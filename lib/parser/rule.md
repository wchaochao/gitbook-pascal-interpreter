# 语法规则

标签（空格分隔）： Pascal解释器

---

规定Token如何组成有意义的结构

## number

数字

```BNF
number: INTEGER_CONST | REAL_CONST
```

![number语法图](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-pascal-interpreter/number.png)

示例

```pascal
1
1.2
```

## variable

变量

```BNF
variable: ID
```

![variable语法图](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-pascal-interpreter/variable.png)

示例

```pascal
a
b
```

## factor

基础值

```BNF
factor: number | variable | LPAREN expr RPAREN
```

![factor语法图](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-pascal-interpreter/factor.png)

示例

```pascal
1
a
(1 + a)
```

## unary

正负表达式

```BNF
unary: (PLUS | MINUS)* factor
```

![unary语法图](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-pascal-interpreter/unary.png)

示例

```pascal
+1
-a
```

## term

乘除表达式

```BNF
term: unary ((MUL | INTEGER_DIV | FLOAT_DIV) unary)*
```

![term语法图](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-pascal-interpreter/term.png)

示例

```pascal
1 * a
1 * 2 / a DIV (-b)
```

## expr

加减表达式

```BNF
expr: term ((PLUS | MINUS) term)*
```

![expr语法图](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-pascal-interpreter/expr.png)

示例

```pascal
1 + 2
1 + a - 2 * b + (-c / 3)
```

## empty

空语句

```BNF
empty:
```

![empty语法图](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-pascal-interpreter/empty.png)

## assignment

赋值语句

```BNF
assignment: variable ASSIGN expr
```

![assignment语法图](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-pascal-interpreter/assignment.png)

示例

```pascal
a = 1
d = 1 + a - 2 * b + (-c / 3)
```

## procedureCall

过程调用

```BNF
procedureCall: ID LPAREN (expr (COMMA expr)*)? RPAREN
```

![procedureCall语法图](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-pascal-interpreter/procedureCall.png)

示例

```pascal
alpha()
beta(1)
gamma(1 + a, 2)
```

## statement

单语句

```BNF
statement: empty | assignment | procedureCall | compound
```

![statement语法图](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-pascal-interpreter/statement.png)

示例

```pascal
a = 1
alpha()
BEGIN
END
```

## statementList

语句列表

```BNF
statementList: statement (SEMI statement)*
```

![statementList语法图](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-pascal-interpreter/statementList.png)

示例

```pascal
a = 1;
alpha();
BEGIN
END;
```

## compound

复合语句

```BNF
compound: BEGIN statementList END
```

![compound语法图](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-pascal-interpreter/compound.png)

示例

```pascal
BEGIN
  a = 1;
  alpha();
  BEGIN
    b = 2 * a
  END;
END
```

## type

类型

```BNF
type: INTEGER | REAL
```

![type语法图](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-pascal-interpreter/type.png)

示例

```pascal
INTEGER
REAL
```

## variableDeclaration

变量声明

```BNF
variableDeclaration: ID (COMMA ID)* COLON type
```

![variableDeclation语法图](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-pascal-interpreter/variableDeclation.png)

示例

```pascal
a: INTEGER
a, b : REAL
```

## variableDeclarationList

变量声明列表

```BNF
variableDeclarationList: (variableDeclaration SEMI)+
```

![variableDeclarationList语法图](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-pascal-interpreter/variableDeclarationList.png)

示例

```pascal
a: INTEGER;
a, b : REAL;
```

## formalParam

形参

```BNF
formalParam: ID (COMMA ID)* COLON type
```

![formalParam语法图](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-pascal-interpreter/formalParam.png)

示例

```pascal
a: INTEGER
a, b : REAL
```

## formalParamList

形参列表

```BNF
formalParamList: formalParam (SEMI formalParam)*
```

![formalParamList语法图](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-pascal-interpreter/formalParamList.png)

示例

```pascal
a: INTEGER
a, b : REAL; c: INTEGER
```

## ProcedureDeclaration

过程声明

```
ProcedureDeclaration: PROCEDURE ID (LPAREN formalParamList RPAREN)? SEMI block SEMI
```
![ProcedureDeclaration语法图](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-pascal-interpreter/ProcedureDeclaration.png)

示例

```pascal
PROCEDURE Alpha;
BEGIN
END;

PROCEDURE Beta(a: INTEGER);
  var b : REAL;
BEGIN
END;

PROCEDURE Gamma(a, b: INTEGER; c: REAL)
BEGIN
ENDD;
```

## declarations

声明

```BNF
declarations: (VAR variableDeclarationList)* (procedureDeclaration)*
```

![declarations语法图](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-pascal-interpreter/declarations.png)

示例

```pascal
VAR a: INTEGER;
VAR b, c: REAL;

PROCEDURE Alpha;
  VAR x: INTEGER

  PROCEDURE Beta(a: INTEGER; b, c: REAL);
  BEGIN
  END;
BEGIN
  x = a + 1
END;
```

## block

代码块

```pascal
block: declarations compound
```

![block语法图](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-pascal-interpreter/block.png)

示例

```pascal
VAR a: INTEGER;
VAR b, c: REAL;

PROCEDURE Alpha;
  VAR x: INTEGER

  PROCEDURE Beta(a: INTEGER; b, c: REAL);
  BEGIN
  END;
BEGIN
  x = a + 1
END;

BEGIN
  a = 1;
  Alpha();
END
```

## program

程序

```BNF
program: PROGRAM ID SEMI block DOT
```

![program语法图](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-pascal-interpreter/program.png)

示例

```pascal
PROGRAM Main;
  VAR a: INTEGER;
  VAR b, c: REAL;

  PROCEDURE Alpha;
    VAR x: INTEGER

    PROCEDURE Beta(a: INTEGER; b, c: REAL);
    BEGIN
    END;
  BEGIN
    x = a + 1
  END;

BEGIN
  a = 1;
  Alpha();
END.
```
