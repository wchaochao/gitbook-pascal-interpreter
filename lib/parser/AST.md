# AST

标签（空格分隔）： Pascal解释器

---

抽象语法树

![抽象语法树](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-pascal-interpreter/AST.png)

## AST类

语法树节点基类

```javascript
export class AST {}
```

## Num类

数字节点，记录数字的值

```javascript
export class Num extends AST {
  constructor (token) {
    super()
    this.token = token
    this.value = this.token.value
  }
}
```

## Var类

变量节点，记录变量名

```javascript
export class Var extends AST {
  constructor (token) {
    super()
    this.token = token
    this.value = this.token.value
  }
}
```

## UnaryOp类

一元表达式节点，记录一元操作符和操作数

```javascript
export class UnaryOp extends AST {
  constructor (op, expr) {
    super()
    this.token = op
    this.expr = expr
  }
}
```

## BinOp类

二元表达式节点，记录二元操作符和操作数

```javascript
export class BinOp extends AST {
  constructor (left, op, right) {
    super()
    this.token = op
    this.left = left
    this.right = right
  }
}
```

## NoOp类

空语句节点

```javascript
export class NoOp extends AST {
  constructor () {
    super()
  }
}
```

## Assign类

赋值语句节点，记录左边变量和右边表达式

```javascript
export class Assign extends AST {
  constructor (left, op, right) {
    super()
    this.token = op
    this.left = left
    this.right = right
  }
}
```

## ProcedureCall类

过程调用节点，记录过程名和实参

```javascript
export class ProcedureCall extends AST {
  constructor (token, params) {
    super()
    this.token = token
    this.name = token.value
    this.params = params
  }
}
```

## Compound类

复合语句节点，记录包含的语句列表

```javascript
export class Compound extends AST {
  constructor (statements) {
    super()
    this.statements = statements
  }
}
```

## Type类

类型节点，记录类型值

```javascript
export class Type extends AST {
  constructor (token) {
    super()
    this.token = token
    this.value = this.token.value
  }
}
```

## VarDecl类

变量声明节点，记录声明的变量和类型

```javascript
export class VarDecl extends AST {
  constructor (varNode, typeNode) {
    super()
    this.varNode = varNode
    this.typeNode = typeNode
  }
}
```

## Param类

形参节点，记录声明的形参和类型

```javascript
export class Param extends AST {
  constructor (varNode, typeNode) {
    super()
    this.varNode = varNode
    this.typeNode = typeNode
  }
}
```

## ProcedureDecl类

过程声明，记录声明的过程名、形参列表、代码块

```javascript
export class ProcedureDecl extends AST {
  constructor (token, params = [], block) {
    super()
    this.token = token
    this.name = token.value
    this.params = params
    this.block = block
  }
}
```

## Block类

代码块节点，记录所有的声明和主复合语句

```javascript
export class Block extends AST {
  constructor (declarations, compound) {
    super()
    this.declarations = declarations
    this.compound = compound
  }
}
```

## Program类

程序节点，记录程序名和代码块

```javascript
export class Program extends AST {
  constructor (name, block) {
    super()
    this.name = name
    this.block = block
  }
}
```
