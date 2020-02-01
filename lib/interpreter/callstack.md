# 调用栈

标签（空格分隔）： Pascal解释器

---

## CallStack类

记录执行过程中正在使用的执行上下文

```JavaScript
export default class CallStack {
  constructor () {
    this.contexts = []
  }
}
```

## push()

进栈

```javascript
  push (context) {
    this.contexts.push(context)
  }
```

## pop()

出栈

```javascript
  pop () {
    const context = this.contexts.pop()
    context.scope.context = null
    return context
  }
```

## peek()

窥探当前上下文

```javascript
  peek () {
    return this.contexts[this.contexts.length - 1]
  }
```
