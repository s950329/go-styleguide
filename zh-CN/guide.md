<!--* toc_depth: 3 *-->

# Go Style Guide

https://google.github.io/styleguide/go/guide

[Overview](index) | [Guide](guide) | [Decisions](decisions) |
[Best practices](best-practices)

<!--

-->

{% raw %}

**Note:** This is part of a series of documents that outline [Go Style](index)
at Google. This document is **[normative](index#normative) and
[canonical](index#canonical)**. See [the overview](index#about) for more
information.

<a id="principles"></a>

## 风格原则

有一些全面的原则总结了如何考虑编写可读的 Go 代码。以下是可读代码的属性，按重要度排序：

1.  **[清晰性（Clarity）]**: 代码的目的和意图对读者清晰明了。
1.  **[简洁性（Simplicity）]**: 代码以最简单的方式实现其目标。
1.  **[简明性（Concision）]**: 代码具有高的噪信比。
1.  **[可维护性（Maintainability）]**: 代码是易于维护的。
1.  **[一致性（Consistency）]**: 代码与更广泛的 Google 代码库保持一致。

[清晰性（clarity）]: #clarity
[简洁性（simplicity）]: #simplicity
[简明性（concision）]: #concision
[可维护性（maintainability）]: #maintainability
[一致性（consistency）]: #consistency

<a id="clarity"></a>

### 清晰性

可读性的核心目标是产生对读者来说清晰的代码。

通过有效的命名、有用的注释和高效的代码组织来实现清晰性。

清晰性应该从读者的角度来看，而不是代码的作者。代码易于阅读比易于编写更重要。代码的清晰性有两个不同的方面：

- [What is the code actually doing?](#clarity-purpose)
- [Why is the code doing what it does?](#clarity-rationale)

<a id="clarity-purpose"></a>

#### What is the code actually doing?

Go 设计的宗旨是让人们相对容易地看出代码在做什么。如果有不确定的情况或读者需要先前的知识才能理解代码，这值得我们花时间让代码的目的更清晰，以便未来的读者更容易理解。例如，可以：

- 使用更具描述性的变量名称
- 添加更多的注释
- 用空格和注释分隔代码
- 重构代码为单独的功能函式/方法，使其更模块化

这里没有一种通用的解决方案，但在开发 Go 代码时，重视清晰性是很重要的。

<a id="clarity-rationale"></a>

#### Why is the code doing what it does?

代码的意图通常可以通过变量、函数、方法或包的名称充分传达。如果无法做到这一点，添加注释就变得非常重要。当代码包含读者可能不熟悉的细微差别时，“为什么？”就显得格外重要，例如：

- 语言中的细微差别，例如闭包会捕获一个循环变量，但该闭包与循环变量相距很远
- 业务逻辑的细微差别，例如一个访问控制检查需要区分实际用户和冒充用户的人

某个 API 可能需要特别小心地使用。例如，一段代码可能因为性能原因而错综复杂，难以理解，或一个复杂的数学运算序列可能以意想不到的方式使用了类型转换。在这些情况和更多情况下，重要的是随附的注释和文件说明这些方面，以便未来的维护人员不会犯错，读者可以理解代码，而无需进行反向工程。

同时，需要意识到某些提供清晰度的尝试（例如添加额外的注释）可能实际上会通过增加混乱、重新陈述代码已经说过的内容、与代码矛盾或增加维护负担来掩盖代码的目的。让代码自己讲话（例如使符号名本身能自我描述）而不是添加冗余注释往往更好。通常，注释应解释为什么要这样做，而不是代码在做什么。

Google 代码库基本上是统一和一致的。通常情况下，突出显示的代码（例如使用不熟悉的模式）之所以这样做，是有很好的理由的，通常是为了性能。保持这一特性对于让读者清楚地了解他们在阅读新的代码时应该关注的方向非常重要。

标准库中包含了很多展现这一原则的范例，其中包括：

- Maintainer comments in
  [`package sort`](https://cs.opensource.google/go/go/+/refs/tags/go1.19.2:src/sort/sort.go).
- Good
  [runnable examples in the same package](https://cs.opensource.google/go/go/+/refs/tags/go1.19.2:src/sort/example_search_test.go),
  which benefit both users (they
  [show up in godoc](https://pkg.go.dev/sort#pkg-examples)) and maintainers
  (they [run as part of tests](decisions#examples)).
- [`strings.Cut`](https://pkg.go.dev/strings#Cut) is only four lines of code,
  but they improve the
  [clarity and correctness of callsites](https://github.com/golang/go/issues/46336).

<a id="simplicity"></a>

### 简单性

你的 Go 代码应该对使用、阅读和维护它的人而言都是简单的。

Go 代码应该以实现其目标为基础，既在行为上，也在性能上，以最简单的方式编写。在 Google 的 Go 代码库中，简单的代码：

- 容易从顶部到底部阅读
- 不假设你已经知道它在做什么
- 不假设你可以记住所有之前的代码
- 没有不必要的抽象层次
- 没有引起读者注意的平凡名称
- 使读者清楚地了解价值观和决策
- 具有解释代码为什么而不是什么的注释，以避免未来偏差
- 具有独立的文件
- 具有有用的错误和有用的测试失败
- 可能经常与“巧妙”的代码相互排斥

代码简单性和 API 使用简单性之间可能存在权衡。例如，代码更复杂，以便 API 的最终用户更容易正确地调用 API，可能是值得的。相反，让 API 的最终用户多做一点额外的工作，以便代码保持简单和易于理解，也可能是值得的。

当代码需要复杂性时，应该有意地添加复杂性。如果需要额外的性能或有特定库或服务的多个不同的客户端，这通常是必需的。复杂性可能是合理的，但应该随附相应的文件，以便客户和未来的维护人员能够理解和运用复杂性。如果代码既有“简单”又有“复杂”的使用方式，尤其是需要演示其正确使用的测试和示例，那么这应该得到补充。

这个原则并不意味着 Go 程式码不能或不应该是复杂的。我们力求让代码库避免不必要的复杂性，这样当复杂性出现时，就表明相应的代码需要仔细理解和维护。理想情况下，应该有相应的注释来解释原理并确定应采取的注意事项。当优化代码以提高性能时，往往需要更复杂的方法，比如在整个 goroutine 的生命周期中预分配缓冲区并重复使用它。当维护人员看到这种情况时，它应该是一个提示，表明相应的代码对性能至关重要，这应该影响到在进行未来更改时所采取的谨慎措施。另一方面，如果不必要地使用这种复杂性，这种复杂性将成为未来需要阅读或更改代码的人的负担。

如果代码的目的应该是简单的，但最终变得非常复杂，这通常表明需要重新检查实现方式，看是否有更简单的方式来实现同样的目标。

<a id="least-mechanism"></a>

#### 最小机制

当有多种表达相同想法的方式时，请优先选择使用最常见的工具。尽管存在复杂的机制，但不应无故使用。增加代码的复杂度很容易，但发现它不必要后却很难删除。

1. 尽可能使用核心语言结构（例如 channel、slice、map、回圈或 struct）来满足您的用例。
2. 如果没有，请在标准库中查找适当的工具（例如 HTTP 客户端或模板引擎）。
3. 最后，考虑是否存在于 Google 代码库中的核心库能够满足需求，才引入新的依赖或创建自己的库。

例如，考虑一个生产代码，其中包含一个绑定到变量的标志，具有预设值，在测试中必须覆盖该值。除非意图测试程式的命令行界面本身（例如，使用 `os/exec`），否则直接覆盖绑定值比使用 `flag.Set` 更简单，因此更可取。

同样地，如果一段代码需要集合成员检查，布林值映射（例如 `map[string]bool`）通常就足够了。仅在需要比使用映射更复杂的操作或操作过于复杂时才使用提供类集合类型和功能的库。

<a id="concision"></a>

### Concision

Go 简洁的程式码具有高信噪比，容易辨识相关细节，而命名和架构可以引导读者了解这些细节。

有许多事情可能妨碍在任何时候突出显示最重要的细节：

- 重复的程式码
- 多余的语法
- [不透明的名称](#naming)
- 不必要的抽象
- 空格

重复的程式码尤其隐藏了每个几乎相同区段之间的差异，需要读者视觉上比较类似的程式码行以找到差异。[Table-driven testing] 是一个很好的例子，可以简洁地将共同代码因素从每个重复的重要细节中提取出来，但是选择包含在表格中的哪些部分将对表格易于理解产生影响。

在考虑多种程式码结构时，值得考虑哪种方式使重要细节最明显。

了解和使用常见的程式码结构和惯用语法对于维持高信噪比也很重要。例如，以下程式码块在[错误处理]中非常常见，读者可以快速了解此块的用途。

```go
// Good:
if err := doSomething(); err != nil {
    // ...
}
```

如果代码看起来与此非常相似，但微妙地不同，读者可能不会注意到更改。在这种情况下，值得有意地 ["增强"] 错误检查的信号，方法是添加一个注释来引起注意。

```go
// Good:
if err := doSomething(); err == nil { // if NO error
    // ...
}
```

[table-driven testing]: https://github.com/golang/go/wiki/TableDrivenTests
[error handling]: https://go.dev/blog/errors-are-values
["boosting"]: best-practices#signal-boost

<a id="maintainability"></a>

### Maintainability

可读性高的程式码不仅对试图理解其工作方式的读者有意义，也对需要更改它的程式设计师有意义。清晰度至关重要。

易于维护的程式码：

- 易于未来的程式设计师正确地修改
- 有能够正常增长的 API 结构
- 对它所做的假设有明确的说明，并选择映射到问题结构而不是代码结构的抽象
- 避免不必要的耦合，并且不包含未使用的功能
- 拥有全面的测试套件，以确保承诺的行为得以维持并且重要的逻辑是正确的，测试提供明确的，可操作的诊断以便在失败时使用。

当使用像介面和类型这样的抽象时，它们从它们所使用的上下文中删除了信息，因此确保它们提供足够的好处非常重要。编辑器和 IDE 可以直接连接到方法定义，并在使用具体类型时显示相应的文档，但是否则只能引用介面定义。介面是一个强大的工具，但是伴随着一些成本，因为维护者可能需要了解底层实现的具体细节，以便正确使用介面，这必须在介面文档或呼叫点处加以说明。

易于维护的代码还避免将重要细节隐藏在容易忽略的地方。例如，在以下每行程式码中，有或缺少一个字符对于理解该行至关重要：

```go
// Bad:
// The use of = instead of := can change this line completely.
if user, err = db.UserByID(userID); err != nil {
    // ...
}
```

```go
// Bad:
// The ! in the middle of this line is very easy to miss.
leap := (year%4 == 0) && (!(year%100 == 0) || (year%400 == 0))
```

这两个范例都不是错误的，但是它们都可以用更明确的方式来撰写，或是加上注释以强调其重要行为：

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
// Gregorian leap years aren't just year%4 == 0.
// See https://en.wikipedia.org/wiki/Leap_year#Algorithm.
var (
    leap4   = year%4 == 0
    leap100 = year%100 == 0
    leap400 = year%400 == 0
)
leap := leap4 && (!leap100 || leap400)
```

同样地，隐藏关键逻辑或重要边缘案例的辅助函数可能会导致未来的更改无法正确处理。

可预测的名称是易于维护的代码的另一个特点。使用套件的用户或程式码的维护者应该能够预测在特定上下文中变量、方法或函数的名称。相同概念的函数参数和接收器名称通常应该共享相同的名称，以便让文件易于理解，并且便于在最小的开销下重构代码。

可维护的代码最小化其依赖（包括隐含和显式的）。依赖于更少的套件意味着更少的代码行可以影响行为。避免依赖于内部或未记录的行为使代码在这些行为在未来发生更改时更少会产生维护负担。

在考虑如何结构化或编写代码时，值得花时间思考代码可能随时间演变的方式。如果一种方法更有助于实现未来更轻松、更安全的更改，即使这意味着稍微复杂一些的设计，那也通常是一个好的折衷方案。

<a id="consistency"></a>

### Consistency

一致性代表的是，在整个程式码库、团队或套件内，甚至是单个档案内，程式码看起来、感觉和行为都类似的程式码。

一致性问题不会覆盖上述任何原则，但如果必须作出选择，通常更有利的做法是遵从一致性。

套件内的一致性通常是一致性中最重要的层级。如果在套件内以多种方式解决相同的问题，或者在档案内使用多个名称表示相同的概念，那么这将会非常令人烦扰。然而，即使如此，这也不应覆盖已经明确定义的风格原则或全域一致性。

<a id="core"></a>

## Core guidelines

以下的指南收集了 Go 风格的最重要方面，所有的 Go 代码都应该遵循这些原则。我们期望这些原则在读取权限获得之前就被学习和遵循。这些原则不会经常变化，新的添加必须符合高标准。下面的指南扩展了 [Effective Go] 中的建议，为整个社区的 Go 代码提供了共同的基准。

[effective go]: https://go.dev/doc/effective_go

<a id="formatting"></a>

### Formatting

所有的 Go 代码文件必须符合 gofmt 工具的输出格式。Google 的代码库中有一个预提交检查会强制执行此格式。通常情况下，[生成的代码] 也应进行格式化（例如使用 [format.Source] 函数），因为这些代码也可以在代码搜寻中被浏览。

[generated code]: https://docs.bazel.build/versions/main/be/general.html#genrule
[`format.source`]: https://pkg.go.dev/go/format#Source

<a id="mixed-caps"></a>

### MixedCaps

Go 语言的变数名称采用大小写混合的方式（`MixedCaps` 或 `mixedCaps`，也叫做驼峰命名法），而不是使用底线(snake case)来写多个单字的名称。

即使这样做与其他语言的命名规则不同，仍然应这样做。例如，如果一个常量是被公开的，那么它应该是 `MaxLength`(而不是 `MAX_LENGTH`)，如果未被公开，则应该是 `maxLength`(而不是 `max_length`)。

为了选择最初的大写字母，局部变数被视为[未公开]（[unexported]）。 // 待润饰

Go source code uses `MixedCaps` or `mixedCaps` (camel case) rather than
underscores (snake case) when writing multi-word names.

This applies even when it breaks conventions in other languages. For example, a
constant is `MaxLength` (not `MAX_LENGTH`) if exported and `maxLength` (not
`max_length`) if unexported.

Local variables are considered [unexported] for the purpose of choosing the
initial capitalization.

<!--#include file="/go/g3doc/style/includes/special-name-exception.md"-->

[unexported]: https://go.dev/ref/spec#Exported_identifiers

<a id="line-length"></a>

### Line length

Go 语言的程式码没有固定的行长限制。如果一行程式码看起来太长，应该考虑重构，而不是将其拆成多行。如果已经尽可能缩短行长，则可以保留长行。

不要在以下情况下拆分一行程式码：

- 在[缩排变更](decisions#indentation-confusion)之前（例如函数声明、条件语句）。
- 将长字符串（例如 URL）分成多个较短的行。

<a id="naming"></a>

### Naming

命名比起科学更像是艺术。在 Go 语言中，名称通常比许多其他语言要短，但相同的[一般指南]适用。名称应该：

- 使用时不应感觉[重复](decisions#repetition)
- 考虑上下文
- 不要重复已经清晰的概念

您可以在[决策](decisions#naming)中找到更具体的命名指南。

[general guidelines]: https://testing.googleblog.com/2017/10/code-health-identifiernamingpostforworl.html

<a id="local-consistency"></a>

### Local consistency

如果风格指南没有对特定的风格点提出任何建议，作者可以自由选择他们喜欢的风格，除非相近的程式码（通常在同一个档案或套件中，但有时在团队或专案目录中）对该问题已经采取了一致的态度。

**有效的** 本地风格考虑的范例：

- Use of `%s` or `%v` for formatted printing of errors
- Usage of buffered channels in lieu of mutexes

Examples of **invalid** local style considerations:

- Line length restrictions for code
- Use of assertion-based testing libraries

如果本地风格与风格指南不一致，但可读性的影响仅限于一个文件，它通常会在代码审查中出现，而一致的修复超出了相关 CL 的范围。此时，适当的做法是提交一个 bug 以跟踪修复。

如果更改将恶化现有的风格偏差，将其表现在更多 API 表面上，扩大存在偏差的文件数量，或者引入实际的 bug，那么本地一致性不再是新代码违反风格指南的借口。在这些情况下，作者应在同一 CL 中清理现有的代码库，对当前 CL 进行重构，或找到一个至少不会使本地问题恶化的替代方案。

<!--

-->

{% endraw %}
