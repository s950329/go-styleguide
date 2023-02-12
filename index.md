# Go Style

https://google.github.io/styleguide/go

[Overview](index) | [Guide](guide) | [Decisions](decisions) |
[Best practices](best-practices)

<!--

-->

{% raw %}

<a id="about"></a>

## About

《Go 風格指南》和相關文件規範了目前最佳的撰寫可讀且符合慣用語法的 Go 的方法。遵從風格指南並非絕對必要，而且這些文件永遠不會是詳盡的。我們的目的是減少寫可讀 Go 時的猜測，讓新手能夠避免常見的錯誤。風格指南也用於統一 Google 中審查 Go 代碼時提供的風格指導。

| Document            | Link                                                  | Primary Audience    | [Normative] | [Canonical] |
| ------------------- | ----------------------------------------------------- | ------------------- | ----------- | ----------- |
| **Style Guide**     | https://google.github.io/styleguide/go/guide          | Everyone            | Yes         | Yes         |
| **Style Decisions** | https://google.github.io/styleguide/go/decisions      | Readability Mentors | Yes         | No          |
| **Best Practices**  | https://google.github.io/styleguide/go/best-practices | Anyone interested   | No          | No          |

[normative]: #normative
[canonical]: #canonical

<a id="docs"></a>

### Documents

1.  **[《風格指南》](<(https://google.github.io/styleguide/go/guide)>)** 概述了 Google 的 Go 風格基礎。該文件具有決定性，是《風格決策》和《最佳實踐》建議的基礎。

2.  **[《風格決策》](https://google.github.io/styleguide/go/decisions)** 是一份更冗長的文件，總結了有關特定風格的決策，並在適當的情況下討論了決策背後的原因。

    這些決策有時會根據新數據、新語言特性、新庫或新興模式而改變，但不要求 Google 內的個人 Go 程序員跟上這份文件的更新。

3.  **[《最佳實踐》](https://google.github.io/styleguide/go/best-practices)** 列出了解決常見問題、易於閱讀且維護代碼需要的某些演化模式。

    這些最佳實踐並非規範，但鼓勵 Google 內的 Go 程序員在可能的情況下使用它們，以保持代碼庫的統一和一致。

這些文件的目的是:

- 確定一組原則，以衡量替代風格
- 將確定的 Go 風格問題規範化
- 記錄和提供 Go 慣用語法的規範範例
- 記錄各種風格決策的優缺點
- 幫助減少 Go 可讀性審查中的驚喜
- 幫助可讀性導師使用一致的術語和指導

這些文件的目的 **不是**：

- 成為可讀性審查中可以提供的詳盡評論列表
- 列出所有人都需要時刻記住和遵循的規則
- 取代在使用語言特性和風格時的良好判斷力
- 為了消除風格差異而進行大規模更改的理由

從一個 Go 程序員到另一個 Go 程序員以及從一個團隊的代碼庫到另一個團隊的代碼庫總是存在差異。然而，對 Google 和 Alphabet 而言，使我們的代碼庫盡可能一致是最有利的（查看 [guide](guide#consistency) 以了解更多一致性）。為此，請隨時進行風格改進，但您無需挑剔每一個違反風格指南的違例。特別是，這些文件可能會隨時間而改變，這不是在現有代碼庫中導致額外改動的原因。只需使用最新的最佳實踐編寫新代碼，並隨著時間解決附近的問題即可。

重要的是要認識到風格問題固有的個人性，並且總是存在固有的權衡。這些文件中的許多指導都是主觀的，但就像使用 gofmt 一樣，它們提供統一性的重要價值。因此，風格建議不會在沒有適當討論的情況下更改，Google 內的 Go 程序員被鼓勵遵循風格指南，即使他們可能不贊同。

<a id="definitions"></a>

## Definitions

在這些風格文件中，以下詞語被定義如下：

- **標準（Canonical）**：建立標準性和持久性規則 <a id="canonical"></a>
  <a id="canonical"></a>

  在這些文件中，「標準」用於描述被認為是所有代碼（新舊代碼）應遵循的標準，並且基本上不會隨著時間的推移而發生變化。標準文件中的原則應該被作者和審查人員所理解，因此，標準文件中包含的所有內容都必須符合高標準。因此，標準文件通常比非標準文件更簡短，並規定了較少的風格。

  https://google.github.io/styleguide/go#canonical

- **規範（Normative）**：旨在建立一致性 <a id="normative"></a>

  在這些文件中，「規範」用於描述被認為是 Go 代碼審查人員使用的風格要素，以便建議、術語和理由是一致的。這些元素可能會隨時間而改變，這些文件將反應此類變化，以便審查人員保持一致且最新。不要求 Go 代碼的作者熟悉規範文件，但審查人員在進行可讀性審查時通常會使用這些文件作為參考。

  https://google.github.io/styleguide/go#normative

- 慣用（Idiomatic）：常見且熟悉的 <a id="idiomatic"></a>

  在這些文件中，「慣用」用於指稱在 Go 代碼中普遍存在並已成為易於識別的熟悉模式。通常，如果在上下文中兩者都能夠達到同樣的目的，則應優先選擇慣用的模式，因為這是讀者最為熟悉的。

  https://google.github.io/styleguide/go#idiomatic

<a id="references"></a>

## Additional references

本指南假設讀者熟悉 [Effective Go]，因為它為整個 Go 社區的 Go 代碼提供了一個共同的基礎。

以下是一些額外的資源，供那些希望自我學習 Go 風格的人和希望在其審查中提供進一步可鏈接內容的審查人員使用。Go 可讀性審查過程的參與者不需要熟悉這些資源，但它們可能會作為可讀性審查中的上下文出現。

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
