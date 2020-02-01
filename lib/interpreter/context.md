# 执行上下文

标签（空格分隔）： Pascal解释器

---

当前代码的执行环境，记录对应作用域内声明的变量值

## 执行上下文类型

* 全局上下文：执行程序时进入全局上下文，与全局作用域对应
* 过程上下文：执行过程调用时进入过程上下文，与过程作用域对应

## 执行上下文关系

* 父子关系：由对应作用域的父子关系决定

## Context类

记录对应的作用域和作用域内声明的变量值

```javascript
export default class Context {
  constructor (scope) {
    this.scope = scope
    scope.context = this
    this.members = {}
  }
}
```

## getCorrespondContext()

获取当前上下文中出现的变量实际对应的上下文

```javascript
  getCorrespondContext (key) {
    const symbol = this.scope.lookup(key)
    return symbol.scope.context
  }
```

## set()

在当前上下文中出现的变量实际对应的上下文中记录该变量的值

```javascript
  set (key, value) {
    const context = this.getCorrespondContext(key)
    context.setItem(key, value)
  }

  setItem (key, value) {
    this.members[key] = value
  }
```

## get()

在当前上下文中出现的变量实际对应的上下文中查找该变量的值

```javascript
  get (key) {
    const context = this.getCorrespondContext(key)
    return context.getItem(key)
  }

  getItem (key) {
    return this.members[key]
  }
```
