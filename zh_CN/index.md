# Go Style

https://google.github.io/styleguide/go

[Overview](index) | [Guide](guide) | [Decisions](decisions) |
[Best practices](best-practices)

<!--

-->

{% raw %}

<a id="about"></a>

## 关于

《Go 风格指南》和相关文件规范了目前最佳的撰写可读且符合惯用语法的 Go 的方法。遵从风格指南并非绝对必要，而且这些文件永远不会是详尽的。我们的目的是减少写可读 Go 时的猜测，让新手能够避免常见的错误。风格指南也用于统一 Google 中审查 Go 代码时提供的风格指导。

| Document            | Link                                                  | Primary Audience    | [Normative] | [Canonical] |
| ------------------- | ----------------------------------------------------- | ------------------- | ----------- | ----------- |
| **Style Guide**     | https://google.github.io/styleguide/go/guide          | Everyone            | Yes         | Yes         |
| **Style Decisions** | https://google.github.io/styleguide/go/decisions      | Readability Mentors | Yes         | No          |
| **Best Practices**  | https://google.github.io/styleguide/go/best-practices | Anyone interested   | No          | No          |

[normative]: #normative
[canonical]: #canonical

<a id="docs"></a>

### 文件

1.  **[《风格指南》](<(https://google.github.io/styleguide/go/guide)>)** 概述了 Google 的 Go 风格基础。该文件具有决定性，是《风格决策》和《最佳实践》建议的基础。

2.  **[《风格决策》](https://google.github.io/styleguide/go/decisions)** 是一份更冗长的文件，总结了有关特定风格的决策，并在适当的情况下讨论了决策背后的原因。

    这些决策有时会根据新数据、新语言特性、新库或新兴模式而改变，但不要求 Google 内的个人 Go 程序员跟上这份文件的更新。

3.  **[《最佳实践》](https://google.github.io/styleguide/go/best-practices)** 列出了解决常见问题、易于阅读且维护代码需要的某些演化模式。

    这些最佳实践并非规范，但鼓励 Google 内的 Go 程序员在可能的情况下使用它们，以保持代码库的统一和一致。

这些文件的目的是:

- 确定一组原则，以衡量替代风格
- 将确定的 Go 风格问题规范化
- 记录和提供 Go 惯用语法的规范范例
- 记录各种风格决策的优缺点
- 帮助减少 Go 可读性审查中的惊喜
- 帮助可读性导师使用一致的术语和指导

这些文件的目的 **不是**：

- 成为可读性审查中可以提供的详尽评论列表
- 列出所有人都需要时刻记住和遵循的规则
- 取代在使用语言特性和风格时的良好判断力
- 为了消除风格差异而进行大规模更改的理由

从一个 Go 程序员到另一个 Go 程序员以及从一个团队的代码库到另一个团队的代码库总是存在差异。然而，对 Google 和 Alphabet 而言，使我们的代码库尽可能一致是最有利的（查看 [guide](guide#consistency) 以了解更多一致性）。为此，请随时进行风格改进，但您无需挑剔每一个违反风格指南的违例。特别是，这些文件可能会随时间而改变，这不是在现有代码库中导致额外改动的原因。只需使用最新的最佳实践编写新代码，并随着时间解决附近的问题即可。

重要的是要认识到风格问题固有的个人性，并且总是存在固有的权衡。这些文件中的许多指导都是主观的，但就像使用 gofmt 一样，它们提供统一性的重要价值。因此，风格建议不会在没有适当讨论的情况下更改，Google 内的 Go 程序员被鼓励遵循风格指南，即使他们可能不赞同。

<a id="definitions"></a>

## Definitions

在这些风格文件中，以下词语被定义如下：

- **标准（Canonical）**：建立标准性和持久性规则 <a id="canonical"></a>
  <a id="canonical"></a>

  在这些文件中，“标准”用于描述被认为是所有代码（新旧代码）应遵循的标准，并且基本上不会随着时间的推移而发生变化。标准文件中的原则应该被作者和审查人员所理解，因此，标准文件中包含的所有内容都必须符合高标准。因此，标准文件通常比非标准文件更简短，并规定了较少的风格。

  https://google.github.io/styleguide/go#canonical

- **规范（Normative）**：旨在建立一致性 <a id="normative"></a>

  在这些文件中，“规范”用于描述被认为是 Go 代码审查人员使用的风格要素，以便建议、术语和理由是一致的。这些元素可能会随时间而改变，这些文件将反应此类变化，以便审查人员保持一致且最新。不要求 Go 代码的作者熟悉规范文件，但审查人员在进行可读性审查时通常会使用这些文件作为参考。

  https://google.github.io/styleguide/go#normative

- 惯用（Idiomatic）：常见且熟悉的 <a id="idiomatic"></a>

  在这些文件中，“惯用”用于指称在 Go 代码中普遍存在并已成为易于识别的熟悉模式。通常，如果在上下文中两者都能够达到同样的目的，则应优先选择惯用的模式，因为这是读者最为熟悉的。

  https://google.github.io/styleguide/go#idiomatic

<a id="references"></a>

## Additional references

本指南假设读者熟悉 [Effective Go]，因为它为整个 Go 社区的 Go 代码提供了一个共同的基础。

以下是一些额外的资源，供那些希望自我学习 Go 风格的人和希望在其审查中提供进一步可链接内容的审查人员使用。Go 可读性审查过程的参与者不需要熟悉这些资源，但它们可能会作为可读性审查中的上下文出现。

[effective go]: https://go.dev/doc/effective_go

**External References**

- [Go Language Specification](https://go.dev/ref/spec)
- [Go FAQ](https://go.dev/doc/faq)
- [Go Memory Model](https://go.dev/ref/mem)
- [Go Data Structures](https://research.swtch.com/godata)
- [Go Interfaces](https://research.swtch.com/interfaces)
- [Go Proverbs](https://go-proverbs.github.io/)

- <a id="gotip"></a> Go Tip Episodes - stay tuned.

- <a id="unit-testing-practices"></a> Unit Testing Practices - stay tuned.

**Relevant Testing-on-the-Toilet articles**

- [TotT: Identifier Naming][tott-431]
- [TotT: Testing State vs. Testing Interactions][tott-281]
- [TotT: Effective Testing][tott-324]
- [TotT: Risk-driven Testing][tott-329]
- [TotT: Change-detector Tests Considered Harmful][tott-350]

[tott-431]: https://testing.googleblog.com/2017/10/code-health-identifiernamingpostforworl.html
[tott-281]: https://testing.googleblog.com/2013/03/testing-on-toilet-testing-state-vs.html
[tott-324]: https://testing.googleblog.com/2014/05/testing-on-toilet-effective-testing.html
[tott-329]: https://testing.googleblog.com/2014/05/testing-on-toilet-risk-driven-testing.html
[tott-350]: https://testing.googleblog.com/2015/01/testing-on-toilet-change-detector-tests.html

**Additional External Writings**

- [Go and Dogma](https://research.swtch.com/dogma)
- [Less is exponentially more](https://commandcenter.blogspot.com/2012/06/less-is-exponentially-more.html)
- [Esmerelda's Imagination](https://commandcenter.blogspot.com/2011/12/esmereldas-imagination.html)
- [Regular expressions for parsing](https://commandcenter.blogspot.com/2011/08/regular-expressions-in-lexing-and.html)
- [Gofmt's style is no one's favorite, yet Gofmt is everyone's favorite](https://www.youtube.com/watch?v=PAAkCSZUG1c&t=8m43s)
  (YouTube)

<!--

-->

{% endraw %}
