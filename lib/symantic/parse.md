# 语义分析

标签（空格分隔）： Pascal解释器

---

遍历抽象语法树

* 声明部分：记录各作用域声明的变量、过程、形参
* 执行部分
 * 变量在使用前要先声明
 * 过程在使用前要先声明，并且实参和形参个数要对应

## NodeVisitor类

观察者模式，使用visit()遍历抽象语法树

```javascript
export default class NodeVisitor {
  visit (node) {
    let methodName = `visit_${getClassName(node)}`
    let visitor = this[methodName] || this.genericVisit
    return visitor.call(this, node)
  }

  genericVisit (node) {
    throw new Error(`no visit_${getClassName(node)} method`)
  }
}
```

## Semantic类

语义分析器，遍历抽象语法树进行语义分析

```javascript
export default class Semantic extends NodeVisitor {
  constructor (tree, shouldLogScope = false) {
    super()
    this.tree = tree
    this.currentScope = null
    this.scopes = {}
  }

  // ...

  analyze () {
    this.visit(this.tree)
    return this.scopes
  }
}
```

## visit_Program()

遍历Program节点

* 进入Program节点时将当前作用域置为新生成的全局作用域，并记录内置类型Symbol
* 离开Program节点时将当前作用域重置为上一作用域

```javascript
  visit_Program (node) {
    const globalScope = new ScopedSymbolTable(1, SCOPE_TYPE.PROGRAM, node.name, this.currentScope)
    globalScope.initBuiltinType()
    this.currentScope = globalScope
    this.scopes[node.name] = globalScope

    this.visit(node.block)
    this.currentScope = this.currentScope.enclosingScope
  }
```

## visit_Var()

遍历Var节点

* 在当前作用域内记录声明的变量Symbol

```javascript
  genVarSymbol (node) {
    return new VarSymbol(node.varNode.value, this.currentScope.lookup(node.typeNode.value))
  }

  visit_VarDecl (node) {
    const varSymbol = this.genVarSymbol(node)
    this.currentScope.insertVar(varSymbol, node.varNode.token)
  }
```

## visit_Procedure()

遍历Procedure节点

* 在当前作用域记录声明的过程Symbol
* 将当前作用域置为新生成的过程作用域，并记录形参Symbol
* 离开Procedure节点时，敬当前作用域重置为上一作用域

```javascript
  visit_ProcedureDecl (node) {
    const procName = node.name
    const procSymbol = new ProcedureSymbol(node)
    this.currentScope.insertVar(procSymbol, node.token)

    const procedureScope = new ScopedSymbolTable(this.currentScope.level + 1, SCOPE_TYPE.PROCEDURE, procName, this.currentScope)
    this.currentScope = procedureScope
    this.scopes[procName] = procedureScope

    node.params.forEach(param => {
      const symbol = this.genVarSymbol(param)
      this.currentScope.insertVar(symbol, param.varNode.token)
      procSymbol.params.push(symbol)
    })

    this.visit(node.block)
    this.currentScope = this.currentScope.enclosingScope
  }
```

## visit_Var()

遍历Var节点

* 检查变量是否在当前作用域的作用域链上已声明

```javascript
  visit_Var (node) {
    const name = node.value
    const symbol = this.currentScope.lookup(name)
    if (!symbol) {
      this.error(node.token)
    }
  }
```

## visit_ProcedureCall()

遍历ProcedureCall节点

* 检查过程是否在当前作用域的作用域链上已声明
* 检查实参个数和形参个数是否相符

```javascript
  visit_ProcedureCall (node) {
    const symbol = this.currentScope.lookup(node.name)
    if (!symbol) {
      this.error(node.token)
    } else {
      if (node.params.length !== symbol.params.length) {
        throw new SemanticError(`${ERROR_CODE.UNEXPECTED_ARGUMENT_COUNT} -> ${node.token}`)
      } else {
        node.params.forEach(param => this.visit(param))
      }
    }
  }
```
