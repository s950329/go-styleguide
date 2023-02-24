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

## 風格原則

有一些全面的原則總結了如何考慮編寫可讀的 Go 代碼。以下是可讀代碼的屬性，按重要度排序：

1.  **[清晰性（Clarity）]**: 代碼的目的和意圖對讀者清晰明瞭。
1.  **[簡潔性（Simplicity）]**: 代碼以最簡單的方式實現其目標。
1.  **[簡明性（Concision）]**: 代碼具有高的噪信比。
1.  **[可維護性（Maintainability）]**: 代碼是易於維護的。
1.  **[一致性（Consistency）]**: 代碼與更廣泛的 Google 代碼庫保持一致。

[清晰性（clarity）]: #clarity
[簡潔性（simplicity）]: #simplicity
[簡明性（concision）]: #concision
[可維護性（maintainability）]: #maintainability
[一致性（consistency）]: #consistency

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

某個 API 可能需要特別小心地使用。例如，一段代碼可能因為性能原因而錯綜複雜，難以理解，或一個複雜的數學運算序列可能以意想不到的方式使用了類型轉換。在這些情況和更多情況下，重要的是隨附的註釋和文件說明這些方面，以便未來的維護人員不會犯錯，讀者可以理解代碼，而無需進行反向工程。

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

### 簡單性

你的 Go 代碼應該對使用、閱讀和維護它的人而言都是簡單的。

Go 代碼應該以實現其目標為基礎，既在行為上，也在性能上，以最簡單的方式編寫。在 Google 的 Go 代碼庫中，簡單的代碼：

- 容易從頂部到底部閱讀
- 不假設你已經知道它在做什麼
- 不假設你可以記住所有之前的代碼
- 沒有不必要的抽象層次
- 沒有引起讀者注意的平凡名稱
- 使讀者清楚地了解價值觀和決策
- 具有解釋代碼為什麼而不是什麼的注釋，以避免未來偏差
- 具有獨立的文件
- 具有有用的錯誤和有用的測試失敗
- 可能經常與“巧妙”的代碼相互排斥

代碼簡單性和 API 使用簡單性之間可能存在權衡。例如，代碼更複雜，以便 API 的最終用戶更容易正確地調用 API，可能是值得的。相反，讓 API 的最終用戶多做一點額外的工作，以便代碼保持簡單和易於理解，也可能是值得的。

當代碼需要複雜性時，應該有意地添加複雜性。如果需要額外的性能或有特定庫或服務的多個不同的客戶端，這通常是必需的。複雜性可能是合理的，但應該隨附相應的文件，以便客戶和未來的維護人員能夠理解和運用複雜性。如果代碼既有“簡單”又有“複雜”的使用方式，尤其是需要演示其正確使用的測試和示例，那麼這應該得到補充。

這個原則並不意味著 Go 程式碼不能或不應該是複雜的。我們力求讓代碼庫避免不必要的複雜性，這樣當複雜性出現時，就表明相應的代碼需要仔細理解和維護。理想情況下，應該有相應的注釋來解釋原理並確定應採取的注意事項。當優化代碼以提高性能時，往往需要更複雜的方法，比如在整個 goroutine 的生命周期中預分配緩衝區並重複使用它。當維護人員看到這種情況時，它應該是一個提示，表明相應的代碼對性能至關重要，這應該影響到在進行未來更改時所採取的謹慎措施。另一方面，如果不必要地使用這種複雜性，這種複雜性將成為未來需要閱讀或更改代碼的人的負擔。

如果代碼的目的應該是簡單的，但最終變得非常複雜，這通常表明需要重新檢查實現方式，看是否有更簡單的方式來實現同樣的目標。

<a id="least-mechanism"></a>

#### 最小機制

當有多種表達相同想法的方式時，請優先選擇使用最常見的工具。儘管存在複雜的機制，但不應無故使用。增加代碼的複雜度很容易，但發現它不必要後卻很難刪除。

1. 盡可能使用核心語言結構（例如 channel、slice、map、迴圈或 struct）來滿足您的用例。
2. 如果沒有，請在標準庫中查找適當的工具（例如 HTTP 客戶端或模板引擎）。
3. 最後，考慮是否存在於 Google 代碼庫中的核心庫能夠滿足需求，才引入新的依賴或創建自己的庫。

例如，考慮一個生產代碼，其中包含一個綁定到變量的標誌，具有預設值，在測試中必須覆蓋該值。除非意圖測試程式的命令行界面本身（例如，使用 `os/exec`），否則直接覆蓋綁定值比使用 `flag.Set` 更簡單，因此更可取。

同樣地，如果一段代碼需要集合成員檢查，布林值映射（例如 `map[string]bool`）通常就足夠了。僅在需要比使用映射更複雜的操作或操作過於複雜時才使用提供類集合類型和功能的庫。

<a id="concision"></a>

### Concision

Go 簡潔的程式碼具有高信噪比，容易辨識相關細節，而命名和架構可以引導讀者了解這些細節。

有許多事情可能妨礙在任何時候突出顯示最重要的細節：

- 重複的程式碼
- 多餘的語法
- [不透明的名稱](#naming)
- 不必要的抽象
- 空格

重複的程式碼尤其隱藏了每個幾乎相同區段之間的差異，需要讀者視覺上比較類似的程式碼行以找到差異。[Table-driven testing] 是一個很好的例子，可以簡潔地將共同代碼因素從每個重複的重要細節中提取出來，但是選擇包含在表格中的哪些部分將對表格易於理解產生影響。

在考慮多種程式碼結構時，值得考慮哪種方式使重要細節最明顯。

了解和使用常見的程式碼結構和慣用語法對於維持高信噪比也很重要。例如，以下程式碼塊在[錯誤處理]中非常常見，讀者可以快速了解此塊的用途。

```go
// Good:
if err := doSomething(); err != nil {
    // ...
}
```

如果代碼看起來與此非常相似，但微妙地不同，讀者可能不會注意到更改。在這種情況下，值得有意地 ["增強"] 錯誤檢查的信號，方法是添加一個注釋來引起注意。

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

可讀性高的程式碼不僅對試圖理解其工作方式的讀者有意義，也對需要更改它的程式設計師有意義。清晰度至關重要。

易於維護的程式碼：

- 易於未來的程式設計師正確地修改
- 有能夠正常增長的 API 結構
- 對它所做的假設有明確的說明，並選擇映射到問題結構而不是代碼結構的抽象
- 避免不必要的耦合，並且不包含未使用的功能
- 擁有全面的測試套件，以確保承諾的行為得以維持並且重要的邏輯是正確的，測試提供明確的，可操作的診斷以便在失敗時使用。

當使用像介面和類型這樣的抽象時，它們從它們所使用的上下文中刪除了信息，因此確保它們提供足夠的好處非常重要。編輯器和 IDE 可以直接連接到方法定義，並在使用具體類型時顯示相應的文檔，但是否則只能引用介面定義。介面是一個強大的工具，但是伴隨著一些成本，因為維護者可能需要了解底層實現的具體細節，以便正確使用介面，這必須在介面文檔或呼叫點處加以說明。

易於維護的代碼還避免將重要細節隱藏在容易忽略的地方。例如，在以下每行程式碼中，有或缺少一個字符對於理解該行至關重要：

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

這兩個範例都不是錯誤的，但是它們都可以用更明確的方式來撰寫，或是加上注釋以強調其重要行為：

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

同樣地，隱藏關鍵邏輯或重要邊緣案例的輔助函數可能會導致未來的更改無法正確處理。

可預測的名稱是易於維護的代碼的另一個特點。使用套件的用戶或程式碼的維護者應該能夠預測在特定上下文中變量、方法或函數的名稱。相同概念的函數參數和接收器名稱通常應該共享相同的名稱，以便讓文件易於理解，並且便於在最小的開銷下重構代碼。

可維護的代碼最小化其依賴（包括隱含和顯式的）。依賴於更少的套件意味著更少的代碼行可以影響行為。避免依賴於內部或未記錄的行為使代碼在這些行為在未來發生更改時更少會產生維護負擔。

在考慮如何結構化或編寫代碼時，值得花時間思考代碼可能隨時間演變的方式。如果一種方法更有助於實現未來更輕鬆、更安全的更改，即使這意味著稍微複雜一些的設計，那也通常是一個好的折衷方案。

<a id="consistency"></a>

### Consistency

一致性代表的是，在整個程式碼庫、團隊或套件內，甚至是單個檔案內，程式碼看起來、感覺和行為都類似的程式碼。

一致性問題不會覆蓋上述任何原則，但如果必須作出選擇，通常更有利的做法是遵從一致性。

套件內的一致性通常是一致性中最重要的層級。如果在套件內以多種方式解決相同的問題，或者在檔案內使用多個名稱表示相同的概念，那麼這將會非常令人煩擾。然而，即使如此，這也不應覆蓋已經明確定義的風格原則或全域一致性。

<a id="core"></a>

## Core guidelines

以下的指南收集了 Go 風格的最重要方面，所有的 Go 代碼都應該遵循這些原則。我們期望這些原則在讀取權限獲得之前就被學習和遵循。這些原則不會經常變化，新的添加必須符合高標準。下面的指南擴展了 [Effective Go] 中的建議，為整個社區的 Go 代碼提供了共同的基準。

[effective go]: https://go.dev/doc/effective_go

<a id="formatting"></a>

### Formatting

所有的 Go 代碼文件必須符合 gofmt 工具的輸出格式。Google 的代碼庫中有一個預提交檢查會強制執行此格式。通常情況下，[生成的代碼] 也應進行格式化（例如使用 [format.Source] 函數），因為這些代碼也可以在代碼搜尋中被瀏覽。

[generated code]: https://docs.bazel.build/versions/main/be/general.html#genrule
[`format.source`]: https://pkg.go.dev/go/format#Source

<a id="mixed-caps"></a>

### MixedCaps

Go 語言的變數名稱採用大小寫混合的方式（`MixedCaps` 或 `mixedCaps`，也叫做駝峰命名法），而不是使用底線(snake case)來寫多個單字的名稱。

即使這樣做與其他語言的命名規則不同，仍然應這樣做。例如，如果一個常量是被公開的，那麼它應該是 `MaxLength`(而不是 `MAX_LENGTH`)，如果未被公開，則應該是 `maxLength`(而不是 `max_length`)。

為了選擇最初的大寫字母，局部變數被視為[未公開]（[unexported]）。 // 待潤飾

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

Go 語言的程式碼沒有固定的行長限制。如果一行程式碼看起來太長，應該考慮重構，而不是將其拆成多行。如果已經盡可能縮短行長，則可以保留長行。

不要在以下情況下拆分一行程式碼：

- 在[縮排變更](decisions#indentation-confusion)之前（例如函數聲明、條件語句）。
- 將長字符串（例如 URL）分成多個較短的行。

<a id="naming"></a>

### Naming

命名比起科學更像是藝術。在 Go 語言中，名稱通常比許多其他語言要短，但相同的[一般指南]適用。名稱應該：

- 使用時不應感覺[重複](decisions#repetition)
- 考慮上下文
- 不要重複已經清晰的概念

您可以在[決策](decisions#naming)中找到更具體的命名指南。

[general guidelines]: https://testing.googleblog.com/2017/10/code-health-identifiernamingpostforworl.html

<a id="local-consistency"></a>

### Local consistency

如果風格指南沒有對特定的風格點提出任何建議，作者可以自由選擇他們喜歡的風格，除非相近的程式碼（通常在同一個檔案或套件中，但有時在團隊或專案目錄中）對該問題已經採取了一致的態度。

**有效的** 本地風格考慮的範例：

- Use of `%s` or `%v` for formatted printing of errors
- Usage of buffered channels in lieu of mutexes

Examples of **invalid** local style considerations:

- Line length restrictions for code
- Use of assertion-based testing libraries

如果本地風格與風格指南不一致，但可讀性的影響僅限於一個文件，它通常會在代碼審查中出現，而一致的修復超出了相關 CL 的範圍。此時，適當的做法是提交一個 bug 以跟踪修復。

如果更改將惡化現有的風格偏差，將其表現在更多 API 表面上，擴大存在偏差的文件數量，或者引入實際的 bug，那麼本地一致性不再是新代碼違反風格指南的藉口。在這些情況下，作者應在同一 CL 中清理現有的代碼庫，對當前 CL 進行重構，或找到一個至少不會使本地問題惡化的替代方案。

<!--

-->

{% endraw %}
