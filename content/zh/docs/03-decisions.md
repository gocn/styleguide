# Go 风格决策

https://google.github.io/styleguide/go/decisions

[概述](https://google.github.io/styleguide/go/index) | [指南](https://google.github.io/styleguide/go/guide) | [决策](https://google.github.io/styleguide/go/decisions) | [最佳实践](https://google.github.io/styleguide/go/best-practices)

**注意：**本文是 Google [Go 风格](https://google.github.io/styleguide/go/index) 系列文档的一部分。本文档是 **[规范性(normative)](https://google.github.io/styleguide/go/index#normative) 但不是[强制规范(canonical)](https://google.github.io/styleguide/go/index#canonical )**，并且从属于[Google 风格指南](https://google.github.io/styleguide/go/guide)。请参阅[概述](https://google.github.io/styleguide/go/index#about)获取更多详细信息。

## 关于

本文包含旨在统一和为 Go 可读性导师给出的建议提供标准指导、解释和示例的风格决策。

本文档**并不详尽**，且会随着时间的推移而增加。如果[风格指南](https://google.github.io/styleguide/go/guide) 与此处给出的建议相矛盾，**风格指南优先**，并且本文档应相应更新。

参见 [关于](https://google.github.io/styleguide/go#about)  获取 Go 风格的全套文档。

以下部分已从样式决策移至指南的一部分：

- **混合大写字母MixedCaps**: 参见 https://google.github.io/styleguide/go/guide#mixed-caps
- **格式化Formatting**: 参见 https://google.github.io/styleguide/go/guide#formatting
- **行长度Line Length**: 参见 https://google.github.io/styleguide/go/guide#line-length

## 命名Naming

有关命名的总体指导，请参阅[核心风格指南](https://google.github.io/styleguide/go/guide#naming) 中的命名部分，以下部分对命名中的特定区域提供进一步的说明。

### 下划线Underscores

Go 中的命名通常不应包含下划线。 这个原则有三个例外：

1. 仅由生成代码导入的包名称可能包含下划线。有关如何选择多词包名称的更多详细信息，请参阅[包名称](https://google.github.io/styleguide/go/decisions#package-names)。
2. `*_test.go` 文件中的测试、基准和示例函数名称可能包含下划线。
3. 与操作系统或 cgo 互操作的低级库可能会重用标识符，如 [`syscall`](https://pkg.go.dev/syscall#pkg-constants) 中所做的那样。在大多数代码库中，这预计是非常罕见的。
### 包名称Package names

Go 包名称应该简短并且只包含小写字母。由多个单词组成的包名称应全部小写。例如，包 [`tabwriter`](https://pkg.go.dev/text/tabwriter) 不应该命名为 `tabWriter`、`TabWriter` 或 `tab_writer`。

避免选择可能被常用局部变量[遮蔽覆盖](https://google.github.io/styleguide/go/best-practices#shadowing) 的包名称。例如，`usercount` 是比 `count` 更好的包名，因为 `count` 是常用变量名。

Go 包名称不应该有下划线。如果您需要导入名称中确实有一个包（通常来自生成的或第三方代码），则必须在导入时将其重命名为适合在 Go 代码中使用的名称。

一个例外是仅由生成的代码导入的包名称可能包含下划线。具体例子包括：

- 对外部测试包使用 _test 后缀，例如集成测试
- 使用 `_test` 后缀作为 [包级文档示例](https://go.dev/blog/examples)

避免使用无意义的包名称，例如 `util`、`utility`、`common`、`helper` 等。查看更多关于[所谓的“实用程序包”](https://google.github.io/styleguide/go/best-practices#util-packages)。

当导入的包被重命名时（例如 `import foob "path/to/foo_go_proto"`），包的本地名称必须符合上述规则，因为本地名称决定了包中的符号在文件中的引用方式.如果给定的导入在多个文件中重命名，特别是在相同或附近的包中，则应尽可能使用相同的本地名称以保持一致性。

另请参阅：https://go.dev/blog/package-names

### 接收者命名Receiver names

[接收者](https://golang.org/ref/spec#Method_declarations) 变量名必须满足:

- 短（通常是一两个字母的长度）
- 类型本身的缩写
- 始终如一地应用于该类型的每个接收者

| 长名称                   | 更好命名               |
| --------------------------- | ------------------------- |
| `func (tray Tray)`          | `func (t Tray)`           |
| `func (info *ResearchInfo)` | `func (ri *ResearchInfo)` |
| `func (this *ReportWriter)` | `func (w *ReportWriter)`  |
| `func (self *Scanner)`      | `func (s *Scanner)`       |

### 常量命名Constant names

常量名称必须像 Go 中的所有其他名称一样使用 [混合大写字母MixedCaps](https://google.github.io/styleguide/go/guide#mixed-caps)。 （[导出](https://tour.golang.org/basics/3) 常量以大写字母开头，而未导出的常量以小写字母开头。）即使打破了其他语言的约定，这也是适用的。常量名称不应是其值的派生词，而应该解释值锁表示的含义。

```
// Good:
const MaxPacketSize = 512

const (
    ExecuteBit = 1 << iota
    WriteBit
    ReadBit
)
```

不要使用非混合大写常量名称或带有 `K` 前缀的常量。

```
// Bad:
const MAX_PACKET_SIZE = 512
const kMaxBufferSize = 1024
const KMaxUsersPergroup = 500
```

根据它们的角色而不是它们的值来命名常量。 如果一个常量除了它的值之外没有其他作用，那么就没有必要将它定义为一个常量。

```
// Bad:
const Twelve = 12

const (
    UserNameColumn = "username"
    GroupColumn    = "group"
)
```

### 缩写词Initialisms

名称中的首字母缩略词或单独的首字母缩略词（例如，“URL”和“NATO”）应该具有相同的大小写。 `URL` 应显示为 `URL` 或 `url`（如 `urlPony` 或 `URLPony`），绝不能显示为 `Url`。 这也适用于 `ID` 是“identifier”的缩写； 写 `appID` 而不是 `appId`。

- 在具有多个首字母缩写的名称中（例如 `XMLAPI` 因为它包含 `XML` 和 `API`），给定首字母缩写中的每个字母都应该具有相同的大小写，但名称中的每个首字母缩写不需要具有相同的大小写。
- 在带有包含小写字母的首字母缩写的名称中（例如`DDoS`、`iOS`、`gRPC`），首字母缩写应该像在标准中一样出现，除非您需要为了满足 [导出](https://golang.org/ref/spec#Exported_identifiers) 而更改第一个字母。在这些情况下，整个缩写词应该是相同的情况（例如 `ddos`、`IOS`、`GRPC`）。

| 缩写词 | 范围      | 正确  | 错误                              |
| ------------- | ---------- | -------- | -------------------------------------- |
| XML API       | Exported   | `XMLAPI` | `XmlApi`, `XMLApi`, `XmlAPI`, `XMLapi` |
| XML API       | Unexported | `xmlAPI` | `xmlapi`, `xmlApi`                     |
| iOS           | Exported   | `IOS`    | `Ios`, `IoS`                           |
| iOS           | Unexported | `iOS`    | `ios`                                  |
| gRPC          | Exported   | `GRPC`   | `Grpc`                                 |
| gRPC          | Unexported | `gRPC`   | `grpc`                                 |
| DDoS          | Exported   | `DDoS`   | `DDOS`, `Ddos`                         |
| DDoS          | Unexported | `ddos`   | `dDoS`, `dDOS`                         |

### Get方法Getters

函数和方法名称不应使用 `Get` 或 `get` 前缀，除非底层概念使用单词“get”（例如 HTTP GET）。此时，更应该直接以名词开头的名称，例如使用 `Counts` 而不是 `GetCounts`。

如果该函数涉及执行复杂的计算或执行远程调用，则可以使用`Compute` 或 `Fetch`等不同的词代替`Get`，以使读者清楚函数调用可能需要时间和 可能会阻塞或失败。

### 变量名Variable names

一般的经验法则是，名称的长度应与其范围的大小成正比，并与其在该范围内使用的次数成反比。在文件范围内创建的变量可能需要多个单词，而单个内部块作用域内的变量可能是单个单词甚至只是一两个字符，以保持代码清晰并避免无关信息。

这是一条粗略的基线。这些数字准则不是严格的规则。要根据上下文、[清晰](https://google.github.io/styleguide/go/guide#clarity) 和[简洁](https://google.github.io/styleguide/go/guide#简洁）来进行判断。

- 小范围是执行一两个小操作的范围，比如 1-7 行。
- 中等范围是一些小的或一个大的操作，比如 8-15 行。
- 大范围是一个或几个大操作，比如 15-25 行。
- 非常大的范围是指超过一页（例如，超过 25 行）的任何内容。

在小范围内可能非常清楚的名称（例如，`c` 表示计数器）在较大范围内可能不够用，并且需要澄清以提醒进一步了解其在代码中的用途。一个作用域中有很多变量，或者表示相似值或概念的变量，那就可能需要比作用域建议的采用更长的变量名称。

概念的特殊性也有助于保持变量名称的简洁。例如，假设只有一个数据库在使用，像`db`这样的短变量名通常可能保留给非常小的范围，即使范围非常大，也可能保持完全清晰。在这种情况下，根据范围的大小，单个词`database`可能是可接受的，但不是必需的，因为`db`是该词的一种非常常见的缩写，几乎没有其他解释。

局部变量的名称应该反映它包含的内容以及它在当前上下文中的使用方式，而不是值的来源。例如，通常情况下最佳局部变量名称与结构或协议缓冲区字段名称不同。

一般来说：

- 像 `count` 或 `options` 这样的单字名称是一个很好的起点。

- 可以添加其他词来消除相似名称的歧义，例如 `userCount` 和 `projectCount`。

- 不要简单地省略字母来节省打字时间。例如，`Sandbox` 优于 `Sbx`，特别是对于导出的名称。

- 大多数变量名可省略 [类型和类似类型的词](https://google.github.io/styleguide/go/decisions#repetitive-with-type) 

  - 对于数字，`userCount` 是比 `numUsers` 或 `usersInt` 更好的名称。
  - 对于切片，`users` 是一个比 `userSlice` 更好的名字。
  - 如果范围内有两个版本的值，则包含类似类型的限定符是可以接受的，例如，您可能将输入存储在 `ageString` 中，并使用 `age` 作为解析值。

- 省略[上下文](https://google.github.io/styleguide/go/decisions#repetitive-in-context) 中清楚的单词。例如，在 UserCount 方法的实现中，名为 userCount 的局部变量可能是多余的； `count`、`users` 甚至 `c` 都具有可读性。

#### 单字母变量名Single-letter variable names

单字母变量名是可以减少[重复](https://google.github.io/styleguide/go/decisions#repetition) 的有用工具，但也可能使代码变得不透明。将它们的使用限制在完整单词很明显以及它会重复出现以代替单字母变量的情况。

一般来说：

- 对于[方法接收者变量](https://google.github.io/styleguide/go/decisions#receiver-names)，最好使用一个字母或两个字母的名称。
- 对常见类型使用熟悉的变量名通常很有帮助：
   - `r` 用于 `io.Reader` 或 `*http.Request`
   - `w` 用于 `io.Writer` 或 `http.ResponseWriter`
- 单字母标识符作为整数循环变量是可接受的，特别是对于索引（例如，`i`）和坐标（例如，`x` 和 `y`）。
- 当范围很短时，循环标识符使用缩写是可接受的，例如`for _, n := range nodes { ... }`。

### 重复Repetition

一段 Go 源代码应该避免不必要的重复。 一个常见的情形是重复名称，其中通常包含不必要的单词或重复其上下文或类型。 如果相同或相似的代码段在很近的地方多次出现，代码本身也可能是不必要的重复。

重复命名可以有多种形式，包括：

#### 包名 vs 可导出符号名Package vs. exported symbol name

当命名导出的符号时，包的名称始终在包外可见，因此应减少或消除两者之间的冗余信息。如果一个包如果需要仅导出一种类型并且以包本身命名，则构造函数的规范名称是`New`（如果需要的话）。

> **实例:** 重复的名称 -> 更好的名称
>
> - `widget.NewWidget` -> `widget.New`
> - `widget.NewWidgetWithName` -> `widget.NewWithName`
> - `db.LoadFromDatabase` -> `db.Load`
> - `goatteleportutil.CountGoatsTeleported` -> `gtutil.CountGoatsTeleported` or `goatteleport.Count`
> - `myteampb.MyTeamMethodRequest` -> `mtpb.MyTeamMethodRequest` or `myteampb.MethodRequest`

#### 变量名 vs 类型Variable name vs. type

编译器总是知道变量的类型，并且在大多数情况下，阅读者也可以通过变量的使用方式清楚地知道变量是什么类型。只有当一个变量的值在同一范围内出现两次时，才有需要明确变量的类型。

| 重复的名称               | 更好的名称            |
| ----------------------------- | ---------------------- |
| `var numUsers int`            | `var users int`        |
| `var nameString string`       | `var name string`      |
| `var primaryProject *Project` | `var primary *Project` |

如果该值以多种形式出现，这可以通过额外的词（如`raw`和`parsed`）或底层表示来澄清：

```
// Good:
limitStr := r.FormValue("limit")
limit, err := strconv.Atoi(limitStr)
// Good:
limitRaw := r.FormValue("limit")
limit, err := strconv.Atoi(limitRaw)
```

#### 外部上下文 vs 本地名称External context vs. local names

包含来自周围上下文信息的名称通常会产生额外的噪音，而没有任何好处。 包名、方法名、类型名、函数名、导入路径，甚至文件名都可以提供自动限定其名称的上下文。
Names that include information from their surrounding context often create extra noise without benefit. The package name, method name, type name, function name, import path, and even filename can all provide context that automatically qualifies all names within.

```
// Bad:
// In package "ads/targeting/revenue/reporting"
type AdsTargetingRevenueReport struct{}

func (p *Project) ProjectName() string
// Good:
// In package "ads/targeting/revenue/reporting"
type Report struct{}

func (p *Project) Name() string
// Bad:
// In package "sqldb"
type DBConnection struct{}
// Good:
// In package "sqldb"
type Connection struct{}
// Bad:
// In package "ads/targeting"
func Process(in *pb.FooProto) *Report {
    adsTargetingID := in.GetAdsTargetingID()
}
// Good:
// In package "ads/targeting"
func Process(in *pb.FooProto) *Report {
    id := in.GetAdsTargetingID()
}
```

重复通常应该在符号用户的上下文中进行评估，而不是孤立地进行评估。例如，下面的代码有很多名称，在某些情况下可能没问题，但在上下文中是多余的：

```
// Bad:
func (db *DB) UserCount() (userCount int, err error) {
    var userCountInt64 int64
    if dbLoadError := db.LoadFromDatabase("count(distinct users)", &userCountInt64); dbLoadError != nil {
        return 0, fmt.Errorf("failed to load user count: %s", dbLoadError)
    }
    userCount = int(userCountInt64)
    return userCount, nil
}
```

相反，在上下文和使用上信息是清楚的情况下，常常可以忽略：

```
// Good:
func (db *DB) UserCount() (int, error) {
    var count int64
    if err := db.Load("count(distinct users)", &count); err != nil {
        return 0, fmt.Errorf("failed to load user count: %s", err)
    }
    return int(count), nil
}
```

## 评论Commentary

关于评论的约定（包括评论什么、使用什么风格、如何提供可运行的示例等）旨在支持阅读公共 API 文档的体验。 有关详细信息，请参阅 [Effective Go](http://golang.org/doc/effective_go.html#commentary)。

最佳实践文档关于 [文档约定](https://google.github.io/styleguide/go/best-practices#documentation-conventions) 的部分进一步讨论了这一点。

**最佳实践：**在开发和代码审查期间使用[文档预览](https://google.github.io/styleguide/go/best-practices#documentation-preview) 查看文档和可运行示例是否有用 并以您期望的方式呈现。

**提示：** Godoc 使用很少的特殊格式； 列表和代码片段通常应该缩进以避免换行。 除缩进外，通常应避免装饰。

### 注释行长度Comment line length

确保即使在较窄的屏幕上注释的可读性。

当评论变得太长时，建议将其包装成多个单行评论。在可能的情况下，争取在 80 列宽的终端上阅读良好的注释，但这并不是硬性限制； Go 中的注释没有固定的行长度限制。例如，标准库经常选择根据标点符号来打断注释，这有时会使个别行更接近 60-70 个字符标记。

有很多现有代码的注释长度超过 80 个字符。本指南不应作为更改此类代码作为可读性审查的一部分的理由（请参阅[一致性](https://google.github.io/styleguide/go/guide#consistency)），但鼓励团队作为其他重构的一部分，有机会时更新注释以遵循此指南。本指南的主要目标是确保所有 Go 可读性导师在提出建议时以及是否提出相同的建议。

有关评论的更多信息，请参阅此 [来自 The Go Blog 的帖子](https://blog.golang.org/godoc-documenting-go-code)。

```
# Good:
// This is a comment paragraph.
// The length of individual lines doesn't matter in Godoc;
// but the choice of wrapping makes it easy to read on narrow screens.
//
// Don't worry too much about the long URL:
// https://supercalifragilisticexpialidocious.example.com:8080/Animalia/Chordata/Mammalia/Rodentia/Geomyoidea/Geomyidae/
//
// Similarly, if you have other information that is made awkward
// by too many line breaks, use your judgment and include a long line
// if it helps rather than hinders.
```

避免在小屏幕上重复换行的评论，这是一种糟糕的阅读体验。

```
# Bad:
// This is a comment paragraph. The length of individual lines doesn't matter in
Godoc;
// but the choice of wrapping causes jagged lines on narrow screens or in
Critique,
// which can be annoying, especially when in a comment block that will wrap
repeatedly.
//
// Don't worry too much about the long URL:
// https://supercalifragilisticexpialidocious.example.com:8080/Animalia/Chordata/Mammalia/Rodentia/Geomyoidea/Geomyidae/
```

### 文档注释Doc comments

所有顶级导出名称都必须有文档注释，具有不明显行为或含义的未导出类型或函数声明也应如此。 这些注释应该是[完整句子](https://google.github.io/styleguide/go/decisions#comment-sentences)，以所描述对象的名称开头。 冠词（“a”、“an”、“the”）可以放在名字前面，使其读起来更自然。

```
// Good:
// A Request represents a request to run a command.
type Request struct { ...

// Encode writes the JSON encoding of req to w.
func Encode(w io.Writer, req *Request) { ...
```

文档注释出现在 [Godoc](https://pkg.go.dev/) 中，并通过 IDE 显示，因此应该为使用该包的任何人编写文档注释。

如果出现在结构中，文档注释适用于以下符号或字段组：

```
// Good:
// Options configure the group management service.
type Options struct {
    // General setup:
    Name  string
    Group *FooGroup

    // Dependencies:
    DB *sql.DB

    // Customization:
    LargeGroupThreshold int // optional; default: 10
    MinimumMembers      int // optional; default: 2
}
```

**最佳实践：**如果你对未导出的代码有文档注释，请遵循与导出代码相同的习惯（即，以未导出的名称开始注释）。 这使得以后导出它变得容易，只需在注释和代码中用新导出的名称替换未导出的名称即可。

### 注释语句Comment sentences

完整句子的注释应该像标准英语句子一样大写和标点符号。 （作为一个例外，如果在其他方面很清楚，可以以非大写的标识符名称开始一个句子。这种情况最好只在段落的开头进行。）

作为句子片段的注释对标点符号或大小写没有此类要求。

[文档注释](https://google.github.io/styleguide/go/decisions#doc-comments) 应始终是完整的句子，因此应始终大写和标点符号。 简单的行尾注释（特别是对于结构字段）可以假设字段名称是主语的简单短语。

```
// Good:
// A Server handles serving quotes from the collected works of Shakespeare.
type Server struct {
    // BaseDir points to the base directory under which Shakespeare's works are stored.
    //
    // The directory structure is expected to be the following:
    //   {BaseDir}/manifest.json
    //   {BaseDir}/{name}/{name}-part{number}.txt
    BaseDir string

    WelcomeMessage  string // displayed when user logs in
    ProtocolVersion string // checked against incoming requests
    PageLength      int    // lines per page when printing (optional; default: 20)
}
```

### 示例Examples

包应该清楚地记录它们的预期用途。 尝试提供一个[可运行的例子](http://blog.golang.org/examples)； 示例出现在 Godoc 中。 可运行示例属于测试文件，而不是生产源文件。 请参阅此示例（[Godoc](https://pkg.go.dev/time#example-Duration)，[source](https://cs.opensource.google/go/go/+/HEAD:src/time /example_test.go））。

如果无法提供可运行的示例，可以在代码注释中提供示例代码。 与注释中的其他代码和命令行片段一样，它应该遵循标准格式约定。

### 命名的结果参数Named result parameters

当有命名参数时，请考虑函数签名在 Godoc 中的显示方式。 函数本身的名称和结果参数的类型通常要足够清楚。

```
// Good:
func (n *Node) Parent1() *Node
func (n *Node) Parent2() (*Node, error)
```

如果一个函数返回两个或多个相同类型的参数，添加名称会很有用。

```
// Good:
func (n *Node) Children() (left, right *Node, err error)
```

如果调用者必须对特定的结果参数采取行动，命名它们可以帮助暗示行动是什么：

```
// Good:
// WithTimeout returns a context that will be canceled no later than d duration
// from now.
//
// The caller must arrange for the returned cancel function to be called when
// the context is no longer needed to prevent a resource leak.
func WithTimeout(parent Context, d time.Duration) (ctx Context, cancel func())
```

在上面的代码中，取消是调用者必须执行的特定操作。但是，如果将结果参数单独写为`(Context, func())`，“取消函数”的含义就不清楚了。

当名称产生 [不必要的重复](https://google.github.io/styleguide/go/decisions#repetitive-with-type) 时，不要使用命名结果参数。

```
// Bad:
func (n *Node) Parent1() (node *Node)
func (n *Node) Parent2() (node *Node, err error)
```

不要为了避免在函数内声明变量而使用命名结果参数。这种做法会导致不必要的 冗长API，但收益只是很小的简洁性。

[裸返回](https://tour.golang.org/basics/7) 仅在小函数中是可接受的。 一旦它是一个中等大小的函数，就需要明确你的返回值。 同样，不要仅仅因为可以裸返回就使用命名结果参数。 [清晰度](https://google.github.io/styleguide/go/guide#clarity) 总是比在你的函数中节省几行更重要。

如果必须在延迟闭包中更改结果参数的值，则命名结果参数始终是可以接受的。

> **提示：** 类型通常比函数签名中的名称更清晰。 [GoTip #38：作为命名类型的函数](https://google.github.io/styleguide/go/index.html#gotip) 演示了这一点。
>
> 在上面的 [`WithTimeout`](https://pkg.go.dev/context#WithTimeout) 中，代码使用了一个 [`CancelFunc`](https://pkg.go.dev/context#CancelFunc) 而不是结果参数列表中的原始`func()`，并且几乎不需要做任何记录工作。

### 包注释

包注释必须出现在包内语句的上方，注释和包名称之间没有空行。 例子：

```
// Good:
// Package math provides basic constants and mathematical functions.
//
// This package does not guarantee bit-identical results across architectures.
package math
```

每个包必须有一个包注释。 如果一个包由多个文件组成，那么其中一个文件应该有包注释。

`main` 包的注释形式略有不同，其中 BUILD 文件中的 `go_binary` 规则的名称代替了包名。

```
// Good:
// The seed_generator command is a utility that generates a Finch seed file
// from a set of JSON study configs.
package main
```

只要二进制文件的名称与 BUILD 文件中所写的完全一致，其他风格的注释也是可以了。 当二进制名称是第一个单词时，即使它与命令行调用的拼写不严格匹配，也需要将其大写。

```
// Good:
// Binary seed_generator ...
// Command seed_generator ...
// Program seed_generator ...
// The seed_generator command ...
// The seed_generator program ...
// Seed_generator ...
```

提示:

- 命令行调用示例和 API 用法可以是有用的文档。 对于 Godoc 格式，缩进包含代码的注释行。

- 如果没有明显的主文件或者包注释特别长，可以将文档注释放在名为 doc.go 的文件中，只有注释和包子句。

- 可以使用多行注释代替多个单行注释。 如果文档包含可能对从源文件复制和粘贴有用的部分，如示例命令行（用于二进制文件）和模板示例，这将非常有用。

  ```
  // Good:
  /*
  The seed_generator command is a utility that generates a Finch seed file
  from a set of JSON study configs.
  
      seed_generator *.json | base64 > finch-seed.base64
  */
  package template
  ```

- 供维护者使用且适用于整个文件的注释通常放在导入声明之后。 这些不会出现在 Godoc 中，也不受上述包注释规则的约束。

## 导入

### 导入重命名

只有在为了避免与其他导入的名称冲突时，才使用重命名导入。 （由此推论，[好的包名称](https://google.github.io/styleguide/go/decisions#package-names) 不需要重命名。）如果发生名称冲突，最好重命名 最本地或特定于项目的导入。 包的本地别名必须遵循[包命名指南](https://google.github.io/styleguide/go/decisions#package-names)，包括禁止使用下划线和大写字母。

生成的 protocol buffer 包必须重命名以从其名称中删除下划线，并且它们的别名必须具有 `pb` 后缀。 有关详细信息，请参阅[proto和stub最佳实践](https://google.github.io/styleguide/go/best-practices#import-protos)。

```
// Good:
import (
    fspb "path/to/package/foo_service_go_proto"
)
```

导入的包名称没有有用的识别信息时（例如 `package v1`），应该重命名以包括以前的路径组件。 重命名必须与导入相同包的其他本地文件一致，并且可以包括版本号。

**注意：** 最好重命名包以符合 [好的包命名规则](https://google.github.io/styleguide/go/decisions#package-names)，但在vendor目录下的包通常是不可行的。


```
// Good:
import (
    core "github.com/kubernetes/api/core/v1"
    meta "github.com/kubernetes/apimachinery/pkg/apis/meta/v1beta1"
)
```

如果您需要导入一个名称与您要使用的公共局部变量名称（例如 `url`、`ssh`）冲突的包，并且您希望重命名该包，首选方法是使用 `pkg ` 后缀（例如 `urlpkg`）。 请注意，可以使用局部变量隐藏包； 仅当此类变量在范围内时仍需要使用此包时，才需要重命名。

### 导入分组

导入应分为两组：

- 标准库包
- 其他（项目和vendor）包

```
// Good:
package main

import (
    "fmt"
    "hash/adler32"
    "os"

    "github.com/dsnet/compress/flate"
    "golang.org/x/text/encoding"
    "google.golang.org/protobuf/proto"
    foopb "myproj/foo/proto/proto"
    _ "myproj/rpc/protocols/dial"
    _ "myproj/security/auth/authhooks"
)
```

将导入项分成多个组是可以接受的，例如，如果您想要一个单独的组来重命名、导入仅为了特殊效果 或另一个特殊的导入组。

```
// Good:
package main

import (
    "fmt"
    "hash/adler32"
    "os"

    "github.com/dsnet/compress/flate"
    "golang.org/x/text/encoding"
    "google.golang.org/protobuf/proto"

    foopb "myproj/foo/proto/proto"

    _ "myproj/rpc/protocols/dial"
    _ "myproj/security/auth/authhooks"
)
```

**注意：** [goimports](https://google.github.io/styleguide/go/golang.org/x/tools/cmd/goimports) 不支持维护可选组 - 超出标准库和 Google 导入之间强制分离所需的拆分。为了保持符合状态，额外的导入子组需要作者和审阅人的注意。

Google 程序有时也是 AppEngine 应用程序，应该有一个单独的组用于 AppEngine 导入。

Gofmt 负责按导入路径对每个组进行排序。但是，它不会自动将导入分成组。流行的 [goimports](https://google.github.io/styleguide/go/golang.org/x/tools/cmd/goimports) 工具结合了 Gofmt 和导入管理，根据上述规则将导入进行分组。通过 [goimports](https://google.github.io/styleguide/go/golang.org/x/tools/cmd/goimports) 来管理导入安排是可行的，但随着文件的修改，其导入列表必须保持内部一致。

### 导入"空" (`import _`)

使用语法 `import _ "package"`导入的包，称为副作用导入，只能在主包或需要它们的测试中导入。

此类软件包的一些示例包括：

- [time/tzdata](https://pkg.go.dev/time/tzdata)
- [image/jpeg](https://pkg.go.dev/image/jpeg) 在图像处理中的代码

避免在工具包中导入空白，即使工具包间接依赖于它们。 将副作用导入限制到主包有助于控制依赖性，并使得编写依赖于不同导入的测试成为可能，而不会发生冲突或浪费构建成本。

以下是此规则的唯一例外情况：

- 您可以使用空白导入来绕过 [nogo 静态检查器](https://github.com/bazelbuild/rules_go/blob/master/go/nogo.rst) 中对不允许导入的检查。
- 您可以在使用 `//go:embed` 编译器指令的源文件中使用 [embed](https://pkg.go.dev/embed) 包的空白导入。

**提示：**如果生产环境中您创建的工具包间接依赖于副作用导入，请记录这里的预期用途。

### 导入 “.” (`import .`)

`import .` 形式是一种语言特性，它允许将从另一个包导出的标识符无条件地带到当前包中。 有关更多信息，请参阅[语言规范](https://go.dev/ref/spec#Import_declarations)。

**不要**在 Google 代码库中使用此功能； 这使得更难判断功能来自何处。

```
// Bad:
package foo_test

import (
    "bar/testutil" // also imports "foo"
    . "foo"
)

var myThing = Bar() // Bar defined in package foo; no qualification needed.
// Good:
package foo_test

import (
    "bar/testutil" // also imports "foo"
    "foo"
)

var myThing = foo.Bar()
```

## 错误

### 返回错误

使用 `error` 表示函数可能会失败。 按照惯例，`error` 是最后一个结果参数。

```
// Good:
func Good() error { /* ... */ }
```

返回 `nil` 错误是表示操作成功的惯用方式，否则表示可能会失败。 如果函数返回错误，除非另有明确说明，否则调用者必须将所有非错误返回值视为未确定。 通常来说，非错误返回值是它们的零值，但也不能直接这么假设。

```
// Good:
func GoodLookup() (*Result, error) {
    // ...
    if err != nil {
        return nil, err
    }
    return res, nil
}
```

返回错误的导出函数应使用`error`类型返回它们。 具体的错误类型容易受到细微错误的影响：一个 `nil` 指针可以包装到接口中，从而就变成非 nil 值（参见 [关于该主题的 Go FAQ 条目](https://golang.org/doc/faq#nil_error)）。

```
// Bad:
func Bad() *os.PathError { /*...*/ }
```

**提示**：采用 `context.Context` 参数的函数通常应返回 `error`，以便调用者可以确定上下文是否在函数运行时被取消。

### 错误字符串

错误字符串不应大写（除非以导出名称、专有名词或首字母缩写词开头）并且不应以标点符号结尾。 这是因为错误字符串通常在打印给用户之前出现在其他上下文中。

```
// Bad:
err := fmt.Errorf("Something bad happened.")
// Good:
err := fmt.Errorf("something bad happened")
```

另一方面，完整显示消息（日志记录、测试失败、API 响应或其他 UI）的样式视情况而定，但通常应大写首字母。

```
// Good:
log.Infof("Operation aborted: %v", err)
log.Errorf("Operation aborted: %v", err)
t.Errorf("Op(%q) failed unexpectedly; err=%v", args, err)
```

### 错误处理

遇到错误的代码应该慎重选择如何处理它。 使用 _ 变量丢弃错误通常是不合适的。 如果函数返回错误，请执行以下操作之一：

- 立即处理并解决错误
- 将错误返回给调用者
- 在特殊情况下，调用 [`log.Fatal`](https://pkg.go.dev/github.com/golang/glog#Fatal) 或（如绝对有必要）则调用 `panic`

**注意：** `log.Fatalf` 不是标准库日志。 参见 [#logging]。

在极少数情况下适合忽略或丢弃错误（例如调用 [`(*bytes.Buffer).Write`](https://pkg.go.dev/bytes#Buffer.Write) 被记录为永远不会失败），随附的注释应该解释为什么这是安全的。

```
// Good:
var b *bytes.Buffer

n, _ := b.Write(p) // never returns a non-nil error
```

关于错误处理的更多讨论和例子，请参见[Effective Go](http://golang.org/doc/effective_go.html#errors)和[最佳实践](https://google.github.io/styleguide/go/best-practices.html#error-handling)。

### In-band 错误

在C和类似语言中，函数通常会返回-1、null或空字符串等值，以示错误或丢失结果。这就是所谓的带内`In-band`处理。

```
// Bad:
// Lookup returns the value for key or -1 if there is no mapping for key.
func Lookup(key string) int
```

未能检查`In-band`错误值会导致错误，并可能将error归于错误的功能。

```
// Bad:
// The following line returns an error that Parse failed for the input value,
// whereas the failure was that there is no mapping for missingKey.
return Parse(Lookup(missingKey))
```

Go对多重返回值的支持提供了一个更好的解决方案（见[Effective Go关于多重返回的部分](http://golang.org/doc/effective_go.html#multiple-returns)）。与其要求调用方检查`In-band`的错误值，函数更应该返回一个额外的值来表明返回值是否有效。这个返回值可以是一个错误，或者在不需要解释时是一个布尔值，并且应该是最终的返回值。

```
// Good:
// Lookup returns the value for key or ok=false if there is no mapping for key.
func Lookup(key string) (value string, ok bool)
```

这个API可以防止调用者错误地编写`Parse(Lookup(key))`，从而导致编译时错误，因为`Lookup(key)`有两个返回值。

以这种方式返回错误，可以鼓励更强大和明确的错误处理。

```
// Good:
value, ok := Lookup(key)
if !ok {
    return fmt.Errorf("no value for %q", key)
}
return Parse(value)
```

一些标准库函数，如包`strings`中的函数，返回`In-band`错误值。这大大简化了字符串处理的代码，但代价是要求程序员更加勤奋。一般来说，Google代码库中的Go代码应该为错误返回额外的值。

### 缩进错误流程

在继续代码的其余部分之前处理错误。这提高了代码的可读性，使读者能够快速找到正常路径。这个逻辑同样适用于任何测试条件并以终端条件结束的代码块（例如，`return`、`panic`、`log.Fatal`）。

如果终止条件没有得到满足，运行的代码应该出现在`if`块之后，而不应该缩进到`else`子句中。

```
// Good:
if err != nil {
    // error handling
    return // or continue, etc.
}
// normal code
// Bad:
if err != nil {
    // error handling
} else {
    // normal code that looks abnormal due to indentation
}
```

> **提示：**如果你使用一个变量超过几行代码，通常不值得使用`带有初始化的if`风格。在这种情况下，通常最好将声明移出，使用标准的`if`语句。
>
> ```
> // Good:
> x, err := f()
> if err != nil {
>   // error handling
>   return
> }
> // lots of code that uses x
> // across multiple lines
> // Bad:
> if x, err := f(); err != nil {
>   // error handling
>   return
> } else {
>   // lots of code that uses x
>   // across multiple lines
> }
> ```

更多细节见[Go Tip #1：视线](https://google.github.io/styleguide/go/index.html#gotip)和[TotT：通过减少嵌套降低代码的复杂性](https://testing.googleblog.com/2017/06/code-health-reduce-nesting-reduce.html)。

## 语言

### 字面格式化

Go有一个非常强大的[复合字面语法](https://golang.org/ref/spec#Composite_literals)，用它可以在一个表达式中表达深度嵌套的复杂值。在可能的情况下，应该使用这种字面语法，而不是逐字段建值。字面意义的 `gofmt`格式一般都很好，但有一些额外的规则可以使这些字面意义保持可读和可维护。

#### 字段名称

对于在当前包之外定义的类型，结构体字面量通常应该指定**字段名**。

- 包括来自其他包的类型的字段名。

  ```
  // Good:
  good := otherpkg.Type{A: 42}
  ```

  结构中字段的位置和字段的完整集合（当字段名被省略时，这两者都是有必要搞清楚的）通常不被认为是结构的公共API的一部分；需要指定字段名以避免不必要的耦合。

  ```
  // Bad:
  // https://pkg.go.dev/encoding/csv#Reader
  r := csv.Reader{',', '#', 4, false, false, false, false}
  ```

  在小型、简单的结构中可以省略字段名，这些结构的组成和顺序都有文档证明是稳定的。

  ```
  // Good:
  okay := image.Point{42, 54}
  also := image.Point{X: 42, Y: 54}
  ```

- 对于包内类型，字段名是可选的。

  ```
  // Good:
  okay := Type{42}
  also := internalType{4, 2}
  ```
  如果能使代码更清晰，还是应该使用字段名，而且这样做是很常见的。例如，一个有大量字段的结构几乎都应该用字段名来初始化。

  ```
  // Good:
  okay := StructWithLotsOfFields{
    field1: 1,
    field2: "two",
    field3: 3.14,
    field4: true,
  }
  ```

#### 匹配的大括号

一对大括号的最后一半应该总是出现在一行中，其缩进量与开头的大括号相同。单行字词必然具有这个属性。当字面意义跨越多行时，保持这一属性可以使字面意义的括号匹配与函数和`if`语句等常见Go语法结构的括号匹配相同。

这方面最常见的错误是在多行结构字中把收尾括号与值放在同一行。在这种情况下，该行应以逗号结束，收尾括号应出现在下一行。

```
// Good:
good := []*Type{{Key: "value"}}
// Good:
good := []*Type{
    {Key: "multi"},
    {Key: "line"},
}
// Bad:
bad := []*Type{
    {Key: "multi"},
    {Key: "line"}}
// Bad:
bad := []*Type{
    {
        Key: "value"},
}
```

#### Cuddled 大括号

只有在以下两种情况下，才允许在大括号之间为切片和数组丢弃空格（又称 "“cuddling”）。

- [缩进匹配](https://google.github.io/styleguide/go/decisions#literal-matching-braces)
- 内部值也是字面意义或原语构建者（即不是变量或其他表达式）

```
// Good:
good := []*Type{
    { // Not cuddled
        Field: "value",
    },
    {
        Field: "value",
    },
}
// Good:
good := []*Type{{ // Cuddled correctly
    Field: "value",
}, {
    Field: "value",
}}
// Good:
good := []*Type{
    first, // Can't be cuddled
    {Field: "second"},
}
// Good:
okay := []*pb.Type{pb.Type_builder{
    Field: "first", // Proto Builders may be cuddled to save vertical space
}.Build(), pb.Type_builder{
    Field: "second",
}.Build()}
// Bad:
bad := []*Type{
    first,
    {
        Field: "second",
    }}
```

#### 重复的类型名称

重复的类型名称可以从slice和map字面上省略，这对减少杂乱是有帮助的。明确重复类型名称的一个合理场合，当在你的项目中处理一个不常见的复杂类型时，当重复的类型名称在相隔很远的行上时，可以提醒读者的上下文。

```
// Good:
good := []*Type{
    {A: 42},
    {A: 43},
}
// Bad:
repetitive := []*Type{
    &Type{A: 42},
    &Type{A: 43},
}
// Good:
good := map[Type1]*Type2{
    {A: 1}: {B: 2},
    {A: 3}: {B: 4},
}
// Bad:
repetitive := map[Type1]*Type2{
    Type1{A: 1}: &Type2{B: 2},
    Type1{A: 3}: &Type2{B: 4},
}
```

**提示：**如果你想删除结构字中重复的类型名称，可以运行`gofmt -s`。

#### 零值字段

[零值](https://golang.org/ref/spec#The_zero_value)字段可以从结构字段中省略，但不能因此而失去清晰度。

设计良好的API经常采用零值结构来提高可读性。例如，从下面的结构中省略三个零值字段，可以使人们注意到正在指定的唯一选项。

```
// Bad:
import (
  "github.com/golang/leveldb"
  "github.com/golang/leveldb/db"
)

ldb := leveldb.Open("/my/table", &db.Options{
    BlockSize int: 1<<16,
    ErrorIfDBExists: true,

    // These fields all have their zero values.
    BlockRestartInterval: 0,
    Comparer: nil,
    Compression: nil,
    FileSystem: nil,
    FilterPolicy: nil,
    MaxOpenFiles: 0,
    WriteBufferSize: 0,
    VerifyChecksums: false,
})
// Good:
import (
  "github.com/golang/leveldb"
  "github.com/golang/leveldb/db"
)

ldb := leveldb.Open("/my/table", &db.Options{
    BlockSize int: 1<<16,
    ErrorIfDBExists: true,
})
```

表驱动的测试中的结构经常受益于[显式字段名](https://google.github.io/styleguide/go/decisions#literal-field-names)，特别是当测试结构不是琐碎的时候。这允许作者在有关字段与测试用例无关时完全省略零值字段。例如，成功的测试案例应该省略任何与错误或失败相关的字段。在零值对于理解测试用例是必要的情况下，例如测试零或 `nil` 输入，应该指定字段名。

**简明**

```
tests := []struct {
    input      string
    wantPieces []string
    wantErr    error
}{
    {
        input:      "1.2.3.4",
        wantPieces: []string{"1", "2", "3", "4"},
    },
    {
        input:   "hostname",
        wantErr: ErrBadHostname,
    },
}
```

**明确**

```
tests := []struct {
    input    string
    wantIPv4 bool
    wantIPv6 bool
    wantErr  bool
}{
    {
        input:    "1.2.3.4",
        wantIPv4: true,
        wantIPv6: false,
    },
    {
        input:    "1:2::3:4",
        wantIPv4: false,
        wantIPv6: true,
    },
    {
        input:    "hostname",
        wantIPv4: false,
        wantIPv6: false,
        wantErr:  true,
    },
}
```

### Nil 切片

在大多数情况下，`nil`和空切片之间没有功能上的区别。像`len`和`cap`这样的内置函数在`nil`片上的表现与预期相同。

```
// Good:
import "fmt"

var s []int         // nil

fmt.Println(s)      // []
fmt.Println(len(s)) // 0
fmt.Println(cap(s)) // 0
for range s {...}   // no-op

s = append(s, 42)
fmt.Println(s)      // [42]
```

如果你声明一个空片作为局部变量（特别是如果它可以成为返回值的来源），最好选择nil初始化，以减少调用者的错误风险。

```
// Good:
var t []string
// Bad:
t := []string{}
```

不要创建强迫调用者区分nil和空片的API。

```
// Good:
// Ping pings its targets.
// Returns hosts that successfully responded.
func Ping(hosts []string) ([]string, error) { ... }
// Bad:
// Ping pings its targets and returns a list of hosts
// that successfully responded. Can be empty if the input was empty.
// nil signifies that a system error occurred.
func Ping(hosts []string) []string { ... }
```

在设计接口时，避免区分 `nil` 切片和非 `nil` 的零长度分片，因为这可能导致微妙的编程错误。这通常是通过使用`len`来检查是否为空，而不是`==nil`来实现的。

这个实现同时接受`nil`和零长度的片子为 "空"。

```
// Good:
// describeInts describes s with the given prefix, unless s is empty.
func describeInts(prefix string, s []int) {
    if len(s) == 0 {
        return
    }
    fmt.Println(prefix, s)
}
```

而不是依靠二者的区别作为API的一部分：
```
// Bad:
func maybeInts() []int { /* ... */ }

// describeInts describes s with the given prefix; pass nil to skip completely.
func describeInts(prefix string, s []int) {
  // The behavior of this function unintentionally changes depending on what
  // maybeInts() returns in 'empty' cases (nil or []int{}).
  if s == nil {
    return
  }
  fmt.Println(prefix, s)
}

describeInts("Here are some ints:", maybeInts())
```

详见 [in-band 错误](https://google.github.io/styleguide/go/decisions#in-band-errors).

### 缩进的混乱

如果断行会使其余的行与缩进的代码块对齐，则应避免引入断行。如果这是不可避免的，请留下一个空间，将代码块中的代码与包线分开。

```
// Bad:
if longCondition1 && longCondition2 &&
    // Conditions 3 and 4 have the same indentation as the code within the if.
    longCondition3 && longCondition4 {
    log.Info("all conditions met")
}
```

具体准则和例子见以下章节：

- [Function formatting](https://google.github.io/styleguide/go/decisions#func-formatting)
- [Conditionals and loops](https://google.github.io/styleguide/go/decisions#conditional-formatting)
- [Literal formatting](https://google.github.io/styleguide/go/decisions#literal-formatting)

### 函数格式化

函数或方法声明的签名应该保持在一行，以避免[缩进的混乱](https://google.github.io/styleguide/go/decisions#indentation-confusion)。

函数参数列表可以成为Go源文件中最长的几行。然而，它们在缩进的变化之前，因此很难以不使后续行看起来像函数体的一部分的方式来断行，从而造成混乱。

```
// Bad:
func (r *SomeType) SomeLongFunctionName(foo1, foo2, foo3 string,
    foo4, foo5, foo6 int) {
    foo7 := bar(foo1)
    // ...
}
```

参见[最佳实践](https://google.github.io/styleguide/go/best-practices#funcargs)，了解一些缩短函数调用的选择，否则这些函数会有很多参数。

```
// Good:
good := foo.Call(long, CallOptions{
    Names:   list,
    Of:      of,
    The:     parameters,
    Func:    all,
    Args:    on,
    Now:     separate,
    Visible: lines,
})
// Bad:
bad := foo.Call(
    long,
    list,
    of,
    parameters,
    all,
    on,
    separate,
    lines,
)
```

通过分解局部变量，通常可以缩短行数。

```
// Good:
local := helper(some, parameters, here)
good := foo.Call(list, of, parameters, local)
```

Similarly, function and method calls should not be separated based solely on line length.

```
// Good:
good := foo.Call(long, list, of, parameters, all, on, one, line)
// Bad:
bad := foo.Call(long, list, of, parameters,
    with, arbitrary, line, breaks)
```

Do not add comments to specific function parameters. Instead, use an [option struct](https://google.github.io/styleguide/go/best-practices#option-structure) or add more detail to the function documentation.

```
// Good:
good := server.New(ctx, server.Options{Port: 42})
// Bad:
bad := server.New(
    ctx,
    42, // Port
)
```

If call-sites are uncomfortably long, consider refactoring:

```
// Good:
// Sometimes variadic arguments can be factored out
replacements := []string{
    "from", "to", // related values can be formatted adjacent to one another
    "source", "dest",
    "original", "new",
}

// Use the replacement struct as inputs to NewReplacer.
replacer := strings.NewReplacer(replacements...)
```

If the API cannot be changed or if the local call is unusual (whether or not the call is too long), it is always permissible to add line breaks if it aids in understanding the call.

```
// Good:
canvas.RenderCube(cube,
    x0, y0, z0,
    x0, y0, z1,
    x0, y1, z0,
    x0, y1, z1,
    x1, y0, z0,
    x1, y0, z1,
    x1, y1, z0,
    x1, y1, z1,
)
```

Note that the lines in the above example are not wrapped at a specific column boundary but are grouped based on co-ordinate triples.

Long string literals within functions should not be broken for the sake of line length. For functions that include such strings, a line break can be added after the string format, and the arguments can be provided on the next or subsequent lines. The decision about where the line breaks should go is best made based on semantic groupings of inputs, rather than based purely on line length.

```
// Good:
log.Warningf("Database key (%q, %d, %q) incompatible in transaction started by (%q, %d, %q)",
    currentCustomer, currentOffset, currentKey,
    txCustomer, txOffset, txKey)
// Bad:
log.Warningf("Database key (%q, %d, %q) incompatible in"+
    " transaction started by (%q, %d, %q)",
    currentCustomer, currentOffset, currentKey, txCustomer,
    txOffset, txKey)
```

### Conditionals and loops

An `if` statement should not be line broken; multi-line `if` clauses can lead to [indentation confusion](https://google.github.io/styleguide/go/decisions#indentation-confusion).

```
// Bad:
// The second if statement is aligned with the code within the if block, causing
// indentation confusion.
if db.CurrentStatusIs(db.InTransaction) &&
    db.ValuesEqual(db.TransactionKey(), row.Key()) {
    return db.Errorf(db.TransactionError, "query failed: row (%v): key does not match transaction key", row)
}
```

If the short-circuit behavior is not required, the boolean operands can be extracted directly:

```
// Good:
inTransaction := db.CurrentStatusIs(db.InTransaction)
keysMatch := db.ValuesEqual(db.TransactionKey(), row.Key())
if inTransaction && keysMatch {
    return db.Error(db.TransactionError, "query failed: row (%v): key does not match transaction key", row)
}
```

There may also be other locals that can be extracted, especially if the conditional is already repetitive:

```
// Good:
uid := user.GetUniqueUserID()
if db.UserIsAdmin(uid) || db.UserHasPermission(uid, perms.ViewServerConfig) || db.UserHasPermission(uid, perms.CreateGroup) {
    // ...
}
// Bad:
if db.UserIsAdmin(user.GetUniqueUserID()) || db.UserHasPermission(user.GetUniqueUserID(), perms.ViewServerConfig) || db.UserHasPermission(user.GetUniqueUserID(), perms.CreateGroup) {
    // ...
}
```

`if` statements that contain closures or multi-line struct literals should ensure that the [braces match](https://google.github.io/styleguide/go/decisions#literal-matching-braces) to avoid [indentation confusion](https://google.github.io/styleguide/go/decisions#indentation-confusion).

```
// Good:
if err := db.RunInTransaction(func(tx *db.TX) error {
    return tx.Execute(userUpdate, x, y, z)
}); err != nil {
    return fmt.Errorf("user update failed: %s", err)
}
// Good:
if _, err := client.Update(ctx, &upb.UserUpdateRequest{
    ID:   userID,
    User: user,
}); err != nil {
    return fmt.Errorf("user update failed: %s", err)
}
```

Similarly, don’t try inserting artificial linebreaks into `for` statements. You can always let the line simply be long if there is no elegant way to refactor it:

```
// Good:
for i, max := 0, collection.Size(); i < max && !collection.HasPendingWriters(); i++ {
    // ...
}
```

Often, though, there is:

```
// Good:
for i, max := 0, collection.Size(); i < max; i++ {
    if collection.HasPendingWriters() {
        break
    }
    // ...
}
```

`switch` and `case` statements should also remain on a single line.

```
// Good:
switch good := db.TransactionStatus(); good {
case db.TransactionStarting, db.TransactionActive, db.TransactionWaiting:
    // ...
case db.TransactionCommitted, db.NoTransaction:
    // ...
default:
    // ...
}
// Bad:
switch bad := db.TransactionStatus(); bad {
case db.TransactionStarting,
    db.TransactionActive,
    db.TransactionWaiting:
    // ...
case db.TransactionCommitted,
    db.NoTransaction:
    // ...
default:
    // ...
}
```

If the line is excessively long, indent all cases and separate them with a blank line to avoid [indentation confusion](https://google.github.io/styleguide/go/decisions#indentation-confusion):

```
// Good:
switch db.TransactionStatus() {
case
    db.TransactionStarting,
    db.TransactionActive,
    db.TransactionWaiting,
    db.TransactionCommitted:

    // ...
case db.NoTransaction:
    // ...
default:
    // ...
}
```

In conditionals comparing a variable to a constant, place the variable value on the left hand side of the equality operator:

```
// Good:
if result == "foo" {
  // ...
}
```

Instead of the less clear phrasing where the constant comes first ([“Yoda style conditionals”](https://en.wikipedia.org/wiki/Yoda_conditions)):

```
// Bad:
if "foo" == result {
  // ...
}
```

### Copying

To avoid unexpected aliasing and similar bugs, be careful when copying a struct from another package. For example, synchronization objects such as `sync.Mutex` must not be copied.

The `bytes.Buffer` type contains a `[]byte` slice and, as an optimization for small strings, a small byte array to which the slice may refer. If you copy a `Buffer`, the slice in the copy may alias the array in the original, causing subsequent method calls to have surprising effects.

In general, do not copy a value of type `T` if its methods are associated with the pointer type, `*T`.

```
// Bad:
b1 := bytes.Buffer{}
b2 := b1
```

Invoking a method that takes a value receiver can hide the copy. When you author an API, you should generally take and return pointer types if your structs contain fields that should not be copied.

These are acceptable:

```
// Good:
type Record struct {
  buf bytes.Buffer
  // other fields omitted
}

func New() *Record {...}

func (r *Record) Process(...) {...}

func Consumer(r *Record) {...}
```

But these are usually wrong:

```
// Bad:
type Record struct {
  buf bytes.Buffer
  // other fields omitted
}

func (r Record) Process(...) {...} // Makes a copy of r.buf

func Consumer(r Record) {...} // Makes a copy of r.buf
```

This guidance also applies to copying `sync.Mutex`.

### Don’t panic

Do not use `panic` for normal error handling. Instead, use `error` and multiple return values. See the [Effective Go section on errors](http://golang.org/doc/effective_go.html#errors).

Within `package main` and initialization code, consider [`log.Exit`](https://pkg.go.dev/github.com/golang/glog#Exit) for errors that should terminate the program (e.g., invalid configuration), as in many of these cases a stack trace will not help the reader. Please note that [`log.Exit`](https://pkg.go.dev/github.com/golang/glog#Exit) calls [`os.Exit`](https://pkg.go.dev/os#Exit) and any deferred functions will not be run.

For errors that indicate “impossible” conditions, namely bugs that should always be caught during code review and/or testing, a function may reasonably return an error or call [`log.Fatal`](https://pkg.go.dev/github.com/golang/glog#Fatal).

**Note:** `log.Fatalf` is not the standard library log. See [#logging].

### Must functions

Setup helper functions that stop the program on failure follow the naming convention `MustXYZ` (or `mustXYZ`). In general, they should only be called early on program startup, not on things like user input where normal Go error handling is preferred.

This often comes up for functions called to initialize package-level variables exclusively at [package initialization time](https://golang.org/ref/spec#Package_initialization) (e.g. [template.Must](https://golang.org/pkg/text/template/#Must) and [regexp.MustCompile](https://golang.org/pkg/regexp/#MustCompile)).

```
// Good:
func MustParse(version string) *Version {
    v, err := Parse(version)
    if err != nil {
        log.Fatalf("MustParse(%q) = _, %v", version, err)
    }
    return v
}

// Package level "constant". If we wanted to use `Parse`, we would have had to
// set the value in `init`.
var DefaultVersion = MustParse("1.2.3")
```

**Note:** `log.Fatalf` is not the standard library log. See [#logging].

The same convention may be used in test helpers that only stop the current test (using `t.Fatal`). Such helpers are often convenient in creating test values, for example in struct fields of [table driven tests](https://google.github.io/styleguide/go/decisions#table-driven-tests), as functions that return errors cannot be directly assigned to a struct field.

```
// Good:
func mustMarshalAny(t *testing.T, m proto.Message) *anypb.Any {
  t.Helper()
  any, err := anypb.New(m)
  if err != nil {
    t.Fatalf("MustMarshalAny(t, m) = %v; want %v", err, nil)
  }
  return any
}

func TestCreateObject(t *testing.T) {
  tests := []struct{
    desc string
    data *anypb.Any
  }{
    {
      desc: "my test case",
      // Creating values directly within table driven test cases.
      data: mustMarshalAny(t, mypb.Object{}),
    },
    // ...
  }
  // ...
}
```

In both of these cases, the value of this pattern is that the helpers can be called in a “value” context. These helpers should not be called in places where it’s difficult to ensure an error would be caught or in a context where an error should be [checked](https://google.github.io/styleguide/go/decisions#handle-errors) (e.g., in many request handlers). For constant inputs, this allows tests to easily ensure that the `Must` arguments are well-formed, and for non-constant inputs it permits tests to validate that errors are [properly handled or propagated](https://google.github.io/styleguide/go/best-practices#error-handling).

Where `Must` functions are used in a test, they should generally be [marked as a test helper](https://google.github.io/styleguide/go/decisions#mark-test-helpers) and call `t.Fatal` on error (see [error handling in test helpers](https://google.github.io/styleguide/go/best-practices#test-helper-error-handling) for more considerations of using that).

They should not be used when [ordinary error handling](https://google.github.io/styleguide/go/best-practices#error-handling) is possible (including with some refactoring):

```
// Bad:
func Version(o *servicepb.Object) (*version.Version, error) {
    // Return error instead of using Must functions.
    v := version.MustParse(o.GetVersionString())
    return dealiasVersion(v)
}
```

### Goroutine lifetimes

When you spawn goroutines, make it clear when or whether they exit.

Goroutines can leak by blocking on channel sends or receives. The garbage collector will not terminate a goroutine even if the channels it is blocked on are unreachable.

Even when goroutines do not leak, leaving them in-flight when they are no longer needed can cause other subtle and hard-to-diagnose problems. Sending on a channel that has been closed causes a panic.

```
// Bad:
ch := make(chan int)
ch <- 42
close(ch)
ch <- 13 // panic
```

Modifying still-in-use inputs “after the result isn’t needed” can lead to data races. Leaving goroutines in-flight for arbitrarily long can lead to unpredictable memory usage.

Concurrent code should be written such that the goroutine lifetimes are obvious. Typically this will mean keeping synchronization-related code constrained within the scope of a function and factoring out the logic into [synchronous functions](https://google.github.io/styleguide/go/decisions#synchronous-functions). If the concurrency is still not obvious, it is important to document when and why the goroutines exit.

Code that follows best practices around context usage often helps make this clear. It is conventionally managed with a `context.Context`:

```
// Good:
func (w *Worker) Run(ctx context.Context) error {
    // ...
    for item := range w.q {
        // process returns at latest when the context is cancelled.
        go process(ctx, item)
    }
    // ...
}
```

There are other variants of the above that use raw signal channels like `chan struct{}`, synchronized variables, [condition variables](https://drive.google.com/file/d/1nPdvhB0PutEJzdCq5ms6UI58dp50fcAN/view), and more. The important part is that the goroutine’s end is evident for subsequent maintainers.

In contrast, the following code is careless about when its spawned goroutines finish:

```
// Bad:
func (w *Worker) Run() {
    // ...
    for item := range w.q {
        // process returns when it finishes, if ever, possibly not cleanly
        // handling a state transition or termination of the Go program itself.
        go process(item)
    }
    // ...
}
```

This code may look OK, but there are several underlying problems:

- The code probably has undefined behavior in production, and the program may not terminate cleanly, even if the operating system releases the resources.
- The code is difficult to test meaningfully due to the code’s indeterminate lifecycle.
- The code may leak resources as described above.

See also:

- [Never start a goroutine without knowing how it will stop](https://dave.cheney.net/2016/12/22/never-start-a-goroutine-without-knowing-how-it-will-stop)
- Rethinking Classical Concurrency Patterns: [slides](https://drive.google.com/file/d/1nPdvhB0PutEJzdCq5ms6UI58dp50fcAN/view), [video](https://www.youtube.com/watch?v=5zXAHh5tJqQ)
- [When Go programs end](https://changelog.com/gotime/165)

### Interfaces

Go interfaces generally belong in the package that *consumes* values of the interface type, not a package that *implements* the interface type. The implementing package should return concrete (usually pointer or struct) types. That way, new methods can be added to implementations without requiring extensive refactoring. See [GoTip #49: Accept Interfaces, Return Concrete Types](https://google.github.io/styleguide/go/index.html#gotip) for more details.

Do not export a [test double](https://abseil.io/resources/swe-book/html/ch13.html#techniques_for_using_test_doubles) implementation of an interface from an API that consumes it. Instead, design the API so that it can be tested using the [public API](https://abseil.io/resources/swe-book/html/ch12.html#test_via_public_apis) of the [real implementation](https://google.github.io/styleguide/go/best-practices#use-real-transports). See [GoTip #42: Authoring a Stub for Testing](https://google.github.io/styleguide/go/index.html#gotip) for more details. Even when it is not feasible to use the real implementation, it may not be necessary to introduce an interface fully covering all methods in the real type; the consumer can create an interface containing only the methods it needs, as demonstrated in [GoTip #78: Minimal Viable Interfaces](https://google.github.io/styleguide/go/index.html#gotip).

To test packages that use Stubby RPC clients, use a real client connection. If a real server cannot be run in the test, Google’s internal practice is to obtain a real client connection to a local [test double] using the internal rpctest package (coming soon!).

Do not define interfaces before they are used (see [TotT: Code Health: Eliminate YAGNI Smells](https://testing.googleblog.com/2017/08/code-health-eliminate-yagni-smells.html) ). Without a realistic example of usage, it is too difficult to see whether an interface is even necessary, let alone what methods it should contain.

Do not use interface-typed parameters if the users of the package do not need to pass different types for them.

Do not export interfaces that the users of the package do not need.

**TODO:** Write a more in-depth doc on interfaces and link to it here.

```
// Good:
package consumer // consumer.go

type Thinger interface { Thing() bool }

func Foo(t Thinger) string { ... }
// Good:
package consumer // consumer_test.go

type fakeThinger struct{ ... }
func (t fakeThinger) Thing() bool { ... }
...
if Foo(fakeThinger{...}) == "x" { ... }
// Bad:
package producer

type Thinger interface { Thing() bool }

type defaultThinger struct{ ... }
func (t defaultThinger) Thing() bool { ... }

func NewThinger() Thinger { return defaultThinger{ ... } }
// Good:
package producer

type Thinger struct{ ... }
func (t Thinger) Thing() bool { ... }

func NewThinger() Thinger { return Thinger{ ... } }
```

### Generics

Generics (formally called “[Type Parameters](https://go.dev/design/43651-type-parameters)”) are allowed where they fulfill your business requirements. In many applications, a conventional approach using existing language features (slices, maps, interfaces, and so on) works just as well without the added complexity, so be wary of premature use. See the discussion on [least mechanism](https://google.github.io/styleguide/go/guide#least-mechanism).

When introducing an exported API that uses generics, make sure it is suitably documented. It’s highly encouraged to include motivating runnable [examples](https://google.github.io/styleguide/go/decisions#examples).

Do not use generics just because you are implementing an algorithm or data structure that does not care about the type of its member elements. If there is only one type being instantiated in practice, start by making your code work on that type without using generics at all. Adding polymorphism later will be straightforward compared to removing abstraction that is found to be unnecessary.

Do not use generics to invent domain-specific languages (DSLs). In particular, refrain from introducing error-handling frameworks that might put a significant burden on readers. Instead prefer established [error handling](https://google.github.io/styleguide/go/decisions#errors) practices. For testing, be especially wary of introducing [assertion libraries](https://google.github.io/styleguide/go/decisions#assert) or frameworks that result in less useful [test failures](https://google.github.io/styleguide/go/decisions#useful-test-failures).

In general:

- [Write code, don’t design types](https://www.youtube.com/watch?v=Pa_e9EeCdy8&t=1250s). From a GopherCon talk by Robert Griesemer and Ian Lance Taylor.
- If you have several types that share a useful unifying interface, consider modeling the solution using that interface. Generics may not be needed.
- Otherwise, instead of relying on the `any` type and excessive [type switching](https://tour.golang.org/methods/16), consider generics.

See also:

- [Using Generics in Go](https://www.youtube.com/watch?v=nr8EpUO9jhw), talk by Ian Lance Taylor
- [Generics tutorial](https://go.dev/doc/tutorial/generics) on Go’s webpage

### Pass values

Do not pass pointers as function arguments just to save a few bytes. If a function reads its argument `x` only as `*x` throughout, then the argument shouldn’t be a pointer. Common instances of this include passing a pointer to a string (`*string`) or a pointer to an interface value (`*io.Reader`). In both cases, the value itself is a fixed size and can be passed directly.

This advice does not apply to large structs, or even small structs that may increase in size. In particular, protocol buffer messages should generally be handled by pointer rather than by value. The pointer type satisfies the `proto.Message` interface (accepted by `proto.Marshal`, `protocmp.Transform`, etc.), and protocol buffer messages can be quite large and often grow larger over time.

### Receiver type

A [method receiver](https://golang.org/ref/spec#Method_declarations) can be passed either as a value or a pointer, just as if it were a regular function parameter. The choice of which to choose should be based on which [method set(s)](https://golang.org/ref/spec#Method_sets) the method should be a part of.

**Correctness wins over speed or simplicity.** There are cases where you must use a pointer value. In other cases, pick pointers for large types or as future-proofing if you don’t have a good sense of how the code will grow, and use values for simple [plain old data](https://en.wikipedia.org/wiki/Passive_data_structure).

The list below spells out each case in further detail:

- If the receiver is a slice and the method doesn’t reslice or reallocate the slice, use a value rather than a pointer.

  ```
  // Good:
  type Buffer []byte
  
  func (b Buffer) Len() int { return len(b) }
  ```

- If the method needs to mutate the receiver, the receiver must be a pointer.

  ```
  // Good:
  type Counter int
  
  func (c *Counter) Inc() { *c++ }
  
  // See https://pkg.go.dev/container/heap.
  type Queue []Item
  
  func (q *Queue) Push(x Item) { *q = append([]Item{x}, *q...) }
  ```

- If the receiver is a struct containing fields that [cannot safely be copied](https://google.github.io/styleguide/go/decisions#copying), use a pointer receiver. Common examples are [`sync.Mutex`](https://pkg.go.dev/sync#Mutex) and other synchronization types.

  ```
  // Good:
  type Counter struct {
      mu    sync.Mutex
      total int
  }
  
  func (c *Counter) Inc() {
      c.mu.Lock()
      defer c.mu.Unlock()
      c.total++
  }
  ```

  **Tip:** Check the type’s [Godoc](https://pkg.go.dev/time#example-Duration) for information about whether it is safe or unsafe to copy.

- If the receiver is a “large” struct or array, a pointer receiver may be more efficient. Passing a struct is equivalent to passing all of its fields or elements as arguments to the method. If that seems too large to [pass by value](https://google.github.io/styleguide/go/decisions#pass-values), a pointer is a good choice.

- For methods that will call or run concurrently with other functions that modify the receiver, use a value if those modifications should not be visible to your method; otherwise use a pointer.

- If the receiver is a struct or array, any of whose elements is a pointer to something that may be mutated, prefer a pointer receiver to make the intention of mutability clear to the reader.

  ```
  // Good:
  type Counter struct {
      m *Metric
  }
  
  func (c *Counter) Inc() {
      c.m.Add(1)
  }
  ```

- If the receiver is a [built-in type](https://pkg.go.dev/builtin), such as an integer or a string, that does not need to be modified, use a value.

  ```
  // Good:
  type User string
  
  func (u User) String() { return string(u) }
  ```

- If the receiver is a map, function, or channel, use a value rather than a pointer.

  ```
  // Good:
  // See https://pkg.go.dev/net/http#Header.
  type Header map[string][]string
  
  func (h Header) Add(key, value string) { /* omitted */ }
  ```

- If the receiver is a “small” array or struct that is naturally a value type with no mutable fields and no pointers, a value receiver is usually the right choice.

  ```
  // Good:
  // See https://pkg.go.dev/time#Time.
  type Time struct { /* omitted */ }
  
  func (t Time) Add(d Duration) Time { /* omitted */ }
  ```

- When in doubt, use a pointer receiver.

As a general guideline, prefer to make the methods for a type either all pointer methods or all value methods.

**Note:** There is a lot of misinformation about whether passing a value or a pointer to a function can affect performance. The compiler can choose to pass pointers to values on the stack as well as copying values on the stack, but these considerations should not outweigh the readability and correctness of the code in most circumstances. When the performance does matter, it is important to profile both approaches with a realistic benchmark before deciding that one approach outperforms the other.

### `switch` and `break`

Do not use `break` statements without target labels at the ends of `switch` clauses; they are redundant. Unlike in C and Java, `switch` clauses in Go automatically break, and a `fallthrough` statement is needed to achieve the C-style behavior. Use a comment rather than `break` if you want to clarify the purpose of an empty clause.

```
// Good:
switch x {
case "A", "B":
    buf.WriteString(x)
case "C":
    // handled outside of the switch statement
default:
    return fmt.Errorf("unknown value: %q", x)
}
// Bad:
switch x {
case "A", "B":
    buf.WriteString(x)
    break // this break is redundant
case "C":
    break // this break is redundant
default:
    return fmt.Errorf("unknown value: %q", x)
}
```

> **Note:** If a `switch` clause is within a `for` loop, using `break` within `switch` does not exit the enclosing `for` loop.
>
> ```
> for {
>   switch x {
>   case "A":
>      break // exits the switch, not the loop
>   }
> }
> ```
>
> To escape the enclosing loop, use a label on the `for` statement:
>
> ```
> loop:
>   for {
>     switch x {
>     case "A":
>        break loop // exits the loop
>     }
>   }
> ```

### Synchronous functions

Synchronous functions return their results directly and finish any callbacks or channel operations before returning. Prefer synchronous functions over asynchronous functions.

Synchronous functions keep goroutines localized within a call. This helps to reason about their lifetimes, and avoid leaks and data races. Synchronous functions are also easier to test, since the caller can pass an input and check the output without the need for polling or synchronization.

If necessary, the caller can add concurrency by calling the function in a separate goroutine. However, it is quite difficult (sometimes impossible) to remove unnecessary concurrency at the caller side.

See also:

- “Rethinking Classical Concurrency Patterns”, talk by Bryan Mills: [slides](https://drive.google.com/file/d/1nPdvhB0PutEJzdCq5ms6UI58dp50fcAN/view), [video](https://www.youtube.com/watch?v=5zXAHh5tJqQ)

### Type aliases

Use a *type definition*, `type T1 T2`, to define a new type. Use a [*type alias*](http://golang.org/ref/spec#Type_declarations), `type T1 = T2`, to refer to an existing type without defining a new type. Type aliases are rare; their primary use is to aid migrating packages to new source code locations. Don’t use type aliasing when it is not needed.

### Use %q

Go’s format functions (`fmt.Printf` etc.) have a `%q` verb which prints strings inside double-quotation marks.

```
// Good:
fmt.Printf("value %q looks like English text", someText)
```

Prefer using `%q` over doing the equivalent manually, using `%s`:

```
// Bad:
fmt.Printf("value \"%s\" looks like English text", someText)
// Avoid manually wrapping strings with single-quotes too:
fmt.Printf("value '%s' looks like English text", someText)
```

Using `%q` is recommended in output intended for humans where the input value could possibly be empty or contain control characters. It can be very hard to notice a silent empty string, but `""` stands out clearly as such.

### Use any

Go 1.18 introduces an `any` type as an [alias](https://go.googlesource.com/proposal/+/master/design/18130-type-alias.md) to `interface{}`. Because it is an alias, `any` is equivalent to `interface{}` in many situations and in others it is easily interchangeable via an explicit conversion. Prefer to use `any` in new code.

## Common libraries

### Flags

Go programs in the Google codebase use an internal variant of the [standard `flag` package](https://golang.org/pkg/flag/). It has a similar interface but interoperates well with internal Google systems. Flag names in Go binaries should prefer to use underscores to separate words, though the variables that hold a flag’s value should follow the standard Go name style ([mixed caps](https://google.github.io/styleguide/go/guide#mixed-caps)). Specifically, the flag name should be in snake case, and the variable name should be the equivalent name in camel case.

```
// Good:
var (
    pollInterval = flag.Duration("poll_interval", time.Minute, "Interval to use for polling.")
)
// Bad:
var (
    poll_interval = flag.Int("pollIntervalSeconds", 60, "Interval to use for polling in seconds.")
)
```

Flags must only be defined in `package main` or equivalent.

General-purpose packages should be configured using Go APIs, not by punching through to the command-line interface; don’t let importing a library export new flags as a side effect. That is, prefer explicit function arguments or struct field assignment or much less frequently and under the strictest of scrutiny exported global variables. In the extremely rare case that it is necessary to break this rule, the flag name must clearly indicate the package that it configures.

If your flags are global variables, place them in their own `var` group, following the imports section.

There is additional discussion around best practices for creating [complex CLIs](https://google.github.io/styleguide/go/best-practices#complex-clis) with subcommands.

See also:

- [Tip of the Week #45: Avoid Flags, Especially in Library Code](https://abseil.io/tips/45)
- [Go Tip #10: Configuration Structs and Flags](https://google.github.io/styleguide/go/index.html#gotip)
- [Go Tip #80: Dependency Injection Principles](https://google.github.io/styleguide/go/index.html#gotip)

### Logging

Go programs in the Google codebase use a variant of the [standard `log` package](https://pkg.go.dev/log). It has a similar but more powerful interface and interoperates well with internal Google systems. An open source version of this library is available as [package `glog`](https://pkg.go.dev/github.com/golang/glog), and open source Google projects may use that, but this guide refers to it as `log` throughout.

**Note:** For abnormal program exits, this library uses `log.Fatal` to abort with a stacktrace, and `log.Exit` to stop without one. There is no `log.Panic` function as in the standard library.

**Tip:** `log.Info(v)` is equivalent `log.Infof("%v", v)`, and the same goes for other logging levels. Prefer the non-formatting version when you have no formatting to do.

See also:

- Best practices on [logging errors](https://google.github.io/styleguide/go/best-practices#error-logging) and [custom verbosily levels](https://google.github.io/styleguide/go/best-practices#vlog)
- When and how to use the log package to [stop the program](https://google.github.io/styleguide/go/best-practices#checks-and-panics)

### Contexts

Values of the [`context.Context`](https://pkg.go.dev/context) type carry security credentials, tracing information, deadlines, and cancellation signals across API and process boundaries. Unlike C++ and Java, which in the Google codebase use thread-local storage, Go programs pass contexts explicitly along the entire function call chain from incoming RPCs and HTTP requests to outgoing requests.

When passed to a function or method, `context.Context` is always the first parameter.

```
func F(ctx context.Context /* other arguments */) {}
```

Exceptions are:

- In an HTTP handler, where the context comes from [`req.Context()`](https://pkg.go.dev/net/http#Request.Context).

- In streaming RPC methods, where the context comes from the stream.

  Code using gRPC streaming accesses a context from a `Context()` method in the generated server type, which implements `grpc.ServerStream`. See https://grpc.io/docs/languages/go/generated-code/.

- In entrypoint functions (see below for examples of such functions), use [`context.Background()`](https://pkg.go.dev/context/#Background).

  - In binary targets: `main`
  - In general purpose code and libraries: `init`
  - In tests: `TestXXX`, `BenchmarkXXX`, `FuzzXXX`

> **Note**: It is very rare for code in the middle of a callchain to require creating a base context of its own using `context.Background()`. Always prefer taking a context from your caller, unless it’s the wrong context.
>
> You may come across server libraries (the implementation of Stubby, gRPC, or HTTP in Google’s server framework for Go) that construct a fresh context object per request. These contexts are immediately filled with information from the incoming request, so that when passed to the request handler, the context’s attached values have been propagated to it across the network boundary from the client caller. Moreover, these contexts’ lifetimes are scoped to that of the request: when the request is finished, the context is cancelled.
>
> Unless you are implementing a server framework, you shouldn’t create contexts with `context.Background()` in library code. Instead, prefer using context detachment, which is mentioned below, if there is an existing context available. If you think you do need `context.Background()` outside of entrypoint functions, discuss it with the Google Go style mailing list before committing to an implementation.

The convention that `context.Context` comes first in functions also applies to test helpers.

```
// Good:
func readTestFile(ctx context.Context, t *testing.T, path string) string {}
```

Do not add a context member to a struct type. Instead, add a context parameter to each method on the type that needs to pass it along. The one exception is for methods whose signature must match an interface in the standard library or in a third party library outside Google’s control. Such cases are very rare, and should be discussed with the Google Go style mailing list before implementation and readability review.

Code in the Google codebase that must spawn background operations which can run after the parent context has been cancelled can use an internal package for detachment. Follow https://github.com/golang/go/issues/40221 for discussions on an open source alternative.

Since contexts are immutable, it is fine to pass the same context to multiple calls that share the same deadline, cancellation signal, credentials, parent trace, and so on.

See also:

- [Contexts and structs](https://go.dev/blog/context-and-structs)

#### Custom contexts

Do not create custom context types or use interfaces other than context in function signatures. There are no exceptions to this rule.

Imagine if every team had a custom context. Every function call from package P to package Q would have to determine how to convert a `PContext` to a `QContext`, for all pairs of packages P and Q. This is impractical and error-prone for humans, and it makes automated refactorings that add context parameters nearly impossible.

If you have application data to pass around, put it in a parameter, in the receiver, in globals, or in a Context value if it truly belongs there. Creating your own Context type is not acceptable since it undermines the ability of the Go team to make Go programs work properly in production.

### crypto/rand

Do not use package `math/rand` to generate keys, even throwaway ones. If unseeded, the generator is completely predictable. Seeded with `time.Nanoseconds()`, there are just a few bits of entropy. Instead, use `crypto/rand`’s Reader, and if you need text, print to hexadecimal or base64.

```
// Good:
import (
    "crypto/rand"
    // "encoding/base64"
    // "encoding/hex"
    "fmt"

    // ...
)

func Key() string {
    buf := make([]byte, 16)
    if _, err := rand.Read(buf); err != nil {
        log.Fatalf("Out of randomness, should never happen: %v", err)
    }
    return fmt.Sprintf("%x", buf)
    // or hex.EncodeToString(buf)
    // or base64.StdEncoding.EncodeToString(buf)
}
```

**Note:** `log.Fatalf` is not the standard library log. See [#logging].

## Useful test failures

It should be possible to diagnose a test’s failure without reading the test’s source. Tests should fail with helpful messages detailing:

- What caused the failure
- What inputs resulted in an error
- The actual result
- What was expected

Specific conventions for achieving this goal are outlined below.

### Assertion libraries

Do not create “assertion libraries” as helpers for testing.

Assertion libraries are libraries that attempt to combine the validation and production of failure messages within a test (though the same pitfalls can apply to other test helpers as well). For more on the distinction between test helpers and assertion libraries, see [best practices](https://google.github.io/styleguide/go/best-practices#test-functions).

```
// Bad:
var obj BlogPost

assert.IsNotNil(t, "obj", obj)
assert.StringEq(t, "obj.Type", obj.Type, "blogPost")
assert.IntEq(t, "obj.Comments", obj.Comments, 2)
assert.StringNotEq(t, "obj.Body", obj.Body, "")
```

Assertion libraries tend to either stop the test early (if `assert` calls `t.Fatalf` or `panic`) or omit relevant information about what the test got right:

```
// Bad:
package assert

func IsNotNil(t *testing.T, name string, val interface{}) {
    if val == nil {
        t.Fatalf("data %s = nil, want not nil", name)
    }
}

func StringEq(t *testing.T, name, got, want string) {
    if got != want {
        t.Fatalf("data %s = %q, want %q", name, got, want)
    }
}
```

Complex assertion functions often do not provide [useful failure messages](https://google.github.io/styleguide/go/decisions#useful-test-failures) and context that exists within the test function. Too many assertion functions and libraries lead to a fragmented developer experience: which assertion library should I use, what style of output format should it emit, etc.? Fragmentation produces unnecessary confusion, especially for library maintainers and authors of large-scale changes, who are responsible for fixing potential downstream breakages. Instead of creating a domain-specific language for testing, use Go itself.

Assertion libraries often factor out comparisons and equality checks. Prefer using standard libraries such as [`cmp`](https://pkg.go.dev/github.com/google/go-cmp/cmp) and [`fmt`](https://golang.org/pkg/fmt/) instead:

```
// Good:
var got BlogPost

want := BlogPost{
    Comments: 2,
    Body:     "Hello, world!",
}

if !cmp.Equal(got, want) {
    t.Errorf("blog post = %v, want = %v", got, want)
}
```

For more domain-specific comparison helpers, prefer returning a value or an error that can be used in the test’s failure message instead of passing `*testing.T` and calling its error reporting methods:

```
// Good:
func postLength(p BlogPost) int { return len(p.Body) }

func TestBlogPost_VeritableRant(t *testing.T) {
    post := BlogPost{Body: "I am Gunnery Sergeant Hartman, your senior drill instructor."}

    if got, want := postLength(post), 60; got != want {
        t.Errorf("length of post = %v, want %v", got, want)
    }
}
```

**Best Practice:** Were `postLength` non-trivial, it would make sense to test it directly, independently of any tests that use it.

See also:

- [Equality comparison and diffs](https://google.github.io/styleguide/go/decisions#types-of-equality)
- [Print diffs](https://google.github.io/styleguide/go/decisions#print-diffs)
- For more on the distinction between test helpers and assertion helpers, see [best practices](https://google.github.io/styleguide/go/best-practices#test-functions)

### Identify the function

In most tests, failure messages should include the name of the function that failed, even though it seems obvious from the name of the test function. Specifically, your failure message should be `YourFunc(%v) = %v, want %v` instead of just `got %v, want %v`.

### Identify the input

In most tests, failure messages should include the function inputs if they are short. If the relevant properties of the inputs are not obvious (for example, because the inputs are large or opaque), you should name your test cases with a description of what’s being tested and print the description as part of your error message.

### Got before want

Test outputs should include the actual value that the function returned before printing the value that was expected. A standard format for printing test outputs is `YourFunc(%v) = %v, want %v`. Where you would write “actual” and “expected”, prefer using the words “got” and “want”, respectively.

For diffs, directionality is less apparent, and as such it is important to include a key to aid in interpreting the failure. See the [section on printing diffs](https://google.github.io/styleguide/go/decisions#print-diffs). Whichever diff order you use in your failure messages, you should explicitly indicate it as a part of the failure message, because existing code is inconsistent about the ordering.

### Full structure comparisons

If your function returns a struct (or any data type with multiple fields such as slices, arrays, and maps), avoid writing test code that performs a hand-coded field-by-field comparison of the struct. Instead, construct the data that you’re expecting your function to return, and compare directly using a [deep comparison](https://google.github.io/styleguide/go/decisions#types-of-equality).

**Note:** This does not apply if your data contains irrelevant fields that obscure the intention of the test.

If your struct needs to be compared for approximate (or equivalent kind of semantic) equality or it contains fields that cannot be compared for equality (e.g., if one of the fields is an `io.Reader`), tweaking a [`cmp.Diff`](https://pkg.go.dev/github.com/google/go-cmp/cmp/cmp#Diff) or [`cmp.Equal`](https://pkg.go.dev/github.com/google/go-cmp/cmp/cmp#Equal) comparison with [`cmpopts`](https://pkg.go.dev/github.com/google/go-cmp/cmp/cmpopts) options such as [`cmpopts.IgnoreInterfaces`](https://pkg.go.dev/github.com/google/go-cmp/cmp/cmpopts#IgnoreInterfaces) may meet your needs ([example](https://play.golang.org/p/vrCUNVfxsvF)).

If your function returns multiple return values, you don’t need to wrap those in a struct before comparing them. Just compare the return values individually and print them.

```
// Good:
val, multi, tail, err := strconv.UnquoteChar(`\"Fran & Freddie's Diner\"`, '"')
if err != nil {
  t.Fatalf(...)
}
if val != `"` {
  t.Errorf(...)
}
if multi {
  t.Errorf(...)
}
if tail != `Fran & Freddie's Diner"` {
  t.Errorf(...)
}
```

### Compare stable results

Avoid comparing results that may depend on output stability of a package that you do not own. Instead, the test should compare on semantically relevant information that is stable and resistant to changes in dependencies. For functionality that returns a formatted string or serialized bytes, it is generally not safe to assume that the output is stable.

For example, [`json.Marshal`](https://golang.org/pkg/encoding/json/#Marshal) can change (and has changed in the past) the specific bytes that it emits. Tests that perform string equality on the JSON string may break if the `json` package changes how it serializes the bytes. Instead, a more robust test would parse the contents of the JSON string and ensure that it is semantically equivalent to some expected data structure.

### Keep going

Tests should keep going for as long as possible, even after a failure, in order to print out all of the failed checks in a single run. This way, a developer who is fixing the failing test doesn’t have to re-run the test after fixing each bug to find the next bug.

Prefer calling `t.Error` over `t.Fatal` for reporting a mismatch. When comparing several different properties of a function’s output, use `t.Error` for each of those comparisons.

Calling `t.Fatal` is primarily useful for reporting an unexpected error condition, when subsequent comparison failures are not going to be meaningful.

For table-driven test, consider using subtests and use `t.Fatal` rather than `t.Error` and `continue`. See also [GoTip #25: Subtests: Making Your Tests Lean](https://google.github.io/styleguide/go/index.html#gotip).

**Best practice:** For more discussion about when `t.Fatal` should be used, see [best practices](https://google.github.io/styleguide/go/best-practices#t-fatal).

### Equality comparison and diffs

The `==` operator evaluates equality using [language-defined comparisons](http://golang.org/ref/spec#Comparison_operators). Scalar values (numbers, booleans, etc) are compared based on their values, but only some structs and interfaces can be compared in this way. Pointers are compared based on whether they point to the same variable, rather than based on the equality of the values to which they point.

The [`cmp`](https://pkg.go.dev/github.com/google/go-cmp/cmp) package can compare more complex data structures not appropriately handled by `==`, such as slices. Use [`cmp.Equal`](https://pkg.go.dev/github.com/google/go-cmp/cmp/cmp#Equal) for equality comparison and [`cmp.Diff`](https://pkg.go.dev/github.com/google/go-cmp/cmp/cmp#Diff) to obtain a human-readable diff between objects.

```
// Good:
want := &Doc{
    Type:     "blogPost",
    Comments: 2,
    Body:     "This is the post body.",
    Authors:  []string{"isaac", "albert", "emmy"},
}
if !cmp.Equal(got, want) {
    t.Errorf("AddPost() = %+v, want %+v", got, want)
}
```

As a general-purpose comparison library, `cmp` may not know how to compare certain types. For example, it can only compare protocol buffer messages if passed the [`protocmp.Transform`](https://pkg.go.dev/google.golang.org/protobuf/testing/protocmp#Transform) option.

```
// Good:
if diff := cmp.Diff(want, got, protocmp.Transform()); diff != "" {
    t.Errorf("Foo() returned unexpected difference in protobuf messages (-want +got):\n%s", diff)
}
```

Although the `cmp` package is not part of the Go standard library, it is maintained by the Go team and should produce stable equality results over time. It is user-configurable and should serve most comparison needs.

Existing code may make use of the following older libraries, and may continue using them for consistency:

- [`pretty`](https://pkg.go.dev/github.com/kylelemons/godebug/pretty) produces aesthetically pleasing difference reports. However, it quite deliberately considers values that have the same visual representation as equal. In particular, `pretty` does not catch differences between nil slices and empty ones, is not sensitive to different interface implementations with identical fields, and it is possible to use a nested map as the basis for comparison with a struct value. It also serializes the entire value into a string before producing a diff, and as such is not a good choice for comparing large values. By default, it compares unexported fields, which makes it sensitive to changes in implementation details in your dependencies. For this reason, it is not appropriate to use `pretty` on protobuf messages.

Prefer using `cmp` for new code, and it is worth considering updating older code to use `cmp` where and when it is practical to do so.

Older code may use the standard library `reflect.DeepEqual` function to compare complex structures. `reflect.DeepEqual` should not be used for checking equality, as it is sensitive to changes in unexported fields and other implementation details. Code that is using `reflect.DeepEqual` should be updated to one of the above libraries.

**Note:** The `cmp` package is designed for testing, rather than production use. As such, it may panic when it suspects that a comparison is performed incorrectly to provide instruction to users on how to improve the test to be less brittle. Given cmp’s propensity towards panicking, it makes it unsuitable for code that is used in production as a spurious panic may be fatal.

### Level of detail

The conventional failure message, which is suitable for most Go tests, is `YourFunc(%v) = %v, want %v`. However, there are cases that may call for more or less detail:

- Tests performing complex interactions should describe the interactions too. For example, if the same `YourFunc` is called several times, identify which call failed the test. If it’s important to know any extra state of the system, include that in the failure output (or at least in the logs).
- If the data is a complex struct with significant boilerplate, it is acceptable to describe only the important parts in the message, but do not overly obscure the data.
- Setup failures do not require the same level of detail. If a test helper populates a Spanner table but Spanner was down, you probably don’t need to include which test input you were going to store in the database. `t.Fatalf("Setup: Failed to set up test database: %s", err)` is usually helpful enough to resolve the issue.

**Tip:** Make your failure mode trigger during development. Review what the failure message looks like and whether a maintainer can effectively deal with the failure.

There are some techniques for reproducing test inputs and outputs clearly:

- When printing string data, [`%q` is often useful](https://google.github.io/styleguide/go/decisions#use-percent-q) to emphasize that the value is important and to more easily spot bad values.
- When printing (small) structs, `%+v` can be more useful than `%v`.
- When validation of larger values fails, [printing a diff](https://google.github.io/styleguide/go/decisions#print-diffs) can make it easier to understand the failure.

### Print diffs

If your function returns large output then it can be hard for someone reading the failure message to find the differences when your test fails. Instead of printing both the returned value and the wanted value, make a diff.

To compute diffs for such values, `cmp.Diff` is preferred, particularly for new tests and new code, but other tools may be used. See [types of equality](https://google.github.io/styleguide/go/decisions#types-of-equality) for guidance regarding the strengths and weaknesses of each function.

- [`cmp.Diff`](https://pkg.go.dev/github.com/google/go-cmp/cmp/cmp#Diff)
- [`pretty.Compare`](https://pkg.go.dev/github.com/kylelemons/godebug/pretty#Compare)

You can use the [`diff`](https://pkg.go.dev/github.com/kylelemons/godebug/diff) package to compare multi-line strings or lists of strings. You can use this as a building block for other kinds of diffs.

Add some text to your failure message explaining the direction of the diff.

- Something like `diff (-want +got)` is good when you’re using the `cmp`, `pretty`, and `diff` packages (if you pass `(want, got)` to the function), because the `-` and `+` that you add to your format string will match the `-` and `+` that actually appear at the beginning of the diff lines. If you pass `(got, want)` to your function, the correct key would be `(-got +want)` instead.
- The `messagediff` package uses a different output format, so the message `diff (want -> got)` is appropriate when you’re using it (if you pass `(want, got)` to the function), because the direction of the arrow will match the direction of the arrow in the “modified” lines.

The diff will span multiple lines, so you should print a newline before you print the diff.

### Test error semantics

When a unit test performs string comparisons or uses a vanilla `cmp` to check that particular kinds of errors are returned for particular inputs, you may find that your tests are brittle if any of those error messages are reworded in the future. Since this has the potential to turn your unit test into a change detector (see [TotT: Change-Detector Tests Considered Harmful](https://testing.googleblog.com/2015/01/testing-on-toilet-change-detector-tests.html) ), don’t use string comparison to check what type of error your function returns. However, it is permissible to use string comparisons to check that error messages coming from the package under test satisfy certain properties, for example, that it includes the parameter name.

Error values in Go typically have a component intended for human eyes and a component intended for semantic control flow. Tests should seek to only test semantic information that can be reliably observed, rather than display information that is intended for human debugging, as this is often subject to future changes. For guidance on constructing errors with semantic meaning see [best-practices regarding errors](https://google.github.io/styleguide/go/best-practices#error-handling). If an error with insufficient semantic information is coming from a dependency outside your control, consider filing a bug against the owner to help improve the API, rather than relying on parsing the error message.

Within unit tests, it is common to only care whether an error occurred or not. If so, then it is sufficient to only test whether the error was non-nil when you expected an error. If you would like to test that the error semantically matches some other error, then consider using `cmp` with [`cmpopts.EquateErrors`](https://pkg.go.dev/github.com/google/go-cmp/cmp/cmpopts#EquateErrors).

> **Note:** If a test uses [`cmpopts.EquateErrors`](https://pkg.go.dev/github.com/google/go-cmp/cmp/cmpopts#EquateErrors) but all of its `wantErr` values are either `nil` or `cmpopts.AnyError`, then using `cmp` is [unnecessary mechanism](https://google.github.io/styleguide/go/guide#least-mechanism). Simplify the code by making the want field a `bool`. You can then use a simple comparison with `!=`.
>
> ```
> // Good:
> gotErr := f(test.input) != nil
> if gotErr != test.wantErr {
>     t.Errorf("f(%q) returned err = %v, want error presence = %v", test.input, gotErr, test.wantErr)
> }
> ```

See also [GoTip #13: Designing Errors for Checking](https://google.github.io/styleguide/go/index.html#gotip).

## Test structure

### Subtests

The standard Go testing library offers a facility to [define subtests](https://pkg.go.dev/testing#hdr-Subtests_and_Sub_benchmarks). This allows flexibility in setup and cleanup, controlling parallelism, and test filtering. Subtests can be useful (particularly for table-driven tests), but using them is not mandatory. See also https://blog.golang.org/subtests.

Subtests should not depend on the execution of other cases for success or initial state, because subtests are expected to be able to be run individually with using `go test -run` flags or with Bazel [test filter](https://bazel.build/docs/user-manual#test-filter) expressions.

#### Subtest names

Name your subtest such that it is readable in test output and useful on the command line for users of test filtering. When you use `t.Run` to create a subtest, the first argument is used as a descriptive name for the test. To ensure that test results are legible to humans reading the logs, choose subtest names that will remain useful and readable after escaping. Think of subtest names more like a function identifier than a prose description. The test runner replaces spaces with underscores, and escapes non-printing characters. If your test data benefits from a longer description, consider putting the description in a separate field (perhaps to be printed using `t.Log` or alongside failure messages).

Subtests may be run individually using flags to the [Go test runner](https://golang.org/cmd/go/#hdr-Testing_flags) or Bazel [test filter](https://bazel.build/docs/user-manual#test-filter), so choose descriptive names that are also easy to type.

> **Warning:** Slash characters are particularly unfriendly in subtest names, since they have [special meaning for test filters](https://blog.golang.org/subtests#:~:text=Perhaps a bit,match any tests).
>
> > ```
> > # Bad:
> > # Assuming TestTime and t.Run("America/New_York", ...)
> > bazel test :mytest --test_filter="Time/New_York"    # Runs nothing!
> > bazel test :mytest --test_filter="Time//New_York"   # Correct, but awkward.
> > ```

To [identify the inputs](https://google.github.io/styleguide/go/decisions#identify-the-input) of the function, include them in the test’s failure messages, where they won’t be escaped by the test runner.

```
// Good:
func TestTranslate(t *testing.T) {
    data := []struct {
        name, desc, srcLang, dstLang, srcText, wantDstText string
    }{
        {
            name:        "hu=en_bug-1234",
            desc:        "regression test following bug 1234. contact: cleese",
            srcLang:     "hu",
            srcText:     "cigarettát és egy öngyújtót kérek",
            dstLang:     "en",
            wantDstText: "cigarettes and a lighter please",
        }, // ...
    }
    for _, d := range data {
        t.Run(d.name, func(t *testing.T) {
            got := Translate(d.srcLang, d.dstLang, d.srcText)
            if got != d.wantDstText {
                t.Errorf("%s\nTranslate(%q, %q, %q) = %q, want %q",
                    d.desc, d.srcLang, d.dstLang, d.srcText, got, d.wantDstText)
            }
        })
    }
}
```

Here are a few examples of things to avoid:

```
// Bad:
// Too wordy.
t.Run("check that there is no mention of scratched records or hovercrafts", ...)
// Slashes cause problems on the command line.
t.Run("AM/PM confusion", ...)
```

### Table-driven tests

Use table-driven tests when many different test cases can be tested using similar testing logic.

- When testing whether the actual output of a function is equal to the expected output. For example, the many [tests of `fmt.Sprintf`](https://cs.opensource.google/go/go/+/master:src/fmt/fmt_test.go) or the minimal snippet below.
- When testing whether the outputs of a function always conform to the same set of invariants. For example, [tests for `net.Dial`](https://cs.opensource.google/go/go/+/master:src/net/dial_test.go;l=318;drc=5b606a9d2b7649532fe25794fa6b99bd24e7697c).

Here is the minimal structure of a table-driven test, copied from the standard `strings` library. If needed, you may use different names, move the test slice into the test function, or add extra facilities such as subtests or setup and cleanup functions. Always keep [useful test failures](https://google.github.io/styleguide/go/decisions#useful-test-failures) in mind.

```
// Good:
var compareTests = []struct {
    a, b string
    i    int
}{
    {"", "", 0},
    {"a", "", 1},
    {"", "a", -1},
    {"abc", "abc", 0},
    {"ab", "abc", -1},
    {"abc", "ab", 1},
    {"x", "ab", 1},
    {"ab", "x", -1},
    {"x", "a", 1},
    {"b", "x", -1},
    // test runtime·memeq's chunked implementation
    {"abcdefgh", "abcdefgh", 0},
    {"abcdefghi", "abcdefghi", 0},
    {"abcdefghi", "abcdefghj", -1},
}

func TestCompare(t *testing.T) {
    for _, tt := range compareTests {
        cmp := Compare(tt.a, tt.b)
        if cmp != tt.i {
            t.Errorf(`Compare(%q, %q) = %v`, tt.a, tt.b, cmp)
        }
    }
}
```

**Note**: The failure messages in this example above fulfill the guidance to [identify the function](https://google.github.io/styleguide/go/decisions#identify-the-function) and [identify the input](https://google.github.io/styleguide/go/decisions#identify-the-input). There’s no need to [identify the row numerically](https://google.github.io/styleguide/go/decisions#table-tests-identifying-the-row).

When some test cases need to be checked using different logic from other test cases, it is more appropriate to write multiple test functions, as explained in [GoTip #50: Disjoint Table Tests](https://google.github.io/styleguide/go/index.html#gotip). The logic of your test code can get difficult to understand when each entry in a table has its own different conditional logic to check each output for its inputs. If test cases have different logic but identical setup, a sequence of [subtests](https://google.github.io/styleguide/go/decisions#subtests) within a single test function might make sense.

You can combine table-driven tests with multiple test functions. For example, when testing that a function’s output exactly matches the expected output and that the function returns a non-nil error for an invalid input, then writing two separate table-driven test functions is the best approach: one for normal non-error outputs, and one for error outputs.

#### Data-driven test cases

Table test rows can sometimes become complicated, with the row values dictating conditional behavior inside the test case. The extra clarity from the duplication between the test cases is necessary for readability.

```
// Good:
type decodeCase struct {
    name   string
    input  string
    output string
    err    error
}

func TestDecode(t *testing.T) {
    // setupCodex is slow as it creates a real Codex for the test.
    codex := setupCodex(t)

    var tests []decodeCase // rows omitted for brevity

    for _, test := range tests {
        t.Run(test.name, func(t *testing.T) {
            output, err := Decode(test.input, codex)
            if got, want := output, test.output; got != want {
                t.Errorf("Decode(%q) = %v, want %v", test.input, got, want)
            }
            if got, want := err, test.err; !cmp.Equal(got, want) {
                t.Errorf("Decode(%q) err %q, want %q", test.input, got, want)
            }
        })
    }
}

func TestDecodeWithFake(t *testing.T) {
    // A fakeCodex is a fast approximation of a real Codex.
    codex := newFakeCodex()

    var tests []decodeCase // rows omitted for brevity

    for _, test := range tests {
        t.Run(test.name, func(t *testing.T) {
            output, err := Decode(test.input, codex)
            if got, want := output, test.output; got != want {
                t.Errorf("Decode(%q) = %v, want %v", test.input, got, want)
            }
            if got, want := err, test.err; !cmp.Equal(got, want) {
                t.Errorf("Decode(%q) err %q, want %q", test.input, got, want)
            }
        })
    }
}
```

In the counterexample below, note how hard it is to distinguish between which type of `Codex` is used per test case in the case setup. (The highlighted parts run afoul of the advice from [TotT: Data Driven Traps!](https://testing.googleblog.com/2008/09/tott-data-driven-traps.html) .)

```
// Bad:
type decodeCase struct {
  name   string
  input  string
  codex  testCodex
  output string
  err    error
}

type testCodex int

const (
  fake testCodex = iota
  prod
)

func TestDecode(t *testing.T) {
  var tests []decodeCase // rows omitted for brevity

  for _, test := tests {
    t.Run(test.name, func(t *testing.T) {
      var codex Codex
      switch test.codex {
      case fake:
        codex = newFakeCodex()
      case prod:
        codex = setupCodex(t)
      default:
        t.Fatalf("unknown codex type: %v", codex)
      }
      output, err := Decode(test.input, codex)
      if got, want := output, test.output; got != want {
        t.Errorf("Decode(%q) = %q, want %q", test.input, got, want)
      }
      if got, want := err, test.err; !cmp.Equal(got, want) {
        t.Errorf("Decode(%q) err %q, want %q", test.input, got, want)
      }
    })
  }
}
```

#### Identifying the row

Do not use the index of the test in the test table as a substitute for naming your tests or printing the inputs. Nobody wants to go through your test table and count the entries in order to figure out which test case is failing.

```
// Bad:
tests := []struct {
    input, want string
}{
    {"hello", "HELLO"},
    {"wORld", "WORLD"},
}
for i, d := range tests {
    if strings.ToUpper(d.input) != d.want {
        t.Errorf("failed on case #%d", i)
    }
}
```

Add a test description to your test struct and print it along failure messages. When using subtests, your subtest name should be effective in identifying the row.

**Important:** Even though `t.Run` scopes the output and execution, you must always [identify the input](https://google.github.io/styleguide/go/decisions#identify-the-input). The table test row names must follow the [subtest naming](https://google.github.io/styleguide/go/decisions#subtest-names) guidance.

### Test helpers

A test helper is a function that performs a setup or cleanup task. All failures that occur in test helpers are expected to be failures of the environment (not from the code under test) — for example when a test database cannot be started because there are no more free ports on this machine.

If you pass a `*testing.T`, call [`t.Helper`](https://pkg.go.dev/testing#T.Helper) to attribute failures in the test helper to the line where the helper is called. This parameter should come after a [context](https://google.github.io/styleguide/go/decisions#contexts) parameter, if present, and before any remaining parameters.

```
// Good:
func TestSomeFunction(t *testing.T) {
    golden := readFile(t, "testdata/golden-result.txt")
    // ... tests against golden ...
}

// readFile returns the contents of a data file.
// It must only be called from the same goroutine as started the test.
func readFile(t *testing.T, filename string) string {
    t.Helper()
    contents, err := runfiles.ReadFile(filename)
    if err != nil {
        t.Fatal(err)
    }
    return string(contents)
}
```

Do not use this pattern when it obscures the connection between a test failure and the conditions that led to it. Specifically, the guidance about [assert libraries](https://google.github.io/styleguide/go/decisions#assert) still applies, and [`t.Helper`](https://pkg.go.dev/testing#T.Helper) should not be used to implement such libraries.

**Tip:** For more on the distinction between test helpers and assertion helpers, see [best practices](https://google.github.io/styleguide/go/best-practices#test-functions).

Although the above refers to `*testing.T`, much of the advice stays the same for benchmark and fuzz helpers.

### Test package

#### Tests in the same package

Tests may be defined in the same package as the code being tested.

To write a test in the same package:

- Place the tests in a `foo_test.go` file
- Use `package foo` for the test file
- Do not explicitly import the package to be tested

```build
# Good:
go_library(
    name = "foo",
    srcs = ["foo.go"],
    deps = [
        ...
    ],
)

go_test(
    name = "foo_test",
    size = "small",
    srcs = ["foo_test.go"],
    library = ":foo",
    deps = [
        ...
    ],
)
```

A test in the same package can access unexported identifiers in the package. This may enable better test coverage and more concise tests. Be aware that any [examples](https://google.github.io/styleguide/go/decisions#examples) declared in the test will not have the package names that a user will need in their code.

#### Tests in a different package

It is not always appropriate or even possible to define a test in the same package as the code being tested. In these cases, use a package name with the `_test` suffix. This is an exception to the “no underscores” rule to [package names](https://google.github.io/styleguide/go/decisions#package-names). For example:

- If an integration test does not have an obvious library that it belongs to

  ```
  // Good:
  package gmailintegration_test
  
  import "testing"
  ```

- If defining the tests in the same package results in circular dependencies

  ```
  // Good:
  package fireworks_test
  
  import (
    "fireworks"
    "fireworkstestutil" // fireworkstestutil also imports fireworks
  )
  ```

### Use package `testing`

The Go standard library provides the [`testing` package](https://pkg.go.dev/testing). This is the only testing framework permitted for Go code in the Google codebase. In particular, [assertion libraries](https://google.github.io/styleguide/go/decisions#assert) and third-party testing frameworks are not allowed.

The `testing` package provides a minimal but complete set of functionality for writing good tests:

- Top-level tests
- Benchmarks
- [Runnable examples](https://blog.golang.org/examples)
- Subtests
- Logging
- Failures and fatal failures

These are designed to work cohesively with core language features like [composite literal](https://go.dev/ref/spec#Composite_literals) and [if-with-initializer](https://go.dev/ref/spec#If_statements) syntax to enable test authors to write [clear, readable, and maintainable tests].

## Non-decisions

A style guide cannot enumerate positive prescriptions for all matters, nor can it enumerate all matters about which it does not offer an opinion. That said, here are a few things where the readability community has previously debated and has not achieved consensus about.

- **Local variable initialization with zero value**. `var i int` and `i := 0` are equivalent. See also [initialization best practices](https://google.github.io/styleguide/go/best-practices#vardeclinitialization).
- **Empty composite literal vs. `new` or `make`**. `&File{}` and `new(File)` are equivalent. So are `map[string]bool{}` and `make(map[string]bool)`. See also [composite declaration best practices](https://google.github.io/styleguide/go/best-practices#vardeclcomposite).
- **got, want argument ordering in cmp.Diff calls**. Be locally consistent, and [include a legend](https://google.github.io/styleguide/go/decisions#print-diffs) in your failure message.
- **`errors.New` vs `fmt.Errorf` on non-formatted strings**. `errors.New("foo")` and `fmt.Errorf("foo")` may be used interchangeably.

If there are special circumstances where they come up again, the readability mentor might make an optional comment, but in general the author is free to pick the style they prefer in the given situation.

Naturally, if anything not covered by the style guide does need more discussion, authors are welcome to ask – either in the specific review, or on internal message boards.