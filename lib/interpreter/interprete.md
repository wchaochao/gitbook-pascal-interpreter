# 解释执行

标签（空格分隔）： Pascal解释器

---

## Interpreter类

遍历抽象语法树，在执行上下文内解释执行树节点

```javascript
export default class Interpreter extends NodeVisitor {
  constructor (tree, shouldLogStack) {
    super()
    this.tree = tree
    this.scopes = new Semantic(tree).analyze()
    this.callStack = new CallStack()
  }

  // ...

  interprete () {
    this.visit(this.tree)
  }
}
```

## visit_Num()

解释Num节点

* 返回Num节点的值

```javascript
  visit_Num (node) {
    return node.value
  }
```

## visit_Var()

解释Var节点

* 返回变量所在执行上下文中的值

```javascript
  visit_Var (node) {
    let varName = node.value
    const context = this.callStack.peek()
    return context.get(varName)
  }
```

## visit_UnaryOp()

解释UnaryOp节点

* 进行一元运算

```javascript
  visit_UnaryOp (node) {
    let op = node.token
    if (op.type === TOKEN_TYPE.PLUS) {
      return +(this.visit(node.expr))
    } else if (op.type === TOKEN_TYPE.MINUS) {
      return -(this.visit(node.expr))
    }
  }

```

## visit_BinOp()

解释BinOp节点

* 进行二元运算

```javascript
  visit_BinOp (node) {
    let op = node.token
    if (op.type === TOKEN_TYPE.PLUS) {
      return this.visit(node.left) + this.visit(node.right)
    } else if (op.type === TOKEN_TYPE.MINUS) {
      return this.visit(node.left) - this.visit(node.right)
    } else if (op.type === TOKEN_TYPE.MUL) {
      return this.visit(node.left) * this.visit(node.right)
    } else if (op.type === TOKEN_TYPE.DIV) {
      return parseInt(this.visit(node.left) / this.visit(node.right))
    } else if (op.type === TOKEN_TYPE.FLOAT_DIV) {
      return this.visit(node.left) / this.visit(node.right)
    }
  }
```

## visit_NoOp()

解释NoOp节点

* 不作处理

```javascript
  visit_NoOp () {}
```

## visit_Assign()

解释Assign节点

* 设置变量所在执行上下文中的值

```javascript
  visit_Assign (node) {
    let varName = node.left.value
    const context = this.callStack.peek()
    context.set(varName, this.visit(node.right))
  }
```

## visit_ProcedureCall()

解释ProcedureCall节点

* 根据过程作用域生成过程上下文，并压入调用栈
* 设置形参值为传入的实参值，并解释执行过程的Block节点
* 退出调用栈

```javascript
  visit_ProcedureCall (node) {
    const scope = this.scopes[node.name]
    const context = new Context(scope)
    this.callStack.push(context)
    const symbol = scope.lookup(node.name)
    symbol.params.forEach((param, index) => {
      context.set(param.name, this.visit(node.params[index]))
    })
    this.visit(symbol.node.block)
    this.callStack.pop()
  }
```

## visit_Compound()

解释Compound节点

* 解释Compound节点的各语句节点

```javascript
  visit_Compound (node) {
    node.statements.forEach(statement => this.visit(statement))
  }
```

## visit_VarDecl()

解释VarDecl节点

* 语义分析中已处理，不用再处理

```javascript
  visit_VarDecl () {}
```

## visit_ProcedureDecl()

解释ProcedureDecl节点

* 语义分析中已处理，不用再处理

```javascript
  visit_ProcedureDecl () {}
```

## visit_Block()

解释Block节点

* 解释Block的声明节点和Compound节点

```javascript
  visit_Block (node) {
    node.declarations.forEach(declaration => this.visit(declaration))
    this.visit(node.compound)
  }
```

## visit_Program()

解释Program节点

* 根据全局作用域生成全局上下文，并压入调用栈
* 解释执行Program的Block节点
* 退出调用栈

```javascript
  visit_Program (node) {
    const scope = this.scopes[node.name]
    const context = new Context(scope)
    this.callStack.push(context)
    this.visit(node.block)
    this.callStack.pop()
  }
```
