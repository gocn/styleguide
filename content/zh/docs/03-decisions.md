# Go 风格决策

原文：[https://google.github.io/styleguide/go](https://google.github.io/styleguide/go)

[概述](https://gocn.github.io/styleguide/docs/01-overview/) | [风格指南](https://gocn.github.io/styleguide/docs/02-guide/) | [风格决策](https://gocn.github.io/styleguide/docs/03-decisions/) | [最佳实践](https://gocn.github.io/styleguide/docs/04-best-practices/)

**注意：** 本文是 Google [Go 风格](https://gocn.github.io/styleguide/docs/01-overview/) 系列文档的一部分。本文档是 **[规范性(normative)](https://gocn.github.io/styleguide/docs/01-overview/#标准normative) 但不是[强制规范(canonical)](https://gocn.github.io/styleguide/docs/01-overview/#规范canonical)**，并且从属于[Google 风格指南](https://gocn.github.io/styleguide/docs/02-guide/)。请参阅[概述](https://gocn.github.io/styleguide/docs/01-overview/#关于)获取更多详细信息。

## 关于

本文包含旨在统一和为 Go 可读性导师给出的建议提供标准指导、解释和示例的风格决策。

本文档**并不详尽**，且会随着时间的推移而增加。如果[风格指南](https://gocn.github.io/styleguide/docs/02-guide/) 与此处给出的建议相矛盾，**风格指南优先**，并且本文档应相应更新。

参见 [关于](https://gocn.github.io/styleguide/docs/01-overview/#关于)  获取 Go 风格的全套文档。

以下部分已从样式决策移至指南的一部分：

- **混合大写字母MixedCaps**: 参见 https://gocn.github.io/styleguide/docs/02-guide/#大小写混合
- **格式化Formatting**: 参见 https://gocn.github.io/styleguide/docs/02-guide/#格式化
- **行长度Line Length**: 参见 https://gocn.github.io/styleguide/docs/02-guide/#行长度

## 命名Naming

有关命名的总体指导，请参阅[核心风格指南](https://gocn.github.io/styleguide/docs/02-guide/#命名) 中的命名部分，以下部分对命名中的特定区域提供进一步的说明。

### 下划线Underscores

Go 中的命名通常不应包含下划线。这个原则有三个例外：

1. 仅由生成代码导入的包名称可能包含下划线。有关如何选择多词包名称的更多详细信息，请参阅[包名称](https://gocn.github.io/styleguide/docs/03-decisions/#包名称package-names)。
2. `*_test.go` 文件中的测试、基准和示例函数名称可能包含下划线。
3. 与操作系统或 cgo 互操作的低级库可能会重用标识符，如 [`syscall`](https://pkg.go.dev/syscall#pkg-constants) 中所做的那样。在大多数代码库中，这预计是非常罕见的。
### 包名称Package names

Go 包名称应该简短并且只包含小写字母。由多个单词组成的包名称应全部小写。例如，包 [`tabwriter`](https://pkg.go.dev/text/tabwriter) 不应该命名为 `tabWriter`、`TabWriter` 或 `tab_writer`。

避免选择可能被常用局部变量[遮蔽覆盖](https://gocn.github.io/styleguide/docs/04-best-practices/#阴影) 的包名称。例如，`usercount` 是比 `count` 更好的包名，因为 `count` 是常用变量名。

Go 包名称不应该有下划线。如果你需要导入名称中确实有一个包（通常来自生成的或第三方代码），则必须在导入时将其重命名为适合在 Go 代码中使用的名称。

一个例外是仅由生成的代码导入的包名称可能包含下划线。具体例子包括：

- 对外部测试包使用 _test 后缀，例如集成测试
- 使用 `_test` 后缀作为 [包级文档示例](https://go.dev/blog/examples)

避免使用无意义的包名称，例如 `util`、`utility`、`common`、`helper` 等。查看更多关于[所谓的“实用程序包”](https://gocn.github.io/styleguide/docs/04-best-practices/#工具包)。

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

常量名称必须像 Go 中的所有其他名称一样使用 [混合大写字母MixedCaps](https://gocn.github.io/styleguide/docs/02-guide#大小写混合)。（[导出](https://tour.golang.org/basics/3) 常量以大写字母开头，而未导出的常量以小写字母开头。）即使打破了其他语言的约定，这也是适用的。常量名称不应是其值的派生词，而应该解释值所表示的含义。

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

根据它们的角色而不是它们的值来命名常量。如果一个常量除了它的值之外没有其他作用，那么就没有必要将它定义为一个常量。

```
// Bad:
const Twelve = 12

const (
    UserNameColumn = "username"
    GroupColumn    = "group"
)
```

### 缩写词Initialisms

名称中的首字母缩略词或单独的首字母缩略词（例如，“URL”和“NATO”）应该具有相同的大小写。`URL` 应显示为 `URL` 或 `url`（如 `urlPony` 或 `URLPony`），绝不能显示为 `Url`。这也适用于 `ID` 是“identifier”的缩写； 写 `appID` 而不是 `appId`。

- 在具有多个首字母缩写的名称中（例如 `XMLAPI` 因为它包含 `XML` 和 `API`），给定首字母缩写中的每个字母都应该具有相同的大小写，但名称中的每个首字母缩写不需要具有相同的大小写。
- 在带有包含小写字母的首字母缩写的名称中（例如`DDoS`、`iOS`、`gRPC`），首字母缩写应该像在标准中一样出现，除非你需要为了满足 [导出](https://golang.org/ref/spec#Exported_identifiers) 而更改第一个字母。在这些情况下，整个缩写词应该是相同的情况（例如 `ddos`、`IOS`、`GRPC`）。

| 缩写词     | 范围         | 正确       | 错误                                     |
|---------|------------|----------|----------------------------------------|
| XML API | Exported   | `XMLAPI` | `XmlApi`, `XMLApi`, `XmlAPI`, `XMLapi` |
| XML API | Unexported | `xmlAPI` | `xmlapi`, `xmlApi`                     |
| iOS     | Exported   | `IOS`    | `Ios`, `IoS`                           |
| iOS     | Unexported | `iOS`    | `ios`                                  |
| gRPC    | Exported   | `GRPC`   | `Grpc`                                 |
| gRPC    | Unexported | `gRPC`   | `grpc`                                 |
| DDoS    | Exported   | `DDoS`   | `DDOS`, `Ddos`                         |
| DDoS    | Unexported | `ddos`   | `dDoS`, `dDOS`                         |

### Get方法Getters

函数和方法名称不应使用 `Get` 或 `get` 前缀，除非底层概念使用单词“get”（例如 HTTP GET）。此时，更应该直接以名词开头的名称，例如使用 `Counts` 而不是 `GetCounts`。

如果该函数涉及执行复杂的计算或执行远程调用，则可以使用`Compute` 或 `Fetch`等不同的词代替`Get`，以使读者清楚函数调用可能需要时间，并有可能会阻塞或失败。

### 变量名Variable names

一般的经验法则是，名称的长度应与其范围的大小成正比，并与其在该范围内使用的次数成反比。在文件范围内创建的变量可能需要多个单词，而单个内部块作用域内的变量可能是单个单词甚至只是一两个字符，以保持代码清晰并避免无关信息。

这是一条粗略的基础原则。这些数字准则不是严格的规则。要根据上下文、[清晰](https://gocn.github.io/styleguide/docs/02-guide/#清晰) 和[简洁](https://gocn.github.io/styleguide/docs/02-guide/#简洁）来进行判断。

- 小范围是执行一两个小操作的范围，比如 1-7 行。
- 中等范围是一些小的或一个大的操作，比如 8-15 行。
- 大范围是一个或几个大操作，比如 15-25 行。
- 非常大的范围是指超过一页（例如，超过 25 行）的任何内容。

在小范围内可能非常清楚的名称（例如，`c` 表示计数器）在较大范围内可能不够用，并且需要澄清以提示进一步了解其在代码中的用途。一个作用域中有很多变量，或者表示相似值或概念的变量，那就可能需要比作用域建议的采用更长的变量名称。

概念的特殊性也有助于保持变量名称的简洁。例如，假设只有一个数据库在使用，像`db`这样的短变量名通常可能保留给非常小的范围，即使范围非常大，也可能保持完全清晰。在这种情况下，根据范围的大小，单个词`database`可能是可接受的，但不是必需的，因为`db`是该词的一种非常常见的缩写，几乎没有其他解释。

局部变量的名称应该反映它包含的内容以及它在当前上下文中的使用方式，而不是值的来源。例如，通常情况下最佳局部变量名称与结构或协议缓冲区字段名称不同。

一般来说：

- 像 `count` 或 `options` 这样的单字名称是一个很好的起点。

- 可以添加其他词来消除相似名称的歧义，例如 `userCount` 和 `projectCount`。

- 不要简单地省略字母来节省打字时间。例如，`Sandbox` 优于 `Sbx`，特别是对于导出的名称。

- 大多数变量名可省略 [类型和类似类型的词](https://gocn.github.io/styleguide/docs/03-decisions/#变量名-vs-类型variable-name-vs-type)

  - 对于数字，`userCount` 是比 `numUsers` 或 `usersInt` 更好的名称。
  - 对于切片，`users` 是一个比 `userSlice` 更好的名字。
  - 如果范围内有两个版本的值，则包含类似类型的限定符是可以接受的，例如，你可能将输入存储在 `ageString` 中，并使用 `age` 作为解析值。

- 省略[上下文](https://gocn.github.io/styleguide/docs/03-decisions/#外部上下文-vs-本地名称external-context-vs-local-names) 中清楚的单词。例如，在 UserCount 方法的实现中，名为 userCount 的局部变量可能是多余的； `count`、`users` 甚至 `c` 都具有可读性。

#### 单字母变量名Single-letter variable names

单字母变量名是可以减少[重复](https://gocn.github.io/styleguide/docs/03-decisions/#重复repetition) 的有用工具，但也可能使代码变得不透明。将它们的使用限制在完整单词很明显以及它会重复出现以代替单字母变量的情况。

一般来说：

- 对于[方法接收者变量](https://gocn.github.io/styleguide/docs/03-decisions/#方法接收者变量receiver-names)，最好使用一个字母或两个字母的名称。
- 对常见类型使用熟悉的变量名通常很有帮助：
   - `r` 用于 `io.Reader` 或 `*http.Request`
   - `w` 用于 `io.Writer` 或 `http.ResponseWriter`
- 单字母标识符作为整数循环变量是可接受的，特别是对于索引（例如，`i`）和坐标（例如，`x` 和 `y`）。
- 当范围很短时，循环标识符使用缩写是可接受的，例如`for _, n := range nodes { ... }`。

### 重复Repetition

一段 Go 源代码应该避免不必要的重复。一个常见的情形是重复名称，其中通常包含不必要的单词或重复其上下文或类型。如果相同或相似的代码段在很近的地方多次出现，代码本身也可能是不必要的重复。

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

| 重复的名称                         | 更好的名称                  |
|-------------------------------|------------------------|
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

包含来自周围上下文信息的名称通常会产生额外的噪音，而没有任何好处。包名、方法名、类型名、函数名、导入路径，包含来自其上下文信息的名称。

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

重复通常应该在符号使用者的上下文中进行评估，而不是孤立地进行评估。例如，下面的代码有很多名称，在某些情况下可能没问题，但在上下文中是多余的：

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

关于评论的约定（包括评论什么、使用什么风格、如何提供可运行的示例等）旨在支持阅读公共 API 文档的体验。有关详细信息，请参阅 [Effective Go](http://golang.org/doc/effective_go.html#commentary)。

最佳实践文档关于 [文档约定](https://gocn.github.io/styleguide/docs/04-best-practices/#公约) 的部分进一步讨论了这一点。

**最佳实践：** 在开发和代码审查期间使用[文档预览](https://gocn.github.io/styleguide/docs/04-best-practices/#预览) 查看文档和可运行示例是否有用并以你期望的方式呈现。

**提示：** Godoc 使用很少的特殊格式； 列表和代码片段通常应该缩进以避免换行。除缩进外，通常应避免装饰。

### 注释行长度Comment line length

确保注释在即使在较窄的屏幕上的可读性。

当评论变得太长时，建议将其包装成多个单行评论。在可能的情况下，争取在 80 列宽的终端上阅读良好的注释，但这并不是硬性限制； Go 中的注释没有固定的行长度限制。例如，标准库经常选择根据标点符号来打断注释，这有时会使个别行更接近 60-70 个字符标记。

有很多现有代码的注释长度超过 80 个字符。本指南不应作为更改此类代码作为可读性审查的一部分的理由（请参阅[一致](https://gocn.github.io/styleguide/docs/02-guide/#一致)），但鼓励团队作为其他重构的一部分，有机会时更新注释以遵循此指南。本指南的主要目标是确保所有 Go 可读性导师在提出建议时以及是否提出相同的建议。

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

避免注释在小屏幕上重复换行，这是一种糟糕的阅读体验。

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

所有顶级导出名称都必须有文档注释，具有不明显行为或含义的未导出类型或函数声明也应如此。这些注释应该是[完整句子](https://gocn.github.io/styleguide/docs/03-decisions/#注释语句comment-sentences)，以所描述对象的名称开头。冠词（“a”、“an”、“the”）可以放在名字前面，使其读起来更自然。

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

**最佳实践：** 如果你对未导出的代码有文档注释，请遵循与导出代码相同的习惯（即，以未导出的名称开始注释）。这使得以后导出它变得容易，只需在注释和代码中用新导出的名称替换未导出的名称即可。

### 注释语句Comment sentences

完整的注释应该像标准英语句子一样包含大写和标点符号。（作为一个例外，如果在其他方面很清楚，可以以非大写的标识符名称开始一个句子。这种情况最好只在段落的开头进行。）

作为句子片段的注释对标点符号或大小写没有此类要求。

[文档注释](https://gocn.github.io/styleguide/docs/03-decisions/#文档注释doc-comments) 应始终是完整的句子，因此应始终大写和标点符号。简单的行尾注释（特别是对于结构字段）可以为假设字段名称是主语的简单短语。

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

包应该清楚地记录它们的预期用途。尝试提供一个[可运行的例子](http://blog.golang.org/examples)； 示例出现在 Godoc 中。可运行示例属于测试文件，而不是生产源文件。请参阅此示例（[Godoc](https://pkg.go.dev/time#example-Duration)，[source](https://cs.opensource.google/go/go/+/HEAD:src/time /example_test.go））。

如果无法提供可运行的示例，可以在代码注释中提供示例代码。与注释中的其他代码和命令行片段一样，它应该遵循标准格式约定。

### 命名的结果参数Named result parameters

当有命名参数时，请考虑函数签名在 Godoc 中的显示方式。函数本身的名称和结果参数的类型通常要足够清楚。

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

当名称产生 [不必要的重复](https://gocn.github.io/styleguide/docs/03-decisions/#变量名-vs-类型variable-name-vs-type) 时，不要使用命名结果参数。

```
// Bad:
func (n *Node) Parent1() (node *Node)
func (n *Node) Parent2() (node *Node, err error)
```

不要为了避免在函数内声明变量而使用命名结果参数。这种做法会导致不必要的冗长API，但收益只是微小的简洁性。

[裸返回](https://tour.golang.org/basics/7) 仅在小函数中是可接受的。一旦它是一个中等大小的函数，就需要明确你的返回值。同样，不要仅仅因为可以裸返回就使用命名结果参数。[清晰](https://gocn.github.io/styleguide/docs/02-guide/#清晰) 总是比在你的函数中节省几行更重要。

如果必须在延迟闭包中更改结果参数的值，则命名结果参数始终是可以接受的。

> **提示：** 类型通常比函数签名中的名称更清晰。[GoTip #38：作为命名类型的函数](https://gocn.github.io/styleguide/docs/01-overview/#gotip) 演示了这一点。
>
> 在上面的 [`WithTimeout`](https://pkg.go.dev/context#WithTimeout) 中，代码使用了一个 [`CancelFunc`](https://pkg.go.dev/context#CancelFunc) 而不是结果参数列表中的原始`func()`，并且几乎不需要做任何记录工作。

### 包注释

包注释必须出现在包内语句的上方，注释和包名称之间没有空行。例子：

```
// Good:
// Package math provides basic constants and mathematical functions.
//
// This package does not guarantee bit-identical results across architectures.
package math
```

每个包必须有一个包注释。如果一个包由多个文件组成，那么其中一个文件应该有包注释。

`main` 包的注释形式略有不同，其中 BUILD 文件中的 `go_binary` 规则的名称代替了包名。

```
// Good:
// The seed_generator command is a utility that generates a Finch seed file
// from a set of JSON study configs.
package main
```

只要二进制文件的名称与 BUILD 文件中所写的完全一致，其他风格的注释也是可以了。当二进制名称是第一个单词时，即使它与命令行调用的拼写不严格匹配，也需要将其大写。

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

- 命令行调用示例和 API 用法可以是有用的文档。对于 Godoc 格式，缩进包含代码的注释行。

- 如果没有明显的main文件或者包注释特别长，可以将文档注释放在名为 doc.go 的文件中，只有注释和包子句。

- 可以使用多行注释代替多个单行注释。如果文档包含可能对从源文件复制和粘贴有用的部分，如示例命令行（用于二进制文件）和模板示例，这将非常有用。

  ```
  // Good:
  /*
  The seed_generator command is a utility that generates a Finch seed file
  from a set of JSON study configs.

      seed_generator *.json | base64 > finch-seed.base64
  */
  package template
  ```

- 供维护者使用且适用于整个文件的注释通常放在导入声明之后。这些不会出现在 Godoc 中，也不受上述包注释规则的约束。

## 导入

### 导入重命名

只有在为了避免与其他导入的名称冲突时，才使用重命名导入。（由此推论，[好的包名称](https://gocn.github.io/styleguide/docs/03-decisions/#包名称package-names) 不需要重命名。）如果发生名称冲突，最好重命名 最本地或特定于项目的导入。包的本地别名必须遵循[包命名指南](https://gocn.github.io/styleguide/docs/03-decisions/#包名称package-names)，包括禁止使用下划线和大写字母。

生成的 protocol buffer 包必须重命名以从其名称中删除下划线，并且它们的别名必须具有 `pb` 后缀。有关详细信息，请参阅[ proto 和 stub 最佳实践](https://gocn.github.io/styleguide/docs/04-best-practices/#protos-and-stubs)。

```
// Good:
import (
    fspb "path/to/package/foo_service_go_proto"
)
```

导入的包名称没有有用的识别信息时（例如 `package v1`），应该重命名以包括以前的路径组件。重命名必须与导入相同包的其他本地文件一致，并且可以包括版本号。

**注意：** 最好重命名包以符合 [好的包命名规则](https://gocn.github.io/styleguide/docs/03-decisions/#包名称package-names)，但在vendor目录下的包通常是不可行的。


```
// Good:
import (
    core "github.com/kubernetes/api/core/v1"
    meta "github.com/kubernetes/apimachinery/pkg/apis/meta/v1beta1"
)
```

如果你需要导入一个名称与你要使用的公共局部变量名称（例如 `url`、`ssh`）冲突的包，并且你希望重命名该包，首选方法是使用 `pkg` 后缀（例如 `urlpkg`）。请注意，可以使用局部变量隐藏包； 仅当此类变量在范围内时仍需要使用此包时，才需要重命名。

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

将导入项分成多个组是可以接受的，例如，如果你想要一个单独的组来重命名、导入仅为了特殊效果 或另一个特殊的导入组。

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

Gofmt 负责按导入路径对每个组进行排序。但是，它不会自动将导入分成组。流行的 [goimports](https://google.github.io/styleguide/go/golang.org/x/tools/cmd/goimports) 工具结合了 Gofmt 和导入管理，根据上述规则将导入进行分组。通过 [goimports](https://google.github.io/styleguide/go/golang.org/x/tools/cmd/goimports) 来管理导入顺序是可行的，但随着文件的修改，其导入列表必须保持内部一致。

### 导入"blank" (`import _`)

使用语法 `import _ "package"`导入的包，称为副作用导入，只能在主包或需要它们的测试中导入。

此类包的一些示例包括：

- [time/tzdata](https://pkg.go.dev/time/tzdata)
- [image/jpeg](https://pkg.go.dev/image/jpeg) 在图像处理中的代码

避免在工具包中导入空白，即使工具包间接依赖于它们。将副作用导入限制到主包有助于控制依赖性，并使得编写依赖于不同导入的测试成为可能，而不会发生冲突或浪费构建成本。

以下是此规则的唯一例外情况：

- 你可以使用空白导入来绕过 [nogo 静态检查器](https://github.com/bazelbuild/rules_go/blob/master/go/nogo.rst) 中对不允许导入的检查。
- 你可以在使用 `//go:embed` 编译器指令的源文件中使用 [embed](https://pkg.go.dev/embed) 包的空白导入。

**提示：** 如果生产环境中你创建的工具包间接依赖于副作用导入，请记录这里的预期用途。

### 导入 "dot" (`import .`)

`import .` 形式是一种语言特性，它允许将从另一个包导出的标识符无条件地带到当前包中。有关更多信息，请参阅[语言规范](https://go.dev/ref/spec#Import_declarations)。

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

使用 `error` 表示函数可能会失败。按照惯例，`error` 是最后一个结果参数。

```
// Good:
func Good() error { /* ... */ }
```

返回 `nil` 错误是表示操作成功的惯用方式，否则表示可能会失败。如果函数返回错误，除非另有明确说明，否则调用者必须将所有非错误返回值视为未确定。通常来说，非错误返回值是它们的零值，但也不能直接这么假设。

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

返回错误的导出函数应使用`error`类型返回它们。具体的错误类型容易受到细微错误的影响：一个 `nil` 指针可以包装到接口中，从而就变成非 nil 值（参见 [关于该主题的 Go FAQ 条目](https://golang.org/doc/faq#nil_error)）。

```
// Bad:
func Bad() *os.PathError { /*...*/ }
```

**提示**：采用 `context.Context` 参数的函数通常应返回 `error`，以便调用者可以确定上下文是否在函数运行时被取消。

### 错误字符串

错误字符串不应大写（除非以导出名称、专有名词或首字母缩写词开头）并且不应以标点符号结尾。这是因为错误字符串通常在打印给用户之前出现在其他上下文中。

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

遇到错误的代码应该慎重选择如何处理它。使用 _ 变量丢弃错误通常是不合适的。如果函数返回错误，请执行以下操作之一：

- 立即处理并解决错误
- 将错误返回给调用者
- 在特殊情况下，调用 [`log.Fatal`](https://pkg.go.dev/github.com/golang/glog#Fatal) 或（如绝对有必要）则调用 `panic`

**注意：** `log.Fatalf` 不是标准库日志。参见 [#logging]。

在极少数情况下适合忽略或丢弃错误（例如调用 [`(*bytes.Buffer).Write`](https://pkg.go.dev/bytes#Buffer.Write) 被记录为永远不会失败），随附的注释应该解释为什么这是安全的。

```
// Good:
var b *bytes.Buffer

n, _ := b.Write(p) // never returns a non-nil error
```

关于错误处理的更多讨论和例子，请参见[Effective Go](http://golang.org/doc/effective_go.html#errors)和[最佳实践](https://gocn.github.io/styleguide/docs/04-best-practices/#错误处理)。

### In-band 错误

在C和类似语言中，函数通常会返回-1、null或空字符串等值，以示错误或丢失结果。这就是所谓的`In-band`处理。

```
// Bad:
// Lookup returns the value for key or -1 if there is no mapping for key.
func Lookup(key string) int
```

未能检查`In-band`错误值会导致错误，并可能将 error 归于错误的功能。

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

这个 API 可以防止调用者错误地编写`Parse(Lookup(key))`，从而导致编译时错误，因为`Lookup(key)`有两个返回值。

以这种方式返回错误，来构筑更强大和明确的错误处理。

```
// Good:
value, ok := Lookup(key)
if !ok {
    return fmt.Errorf("no value for %q", key)
}
return Parse(value)
```

一些标准库函数，如包`strings`中的函数，返回`In-band`错误值。这大大简化了字符串处理的代码，但代价是要求程序员更加勤奋。一般来说，Google 代码库中的 Go 代码应该为错误返回额外的值

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

> **提示：** 如果你使用一个变量超过几行代码，通常不值得使用`带有初始化的 if `风格。在这种情况下，通常最好将声明移出，使用标准的`if`语句。
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

更多细节见[Go Tip #1：视线](https://gocn.github.io/styleguide/docs/01-overview/#gotip)和[TotT：通过减少嵌套降低代码的复杂性](https://testing.googleblog.com/2017/06/code-health-reduce-nesting-reduce.html)。

## 语言

### 字面格式化

Go 有一个非常强大的[复合字面语法](https://golang.org/ref/spec#Composite_literals)，用它可以在一个表达式中表达深度嵌套的复杂值。在可能的情况下，应该使用这种字面语法，而不是逐字段建值。字面意义的 `gofmt`格式一般都很好，但有一些额外的规则可以使这些字面意义保持可读和可维护。

#### 字段名称

对于在当前包之外定义的类型，结构体字面量通常应该指定**字段名**。

- 包括来自其他包的类型的字段名。

  ```
  // Good:
  good := otherpkg.Type{A: 42}
  ```

  结构中字段的位置和字段的完整集合（当字段名被省略时，这两者都是有必要搞清楚的）通常不被认为是结构的公共 API 的一部分；需要指定字段名以避免不必要的耦合。

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

一对大括号的最后一半应该总是出现在一行中，其缩进量与开头的大括号相同。单行字词必然具有这个属性。当字面意义跨越多行时，保持这一属性可以使字面意义的括号匹配与函数和`if`语句等常见 Go 语法结构的括号匹配相同。

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

- [缩进匹配](https://gocn.github.io/styleguide/docs/03-decisions/#匹配的大括号)
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

重复的类型名称可以从 slice 和 map 字面上省略，这对减少杂乱是有帮助的。明确重复类型名称的一个合理场合，当在你的项目中处理一个不常见的复杂类型时，当重复的类型名称在一行上却相隔很远的时候，可以提醒读者的上下文。

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

**提示：** 如果你想删除结构字中重复的类型名称，可以运行`gofmt -s`。

#### 零值字段

[零值](https://golang.org/ref/spec#The_zero_value)字段可以从结构字段中省略，但不能因此而失去`清晰`这个风格原则。

设计良好的 API 经常采用零值结构来提高可读性。例如，从下面的结构中省略三个零值字段，可以使人们注意到正在指定的唯一选项。

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

表驱动的测试中的结构经常受益于[显式字段名](https://gocn.github.io/styleguide/docs/03-decisions/#字段名称)，特别是当测试结构不是琐碎的时候。这允许作者在有关字段与测试用例无关时完全省略零值字段。例如，成功的测试案例应该省略任何与错误或失败相关的字段。在零值对于理解测试用例是必要的情况下，例如测试零或 `nil` 输入，应该指定字段名。

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

如果你声明一个空切片作为局部变量（特别是如果它可以成为返回值的来源），最好选择 nil 初始化，以减少调用者的错误风险。

```
// Good:
var t []string
// Bad:
t := []string{}
```

不要创建强迫调用者区分 nil 和空切片的 API。

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

在设计接口时，避免区分 `nil` 切片和非 `nil` 的零长度切片，因为这可能导致微妙的编程错误。这通常是通过使用`len`来检查是否为空，而不是`==nil`来实现的。

这个实现同时将`nil`和零长度的切片视为 "空"。

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

详见 [in-band 错误](https://gocn.github.io/styleguide/docs/03-decisions/#in-band-错误).

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

- [函数格式化](https://gocn.github.io/styleguide/docs/03-decisions/#函数格式化)
- [Conditionals and loops](https://gocn.github.io/styleguide/docs/03-decisions/#条件和循环)
- [Literal formatting](https://gocn.github.io/styleguide/docs/03-decisions/#字面格式化)

### 函数格式化

函数定义或方法声明的签名应该保持在一行，以避免[缩进的混乱](https://gocn.github.io/styleguide/docs/03-decisions/#缩进的混乱)。

函数参数列表可以成为Go源文件中最长的几行。然而，它们在缩进的变化之前，因此很难以不使后续行看起来像函数体的一部分的混乱方式来断行。

```
// Bad:
func (r *SomeType) SomeLongFunctionName(foo1, foo2, foo3 string,
    foo4, foo5, foo6 int) {
    foo7 := bar(foo1)
    // ...
}
```

参见[最佳实践](https://gocn.github.io/styleguide/docs/04-best-practices/#函数参数列表)，了解一些缩短函数调用的选择，否则这些函数会有很多参数。

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

类似地，函数和方法调用不应该仅仅由于行的长度而进行换行。

```
// Good:
good := foo.Call(long, list, of, parameters, all, on, one, line)
// Bad:
bad := foo.Call(long, list, of, parameters,
    with, arbitrary, line, breaks)
```

不要为特定的函数参数添加注释。相反，使用 [option struct](https://gocn.github.io/styleguide/docs/04-best-practices/#option-模式) 或在函数文档中添加更多细节。

```
// Good:
good := server.New(ctx, server.Options{Port: 42})
// Bad:
bad := server.New(
    ctx,
    42, // Port
)
```

如果调用参数确实长得令人很难受，那么就应该考虑重构：

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

当 API 无法更改或本地调用是不频繁的（无论调用是否太长），在有助于理解本次调用的前提下，那么是始终允许添加换行符的。

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

请注意，上面示例中的行没有在特定的列边界处换行，而是根据坐标三元组进行分组。

函数中的长字符串不应该因为行的长度而被破坏。对于包含此类字符串的函数，可以在字符串格式之后添加换行符，并且可以在下一行或后续行中提供参数。最好根据输入的语义分组来决定换行符应该放在哪里，而不是单纯基于行长。

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

### 条件和循环

`if` 语句不应换行； 多行 `if` 子句的形式会出现 [缩进混乱带来的困扰](https://gocn.github.io/styleguide/docs/03-decisions/#缩进的混乱)。

```
// Bad:
// The second if statement is aligned with the code within the if block, causing
// indentation confusion.
if db.CurrentStatusIs(db.InTransaction) &&
    db.ValuesEqual(db.TransactionKey(), row.Key()) {
    return db.Errorf(db.TransactionError, "query failed: row (%v): key does not match transaction key", row)
}
```

如果不需要短路(short-circuit)行为，可以直接提取布尔操作数：

```
// Good:
inTransaction := db.CurrentStatusIs(db.InTransaction)
keysMatch := db.ValuesEqual(db.TransactionKey(), row.Key())
if inTransaction && keysMatch {
    return db.Error(db.TransactionError, "query failed: row (%v): key does not match transaction key", row)
}
```

尤其注意，在条件已经重复的情况下，很可能还是有可以提取的局部变量：

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

包含闭包或多行结构文字的 `if` 语句应确保 [大括号匹配](https://gocn.github.io/styleguide/docs/03-decisions/#匹配的大括号) 以避免 [缩进混淆](https://gocn.github.io/styleguide/docs/03-decisions/#缩进的混乱)。

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

同样，不要尝试在 `for` 语句中人为的插入换行符。如果没有优雅的重构方式，是可以允许单纯的较长的行：

```
// Good:
for i, max := 0, collection.Size(); i < max && !collection.HasPendingWriters(); i++ {
    // ...
}
```

但是，通常可以优化为：

```
// Good:
for i, max := 0, collection.Size(); i < max; i++ {
    if collection.HasPendingWriters() {
        break
    }
    // ...
}
```

`switch` 和 `case` 语句都应始终保持在一行：

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

如果行太长，将所有大小写缩进并用空行分隔以避免[缩进混淆](https://gocn.github.io/styleguide/docs/03-decisions/#缩进的混乱)：

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

在将变量比较的条件中，变量值放在等号运算符的左侧：

```
// Good:
if result == "foo" {
  // ...
}
```

不要采用常量在前的表达含糊的写法([尤达条件式](https://en.wikipedia.org/wiki/Yoda_conditions))

```
// Bad:
if "foo" == result {
  // ...
}
```

### 复制

为了避免意外的别名和类似的错误，从另一个包复制结构时要小心。例如 `sync.Mutex` 是不能复制的同步对象。

`bytes.Buffer` 类型包含一个 `[]byte` 切片和切片可以引用的小数组，这是为了对小字符串的优化。如果你复制一个 `Buffer`，复制的切片会指向原始切片中的数组，从而在后续方法调用产生意外的效果。

一般来说，如果类型的方法与指针类型`*T`相关联，不要复制类型为`T`的值。


```
// Bad:
b1 := bytes.Buffer{}
b2 := b1
```

调用值接收者的方法可以隐藏拷贝。当你编写 API 时，如果你的结构包含不应复制的字段，你通常应该采用并返回指针类型。

如此是可接受的:

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

但下面这种通常是错误的:

```
// Bad:
type Record struct {
  buf bytes.Buffer
  // other fields omitted
}

func (r Record) Process(...) {...} // Makes a copy of r.buf

func Consumer(r Record) {...} // Makes a copy of r.buf
```

这一指南同样也适用于 `sync.Mutex` 复制的情况。

### 不要 panic

不要使用 `panic` 进行正常的错误处理。相反，使用 `error` 和多个返回值。请参阅 [关于错误的有效 Go 部分](http://golang.org/doc/effective_go.html#errors)。

在 `package main` 和初始化代码中，考虑 [`log.Exit`](https://pkg.go.dev/github.com/golang/glog#Exit) 中应该终止程序的错误（例如，无效配置 )，因为在许多这些情况下，堆栈跟踪对阅读者没有帮助。请注意 [`log.Exit`](https://pkg.go.dev/github.com/golang/glog#Exit) 中调用了 [`os.Exit`](https://pkg.go.dev/os#Exit) ，此时所有`defer`函数都将不会运行。

对于那些表示“不可能”出现的条件错误、命名错误，应该在代码评审、测试期间发现，函数应合理地返回错误或调用 [`log.Fatal`](https://pkg.go.dev/github.com/golang/glog#Fatal）。

**注意：** `log.Fatalf` 不是标准库日志。请参阅 [#logging]。

### Must类函数

用于在失败时停止程序的辅助函数应遵循命名约定“MustXYZ”（或“mustXYZ”）。一般来说，它们应该只在程序启动的早期被调用，而不是在像用户输入时，此时更应该首选 `error` 处理。

这类方式，通常只在[包初始化时]（https://golang.org/ref/spec#Package_initialization）进行包级变量初始化的函数常见（例如[template.Must](https://golang.org/pkg/text/template/#Must) 和 [regexp.MustCompile](https://golang.org/pkg/regexp/#MustCompile))。

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

相同的约定也可用于仅停止当前测试的情况（使用 `t.Fatal`）。这样在创建测试时通常很方便的，例如在 [表驱动测试](https://gocn.github.io/styleguide/docs/03-decisions/#表驱动测试) 的结构字段中，作为返回错误的函数是不能直接复制给结构字段的。

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

在这两种情况下，这种模式的价值在于可以在“值”上下文中调用。不应在难以确保捕获错误的地方或应[检查](https://gocn.github.io/styleguide/docs/03-decisions/#错误处理)错误的上下文中调用这些程序（如，在许多请求处理程序中）。对于常量输入，这允许测试确保“必须”的参数格式正确，对于非常量的输入，它允许测试验证错误是否[正确处理或传播](https://gocn.github.io/styleguide/docs/04-best-practices/#错误处理)。

在测试中使用 `Must` 函数的地方，通常应该 [标记为测试辅助函数](https://gocn.github.io/styleguide/docs/03-decisions/#测试辅助函数) 并调用 `t.Fatal`（请参阅[测试辅助函数中的错误处理](https://gocn.github.io/styleguide/docs/04-best-practices/#在测试辅助函数中处理错误)来了解使用它的更多注意事项）。

当有可能通过 [普通错误处理](https://gocn.github.io/styleguide/docs/04-best-practices/#错误处理) 处理时，就不应该使用`Must`类函数：

```
// Bad:
func Version(o *servicepb.Object) (*version.Version, error) {
    // Return error instead of using Must functions.
    v := version.MustParse(o.GetVersionString())
    return dealiasVersion(v)
}
```

### Goroutine 生命周期

当你生成 goroutines 时，要明确它们何时或是否退出。

Goroutines 可以在阻塞通道发送或接收出现泄漏。垃圾收集器不会终止一个 goroutine，即使它被阻塞的通道已经不可用。

即使 goroutine 没有泄漏，在不再需要时仍处于运行状态也会导致其他微妙且难以诊断的问题。向已关闭的通道上发送会导致panic。


```
// Bad:
ch := make(chan int)
ch <- 42
close(ch)
ch <- 13 // panic
```

“在结果已经不需要之后”修改仍在使用的入参可能会导致数据竞争。运行任意长时间的 goroutine 会导致不可预测的内存占用。

并发代码的编写应该让 goroutine 生命周期非常明显。通常，这意味着在与同步相关的代码限制的函数范围内，将逻辑分解为 [同步函数](https://gocn.github.io/styleguide/docs/03-decisions/#同步函数)。如果并发性仍然不明显，那么文档说明 goroutine 在何时、为何退出就很重要。

遵循上下文使用最佳实践的代码通常有助于明确这一点，其通常使用 `context.Context` 进行管理：

```
// Good:
func (w *Worker) Run(ctx context.Context) error {
    // ...
    for item := range w.q {
        // process 至少在ctx取消时会返回
        go process(ctx, item)
    }
    // ...
}
```

上面还有其他使用通道的情况，例如 `chan struct{}`、同步变量、[条件变量](https://drive.google.com/file/d/1nPdvhB0PutEJzdCq5ms6UI58dp50fcAN/view) 等等。重要的部分是 goroutine 的 结束对于后续维护者来说是显而易见的。

相比之下，以下代码不关心其衍生的 goroutine 何时完成：

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

这段代码看起来还行，但有几个潜在的问题：

- 该代码在生产中可能有未定义的行为，即使操作系统已经释放资源，程序也可能没有完全干净地结束
- 由于代码的不确定生命周期，代码难以进行有效的测试
- 代码可能会出现资源泄漏，如上所述

更多可阅读：

- [永远不要在不知道它将如何停止的情况下启动 goroutine](https://dave.cheney.net/2016/12/22/never-start-a-goroutine-without-knowing-how-it-will-stop)
- 重新思考经典并发模式：[幻灯片](https://drive.google.com/file/d/1nPdvhB0PutEJzdCq5ms6UI58dp50fcAN/view)，[视频](https://www.youtube.com/watch?v=5zXAHh5tJqQ)
- [Go 程序何时结束](https://changelog.com/gotime/165)

### 接口

Go 接口通常属于*使用*接口类型值的包，而不是*实现*接口类型的包。实现包应该返回具体的（通常是指针或结构）类型。这样就可以将新方法添加到实现中，而无需进行大量重构。有关详细信息，请参阅 [GoTip #49：接受接口、返回具体类型](https://gocn.github.io/styleguide/docs/01-overview/#gotip)。

不要从使用 API 导出接口的 [test double](https://abseil.io/resources/swe-book/html/ch13.html#techniques_for_using_test_doubles) 实现。相反，应设计可以使用 [实际实现](https://abseil.io/resources/swe-book/html/ch12.html#test_via_public_apis) 的[公共API]((https://abseil.io/resources/swe-book/html/ch12.html#test_via_public_apis))进行测试。有关详细信息，请参阅 [GoTip #42：为测试编写存根](https://gocn.github.io/styleguide/docs/01-overview/#gotip)。即使在使用实现不可行的情况下，也没有必要引入一个完全覆盖类型所有方法的接口；消费者可以创建一个只包含它需要的方法的接口，如 [GoTip #78: Minimal Viable Interfaces](https://gocn.github.io/styleguide/docs/01-overview/#gotip) 中所示。

要测试使用 Stubby RPC 客户端的包，请使用真实的客户端连接。如果无法在测试中运行真实服务器，Google 的内部做法是使用内部 rpctest 包（即将推出！）获得与本地 [test double] 的真实客户端连接。

在使用之前不要定义接口（参见 [TotT: Code Health: Eliminate YAGNI Smells](https://testing.googleblog.com/2017/08/code-health-eliminate-yagni-smells.html)）。如果没有实际的使用示例，就很难判断一个接口是否必要，更不用说它应该包含哪些方法了。

如果不需要传递不同的类型，则不要使用接口类型作为参数。

不要导出不需要开放的接口。

**TODO:** 写一个关于接口的更深入的文档并在这里链接到它。

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

### 泛型

在满足业务需求时，泛型（正式名称为“[类型参数](https://go.dev/design/43651-type-parameters)”）才应该被使用。在许多应用程序中，使用现有语言特性中传统方式（切片、映射、接口等）也可以正常工作，而不会增加复杂性，因此请注意不要过早使用。请参阅关于 [最小机制](https://gocn.github.io/styleguide/docs/02-guide/#最小化机制) 的讨论。

在引入使用泛型的导出 API 时，请确保对其进行适当的记录。强烈鼓励包含可运行的 [示例](https://gocn.github.io/styleguide/docs/03-decisions/#示例examples)。

不要仅仅因为你正在实现一个算法或不关心元素类型的数据结构而使用泛型。如果在实践中只有一种类型可以使用，那么首先让您的代码在该类型上工作，而不使用泛型。与其删除不必要的抽象，稍后为其添加多态将更简单。

不要使用泛型来发明领域特定语言 (DSL)。特别是，不要引入可能会给阅读者带来沉重负担的错误处理框架。相反，更应该使用 [错误处理](https://gocn.github.io/styleguide/docs/03-decisions/#错误) 做法。对于测试，要特别小心引入 [断言库](https://gocn.github.io/styleguide/docs/03-decisions/#断言库) 或框架，尤其是很少发现[失败case](https://gocn.github.io/styleguide/docs/03-decisions/#有用的测试失败)的。

一般来说：

- [写代码，不要去设计类型](https://www.youtube.com/watch?v=Pa_e9EeCdy8&t=1250s)。来自 Robert Griesemer 和 Ian Lance Taylor 的 GopherCon 演讲。
- 如果你有几种类型共享一个统一接口，请考虑使用该接口对解决方案进行建模。这种情况可能不需要泛型。
- 否则，不要依赖 `any` 类型和过多的 [类型断言](https://tour.golang.org/methods/16)的情况，应考虑泛型。

更多也可以参考：

- [在 Go 中使用泛型](https://www.youtube.com/watch?v=nr8EpUO9jhw)，Ian Lance Taylor 的演讲
- Go 网页上的[泛型教程](https://go.dev/doc/tutorial/generics)

### 参数值传递

不要为了节省几个字节而将指针作为函数参数传递。如果一个函数在整个过程中只将参数`x`处理为`*x`，那么不应该采用指针。常见的例子包括传递一个指向字符串的指针（`*string`）或一个指向接口值的指针（`*io.Reader`）。在这两种情况下，值本身都是固定大小的，可以直接传递。

此建议不适用于大型结构体，甚至可能会增加大小的小型结构。特别是，`pb`消息通常应该通过指针而不是值来处理。指针类型满足 `proto.Message` 接口（被 `proto.Marshal`、`protocmp.Transform` 等接受），并且协议缓冲区消息可能非常大，并且随着时间的推移通常会变得更大。

### 接收者类型

[方法接收者](https://golang.org/ref/spec#Method_declarations) 和常规函数参数一样，也可以使用值或指针传递。选择哪个应该基于该方法应该属于哪个[方法集]（https://golang.org/ref/spec#Method_sets）。

**正确性胜过速度或简单性。** 在某些情况下是必须使用指针的。在其他情况下，如果你对代码的增长方式没有很好的了解，请为大类型或考虑未来适用性上选择指针，并为[简单的的数据]((https://en.wikipedia.org/wiki/Passive_data_structure))使用值。

下面的列表更详细地说明了每个案例：

- 如果接收者是一个切片并且该方法没有重新切片或重新分配切片，应使用值而不是指针。

  ```
  // Good:
  type Buffer []byte

  func (b Buffer) Len() int { return len(b) }
  ```

- 如果方法需要修改接收者，应使用指针。

  ```
  // Good:
  type Counter int

  func (c *Counter) Inc() { *c++ }

  // See https://pkg.go.dev/container/heap.
  type Queue []Item

  func (q *Queue) Push(x Item) { *q = append([]Item{x}, *q...) }
  ```

- 如果接收者包含 [不能被安全复制的](https://gocn.github.io/styleguide/docs/03-decisions/#复制) 字段, 应使用指针接收者。常见的例子是 [`sync.Mutex`](https://pkg.go.dev/sync#Mutex) 和其他同步类型。

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

  **提示：** 检查类型是否可被安全复制的相关信息可参考 [Godoc](https://pkg.go.dev/time#example-Duration)。

- 如果接收者是“大”结构或数组，则指针接收者可能更有效。传递结构相当于将其所有字段或元素作为参数传递给方法。如果这看起来太大而无法[按值传递](https://gocn.github.io/styleguide/docs/03-decisions/#参数值传递)，那么指针是一个不错的选择。

- 对于将调用修改接收者的其他函数，而这些修改对此方法不可见，请使用值类型； 否则使用指针。

- 如果接收者是一个结构或数组，其元素中的任何一个都是指向可能发生变化的东西的指针，那么更应该指针接收者以使阅读者清楚地了解可变性的意图。

  ```
  // Good:
  type Counter struct {
      m *Metric
  }

  func (c *Counter) Inc() {
      c.m.Add(1)
  }
  ```

- 如果接收者是[内置类型](https://pkg.go.dev/builtin)，例如整数或字符串，不需要修改，使用值。

  ```
  // Good:
  type User string

  func (u User) String() { return string(u) }
  ```

- 接收者是`map`, `function` 或 `channel`，使用值类型，而不是指针。

  ```
  // Good:
  // See https://pkg.go.dev/net/http#Header.
  type Header map[string][]string

  func (h Header) Add(key, value string) { /* omitted */ }
  ```

- 如果接收器是一个“小”数组或结构，它自然是一个没有可变字段和指针，那么值接收者通常是正确的选择。

  ```
  // Good:
  // See https://pkg.go.dev/time#Time.
  type Time struct { /* omitted */ }

  func (t Time) Add(d Duration) Time { /* omitted */ }
  ```

- 如有疑问，请使用指针接收者。

作为一般准则，最好将类型的方法设为全部指针方法或全部值方法。

**注意：** 关于是否值或指针的函数是否会影响性能，存在很多错误信息。编译器可以选择将指针传递到堆栈上的值以及复制堆栈上的值，但在大多数情况下，这些考虑不应超过代码的可读性和正确性。当性能确实很重要时，重要的是在确定一种方法优于另一种方法之前，用一个实际的基准来描述这两种方法。

### `switch` 和 `break`

不要在`switch`子句末尾使用没有目标标签的`break`语句； 它们是多余的。与 C 和 Java 不同，Go 中的 `switch` 子句会自动中断，并且需要 `fallthrough` 语句来实现 C 风格的行为。如果你想阐明空子句的目的，请使用注释而不是 `break`。

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

### 同步函数

同步函数直接返回它们的结果，并在返回之前完成所有回调或通道操作。首选同步函数而不是异步函数。

同步函数使 goroutine 在调用中保持本地化。这有助于推理它们的生命周期，并避免泄漏和数据竞争。同步函数也更容易测试，因为调用者可以传递输入并检查输出，而无需轮询或同步。

如有必要，调用者可以通过在单独的 goroutine 中调用函数来添加并发性。然而，在调用方移除不必要的并发是相当困难的（有时是不可能的）。

也可以看看：

- “重新思考经典并发模式”，Bryan Mills 的演讲：[幻灯片](https://drive.google.com/file/d/1nPdvhB0PutEJzdCq5ms6UI58dp50fcAN/view)，[视频](https://www.youtube.com/ 看？v=5zXAHh5tJqQ)

### 类型别名

使用*类型定义*，`type T1 T2`，定义一个新类型。
使用 [*类型别名*](http://golang.org/ref/spec#Type_declarations), `type T1 = T2` 来引用现有类型而不定义新类型。
类型别名很少见； 它们的主要用途是帮助将包迁移到新的源代码位置。不要在不需要时使用类型别名。

### 使用 %q

Go 的格式函数（`fmt.Printf` 等）有一个 `%q` 动词，它在双引号内打印字符串。

```
// Good:
fmt.Printf("value %q looks like English text", someText)
```

更应该使用 `%q` 而不是使用 `%s` 手动执行等效操作：

```
// Bad:
fmt.Printf("value \"%s\" looks like English text", someText)
// Avoid manually wrapping strings with single-quotes too:
fmt.Printf("value '%s' looks like English text", someText)
```

建议在供人使用的输出中使用 `%q`，其输入值可能为空或包含控制字符。可能很难注意到一个无声的空字符串，但是 `""` 就这样清楚地突出了。
### 使用 any

Go 1.18 将 `any` 类型作为 [别名](https://go.googlesource.com/proposal/+/master/design/18130-type-alias.md) 引入到 `interface{}`。因为它是一个别名，所以 `any` 在许多情况下等同于 `interface{}`，而在其他情况下，它可以通过显式转换轻松互换。在新代码中应使用 `any`。
## 通用库

### Flags

Google 代码库中的 Go 程序使用 [标准 `flag` 包](https://golang.org/pkg/flag/) 的内部变体。它具有类似的接口，但与 Google 内部系统的互操作性很好。Go 二进制文件中的标志名称应该更应该使用下划线来分隔单词，尽管包含标志值的变量应该遵循标准的 Go 名称样式（[混合大写字母](https://gocn.github.io/styleguide/docs/02-guide#大小写混合)）。具体来说，标志名称应该是蛇形命名，变量名称应该是驼峰命名。

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

Flags只能在 `package main` 或等效项中定义。

通用包应该使用 Go API 进行配置，而不是通过命令行界面进行配置；不要让导入库导出新标志作为副作用。也就是说，更倾向于显式的函数参数或结构字段分配，或者低频和严格审查的全局变量导出。在需要打破此规则的极少数情况下，标志名称必须清楚地表明它配置的包。

如果你的标志是全局变量，在导入部分之后，将它们放在 `var` 组中。

关于使用子命令创建 [complex CLI](https://gocn.github.io/styleguide/docs/04-best-practices/#复杂的命令行界面) (https://gocn.github.io/styleguide/docs/04-best-practices/#自定义日志级别) (https://gocn.github.io/styleguide/docs/04-best-practices/#自定义日志级别) 的最佳实践还有其他讨论。

也可以看看：

- [本周提示 #45：避免标记，尤其是在库代码中](https://abseil.io/tips/45)
- [Go Tip #10：配置结构和标志](https://gocn.github.io/styleguide/docs/01-overview/#gotip)
- [Go Tip #80：依赖注入原则](https://gocn.github.io/styleguide/docs/01-overview/#gotip)

### 日志

Google 代码库中的 Go 程序使用 [标准 `log` 包](https://pkg.go.dev/log) 的变体。它具有类似但功能更强大的interface，并且可以与 Google 内部系统进行良好的互操作。该库的开源版本可通过 [package `glog`](https://pkg.go.dev/github.com/golang/glog) 获得，开源 Google 项目可能会使用它，但本指南指的是它始终作为“日志”。

**注意：** 对于异常的程序退出，这个库使用 `log.Fatal` 通过堆栈跟踪中止，使用 `log.Exit` 在没有堆栈跟踪的情况下停止。标准库中没有 `log.Panic` 函数。

**提示：** `log.Info(v)` 等价于 `log.Infof("%v", v)`，其他日志级别也是如此。当你没有格式化要做时，首选非格式化版本。

也可以看看：

- [记录错误](https://gocn.github.io/styleguide/docs/04-best-practices/#错误日志) 和 [自定义详细日志级别](https://gocn.github.io/styleguide/docs/04-best-practices/#自定义日志级别)
- 何时以及如何使用日志包[停止程序](https://gocn.github.io/styleguide/docs/04-best-practices/#程序检查和-panic)

### 上下文

[`context.Context`](https://pkg.go.dev/context) 类型的值携带跨 API 和进程边界的安全凭证、跟踪信息、截止日期和取消信号。与 Google 代码库中使用线程本地存储的 C++ 和 Java 不同，Go 程序在整个函数调用链中显式地传递上下文，从传入的 RPC 和 HTTP 请求到传出请求。

当传递给函数或方法时，`context.Context` 始终是第一个参数。

```
func F(ctx context.Context /* other arguments */) {}
```

例外情况是：

- 在 HTTP 处理程序中，上下文来自 [`req.Context()`](https://pkg.go.dev/net/http#Request.Context)。

- 在流式 RPC 方法中，上下文来自流。

  使用 gRPC 流的代码从生成的服务器类型中的 `Context()` 方法访问上下文，该方法实现了 `grpc.ServerStream`。请参阅 https://grpc.io/docs/languages/go/generated-code/。

- 在入口函数（此类函数的示例见下文）中，使用 [`context.Background()`](https://pkg.go.dev/context/#Background)。

  - 在二进制目标中：`main`
  - 在通用代码和库中：`init`
  - 在测试中：`TestXXX`、`BenchmarkXXX`、`FuzzXXX`

> **注意**：调用链中间的代码很少需要使用 `context.Background()` 创建自己的基本上下文。更应该从调用者那里获取上下文，除非它是错误的上下文。
>
> 你可能会遇到服务库（在 Google 的 Go 服务框架中实现 Stubby、gRPC 或 HTTP），它们为每个请求构建一个新的上下文对象。这些上下文立即填充来自传入请求的信息，因此当传递给请求处理程序时，上下文的附加值已从客户端调用者通过网络边界传播给它。此外，这些上下文的生命周期仅限于请求的生命周期：当请求完成时，上下文将被取消。
>
> 除非你正在实现一个服务器框架，否则你不应该在库代码中使用 `context.Background()` 创建上下文。相反，如果有可用的现有上下文，则更应该使用下面提到的上下文分离。如果你认为在入口点函数之外确实需要`context.Background()`，请在提交实现之前与 Google Go 风格的邮件列表讨论它。

`context.Context` 在函数中首先出现的约定也适用于测试辅助函数。

```
// Good:
func readTestFile(ctx context.Context, t *testing.T, path string) string {}
```

不要将上下文成员添加到结构类型。相反，为需要传递它的类型的每个方法添加一个上下文参数。一个例外是其签名必须与标准库或 Google 无法控制的第三方库中的接口匹配的方法。这种情况非常罕见，应该在实施和可读性审查之前与 Google Go 风格的邮件列表讨论。

Google 代码库中必须产生可以在取消父上下文后运行的后台操作的代码可以使用内部包进行分离。关注 https://github.com/golang/go/issues/40221 讨论开源替代方案。

由于上下文是不可变的，因此可以将相同的上下文传递给共享相同截止日期、取消信号、凭据、父跟踪等的多个调用。

更多参见：

- [上下文和结构](https://go.dev/blog/context-and-structs)

#### 自定义上下文

不要在函数签名中创建自定义上下文类型或使用上下文以外的接口。这条规定没有例外。

想象一下，如果每个团队都有一个自定义上下文。对于包 P 和 Q 的所有对，从包 P 到包 Q 的每个函数调用都必须确定如何将“PContext”转换为“QContext”。这对开发者来说是不切实际且容易出错的，并且它会进行自动重构 添加上下文参数几乎是不可能的。

如果你要传递应用程序数据，请将其放入参数、接收器、全局变量中，或者如果它确实属于那里，则放入 Context 值中。创建自己的 Context 类型是不可接受的，因为它破坏了 Go 团队使 Go 程序在生产中正常工作的能力。

### crypto/rand

不要使用包 `math/rand` 来生成密钥，即使是一次性的。如果未生成随机种子，则生成器是完全可预测的。用`time.Nanoseconds()`生成种子，也只有几位熵。相反，请使用 `crypto/rand` ，如果需要文本，请打印为十六进制或 base64。

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

## 有用的测试失败

应该可以在不读取测试代码的情况下诊断测试失败。测试失败应当显示详细有用的消息说明：

- 是什么导致了失败
- 哪些输入导致错误
- 实际结果
- 预期的结果

下面概述了实现这一目标的具体约定。

### 断言库

不要创建“断言库”作为测试辅助函数。

断言库是试图在测试中结合验证和生成失败消息的库（尽管同样的陷阱也可能适用于其他测试辅助函数）。有关测试辅助函数和断言库之间区别的更多信息，请参阅 [最佳实践](https://gocn.github.io/styleguide/docs/04-best-practices/#把测试留给-test-函数)。

```
// Bad:
var obj BlogPost

assert.IsNotNil(t, "obj", obj)
assert.StringEq(t, "obj.Type", obj.Type, "blogPost")
assert.IntEq(t, "obj.Comments", obj.Comments, 2)
assert.StringNotEq(t, "obj.Body", obj.Body, "")
```

断言库倾向于提前停止测试（如果 `assert` 调用 `t.Fatalf` 或 `panic`）或省略有关测试正确的相关信息：

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

复杂的断言函数通常不提供 [有用的失败消息](https://gocn.github.io/styleguide/docs/03-decisions/#有用的测试失败) 和存在于测试函数中的上下文。太多的断言函数和库会导致开发人员体验支离破碎：我应该使用哪个断言库，它应该发出什么样的输出格式等问题？ 碎片化会产生不必要的混乱，特别是对于负责修复潜在下游破坏的库维护者和大规模更改的作者。与其创建用于测试的特定领域语言，不如使用 Go 本身。

断言库通常会排除比较和相等检查。更应该使用标准库，例如 [`cmp`](https://pkg.go.dev/github.com/google/go-cmp/cmp) 和 [`fmt`](https://golang.org/pkg/fmt/) 修改为：

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

对于更多特定于域的比较助手，更应该返回一个可以在测试失败消息中使用的值或错误，而不是传递 `*testing.T` 并调用其错误报告方法：

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

**最佳实践：** 如果 `postLength` 很重要，直接测试它是有意义的，独立于调用它的其他函数测试。

也可以看看：

- [等值比较和差异](https://gocn.github.io/styleguide/docs/03-decisions/#等值比较和差异)
- [打印差异](https://gocn.github.io/styleguide/docs/03-decisions/#打印差异)
- 有关测试辅助函数和断言助手之间区别的更多信息，请参阅[最佳实践](https://gocn.github.io/styleguide/docs/04-best-practices/#把测试留给-test-函数)

### 标识出函数

在大多数测试中，失败消息应该包括失败的函数的名称，即使从测试函数的名称中看起来很明显。具体来说，你的失败信息应该是 `YourFunc(%v) = %v, want %v` 而不仅仅是 `got %v, want %v`。

### 标识出输入

在大多数测试中，失败消息应该包括功能输入（如果它们很短）。如果输入的相关属性不明显（例如，因为输入很大或不透明），你应该使用对正在测试的内容的描述来命名测试用例，并将描述作为错误消息的一部分打印出来。

### Got before want

测试输出应包括函数在打印预期值之前返回的实际值。打印测试输出的标准格式是 `YourFunc(%v) = %v, want %v`。在你会写“实际”和“预期”的地方，更应该分别使用“get”和“want”这两个词。

对于差异，方向性不太明显，因此包含一个有助于解释失败的关键是很重要的。请参阅 [关于打印差异的部分](https://gocn.github.io/styleguide/docs/03-decisions/#打印差异)。无论你在失败消息中使用哪种 diff 顺序，都应将其明确指示为失败消息的一部分，因为现有代码的顺序不一致。

### 全结构比较

如果你的函数返回一个结构体（或任何具有多个字段的数据类型，例如切片、数组和映射），请避免编写执行手动编码的结构体逐个字段比较的测试代码。相反，构建期望函数返回的数据，并使用 [深度比较](https://gocn.github.io/styleguide/docs/03-decisions/#等值比较和差异) 直接进行比较。

**注意：** 如果你的数据包含模糊测试意图的不相关字段，则这不适用。

如果你的结构比较时需要近似相等（或等效类型的语义），或者它包含无法比较相等的字段（例如，如果其中一个字段是 `io.Reader`），请调整 [`cmp. Diff`](https://pkg.go.dev/github.com/google/go-cmp/cmp/cmp#Diff) 或 [`cmp.Equal`](https://pkg.go.dev/github.com/google/go-cmp/cmp/cmp#Equal) 与 [`cmpopts`](https://pkg.go.dev/github.com/google/go-cmp/cmp/cmpopts) 选项比较，例如[`cmpopts.IgnoreInterfaces`](https://pkg.go.dev/github.com/google/go-cmp/cmp/cmpopts#IgnoreInterfaces) 可能满足你的需求（[示例](https://play.golang.org/p/vrCUNVfxsvF))。

如果你的函数返回多个返回值，则无需在比较它们之前将它们包装在结构中。只需单独比较返回值并打印它们。

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

### 比较稳定的结果

避免比较那些可能依赖于非自有包输出稳定性的结果。相反，测试应该在语义相关的信息上进行比较，这些信息是稳定的，并能抵抗依赖关系的变化。对于返回格式化字符串或序列化字节的功能，一般来说，假设输出是稳定的是不安全的。

例如，[`json.Marshal`](https://golang.org/pkg/encoding/json/#Marshal)可以改变（并且在过去已经改变）它所输出的特定字节。如果`json`包改变了它序列化字节的方式，在JSON字符串上执行字符串相等的测试可能会中断。相反，一个更强大的测试将解析JSON字符串的内容，并确保它在语义上等同于一些预期的数据结构。

### 测试继续进行

测试应该尽可能地持续下去，即使是在失败之后，以便在一次运行中打印出所有的失败检查。这样一来，正在修复失败测试的开发人员就不必在修复每个错误后重新运行测试来寻找下一个错误。

更倾向于调用`t.Error`而不是`t.Fatal`来报告不匹配。当比较一个函数输出的几个不同属性时，对每一个比较都使用`t.Error`。

调用`t.Fatal`主要用于报告一个意外的错误情况，当后续的比较失败是没有意义的。

对于表驱动的测试，考虑使用子测试，使用`t.Fatal`而不是`t.Error`和`continue`。参见[GoTip #25: Subtests: Making Your Tests Lean]（https://gocn.github.io/styleguide/docs/01-overview/#gotip）。

**最佳实践：**关于何时应使用`t.Fatal`的更多讨论，见[最佳实践](https://gocn.github.io/styleguide/docs/04-best-practices/#terror-vs-tfatal)。

### 等值比较和差异

`==`操作符使用[语言定义的比较](http://golang.org/ref/spec#Comparison_operators)来评估相等性。标量值(数字、布尔运算等)根据其值进行比较, 但只有一些结构和接口可以用这种方式进行比较。指针的比较是基于它们是否指向同一个变量，而不是基于它们所指向的值是否相等。

对于类似切片这种情况下，`==`是不能正确处理比较的，[`cmp`](https://pkg.go.dev/github.com/google/go-cmp/cmp)包则可以用于比较更复杂的数据结构。使用[`cmp.Equal`](https://pkg.go.dev/github.com/google/go-cmp/cmp/cmp#Equal)进行等价比较，使用[`cmp.Diff`](https://pkg.go.dev/github.com/google/go-cmp/cmp/cmp#Diff)获得对象之间可供人类阅读的差异。

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

作为一个通用的比较库，`cmp`可能不知道如何比较某些类型。例如，它只能在传递[`protocmp.Transform`](https://pkg.go.dev/google.golang.org/protobuf/testing/protocmp#Transform)选项时比较`protobuf`的信息。

```
// Good:
if diff := cmp.Diff(want, got, protocmp.Transform()); diff != "" {
    t.Errorf("Foo() returned unexpected difference in protobuf messages (-want +got):\n%s", diff)
}
```

虽然`cmp`包不是Go标准库的一部分，但它是由Go团队维护的，随着时间的推移应该会产生稳定的相等结果。它是用户可配置的，应该可以满足大多数的比较需求。

现有的代码可能会使用以下旧的库，为了保持一致性，可以继续使用它们。

- [`pretty`](https://pkg.go.dev/github.com/kylelemons/godebug/pretty)产生美观的差异报告。然而，它非常谨慎地认为具有相同视觉表现的数值是相等的。特别注意，`pretty`不区分nil切片和空切片之间的差异，对具有相同字段的不同接口实现也不敏感，而且有可能使用嵌套图作为与结构值比较的基础。在产生差异之前，它还会将整个值序列化为一个字符串，因此对于比较大的值来说不是一个好的选择。默认情况下，它比较的是未导出的字段，这使得它对你的依赖关系中实现细节的变化很敏感。由于这个原因，在protobuf信息上使用`pretty`是不合适的。

在新的代码中更倾向于使用`cmp`，值得考虑更新旧的代码，在实际可行的情况下使用`cmp`。

旧的代码可以使用标准库中的`reflect.DeepEqual`函数来比较复杂的结构。`reflect.DeepEqual`不应该被用来检查等值比较，因为它对未导出的字段和其他实现细节的变化很敏感。使用`reflect.DeepEqual`的代码应该更新为上述库之一。

**注意：** `cmp`包是为测试而设计的，而不是用于生产。因此，当它怀疑一个比较被错误地执行时，它可能会 panic ，以向用户提供如何改进测试的指导，使其不那么脆弱。鉴于cmp的恐慌倾向，它不适合在生产中使用的代码，因为虚假的panic可能是致命的。

### 详细程度

传统的失败信息，适用于大多数Go测试，是`YourFunc(%v) = %v, want %v`。然而，有些情况可能需要更多或更少的细节。

- 进行复杂交互的测试也应该描述交互。例如，如果同一个`YourFunc`被调用了好几次，那么要确定哪个调用未通过测试。如果知道系统的任何额外状态是很重要的，那么在失败输出中应包括这些（或者至少在日志中）。
- 如果数据是一个复杂的结构，在消息中只描述重要的部分是可以接受的，但不要过分掩盖数据。
- 设置失败不需要同样水平的细节。如果一个测试辅助函数填充了一个Spanner表，但Spanner却坏了，你可能不需要包括你要存储在数据库中的测试输入。`t.Fatalf("Setup: Failed to set up test database: %s", err)`通常足以解决这个问题。

**提示：**应该在开发过程中触发失败。审查失败信息是什么样子的，维护者是否能有效地处理失败。

有一些技术可以清晰地再现测试输入和输出：

- 当打印字符串数据时，[`%q`通常是有用的](https://gocn.github.io/styleguide/docs/03-decisions/#使用-q)以强调该值的重要性，并更容易发现坏值。
- 当打印（小）结构时，`%+v`可能比`%v`更有用。
- 当验证较大的值失败时，[打印差异](https://gocn.github.io/styleguide/docs/03-decisions/#打印差异)可以使人们更容易理解失败的原因。

### 打印差异

如果你的函数返回较大的输出，那么当你的测试失败时，阅读失败信息的人很难发现其中的差异。与其同时打印返回值和想要的值，不如做一个差异。

为了计算这些值的差异，`cmp.Diff`是首选，特别是对于新的测试和新的代码，但也可以使用其他工具。关于每个函数的优点和缺点的指导，见[类型的等值](https://gocn.github.io/styleguide/docs/03-decisions/#等值比较和差异)。

- [`cmp.Diff`](https://pkg.go.dev/github.com/google/go-cmp/cmp/cmp#Diff)
- [`pretty.Compare`](https://pkg.go.dev/github.com/kylelemons/godebug/pretty#Compare)

你可以使用[`diff`](https://pkg.go.dev/github.com/kylelemons/godebug/diff)包来比较多行字符串或字符串的列表。你可以把它作为其他类型的比较的构建块。

在失败信息中添加一些文字，解释差异的方向。

- 当你使用`cmp`，`pretty`和`diff`包时，类似`diff (-want +got)`的东西很好（如果把`(want, got)`传递给函数），因为你添加到格式字符串中的`-`和`+`将与实际出现在diff行开头的`-`和`+`匹配。如果把`(got, want)`传给函数，正确的键将是`(-got +want)`。
- `messagediff`包使用不同的输出格式，所以当你使用它时，`diff (want -> got)`的信息是合适的（如果把`(want, got)`传给函数），因为箭头的方向将与 "修改 "行中箭头的方向一致。

差异将跨越多行，所以应该在打印差异之前打印一个新行。

### 测试错误语义

当一个单元测试执行字符串比较或使用 `cmp` 来检查特定的输入是否返回特定种类的错误时，你可能会发现，如果这些错误信息在将来被重新修改，你的测试就会很脆弱。因为这有可能将你的单元测试变成一个变化检测器（参见[TotT: Change-Detector Tests Considered Harmful](https://testing.googleblog.com/2015/01/testing-on-toilet-change-detector-tests.html)），不要使用字符串比较来检查你的函数返回什么类型的错误。然而，允许使用字符串比较来检查来自被测包的错误信息是否满足某些属性，例如，它是否包括参数名称。

Go中的错误值通常有一个用于人眼的部分和一个用于语义控制流的部分。测试应该力求只测试可以可靠观察到的语义信息，而不是显示用于人类调试的信息，因为这往往会在未来发生变化。关于构建具有语义的错误的指导，请参见[关于错误的最佳实践](https://gocn.github.io/styleguide/docs/04-best-practices/#错误处理)。如果语义信息不充分的错误来自于你无法控制的依赖关系，请考虑针对所有者提交一个错误，以帮助改进API，而不是依靠解析错误信息。

在单元测试中，通常只关心错误是否发生。如果是这样，那么在你预期发生错误时，只测试错误是否为非零就足够了。如果你想测试错误在语义上与其他错误相匹配，那么可以考虑使用`cmp`与[`cmpopts.EquateErrors`](https://pkg.go.dev/github.com/google/go-cmp/cmp/cmpopts#EquateErrors)。

> **注意：**如果一个测试使用了[`cmpopts.EquateErrors`](https://pkg.go.dev/github.com/google/go-cmp/cmp/cmpopts#EquateErrors)，但是它所有的`wantErr`值都是`nil`或者`cmpopts.AnyError`，那么使用`cmp`是[不必要的](https://gocn.github.io/styleguide/docs/02-guide/#最小化机制)。简化代码，使want字段改为`bool`类型，然后就可以用`！=`进行简单的比较。
>
> ```
> // Good:
> gotErr := f(test.input) != nil
> if gotErr != test.wantErr {
>     t.Errorf("f(%q) returned err = %v, want error presence = %v", test.input, gotErr, test.wantErr)
> }
> ```

另请参阅 [GoTip #13：设计用于检查的错误](https://gocn.github.io/styleguide/docs/01-overview/#gotip)。

## 测试结构

### 子测试

标准的 Go 测试库提供了一种工具来 [定义子测试](https://pkg.go.dev/testing#hdr-Subtests_and_Sub_benchmarks)。这允许在设置和清理、控制并行性和测试过滤方面具有灵活性。子测试可能很有用（特别是对于表驱动测试），但使用它们不是强制性的。另请参阅 https://blog.golang.org/subtests。

子测试不应该依赖于其他case的执行来获得成功或初始状态，因为子测试应该能够使用 `go test -run` 标志或使用 Bazel [测试过滤器](https://bazel.build/docs/user-manual#test-filter) 表达式。

#### 子测试名称

命名子测试，使其在测试输出中可读，并且在命令行上对测试过滤的用户有用。当你使用“t.Run”创建子测试时，第一个参数用作测试的描述性名称。为了确保测试结果对于阅读日志的人来说是清晰的，请选择在转义后仍然有用且可读的子测试名称。将子测试名称视为函数标识符而不是散文描述。测试运行程序用下划线替换空格，并转义非打印字符。如果你的测试数据受益于更长的描述，请考虑将描述放在单独的字段中（可能使用“t.Log”或与失败消息一起打印）。

可以使用 [Go 测试运行器](https://golang.org/cmd/go/#hdr-Testing_flags) 或 Bazel [测试过滤器](https://bazel.build/docs/user-manual#test-filter) 的标志单独运行子测试，选择易于输入的描述性名称。

> **警告：**斜杠字符在子测试名称中特别不友好，因为它们具有[测试过滤器的特殊含义](https://blog.golang.org/subtests#:~:text=Perhaps 一位匹配任何测试）。
>
> > ```
> > # Bad:
> > # Assuming TestTime and t.Run("America/New_York", ...)
> > bazel test :mytest --test_filter="Time/New_York"    # Runs nothing!
> > bazel test :mytest --test_filter="Time//New_York"   # Correct, but awkward.
> > ```

要[识别函数的输入](https://gocn.github.io/styleguide/docs/03-decisions/#标识出输入)，将它们包含在测试的失败消息中，它们不会被测试执行者所忽略。

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

以下是一些要避免的事情的例子：

```
// Bad:
// Too wordy.
t.Run("check that there is no mention of scratched records or hovercrafts", ...)
// Slashes cause problems on the command line.
t.Run("AM/PM confusion", ...)
```

### 表驱动测试

当许多不同的测试用例可以使用相似的测试逻辑进行测试时，使用表驱动测试。

- 测试函数的实际输出是否等于预期输出时。例如，许多 [`fmt.Sprintf` 的测试](https://cs.opensource.google/go/go/+/master:src/fmt/fmt_test.go) 或下面的最小片段。
- 测试函数的输出是否始终符合同一组不变量时。例如，[测试 `net.Dial`](https://cs.opensource.google/go/go/+/master:src/net/dial_test.go;l=318;drc=5b606a9d2b7649532fe25794fa6b99bd24e7697c)。

这是从标准“字符串”库复制的表驱动测试的最小结构。如果需要，你可以使用不同的名称，将测试切片移动到测试函数中，或者添加额外的工具，例如子测试或设置和清理函数。始终牢记[有用的测试失败](https://gocn.github.io/styleguide/docs/03-decisions/#有用的测试失败)。

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

**注意**：上面这个例子中的失败消息满足了[识别函数](https://gocn.github.io/styleguide/docs/03-decisions/#标识出函数)和[识别输入](https://gocn.github.io/styleguide/docs/03-decisions/#标识出输入)。无需[用数字标识行](https://gocn.github.io/styleguide/docs/03-decisions/#标识行)。

当需要使用与其他测试用例不同的逻辑来检查某些测试用例时，编写多个测试函数更为合适，如 [GoTip #50: Disjoint Table Tests](https://gocn.github.io/styleguide/docs/01-overview/#gotip)。当表中的每个条目都有自己不同的条件逻辑来检查每个输出的输入时，测试代码的逻辑可能会变得难以理解。如果测试用例具有不同的逻辑但设置相同，则单个测试函数中的一系列[子测试](https://gocn.github.io/styleguide/docs/03-decisions/#子测试) 可能有意义。

你可以将表驱动测试与多个测试函数结合起来。例如，当测试函数的输出与预期输出完全匹配并且函数为无效输入返回非零错误时，编写两个单独的表驱动测试函数是最好的方法：一个用于正常的非错误输出，一个用于错误输出。

#### 数据驱动的测试用例

表测试行有时会变得复杂，行值指示测试用例内的条件行为。测试用例之间重复的额外清晰度对于可读性是必要的。
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

在下面的反例中，请注意在case设置中区分每个测试案例使用哪种类型的 `Codex` 是多么困难。（突出显示的部分与 [TotT：数据驱动陷阱！](https://testing.googleblog.com/2008/09/tott-data-driven-traps.html) 的建议相冲突。）

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

#### 标识行

不要使用测试表中的测试索引来代替命名测试或打印输入。没有人愿意通过你的测试表并计算条目以找出哪个测试用例失败。

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

在你的测试结构中添加测试描述，并将其与失败信息一起打印。当使用子测试时，你的子测试名称应能有效识别行。

**重要的是：**即使`t.Run`对输出和执行有一定的范围，你必须始终[识别输入](https://gocn.github.io/styleguide/docs/03-decisions/#标识出输入)。表的测试行名称必须遵循[子测试命名](https://gocn.github.io/styleguide/docs/03-decisions/#子测试名称)的指导。

### 测试辅助函数

一个测试辅助函数是一个执行设置或清理任务的函数。所有发生在测试辅助函数中的故障都被认为是环境的故障（而不是被测代码的故障）--例如，当一个测试数据库不能被启动，因为在这台机器上没有更多的空闲端口。

如果你传递一个`*testing.T`，调用[`t.Helper`](https://pkg.go.dev/testing#T.Helper)，将测试辅助函数中的故障归结到调用助手的那一行。这个参数应该在[context](https://gocn.github.io/styleguide/docs/03-decisions/#上下文)参数之后，如果有的话，在任何其他参数之前。

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

当这种模式掩盖了测试失败和导致失败的条件之间的联系时，请不要使用这种模式。具体来说，关于[断言库](https://gocn.github.io/styleguide/docs/03-decisions/#断言库)的指导仍然适用，[`t.Helper`](https://pkg.go.dev/testing#T.Helper)不应该被用来实现这种库。

**提示：**更多关于测试辅助函数和断言助手的区别，请参见[最佳实践](https://gocn.github.io/styleguide/docs/04-best-practices/#把测试留给-test-函数)。

虽然上面提到的是`*testing.T`，但大部分建议对基准和模糊帮助器来说都是一样的。

### 测试包

#### 同一包内的测试

测试可以和被测试的代码定义在同一个包里。

要在同一个包中编写测试

- 将测试放在一个`foo_test.go`文件中
- 在测试文件中使用`package foo`。
- 不要明确地导入要测试的包

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

同一个包中的测试可以访问包中未导出的标识符。这可以实现更好的测试覆盖率和更简洁的测试。注意在测试中声明的任何[examples](https://gocn.github.io/styleguide/docs/03-decisions/#示例examples)都不会有用户在他们的代码中需要的包名。

#### 不同包中的测试

将测试定义在与被测代码相同的包中并不总是合适的，甚至不可能。在这种情况下，使用一个带有`_test`后缀的包名。这是对[包名](https://gocn.github.io/styleguide/docs/03-decisions/#包名称package-names)的 "不使用下划线"规则的一个例外。比如说。

- 如果一个集成测试没有一个它明显属于的库

  ```
  // Good:
  package gmailintegration_test

  import "testing"
  ```

- 如果在同一包中定义测试会导致循环依赖性

  ```
  // Good:
  package fireworks_test

  import (
    "fireworks"
    "fireworkstestutil" // fireworkstestutil also imports fireworks
  )
  ```

### 使用`testing`包

Go标准库提供了[`testing`包](https://pkg.go.dev/testing)。这是Google代码库中唯一允许用于Go代码的测试框架。特别是[断言库](https://gocn.github.io/styleguide/docs/03-decisions/#断言库)和第三方测试框架是不允许的。

`testing`包为编写好的测试提供了最小但完整的功能集。

- 顶层测试
- 基准
- [可运行的例子](https://blog.golang.org/examples)
- 子测试
- 记录
- 失败和致命的失败

这些设计是为了与核心语言特性如[复合字面](https://go.dev/ref/spec#Composite_literals)和[带有初始化的if语句](https://go.dev/ref/spec#If_statements)语法协同工作，使测试作者能够编写[清晰、可读、可维护的测试]。

## 非决策性的

风格指南不能列举所有事项的正面规定，也不能列举所有它不提供意见的事项。也就是说，这里有几件可读性社区以前争论过但没有达成共识的事情。

- **零值的局部变量初始化**。`var i int`和`i := 0`是等同的。参见[初始化最佳实践](https://gocn.github.io/styleguide/docs/04-best-practices/#初始化)。
- **空的复合字面与`new`或`make`**。`&File{}`和`new(File)`是等同的。`map[string]bool{}`和`make(map[string]bool)`也是如此。参见[复合声明最佳实践](https://gocn.github.io/styleguide/docs/04-best-practices/#复合字面量)。
- **got/want参数在cmp.Diff调用中的排序**。要有本地一致性，并在你的失败信息中[包括一个图例](https://gocn.github.io/styleguide/docs/03-decisions/#打印差异)。
- **`errors.New`与`fmt.Errorf`在非格式化字符串上的对比**。`errors.New("foo")`和`fmt.Errorf("foo")`可以互换使用。

如果有特殊情况，它们又出现了，可读性导师可能会做一个可选的注释，但一般来说，作者可以自由选择他们在特定情况下喜欢的风格。

当然，如果风格指南中没有涉及的东西确实需要更多的讨论，欢迎在具体的审查中，或者在内部留言板上提出来。

{{< button relref="./02-guide.md" >}}上一章{{< /button >}}
{{< button relref="./04-best-practices.md" >}}下一章{{< /button >}}
