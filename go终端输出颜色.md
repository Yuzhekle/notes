通常，我们会对不同等级的日志输出不同的颜色来区分。

```go
fmt.Printf("\033[1;31;40m%s\033[0m\n","Red.")
fmt.Printf("\033[1;37;41m%s\033[0m\n","Red.")
```

第一行是红字黑底，第二行红底白字。

我们来解析 `\033[1;31;40m%s\033[0m\n` 这个字符串中的字符分别代表了什么。

- `\033`：`\` 表示转义，`\033`` 表示设置颜色。
- `[1;31;40m`：定义颜色，`[` 表示开始颜色设置，`m` 为颜色设置结束，以 `;` 号分隔。`1` 代码，表示显示方式，`31` 表示前景颜色（文字的 颜色），`40` 表示背景颜色。
- `\033[0m`：表示恢复终端默认样式。

```go
// 前景 背景 颜色
// ---------------------------------------
// 30 40 黑色
// 31 41 红色
// 32 42 绿色
// 33 43 黄色
// 34 44 蓝色
// 35 45 紫红色
// 36 46 青蓝色
// 37 47 白色

// 3 位前景色, 4 位背景色

// 代码 意义
// -------------------------
// 0 终端默认设置
// 1 高亮显示
// 4 使用下划线
// 5 闪烁
// 7 反白显示
// 8 不可见
// Color defines a single SGR Code
type Color int

// Foreground text colors
const (
    FgBlack Color = iota + 30
    FgRed
    FgGreen
    FgYellow
    FgBlue
    FgMagenta
    FgCyan
    FgWhite
)

// Foreground Hi-Intensity text colors
const (
    FgHiBlack Color = iota + 90
    FgHiRed
    FgHiGreen
    FgHiYellow
    FgHiBlue
    FgHiMagenta
    FgHiCyan
    FgHiWhite
)

// Colorize a string based on given color.
func Colorize(s string, c Color) string {
    return fmt.Sprintf("\033[1;%s;40m%s\033[0m", strconv.Itoa(int(c)), s)
}
```

上面的代码输出：

!["输出"](https://shipengqi.github.io/images/go-colorize/colorize.jpg)
