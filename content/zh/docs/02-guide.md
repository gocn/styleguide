# Go 编程规范

原文：[https://google.github.io/styleguide/go](https://google.github.io/styleguide/go)

[概述](https://gocn.github.io/styleguide/docs/01-overview/) | [风格指南](https://gocn.github.io/styleguide/docs/02-guide/) | [风格决策](https://gocn.github.io/styleguide/docs/03-decisions/) | [最佳实践](https://gocn.github.io/styleguide/docs/04-best-practices/)

## 风格原则

以下几条总体原则总结了如何编写可读的 Go 代码。以下为具有可读性的代码特征，按重要性排序：

1. [清晰](https://gocn.github.io/styleguide/docs/02-guide/#清晰)：代码的目的和设计原理对读者来说是清楚的。
2. [简约](https://gocn.github.io/styleguide/docs/02-guide/#简约)：代码以最简单的方式来完成它的目的。
3. [简洁](https://gocn.github.io/styleguide/docs/02-guide/#简洁)：代码具有很高的信噪比，即写出来的代码是有意义的，非可有可无的。
4. [可维护性](https://gocn.github.io/styleguide/docs/02-guide/#可维护性)：代码可以很容易地被维护。
5. [一致](https://gocn.github.io/styleguide/docs/02-guide/#一致)：代码与更广泛的 Google 代码库一致。

### 清晰

可读性的核心目标是写出对读者来说很清晰的代码。

清晰性主要是通过有效的命名、有用的注释和有效的代码组织来实现的。

清晰性要从读者的角度来看，而不是从代码的作者的角度来看，代码的易读性比易写性更重要。代码的清晰性有两个不同的方面：

- [该代码实际上在做什么？](https://gocn.github.io/styleguide/docs/02-guide/#该代码实际上在做什么)
- [为什么代码会这么做？](https://gocn.github.io/styleguide/docs/02-guide/#为什么代码会这么做)

#### 该代码实际上在做什么

Go 被设计得应该是可以比较直接地看到代码在做什么。在不确定的情况下或者读者可能需要先验知识才能理解代码的情况下，我们值得投入时间以使代码的目的对未来的读者更加明确。例如，它可能有助于：

- 使用更具描述性的变量名称
- 添加额外的评论
- 使用空白与注释来划分代码
- 将代码重构为独立的函数/方法，使其更加模块化

这里没有一个放之四海而皆准的方法，但在开发 Go 代码时，优先考虑清晰性是很重要的。

#### 为什么代码会这么做

代码的基本原理通常由变量、函数、方法或包的名称充分传达。如果不是这样，添加注释是很重要的。当代码中包含读者可能不熟悉的细节时，“为什么？”就显得尤为重要，例如：

- 编程语言中的细微差别，例如，一个闭包将捕获一个循环变量，但闭包在许多行之外
- 业务逻辑的细微差别，例如，需要区分实际用户和虚假用户的访问控制检查

一个 API 可能需要小心翼翼才能正确使用。例如，由于性能原因，一段代码可能错综复杂，难以理解，或者一连串复杂的数学运算可能以一种意想不到的方式使用类型转换。在这些以及更多的情况下，附带的注释和文档对这些方面进行解释是很重要的，这样未来的维护者就不会犯错，读者也可以理解代码而不需要进行逆向工程。

同样重要的是，我们要意识到，一些基于清晰性考虑的尝试（如添加额外的注释），实际上会通过增加杂乱无章的内容、重述代码已经说过的内容、与代码相矛盾或增加维护负担来保持注释的最新性，以此来掩盖代码的目的。让代码自己说话（例如，通过代码中的名称本身进行描述），而不是添加多余的注释。通常情况下，注释最好是解释为什么要做某事，而不是解释代码在做什么。

Google 的代码库基本上是统一和一致的。通常情况下，那些比较突兀的代码（例如，应用一个不熟悉的模式）是基于充分的理由，通常是为了性能。保持这种特性很重要，可以让读者在阅读一段新的代码时清楚地知道他们应该把注意力放在哪里。

标准库中包含了许多这一原则发挥作用的例子。例如：

- 在 `package sort` 中的维护者注释
- 好的[同一软件包中可运行的例子](https://cs.opensource.google/go/go/+/refs/tags/go1.19.2:src/sort/example_search_test.go)，这对用户（他们会[查看 godoc](https://pkg.go.dev/sort#pkg-examples)）和维护者（他们[作为测试的一部分运行](https://gocn.github.io/styleguide/docs/03-decisions/#示例examples)）都有利
- `strings.Cut` 只有四行代码，但它们提高了[callsites 的清晰性和正确性](https://github.com/golang/go/issues/46336)

### 简约

你的 Go 代码对于使用、阅读和维护它的人来说应该是简单的。

Go 代码应该以最简单的方式编写，在行为和性能方面都能实现其目标。在 Google Go 代码库中，简单的代码：

- 从头至尾都易于阅读
- 不预设你已经知道它在做什么
- 不预设你能记住前面所有的代码
- 不含非必要的抽象层次
- 不含过于通用的命名
- 让读者清楚地了解到传值与决定的传播情况
- 有注释，解释为什么，而不是代码正在做什么，以避免未来的歧义
- 有独立的文档
- 包含有效的错误与失败用例测试
- 往往不是看起来“聪明”的代码

在代码的简单性和 API 使用的简单性之间可能会需要权衡。例如，让代码更复杂可能是值得的，这样 API 的终端用户可以更容易地正确调用 API。相反，把一些额外的工作留给 API 的终端用户也是值得的，这样代码就会保持简单和容易理解。

当代码需要复杂性时，应该有意地增加复杂性。如果需要额外的性能，或者一个特定的库或服务有多个不同的客户，这通常是必要的。复杂性可能是合理的，但它应该有相应的文档，以便客户和未来的维护者能够理解和驾驭这种复杂性。这应该用测试和例子来补充，以证明其正确的用法，特别是如果同时有一个“简单”和“复杂”的方法来使用代码。

这一原则并不意味着复杂的代码不能或不应该用 Go 编写，也不意味着 Go 代码不允许复杂。我们努力使代码库避免不必要的复杂性，因此当复杂性出现时，它表明有关的代码需要仔细理解和维护。理想情况下，应该有相应的注释来解释其中的道理，并指出应该注意的地方。在优化代码以提高性能时，经常会出现这种情况；这样做往往需要更复杂的方法，比如预先分配一个缓冲区并在整个 goroutine 生命周期内重复使用它。当维护者看到这种情况时，应该是一个线索，说明相关的代码是基于性能的关键考虑，这应该影响到未来修改时的谨慎。另一方面，如果不必要地使用，这种复杂性会给那些需要在未来阅读或修改代码的人带来负担。

如果代码非常复杂，但其目的应该是简单的，这往往是一个我们可以重新审视代码实现的信号，看看是否有更简单的方法来完成同样的事情。

#### 最小化机制

如果有几种方法来表达同一个想法，最好选择使用最标准工具的方法。复杂的机制经常存在，但不应该无缘无故地使用。根据需要增加代码的复杂性是很容易的，而在发现没有必要的情况下删除现有的复杂性则要难得多。

1. 当足以满足你的使用情况时，争取使用一个核心语言结构（例如通道、切片、地图、循环或结构）
2. 如果没有，就在标准库中寻找一个工具（如 HTTP 客户端或模板引擎）
3. 最后，在引入新的依赖或创建自己的依赖之前，考虑Google 代码库中是否有一个能够满足的核心库

例如，考虑生产代码包含一个绑定在变量上的标志，它的默认值必须在测试中被覆盖。除非打算测试程序的命令行界面本身（例如，用`os/exec`），否则直接覆盖绑定的值比使用 `flag.Set` 更简单，因此更可取。

同样，如果一段代码需要检查集合成员的资格，一个布尔值的映射（例如，`map[string]bool`）通常就足够了。只有在需要更复杂的操作，不能使用 map 或过于复杂时，才应使用提供类似集合类型和功能的库。

### 简洁

简洁的 Go 代码具有很高的信噪比。它很容易分辨出相关的细节，而命名和结构则引导读者了解这些细节。

而有很多东西会常常会阻碍这些最突出的细节：

- 重复代码
- 外来的语法
- [含义不明的名称](https://gocn.github.io/styleguide/docs/02-guide/#命名)
- 不必要的抽象
- 空白

重复代码尤其容易掩盖每个相似代码之间的差异，需要读者直观地比较相似的代码行来发现变化。[表驱动测试](https://github.com/golang/go/wiki/TableDrivenTests)是一个很好的例子，这种机制可以简明地从每个重复的重要细节中找出共同的代码，但是选择哪些部分囊括在表中，会对表格的易懂程度产生影响。

在考虑多种结构代码的方式时，值得考虑哪种方式能使重要的细节最显著。

理解和使用常见的代码结构和规范对于保持高信噪比也很重要。例如，下面的代码块在[错误处理](https://go.dev/blog/errors-are-values)中非常常见，读者可以很快理解这个代码块的目的。

```go
// Good:
if err := doSomething(); err != nil {
    // ...
}
```

如果代码看起来非常相似但却有细微的不同，读者可能不会注意到这种变化。在这样的情况下，值得故意[“提高”](https://gocn.github.io/styleguide/docs/04-best-practices/#信号增强)错误检查的信号，增加一个注释以引起关注。

```go
// Good:
if err := doSomething(); err == nil { // if NO error
    // ...
}
```

### 可维护性

代码被编辑的次数比它写它的次数多得多。可读的代码不仅对试图了解其工作原理的读者有意义，而且对需要改写它的程序员也有意义，清晰性很关键。

可维护的代码：

- 容易让未来的程序员正确进行修改
- 拥有结构化的 API，使其能够优雅地增加
- 清楚代码预设条件，并选择映射到问题结构而不是代码结构的抽象
- 避免不必要的耦合，不包括不使用的功能
- 有一个全面的测试套件，以确保预期行为可控、重要逻辑正确，并且测试在失败的情况下提供清晰、可操作的诊断

当使用像接口和类型这样的抽象时，根据定义，它们会从使用的上下文中移除信息，因此必须确保它们提供足够的好处。当使用具体类型时，编辑器和 IDE 可以直接连接到方法定义并显示相应的文档，但在其他情况下只能参考接口定义。接口是一个强大的工具，但也是有代价的，因为维护者可能需要了解底层实现的具体细节才能正确使用接口，这必须在接口文档中或在调用现场进行解释。

可维护的代码还可以避免在容易忽视的地方隐藏重要的细节。例如，在下面的每一行代码中，是否有 `:` 字符对于理解这一行至关重要。

```go
// Bad:
// 使用 = 而不是 := 可以完全改变这一行的含义
if user, err = db.UserByID(userID); err != nil {
    // ...
}
```

```go
// Bad:
// 这行中间的 ！ 很容易错过
leap := (year%4 == 0) && (!(year%100 == 0) || (year%400 == 0))
```

这两种写法不能说错误，但都可以写得更明确，或者可以有一个附带的评论，提醒注意重要的行为。

```go
// Good:
u, err := db.UserByID(userID)
if err != nil {
    return fmt.Errorf("invalid origin user: %s", err)
}
user = u
```

```go
// Good:
// 公历闰年不仅仅是 year%4 == 0
// 查看 https://en.wikipedia.org/wiki/Leap_year#Algorithm.
var (
    leap4   = year%4 == 0
    leap100 = year%100 == 0
    leap400 = year%400 == 0
)
leap := leap4 && (!leap100 || leap400)
```

同样地，一个隐藏了关键逻辑或重要边界情况的辅助函数，可能会使未来的变化很容易地被误解。

易联想的名字是可维护代码的另一个特点。一个包的用户或一段代码的维护者应该能够联想到一个变量、方法或函数在特定情况下的名称。相同概念的函数参数和接收器名称通常应该共享相同的名称，这既是为了保持文档的可理解性，也是为了方便以最小的开销重构代码。

可维护的代码尽量减少其依赖性（包括隐性和显性）。对更少包的依赖意味着更少的代码行可以影响其行为。避免对内部或未记录的行为的依赖，使得代码在未来这些行为发生变化时，不太容易造成维护负担。

在考虑如何构造或编写代码时，值得花时间去思考代码可能随着时间的推移而演变的方式。如果一个给定的方法更有利于未来更容易和更安全的变化，这往往是一个很好的权衡，即使它意味着一个稍微复杂的设计。

### 一致

一致性的代码是指在更广泛的代码库中，在一个团队或包的范围内，甚至在一个文件中，看起来、感觉和行为都是类似的代码。

一致性的问题并不凌驾于上述的任何原则之上，但如果必须有所取舍，那往往有利于一致性的实现。

一个包内的一致性通常是最直接重要的一致性水平。如果同一个问题在一个包里有多种处理方式，或者同一个概念在一个文件里有很多名字，那就会非常不优雅。然而，即使这样，也不应该凌驾于文件的风格原则或全局一致性之上。

## 核心准则

这些准则收集了所有 Go 代码都应遵循的 Go 风格的最重要方面。我们希望这些原则在你被保障可读性的时候就已经学会并遵循了。这些不会经常改变，新增加内容也有较高准入门槛。

下面的准则是对 [Effective Go](https://go.dev/doc/effective_go) 中建议的扩展，它为整个社区的 Go 代码提供了一个共同的基准线。

### 格式化

所有 Go 源文件必须符合 gofmt 工具所输出的格式。这个格式是由 Google 代码库中的预提交检查强制执行的。[生成的代码](https://docs.bazel.build/versions/main/be/general.html#genrule)通常也应该被格式化（例如，通过使用`format.Source`），因为它也可以在代码搜索中浏览。

### 大小写混合

Go 源代码在编写包含多个字的名称时使用`MixedCaps`或`mixedCaps`（驼峰大写）而不是下划线（蛇形大写）。

即使在其他语言中打破惯例，这也适用。例如，一个常量如果被导出，则为`MaxLength`（而不是`MAX_LENGTH`），如果未被导出，则为`maxLength`（而不是`max_length`）。

基于初始化大小写的考量，局部变量被认为是 [不可导出的](https://go.dev/ref/spec#Exported_identifiers)。

### 行长度

Go 源代码没有固定的行长度。如果觉得某一行太长，就应该对其进行重构而不是破坏。如果它已经很短了，那么就应该允许它继续增加。

不要在以下情况进行分行：

- 在[缩进变化](https://gocn.github.io/styleguide/docs/03-decisions/#缩进的混乱)之前(例如，函数声明、条件)
- 要使一个长的字符串（例如，一个 URL）适合于多个较短的行

### 命名

命名是艺术而不是科学。在 Go 中，名字往往比许多其他语言的名字短一些，但同样的[一般准则](https://testing.googleblog.com/2017/10/code-health-identifiernamingpostforworl.html)也适用，名称应：

- 使用时不感到[重复](https://gocn.github.io/styleguide/docs/03-decisions/#重复repetition)
- 将上下文考虑在内
- 不重复已经明确的概念

你可以在[决定](https://gocn.github.io/styleguide/docs/02-guide/#命名)中找到关于命名的更具体的指导。

### 本地化一致性

如果风格指南对某一特定的风格点没有说明，作者可以自由选择他们喜欢的风格，除非相近的代码（通常在同一个文件或包内，但有时在一个团队或项目目录内）对这个问题采取了一致的立场。

**有效的**本地风格化考虑例子：

- 使用 `%s` or `%v` 来打印错误
- 使用缓冲通道来代替 mutexes

**无效的**本地化风格化考虑例子：

- 代码行长度的限制
- 使用基于断言的测试库

如果本地化风格与风格指南不一致，但对可读性的影响仅限于一个文件，它通常会在代码审查中浮出水面，而一致的修复将超出有关 CL 的范围。在这一点上，提交一个 bug 来跟踪修复是合适的。

如果一个改变会使现有的风格偏差变大，在更多的 API 表面暴露出来，扩大存在偏差的文件数量，或者引入一个实际的错误，那么局部一致性就不再是违反新代码风格指南的有效理由。在这些情况下，作者应该在同一 CL 中清理现有的代码库，在当前 CL 之前进行重构，或者找到一个至少不会使本地化问题恶化的替代方案。

{{< button relref="./01-overview.md" >}}上一章{{< /button >}}
{{< button relref="./03-decisions.md" >}}下一章{{< /button >}}