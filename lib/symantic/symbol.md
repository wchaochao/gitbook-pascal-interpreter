# Symbol

标签（空格分隔）： Pascal解释器

---

## Symbol类

记录声明的内置类型、变量、过程的信息

```javascript
export class Symbol {
  constructor (name, type = null) {
    this.name = name
    this.type = type
  }
}
```

## BuiltinTypeSymbol类

记录内置类型的类型值

```javascript
export class BuiltinTypeSymbol extends Symbol {
  constructor (name) {
    super(name)
  }
}
```

## VarSymbol类

记录变量名和变量类型

```javascript
export class VarSymbol extends Symbol {
  constructor (name, type) {
    super(name, type)
  }
}
```

## ProcedureSymbol类

记录过程名和过程的形参列表

```javascript
export class ProcedureSymbol extends Symbol {
  constructor (node, params = []) {
    super(node.name)
    this.params = params
    this.node = node
  }
}
```





