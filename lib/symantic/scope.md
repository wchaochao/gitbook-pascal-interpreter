# 作用域

标签（空格分隔）： Pascal解释器

---

变量的作用范围

* Program作用域：关键字Program生成的作用域
* Procedure作用域：关键字Procedure生成的作用域
 * 静态作用域，作用域的父子关系在声明时确定

## ScopedSymbolTable类

作用域类，记录作用域的级别、类型、名称、声明的symbol及父作用域

```javascript
export default class ScopedSymbolTable {
  constructor (level, type, name, enclosingScope = null) {
    this.level = level
    this.type = type
    this.name = name
    this._symbols = {}
    this.enclosingScope = enclosingScope
  }
}
```

## insert()

在作用域内插入symbol

```javascript
  insert (symbol) {
    this._symbols[symbol.name] = symbol
    symbol.scope = this
    return symbol
  }
```

## lookup()

在作用域内查询symbol，可以沿着作用域链往上查找

```javascript
  lookup (name, currentScopeOnly) {
    const symbol = this._symbols[name]
    if (symbol) {
      return symbol
    }

    if (currentScopeOnly) {
      return
    }

    if (this.enclosingScope) {
      return this.enclosingScope.lookup(name, currentScopeOnly)
    }
  }
```

## initBuiltinType()

在根作用域记录内置类型Symbol，供子作用域共享

```javascript
  initBuiltinType () {
    this.insert(new BuiltinTypeSymbol('INTEGER'))
    this.insert(new BuiltinTypeSymbol('REAL'))
  }
```

## insertVar()

记录作用域内声明的变量、过程、形参，只能声明一遍

```javascript
  insertVar (symbol, token) {
    if (this.lookup(symbol.name, true)) {
      throw new SemanticError(`${ERROR_CODE.DUPLICATE_ID} -> ${token.toString()}`)
    } else {
      return this.insert(symbol)
    }
  }
```
