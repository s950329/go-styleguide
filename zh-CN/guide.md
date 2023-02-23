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

某个 API 可能需要特别小心地使用。例如，一段代码可能因为性能原因而错综复杂，难以理解，或一个复杂的数学运算序列可能以意想不到的方式使用了类型转换。在这些情况和更多情况下，重要的是随附的注释和文档说明这些方面，以便未来的维护人员不会犯错，读者可以理解代码，而无需进行反向工程。

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
- 具有独立的文档
- 具有有用的错误和有用的测试失败
- 可能经常与“巧妙”的代码相互排斥

代码简单性和 API 使用简单性之间可能存在权衡。例如，代码更复杂，以便 API 的最终用户更容易正确地调用 API，可能是值得的。相反，让 API 的最终用户多做一点额外的工作，以便代码保持简单和易于理解，也可能是值得的。

当代码需要复杂性时，应该有意地添加复杂性。如果需要额外的性能或有特定库或服务的多个不同的客户端，这通常是必需的。复杂性可能是合理的，但应该随附相应的文档，以便客户和未来的维护人员能够理解和运用复杂性。如果代码既有“简单”又有“复杂”的使用方式，尤其是需要演示其正确使用的测试和示例，那么这应该得到补充。

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

Code is edited many more times than it is written. Readable code not only makes
sense to a reader who is trying to understand how it works, but also to the
programmer who needs to change it. Clarity is key.

Maintainable code:

- Is easy for a future programmer to modify correctly
- Has APIs that are structured so that they can grow gracefully
- Is clear about the assumptions that it makes and chooses abstractions that
  map to the structure of the problem, not to the structure of the code
- Avoids unnecessary coupling and doesn't include features that are not used
- Has a comprehensive test suite to ensure promised behaviors are maintained
  and important logic is correct, and the tests provide clear, actionable
  diagnostics in case of failure

When using abstractions like interfaces and types which by definition remove
information from the context in which they are used, it is important to ensure
that they provide sufficient benefit. Editors and IDEs can connect directly to a
method definition and show the corresponding documentation when a concrete type
is used, but can only refer to an interface definition otherwise. Interfaces are
a powerful tool, but come with a cost, since the maintainer may need to
understand the specifics of the underlying implementation in order to correctly
use the interface, which must be explained within the interface documentation or
at the call-site.

Maintainable code also avoids hiding important details in places that are easy
to overlook. For example, in each of the following lines of code, the presence
or lack of a single character is critical to understand the line:

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

Neither of these are incorrect, but both could be written in a more explicit
fashion, or could have an accompanying comment that calls attention to the
important behavior:

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

In the same way, a helper function that hides critical logic or an important
edge-case could make it easy for a future change to fail to account for it
properly.

Predictable names are another feature of maintainable code. A user of a package
or a maintainer of a piece of code should be able to predict the name of a
variable, method, or function in a given context. Function parameters and
receiver names for identical concepts should typically share the same name, both
to keep documentation understandable and to facilitate refactoring code with
minimal overhead.

Maintainable code minimizes its dependencies (both implicit and explicit).
Depending on fewer packages means fewer lines of code that can affect behavior.
Avoiding dependencies on internal or undocumented behavior makes code less
likely to impose a maintenance burden when those behaviors change in the future.

When considering how to structure or write code, it is worth taking the time to
think through ways in which the code may evolve over time. If a given approach
is more conducive to easier and safer future changes, that is often a good
trade-off, even if it means a slightly more complicated design.

<a id="consistency"></a>

### Consistency

Consistent code is code that looks, feels, and behaves like similar code
throughout the broader codebase, within the context of a team or package, and
even within a single file.

Consistency concerns do not override any of the principles above, but if a tie
must be broken, it is often beneficial to break it in favor of consistency.

Consistency within a package is often the most immediately important level of
consistency. It can be very jarring if the same problem is approached in
multiple ways throughout a package, or if the same concept has many names within
a file. However, even this should not override documented style principles or
global consistency.

<a id="core"></a>

## Core guidelines

These guidelines collect the most important aspects of Go style that all Go code
is expected to follow. We expect that these principles be learned and followed
by the time readability is granted. These are not expected to change frequently,
and new additions will have to clear a high bar.

The guidelines below expand on the recommendations in [Effective Go], which
provide a common baseline for Go code across the entire community.

[effective go]: https://go.dev/doc/effective_go

<a id="formatting"></a>

### Formatting

All Go source files must conform to the format outputted by the `gofmt` tool.
This format is enforced by a presubmit check in the Google codebase.
[Generated code] should generally also be formatted (e.g., by using
[`format.Source`]), as it is also browsable in Code Search.

[generated code]: https://docs.bazel.build/versions/main/be/general.html#genrule
[`format.source`]: https://pkg.go.dev/go/format#Source

<a id="mixed-caps"></a>

### MixedCaps

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

There is no fixed line length for Go source code. If a line feels too long, it
should be refactored instead of broken. If it is already as short as it is
practical for it to be, the line should be allowed to remain long.

Do not split a line:

- Before an [indentation change](decisions#indentation-confusion) (e.g.,
  function declaration, conditional)
- To make a long string (e.g., a URL) fit into multiple shorter lines

<a id="naming"></a>

### Naming

Naming is more art than science. In Go, names tend to be somewhat shorter than
in many other languages, but the same [general guidelines] apply. Names should:

- Not feel [repetitive](decisions#repetition) when they are used
- Take the context into consideration
- Not repeat concepts that are already clear

You can find more specific guidance on naming in [decisions](decisions#naming).

[general guidelines]: https://testing.googleblog.com/2017/10/code-health-identifiernamingpostforworl.html

<a id="local-consistency"></a>

### Local consistency

Where the style guide has nothing to say about a particular point of style,
authors are free to choose the style that they prefer, unless the code in close
proximity (usually within the same file or package, but sometimes within a team
or project directory) has taken a consistent stance on the issue.

Examples of **valid** local style considerations:

- Use of `%s` or `%v` for formatted printing of errors
- Usage of buffered channels in lieu of mutexes

Examples of **invalid** local style considerations:

- Line length restrictions for code
- Use of assertion-based testing libraries

If the local style disagrees with the style guide but the readability impact is
limited to one file, it will generally be surfaced in a code review for which a
consistent fix would be outside the scope of the CL in question. At that point,
it is appropriate to file a bug to track the fix.

If a change would worsen an existing style deviation, expose it in more API
surfaces, expand the number of files in which the deviation is present, or
introduce an actual bug, then local consistency is no longer a valid
justification for violating the style guide for new code. In these cases, it is
appropriate for the author to clean up the existing codebase in the same CL,
perform a refactor in advance of the current CL, or find an alternative that at
least does not make the local problem worse.

<!--

-->

{% endraw %}
