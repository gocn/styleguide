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

常量名称必须像 Go 中的所有其他名称一样使用 [混合大写字母MixedCaps](https://google.github.io/styleguide/go/guide#mixed-caps)。 （[导出](https://tour.golang.org/basics/3) 常量以大写字母开头，而未导出的常量以小写字母开头。）即使打破了其他语言的约定，这也是适用的。常量名称不应是其值的派生词，而应该解释值所表示的含义。

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

如果该函数涉及执行复杂的计算或执行远程调用，则可以使用`Compute` 或 `Fetch`等不同的词代替`Get`，以使读者清楚函数调用可能需要时间，并有可能会阻塞或失败。

### 变量名Variable names

一般的经验法则是，名称的长度应与其范围的大小成正比，并与其在该范围内使用的次数成反比。在文件范围内创建的变量可能需要多个单词，而单个内部块作用域内的变量可能是单个单词甚至只是一两个字符，以保持代码清晰并避免无关信息。

这是一条粗略的基础原则。这些数字准则不是严格的规则。要根据上下文、[清晰](https://google.github.io/styleguide/go/guide#clarity) 和[简洁](https://google.github.io/styleguide/go/guide#简洁）来进行判断。

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

完整的注释应该像标准英语句子一样包含大写和标点符号。 （作为一个例外，如果在其他方面很清楚，可以以非大写的标识符名称开始一个句子。这种情况最好只在段落的开头进行。）

作为句子片段的注释对标点符号或大小写没有此类要求。

[文档注释](https://google.github.io/styleguide/go/decisions#doc-comments) 应始终是完整的句子，因此应始终大写和标点符号。 简单的行尾注释（特别是对于结构字段）可以为假设字段名称是主语的简单短语。

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

不要为了避免在函数内声明变量而使用命名结果参数。这种做法会导致不必要的冗长API，但收益只是微小的简洁性。

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

- 如果没有明显的main文件或者包注释特别长，可以将文档注释放在名为 doc.go 的文件中，只有注释和包子句。

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

类似地，函数和方法调用不应该仅仅由于行的长度而进行换行。

```
// Good:
good := foo.Call(long, list, of, parameters, all, on, one, line)
// Bad:
bad := foo.Call(long, list, of, parameters,
    with, arbitrary, line, breaks)
```

不要为特定的函数参数添加注释。 相反，使用 [option struct](https://google.github.io/styleguide/go/best-practices#option-structure) 或在函数文档中添加更多细节。

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

当 API 无法更改或本地调用是不寻常的（无论调用是否太长），如果有助于理解本次调用，始终允许添加换行符。

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

函数中的长字符串不应该因为行的长度而被破坏。 对于包含此类字符串的函数，可以在字符串格式之后添加换行符，并且可以在下一行或后续行中提供参数。 最好根据输入的语义分组来决定换行符应该放在哪里，而不是单纯基于行长。

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

`if` 语句不应换行； 多行 `if` 子句的形式会出现 [缩进混乱带来的困扰](https://google.github.io/styleguide/go/decisions#indentation-confusion)。

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

包含闭包或多行结构文字的 `if` 语句应确保 [大括号匹配](https://google.github.io/styleguide/go/decisions#literal-matching-braces) 以避免 [缩进混淆] （https://google.github.io/styleguide/go/decisions#indentation-confusion）。

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

同样，不要尝试在 `for` 语句中人为的插入换行符。 如果没有优雅的重构方式，是可以允许单纯的较长的行：

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

如果行太长，将所有大小写缩进并用空行分隔以避免[缩进混淆]（https://google.github.io/styleguide/go/decisions#indentation-confusion）：

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

不要采用常量在前的表达含糊的条件写法([尤达条件式](https://en.wikipedia.org/wiki/Yoda_conditions))

```
// Bad:
if "foo" == result {
  // ...
}
```

### 复制

为了避免意外的别名和类似的错误，从另一个包复制结构时要小心。 例如 `sync.Mutex` 是不能复制的同步对象，

`bytes.Buffer` 类型包含一个 `[]byte` 切片和切片可以引用的小数组，这是为了对小字符串的优化。 如果你复制一个 `Buffer`，复制的切片会指向原始切片中的数组，从而在后续方法调用产生意外的效果。

一般来说，如果类型的方法与指针类型`*T`相关联，不要复制类型为`T`的值。


```
// Bad:
b1 := bytes.Buffer{}
b2 := b1
```

调用值接收者的方法可以隐藏副本。 当您编写 API 时，如果您的结构包含不应复制的字段，您通常应该采用并返回指针类型。

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

不要使用 `panic` 进行正常的错误处理。 相反，使用 `error` 和多个返回值。 请参阅 [关于错误的有效 Go 部分](http://golang.org/doc/effective_go.html#errors)。

在 `package main` 和初始化代码中，考虑 [`log.Exit`](https://pkg.go.dev/github.com/golang/glog#Exit) 中应该终止程序的错误（例如，无效配置 )，因为在许多这些情况下，堆栈跟踪对阅读者没有帮助。 请注意 [`log.Exit`](https://pkg.go.dev/github.com/golang/glog#Exit) 中调用了 [`os.Exit`](https://pkg.go.dev/os#Exit) ，此时所有`defer`函数都将不会运行。

对于那些表示“不可能”出现的条件错误、命名错误，应该在代码评审、测试期间发现，函数应合理地返回错误或调用 [`log.Fatal`](https://pkg.go.dev /github.com/golang/glog#Fatal）。

**注意：** `log.Fatalf` 不是标准库日志。 请参阅 [#logging]。

### Must类函数

用于在失败时停止程序的辅助函数应遵循命名约定“MustXYZ”（或“mustXYZ”）。 一般来说，它们应该只在程序启动的早期被调用，而不是在像用户输入时，此时更应该首选 `error` 处理。

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

相同的约定也可用于仅停止当前测试的情况（使用 `t.Fatal`）。 这样在创建测试时通常很方便，例如在 [表驱动测试](https://google.github.io/styleguide/go/decisions#table-driven-tests) 的结构字段中，作为返回错误的函数是不能直接复制给结构字段的。

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

在这两种情况下，这种模式的价值在于可以在“值”上下文中调用。不应在难以确保捕获错误的地方或应[检查](https://google.github.io/styleguide/go/decisions#handle-errors)错误的上下文中调用这些程序（如，在许多请求处理程序中）。对于常量输入，这允许测试确保“必须”的参数格式正确，对于非常量的输入，它允许测试验证错误是否[正确处理或传播](https://google.github.io/styleguide/go/best-practices#error-handling)。

在测试中使用 `Must` 函数的地方，通常应该 [标记为测试助手](https://google.github.io/styleguide/go/decisions#mark-test-helpers) 并调用 `t.Fatal`（请参阅[测试助手中的错误处理](https://google.github.io/styleguide/go/best-practices#test-helper-error-handling)来了解使用它的更多注意事项）。

当有可能通过 [普通错误处理](https://google.github.io/styleguide/go/best-practices#error-handling) 处理时，就不应该使用`Must`类函数：

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

Goroutines 可以在阻塞通道发送或接收出现泄漏。 垃圾收集器不会终止一个 goroutine，即使它被阻塞的通道已经不可用。

即使 goroutine 没有泄漏，在不再需要时仍处于运行状态也会导致其他微妙且难以诊断的问题。 向已关闭的通道上发送会导致panic。


```
// Bad:
ch := make(chan int)
ch <- 42
close(ch)
ch <- 13 // panic
```

“在结果已经不需要之后”修改仍在使用的入参可能会导致数据竞争。 运行任意长时间的 goroutine 会导致不可预测的内存占用。

并发代码的编写应该让 goroutine 生命周期非常明显。 通常，这意味着在与同步相关的代码限制的函数范围内，将逻辑分解为 [同步函数](https://google.github.io/styleguide/go/decisions#synchronous-functions)。 如果并发性仍然不明显，那么文档说明 goroutine 在何时、为何退出就很重要。

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

上面还有其他使用通道的情况，例如 `chan struct{}`、同步变量、[条件变量](https://drive.google.com/file/d/1nPdvhB0PutEJzdCq5ms6UI58dp50fcAN/view) 等等。 重要的部分是 goroutine 的终结对于后续维护者来说是显而易见的。

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

- 代码在可能会有未知的行为，即使操作系统已经释放资源，程序也可能没有完全干净地结束
- 由于代码的不确定生命周期，代码难以进行有效的测试
- 代码可能会出现资源泄漏，如上所述

更多可阅读：

- [永远不要在不知道它将如何停止的情况下启动 goroutine](https://dave.cheney.net/2016/12/22/never-start-a-goroutine-without-knowing-how-it-will-stop)
- 重新思考经典并发模式：[幻灯片](https://drive.google.com/file/d/1nPdvhB0PutEJzdCq5ms6UI58dp50fcAN/view)，[视频](https://www.youtube.com/watch?v=5zXAHh5tJqQ)
- [Go 程序何时结束](https://changelog.com/gotime/165)

### 接口

Go 接口通常属于*使用*接口类型值的包，而不是*实现*接口类型的包。实现包应该返回具体的（通常是指针或结构）类型。这样就可以将新方法添加到实现中，而无需进行大量重构。有关详细信息，请参阅 [GoTip #49：接受接口、返回具体类型](https://google.github.io/styleguide/go/index.html#gotip)。

不要从使用 API 导出接口的 [test double](https://abseil.io/resources/swe-book/html/ch13.html#techniques_for_using_test_doubles) 实现。相反，应设计可以使用 [实际实现](https://abseil.io/resources/swe-book/html/ch12.html#test_via_public_apis) 的[公共API]((https://abseil.io/resources/swe-book/html/ch12.html#test_via_public_apis))进行测试。有关详细信息，请参阅 [GoTip #42：为测试编写存根](https://google.github.io/styleguide/go/index.html#gotip)。即使在使用实现不可行的情况下，也没有必要引入一个完全覆盖类型所有方法的接口；消费者可以创建一个只包含它需要的方法的接口，如 [GoTip #78: Minimal Viable Interfaces](https://google.github.io/styleguide/go/index.html#gotip) 中所示。

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

泛型（正式名称为“[类型参数](https://go.dev/design/43651-type-parameters)”）在满足业务需求时被允许使用。在许多应用程序中，使用现有传统的（切片、映射、接口等）方式也可以正常工作，而不会增加复杂性，因此请注意不要过早使用。请参阅关于 [最小机制](https://google.github.io/styleguide/go/guide#least-mechanism) 的讨论。

在使用泛型的导出时，请确保对其进行适当的记录。强烈鼓励包含可运行的 [examples](https://google.github.io/styleguide/go/decisions#examples)。

不要仅仅因为你正在实现一个不关心其成员元素类型的算法或数据结构而使用泛型。如果在实践中只有一种类型被实例化，那么首先让您的代码在该类型上工作，而不使用泛型。与删除发现不必要的抽象相比，稍后添加多态性将更简单。

不要使用泛型来发明领域特定语言 (DSL)。特别是，不要引入可能会给阅读者带来沉重负担的错误处理框架。相反，更应该使用 [错误处理](https://google.github.io/styleguide/go/decisions#errors) 做法。对于测试，要特别小心引入 [断言库](https://google.github.io/styleguide/go/decisions#assert) 或框架，尤其是很少发现[失败case](https://google.github.io/styleguide/go/decisions#useful-test-failures)的。

一般来说：

- [写代码，不要去设计类型](https://www.youtube.com/watch?v=Pa_e9EeCdy8&t=1250s)。来自 Robert Griesemer 和 Ian Lance Taylor 的 GopherCon 演讲。
- 如果您有几种类型共享一个有用的统一界面，请考虑使用该界面对解决方案进行建模。可能不需要泛型。
- 否则，不要依赖 `any` 类型和过多的 [类型断言](https://tour.golang.org/methods/16)，这时应考虑泛型。

更多也可以参考：

- [在 Go 中使用泛型](https://www.youtube.com/watch?v=nr8EpUO9jhw)，Ian Lance Taylor 的演讲
- Go 网页上的[泛型教程](https://go.dev/doc/tutorial/generics)

### 参数值传递

不要为了节省几个字节而将指针作为函数参数传递。 如果一个函数在整个过程中只将参数`x`处理为`*x`，那么不应该采用指针。 常见的例子包括传递一个指向字符串的指针（`*string`）或一个指向接口值的指针（`*io.Reader`）。 在这两种情况下，值本身都是固定大小的，可以直接传递。

此建议不适用于大型结构体，甚至可能会增加大小的小型结构。 特别是，`pb`消息通常应该通过指针而不是值来处理。 指针类型满足 `proto.Message` 接口（被 `proto.Marshal`、`protocmp.Transform` 等接受），并且协议缓冲区消息可能非常大，并且随着时间的推移通常会变得更大。

### 接收者类型

[方法接收者](https://golang.org/ref/spec#Method_declarations) 和常规函数参数一样，也可以使用值或指针传递。 选择哪个应该基于该方法应该属于哪个[方法集]（https://golang.org/ref/spec#Method_sets）。

**正确性胜过速度或简单性。** 在某些情况下是必须使用指针的。 在其他情况下，如果您对代码的增长方式没有很好的了解，请为大类型选择指针或作为面向未来的指针，并为[简单的的数据]((https://en.wikipedia.org/wiki/Passive_data_structure))使用值。

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

- 如果接收者包含 [不能被安全复制的](https://google.github.io/styleguide/go/decisions#copying) 字段, 应使用指针接收者。常见的例子是 [`sync.Mutex`](https://pkg.go.dev/sync#Mutex) 和其他同步类型。

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

  **提示：** 检查类型的 [Godoc](https://pkg.go.dev/time#example-Duration) 以获取有关复制是否安全的信息。

- 如果接收者是“大”结构或数组，则指针接收者可能更有效。 传递结构相当于将其所有字段或元素作为参数传递给方法。 如果这看起来太大而无法[按值传递](https://google.github.io/styleguide/go/decisions#pass-values)，那么指针是一个不错的选择。

- 对于将调用修改接收者的其他函数，而这些修改对此方法不可见，请使用值类型； 否则使用指针。

- 如果接收者是一个结构或数组，其元素中的任何一个都是指向可能发生变异的东西的指针，那么更应该指针接收者以使阅读者清楚地了解可变性的意图。

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

**注意：** 关于是否值或指针的函数是否会影响性能，存在很多错误信息。 编译器可以选择将指针传递到堆栈上的值以及复制堆栈上的值，但在大多数情况下，这些考虑不应超过代码的可读性和正确性。 当性能确实很重要时，重要的是在确定一种方法优于另一种方法之前，用一个现实的基准来描述这两种方法。

### `switch` 和 `break`

不要在`switch`子句末尾使用没有目标标签的`break`语句； 它们是多余的。 与 C 和 Java 不同，Go 中的 `switch` 子句会自动中断，并且需要 `fallthrough` 语句来实现 C 风格的行为。 如果您想阐明空子句的目的，请使用注释而不是 `break`。

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

同步函数直接返回它们的结果，并在返回之前完成所有回调或通道操作。 首选同步函数而不是异步函数。

同步函数使 goroutine 在调用中保持本地化。 这有助于推理它们的生命周期，并避免泄漏和数据竞争。 同步函数也更容易测试，因为调用者可以传递输入并检查输出，而无需轮询或同步。

如有必要，调用者可以通过在单独的 goroutine 中调用函数来添加并发性。 然而，在调用方移除不必要的并发是相当困难的（有时是不可能的）。

也可以看看：

- “重新思考经典并发模式”，Bryan Mills 的演讲：[幻灯片](https://drive.google.com/file/d/1nPdvhB0PutEJzdCq5ms6UI58dp50fcAN/view)，[视频](https://www.youtube.com/ 看？v=5zXAHh5tJqQ)

### 类型别名

使用*类型定义*，`type T1 T2`，定义一个新类型。 
使用 [*类型别名*](http://golang.org/ref/spec#Type_declarations), `type T1 = T2` 来引用现有类型而不定义新类型。 
类型别名很少见； 它们的主要用途是帮助将包迁移到新的源代码位置。 不要在不需要时使用类型别名。

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

建议在供人使用的输出中使用 `%q`，其输入值可能为空或包含控制字符。 可能很难注意到一个无声的空字符串，但是 `""` 就这样清楚地突出了。
### 使用 any

Go 1.18 将 `any` 类型作为 [别名](https://go.googlesource.com/proposal/+/master/design/18130-type-alias.md) 引入到 `interface{}`。 因为它是一个别名，所以 `any` 在许多情况下等同于 `interface{}`，而在其他情况下，它可以通过显式转换轻松互换。 在新代码中应使用 `any`。
## 通用库

### Flags

Google 代码库中的 Go 程序使用 [标准 `flag` 包](https://golang.org/pkg/flag/) 的内部变体。 它具有类似的接口，但与 Google 内部系统的互操作性很好。 Go 二进制文件中的标志名称应该更应该使用下划线来分隔单词，尽管包含标志值的变量应该遵循标准的 Go 名称样式（[混合大写字母](https://google.github.io/styleguide/go/guide#mixed-caps)）。 具体来说，标志名称应该是蛇大小写，变量名称应该是骆驼大小写。

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

如果您的标志是全局变量，在导入部分之后，将它们放在 `var` 组中。

关于使用子命令创建 [complex CLI](https://google.github.io/styleguide/go/best-practices#complex-clis) 的最佳实践还有其他讨论。

也可以看看：

- [本周提示 #45：避免标记，尤其是在库代码中](https://abseil.io/tips/45)
- [Go Tip #10：配置结构和标志](https://google.github.io/styleguide/go/index.html#gotip)
- [Go Tip #80：依赖注入原则](https://google.github.io/styleguide/go/index.html#gotip)

### 日志

Google 代码库中的 Go 程序使用 [标准 `log` 包](https://pkg.go.dev/log) 的变体。它具有类似但功能更强大的interface，并且可以与 Google 内部系统进行良好的互操作。该库的开源版本可通过 [package `glog`](https://pkg.go.dev/github.com/golang/glog) 获得，开源 Google 项目可能会使用它，但本指南指的是它始终作为“日志”。

**注意：** 对于异常的程序退出，这个库使用 `log.Fatal` 通过堆栈跟踪中止，使用 `log.Exit` 在没有堆栈跟踪的情况下停止。标准库中没有 `log.Panic` 函数。

**提示：** `log.Info(v)` 等价于 `log.Infof("%v", v)`，其他日志级别也是如此。当您没有格式化要做时，首选非格式化版本。

也可以看看：

- [记录错误](https://google.github.io/styleguide/go/best-practices#error-logging) 和 [自定义详细日志级别](https://google.github.io/styleguide/go/best-practices#vlog)
- 何时以及如何使用日志包[停止程序](https://google.github.io/styleguide/go/best-practices#checks-and-panics)

### 上下文

[`context.Context`](https://pkg.go.dev/context) 类型的值携带跨 API 和进程边界的安全凭证、跟踪信息、截止日期和取消信号。 与 Google 代码库中使用线程本地存储的 C++ 和 Java 不同，Go 程序在整个函数调用链中显式地传递上下文，从传入的 RPC 和 HTTP 请求到传出请求。

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
> 您可能会遇到服务器库（在 Google 的 Go 服务器框架中实现 Stubby、gRPC 或 HTTP），它们为每个请求构建一个新的上下文对象。这些上下文立即填充来自传入请求的信息，因此当传递给请求处理程序时，上下文的附加值已从客户端调用者通过网络边界传播给它。此外，这些上下文的生命周期仅限于请求的生命周期：当请求完成时，上下文将被取消。
>
> 除非你正在实现一个服务器框架，否则你不应该在库代码中使用 `context.Background()` 创建上下文。相反，如果有可用的现有上下文，则更应该使用下面提到的上下文分离。如果您认为在入口点函数之外确实需要`context.Background()`，请在提交实现之前与 Google Go 风格的邮件列表讨论它。

`context.Context` 在函数中首先出现的约定也适用于测试助手。

```
// Good:
func readTestFile(ctx context.Context, t *testing.T, path string) string {}
```

不要将上下文成员添加到结构类型。 相反，为需要传递它的类型的每个方法添加一个上下文参数。 一个例外是其签名必须与标准库或 Google 无法控制的第三方库中的接口匹配的方法。 这种情况非常罕见，应该在实施和可读性审查之前与 Google Go 风格的邮件列表讨论。

Google 代码库中必须产生可以在取消父上下文后运行的后台操作的代码可以使用内部包进行分离。 关注 https://github.com/golang/go/issues/40221 讨论开源替代方案。

由于上下文是不可变的，因此可以将相同的上下文传递给共享相同截止日期、取消信号、凭据、父跟踪等的多个调用。

更多参见：

- [上下文和结构](https://go.dev/blog/context-and-structs)

#### 自定义上下文

不要在函数签名中创建自定义上下文类型或使用上下文以外的接口。 这条规定没有例外。

想象一下，如果每个团队都有一个自定义上下文。 对于包 P 和 Q 的所有对，从包 P 到包 Q 的每个函数调用都必须确定如何将“PContext”转换为“QContext”。这对人类来说是不切实际且容易出错的，并且它会进行自动重构 添加上下文参数几乎是不可能的。

如果您要传递应用程序数据，请将其放入参数、接收器、全局变量中，或者如果它确实属于那里，则放入 Context 值中。 创建自己的 Context 类型是不可接受的，因为它破坏了 Go 团队使 Go 程序在生产中正常工作的能力。

### crypto/rand

不要使用包 `math/rand` 来生成密钥，即使是一次性的。 如果未生成随机种子，则生成器是完全可预测的。 用`time.Nanoseconds()`生成种子，也只有几位熵。 相反，请使用 `crypto/rand` ，如果需要文本，请打印为十六进制或 base64。

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

应该可以在不读取测试源的情况下诊断测试失败。 测试应该失败并显示有用的消息详细说明：

- 是什么导致了失败
- 哪些输入导致错误
- 实际结果
- 预期的结果

下面概述了实现这一目标的具体约定。

### 断言库

不要创建“断言库”作为测试的助手。

断言库是试图在测试中结合验证和生成失败消息的库（尽管同样的陷阱也可能适用于其他测试助手）。 有关测试助手和断言库之间区别的更多信息，请参阅 [最佳实践](https://google.github.io/styleguide/go/best-practices#test-functions)。

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

复杂的断言函数通常不提供 [有用的失败消息](https://google.github.io/styleguide/go/decisions#useful-test-failures) 和存在于测试函数中的上下文。 太多的断言函数和库会导致开发人员体验支离破碎：我应该使用哪个断言库，它应该发出什么样的输出格式，等等？ 碎片化会产生不必要的混乱，特别是对于负责修复潜在下游破坏的库维护者和大规模更改的作者。 与其创建用于测试的特定领域语言，不如使用 Go 本身。

断言库通常会排除比较和相等检查。 更应该使用标准库，例如 [`cmp`](https://pkg.go.dev/github.com/google/go-cmp/cmp) 和 [`fmt`](https://golang.org/pkg/fmt/) 修改为：

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

**最佳实践：** 如果 `postLength` 很重要，直接测试它是有意义的，独立于使用它的任何测试。

也可以看看：

- [平等比较和差异](https://google.github.io/styleguide/go/decisions#types-of-equality)
- [打印差异](https://google.github.io/styleguide/go/decisions#print-diffs)
- 有关测试助手和断言助手之间区别的更多信息，请参阅[最佳实践](https://google.github.io/styleguide/go/best-practices#test-functions)

### 标识出方法

在大多数测试中，失败消息应该包括失败的函数的名称，即使从测试函数的名称中看起来很明显。 具体来说，你的失败信息应该是 `YourFunc(%v) = %v, want %v` 而不仅仅是 `got %v, want %v`。

### 标识出输入

在大多数测试中，失败消息应该包括功能输入（如果它们很短）。 如果输入的相关属性不明显（例如，因为输入很大或不透明），您应该使用对正在测试的内容的描述来命名测试用例，并将描述作为错误消息的一部分打印出来。

### Got before want

测试输出应包括函数在打印预期值之前返回的实际值。 打印测试输出的标准格式是 `YourFunc(%v) = %v, want %v`。 在你会写“实际”和“预期”的地方，更应该分别使用“得到”和“想要”这两个词。

对于差异，方向性不太明显，因此包含一个有助于解释失败的关键是很重要的。 请参阅 [关于打印差异的部分](https://google.github.io/styleguide/go/decisions#print-diffs)。 无论您在失败消息中使用哪种 diff 顺序，都应将其明确指示为失败消息的一部分，因为现有代码的顺序不一致。

### 全结构比较

如果您的函数返回一个结构体（或任何具有多个字段的数据类型，例如切片、数组和映射），请避免编写执行手动编码的结构体逐个字段比较的测试代码。相反，构建期望函数返回的数据，并使用 [深度比较](https://google.github.io/styleguide/go/decisions#types-of-equality) 直接进行比较。

**注意：** 如果您的数据包含模糊测试意图的不相关字段，则这不适用。

如果您的结构需要比较近似（或等效类型的语义）相等，或者它包含无法比较相等的字段（例如，如果其中一个字段是 `io.Reader`），请调整 [`cmp. Diff`](https://pkg.go.dev/github.com/google/go-cmp/cmp/cmp#Diff) 或 [`cmp.Equal`](https://pkg.go.dev/github.com/google/go-cmp/cmp/cmp#Equal) 与 [`cmpopts`](https://pkg.go.dev/github.com/google/go-cmp/cmp/cmpopts) 选项比较，例如[`cmpopts.IgnoreInterfaces`](https://pkg.go.dev/github.com/google/go-cmp/cmp/cmpopts#IgnoreInterfaces) 可能满足您的需求（[示例](https://play.golang.org/p/vrCUNVfxsvF))。

如果您的函数返回多个返回值，则无需在比较它们之前将它们包装在结构中。只需单独比较返回值并打印它们。

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