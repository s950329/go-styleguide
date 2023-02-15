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

：
：
：
：
：

代碼實際上在做什麼？
代碼為什麼要做它所做的？

## 風格原則

有一些全面的原則總結了如何考慮編寫可讀的 Go 代碼。以下是可讀代碼的屬性，按重要度排序：

1.  **[清晰性（Clarity）]**: 代碼的目的和意圖對讀者清晰明瞭。
1.  **[簡潔性（Simplicity）]**: 代碼以最簡單的方式實現其目標。
1.  **[簡明性（Concision）]**: 代碼具有高的噪信比。
1.  **[可維護性（Maintainability）]**: 代碼是易於維護的。
1.  **[一致性（Consistency）]**: 代碼與更廣泛的 Google 代碼庫保持一致。

[clarity]: #clarity
[simplicity]: #simplicity
[concision]: #concision
[maintainability]: #maintainability
[consistency]: #consistency

<a id="clarity"></a>

### 清晰性

可讀性的核心目標是產生對讀者來說清晰的代碼。

通過有效的命名、有用的註釋和高效的代碼組織來實現清晰性。

清晰性應該從讀者的角度來看，而不是代碼的作者。代碼易於閱讀比易於編寫更重要。代碼的清晰性有兩個不同的方面：

- [What is the code actually doing?](#clarity-purpose)
- [Why is the code doing what it does?](#clarity-rationale)

<a id="clarity-purpose"></a>

#### What is the code actually doing?

Go 設計的宗旨是讓人們相對容易地看出代碼在做什麼。如果有不確定的情況或讀者需要先前的知識才能理解代碼，這值得我們花時間讓代碼的目的更清晰，以便未來的讀者更容易理解。例如，可以：

- 使用更具描述性的變量名稱
- 添加更多的註釋
- 用空格和註釋分隔代碼
- 重構代碼為單獨的功能函式/方法，使其更模塊化

這裡沒有一種通用的解決方案，但在開發 Go 代碼時，重視清晰性是很重要的。

<a id="clarity-rationale"></a>

#### Why is the code doing what it does?

代碼的意圖通常可以通過變量、函數、方法或包的名稱充分傳達。如果無法做到這一點，添加註釋就變得非常重要。當代碼包含讀者可能不熟悉的細微差別時，「為什麼？」就顯得格外重要，例如：

- 語言中的細微差別，例如閉包會捕獲一個循環變量，但該閉包與循環變量相距很遠
- 業務邏輯的細微差別，例如一個訪問控制檢查需要區分實際用戶和冒充用戶的人

某個 API 可能需要特別小心地使用。例如，一段代碼可能因為性能原因而錯綜複雜，難以理解，或一個複雜的數學運算序列可能以意想不到的方式使用了類型轉換。在這些情況和更多情況下，重要的是隨附的註釋和文檔說明這些方面，以便未來的維護人員不會犯錯，讀者可以理解代碼，而無需進行反向工程。

同時，需要意識到某些提供清晰度的嘗試（例如添加額外的註釋）可能實際上會通過增加混亂、重新陳述代碼已經說過的內容、與代碼矛盾或增加維護負擔來掩蓋代碼的目的。讓代碼自己講話（例如使符號名本身能自我描述）而不是添加冗余註釋往往更好。通常，註釋應解釋為什麼要這樣做，而不是代碼在做什麼。

Google 代碼庫基本上是統一和一致的。通常情況下，突出顯示的代碼（例如使用不熟悉的模式）之所以這樣做，是有很好的理由的，通常是為了性能。保持這一特性對於讓讀者清楚地了解他們在閱讀新的代碼時應該關注的方向非常重要。

標準庫中包含了很多展現這一原則的範例，其中包括：

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

### Simplicity

Your Go code should be simple for those using, reading, and maintaining it.

Go code should be written in the simplest way that accomplishes its goals, both
in terms of behavior and performance. Within the Google Go codebase, simple
code:

- Is easy to read from top to bottom
- Does not assume that you already know what it is doing
- Does not assume that you can memorize all of the preceding code
- Does not have unnecessary levels of abstraction
- Does not have names that call attention to something mundane
- Makes the propagation of values and decisions clear to the reader
- Has comments that explain why, not what, the code is doing to avoid future
  deviation
- Has documentation that stands on its own
- Has useful errors and useful test failures
- May often be mutually exclusive with "clever" code

Tradeoffs can arise between code simplicity and API usage simplicity. For
example, it may be worthwhile to have the code be more complex so that the end
user of the API may more easily call the API correctly. In contrast, it may also
be worthwhile to leave a bit of extra work to the end user of the API so that
the code remains simple and easy to understand.

When code needs complexity, the complexity should be added deliberately. This is
typically necessary if additional performance is required or where there are
multiple disparate customers of a particular library or service. Complexity may
be justified, but it should come with accompanying documentation so that clients
and future maintainers are able to understand and navigate the complexity. This
should be supplemented with tests and examples that demonstrate its correct
usage, especially if there is both a "simple" and a "complex" way to use the
code.

This principle does not imply that complex code cannot or should not be written
in Go or that Go code is not allowed to be complex. We strive for a codebase
that avoids unnecessary complexity so that when complexity does appear, it
indicates that the code in question requires care to understand and maintain.
Ideally, there should be accompanying commentary that explains the rationale and
identifies the care that should be taken. This often arises when optimizing code
for performance; doing so often requires a more complex approach, like
preallocating a buffer and reusing it throughout a goroutine lifetime. When a
maintainer sees this, it should be a clue that the code in question is
performance-critical, and that should influence the care that is taken when
making future changes. If employed unnecessarily, on the other hand, this
complexity is a burden on those who need to read or change the code in the
future.

If code turns out to be very complex when its purpose should be simple, this is
often a signal to revisit the implementation to see if there is a simpler way to
accomplish the same thing.

<a id="least-mechanism"></a>

#### Least mechanism

Where there are several ways to express the same idea, prefer the one that uses
the most standard tools. Sophisticated machinery often exists, but should not be
employed without reason. It is easy to add complexity to code as needed, whereas
it is much harder to remove existing complexity after it has been found to be
unnecessary.

1.  Aim to use a core language construct (for example a channel, slice, map,
    loop, or struct) when sufficient for your use case.
2.  If there isn't one, look for a tool within the standard library (like an
    HTTP client or a template engine).
3.  Finally, consider whether there is a core library in the Google codebase
    that is sufficient before introducing a new dependency or creating your own.

As an example, consider production code that contains a flag bound to a variable
with a default value which must be overridden in tests. Unless intending to test
the program's command-line interface itself (say, with `os/exec`), it is simpler
and therefore preferable to override the bound value directly rather than by
using `flag.Set`.

Similarly, if a piece of code requires a set membership check, a boolean-valued
map (e.g., `map[string]bool`) often suffices. Libraries that provide set-like
types and functionality should only be used if more complicated operations are
required that are impossible or overly complicated with a map.

<a id="concision"></a>

### Concision

Concise Go code has a high signal-to-noise ratio. It is easy to discern the
relevant details, and the naming and structure guide the reader through these
details.

There are many things that can get in the way of surfacing the most salient
details at any given time:

- Repetitive code
- Extraneous syntax
- [Opaque names](#naming)
- Unnecessary abstraction
- Whitespace

Repetitive code especially obscures the differences between each
nearly-identical section, and requires a reader to visually compare similar
lines of code to find the changes. [Table-driven testing] is a good example of a
mechanism that can concisely factor out the common code from the important
details of each repetition, but the choice of which pieces to include in the
table will have an impact on how easy the table is to understand.

When considering multiple ways to structure code, it is worth considering which
way makes important details the most apparent.

Understanding and using common code constructions and idioms are also important
for maintaining a high signal-to-noise ratio. For example, the following code
block is very common in [error handling], and the reader can quickly understand
the purpose of this block.

```go
// Good:
if err := doSomething(); err != nil {
    // ...
}
```

If code looks very similar to this but is subtly different, a reader may not
notice the change. In cases like this, it is worth intentionally ["boosting"]
the signal of the error check by adding a comment to call attention to it.

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
