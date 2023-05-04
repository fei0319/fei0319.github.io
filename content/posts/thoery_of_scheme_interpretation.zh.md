---
title: Scheme 解释器原理
date: 2023-04-07T16:25:00+08:00
tags: ["Scheme"]
categories: ["编译原理"]
math: true
description: "“Scheme 是最美的语言”"
---

Scheme 是 Lisp 的一种方言，其语法极为简洁。

在 Scheme 中，所有的过程都通过 `(procedure param)` 的形式调用。下面是一些例子：

- `(eq? 3 (+ 1 2))`：计算 3 是否等于 1 + 2
- `(define x (* 3 4))`：将变量 `x` 绑定至 3 \* 4 的结果
- `(define (abs x) (if (> x 0) x (- x)))`：定义过程 `abs`

可见，Scheme 使用波兰表示法而非我们惯用的中缀表示法。这对于初用者来说可能很麻烦，但也方便了我们实现其解释器。我们不用考虑种种运算符的运算顺序，然后在此基础上复杂地将中缀表达式转换为波兰表达式处理。

需要注意的是，在 Scheme 中括号不用于表示运算顺序，而是表示过程调用。例如 `+` 和 `(+)` 是不同的，前者表示加法函数，后者表示无参数地调用加法函数。

## Read-Eval-Print 循环

类似 Python，Scheme 解释器在交互模式下也按照“读入-求值-输出”的模式循环。

### 读入

首先以空格、括号为界，将输入 tokenize。例如 `(+ 1 (* 2 3))` 在被 tokenized 后会成为 `['(', '+',  '1', '(', '*', '2', '3' ')', ')']`。

之后，再将 token 转化为可供求值的对象。在我们的设计中，任何可供求值的对象，要么是个字符串，要么是个本质为链表的 `Pair`。任何被括号包围的 token 将被转化为 `Pair`，而独立的 token 将被转化为字符串。例如，`'1'` 会被转化为 `'1'`（技术上，它其实没发生任何改变），而 `('1')` 却会被转化为 `Pair('1', nil)`（nil 代表尾节点）。

```python
class Pair(object):
    def __init__(self, first, rest):
        self.first = first
        self.rest = rest
```

对于字符串，我们在当前环境下查找其值，例如 `'+'` 对应了一个代表加法的过程，`'#t'` 对应了为真的布尔值，`'a'` 对应了变量 `a` 的值。对于 `Pair`，在求值时我们认为其是个过程调用，当前数据为所调用的过程，下一个链表节点为过程的参数，例如 `Pair('+', Pair('1', Pair('2', 'nil')))` 将被认为是一个以 `Pair('1', Pair('2', 'nil'))` 为参数对过程 `'+'` 的调用。`Pair` 除了代表过程调用，还代表普通的链表，例如在刚才的例子中，`Pair('1', Pair('2', 'nil'))` 代表一个普通的链表 `['1', '2']` 而非对过程 `'1'` 的调用。

那么，一个 `Pair` 何时被视作过程调用，何时被视作链表呢？`Pair` 在被求值时被视作过程调用，而存储时被视作链表。这种说法有些抽象，将在接下来的对求值的介绍中予以澄清。

### 求值

之前我们提到过，可供求值的对象要么是字符串，要么是 `Pair`；独立的字符串以及包含在 `Pair` 中的字符串可以被轻松的实现求值，于是我们要实现求值的只剩 `Pair` 了。

```python
def scheme_eval(expr):
    if isinstance(expr, str):
        # if it is a string
        # we provoke some procedure to evaluate it
        if expr == '#t': return True
        elif expr == '#f': return False
        elif expr == '1': return 1
        elif expr == '2': return 2
        elif expr == '+': return plus_function
        # blah blah blah...
        # of course the real implementation won't be this stupid; this is just a demo

    if isinstance(expr, Pair):
        # otherwise it is a pair
        # expr.first is the procedure while expr.rest being the argument
        return scheme_apply(scheme_eval(expr.first, env), expr.rest)
```

我们通过函数 `scheme_apply(procedure, args)` 实现 `Pair` 的求值。将 `args` 视作一个链表而非过程调用，逐个地对其中元素调用 `scheme_eval`：

```python
def scheme_apply(procedure, args):
    lst = list()

    while args != nil:
        lst.append(scheme_eval(args.first))
        args = args.rest

    return procedure(＊lst)
```

### 输出

简单地输出调用 `scheme_eval` 的返回值即可。

## 框架和环境

框架（Frame）和环境（Environment）都是关于变量和实例绑定的概念，都是从变量到实例的单射。但它们并非完全一样的概念。请参考下面的介绍：

> An frame is a box that contains bindings from variables to values. An frame can “extend” another frame; that is, this frame can see all bindings of the frame it extends. We represent this by drawing an arrow from an environment frame to the frame it is extending. The global environment is the only environment that extends nothing.
>
> An environment is a series of frames, which we get from extending the current frame. To determine which frames are in an environment, follow the arrows until reaching the global environment. Every frame you passed through is part of that environment. The global environment is only made up of the global frame, because its frame extends nothing.

之前的 `scheme_eval` 中，对于字符串，我们采用了类似 `if expr == '1': return 1` 这样的愚蠢实现，并表示其只是个 demo。那么对于字符串我们究竟要怎么处理呢？

字符串分为两类：字面量和变量。对于字面量，我们免不了要使用一些烦人的分类讨论。对于变量，我们要在环境内寻找其值。

```python
class Frame:
    def __init__(self, parent):
        self.bindings = dict()
        self.parent = parent
    def lookup(self, name):
        if name in self.bindings:
            return self.bindings[name]
        if self.parent is None:
            raise SomeError
        return self.parent.lookup(name)
```

为了确定当前的环境，`scheme_eval` 和 `scheme_apply` 要加上一个新的参数 `env` 表示当前环境：

```python
def scheme_eval(expr, env):
    if isinstance(expr, str):
        if is_literal(expr):
            return literal(expr)
        return env.lookup(expr)
    return scheme_apply(scheme_eval(expr.first, env), expr.rest, env)

def scheme_apply(procedure, args, env):
    lst = list()

    while args != nil:
        lst.append(scheme_eval(args.first, env))
        args = args.rest

    return procedure(*lst)
```

我们要预先创建一个 global 框架，并向其中加入一些预设的变量。

### if

在 C/C++ 和 Java 中，if/while/for 等语句会以当前环境为父环境创建新的环境，但在 Scheme 中 if 并不会。

### 函数

和其他大多数语言一样，Scheme 的函数调用会创建新环境，父环境为声明函数的环境。

## 特殊形式

上面的设计只考虑了函数，而未考虑其他的特殊形式。函数和特殊形式的不同之处在于，函数总是会先对所有参数进行求值，而特殊形式却并不会。

- `(define a (+ 1 2))` 的参数有 `a` 和 `(+ 1 2)`，但 `a` 不会被求值，只有 `(+ 1 2)` 会被求值
- `(and #f (+ 1 2))` 的参数有 `#f` 和 `(+ 1 2)`，但由于逻辑与的短路规则，`(+ 1 2)` 并不会被求值
- `(define (plus x y) (+ x y))` 的参数有 `(plus x y)` 和 `(+ x y)`，但它们都不会被求值

因此，我们需要改变 `scheme_apply` 的实现。具体地，若 `procedure` 是特殊形式，我们将不对 `args` 中的元素求值，而是将其直接传给 `procedure`。

Scheme 中的特殊形式有很多，这里只介绍其中关键的特殊形式的实现。

### 逻辑运算

### 变量定义

### 函数定义和 Lambda 表达式

### Quote 和 Quasiquote

## 尾递归

## 结语
