# [jpera] -- 西暦から和暦に変換する Go 言語用パッケージ

[![GitHub license](https://img.shields.io/badge/license-Apache%202-blue.svg)](https://raw.githubusercontent.com/spiegel-im-spiegel/jpera/master/LICENSE)
[![GitHub release](http://img.shields.io/github/release/spiegel-im-spiegel/jpera.svg)](https://github.com/spiegel-im-spiegel/jpera/releases/latest)

元号を含む和暦と西暦との変換を行います。
元号は以下のものに対応しています。

| 元号             | 起点           |
| ---------------- | -------------- |
| 明治（改暦以降） | 1873年1月1日   |
| 大正             | 1912年7月30日  |
| 昭和             | 1926年12月25日 |
| 平成             | 1989年1月8日   |
| 令和             | 2019年5月1日   |

具体的な使い方はサンプルを参考にしてください。

## 西暦から和暦への変換

```go
package main

import (
    "flag"
    "fmt"
    "os"
    "strconv"
    "time"

    "github.com/spiegel-im-spiegel/jpera"
)

func main() {
    flag.Parse()
    argsStr := flag.Args()
    tm := time.Now()
    if len(argsStr) > 0 {
        if len(argsStr) < 3 {
            fmt.Fprintln(os.Stderr, "年月日を指定してください")
            return
        }
        args := make([]int, 3)
        for i := 0; i < 3; i++ {
            num, err := strconv.Atoi(argsStr[i])
            if err != nil {
                fmt.Fprintln(os.Stderr, err)
                return
            }
            args[i] = num
        }
        tm = time.Date(args[0], time.Month(args[1]), args[2], 0, 0, 0, 0, time.Local)
    }
    te := jpera.New(tm)
    n, y := te.YearEraString()
    if len(n) == 0 {
        fmt.Fprintln(os.Stderr, "正しい年月日を指定してください")
        return
    }
    fmt.Printf("%s%s%d月%d日\n", n, y, te.Month(), te.Day())
}
```

これを実行すると以下のような結果になります。

```
$ go run sample1/sample1.go 2019 4 30
平成31年4月30日

$ go run sample1/sample1.go 2019 5 1
令和元年5月1日
```

## 和暦から西暦への変換

```go
package main

import (
    "flag"
    "fmt"
    "os"
    "strconv"
    "time"

    "github.com/spiegel-im-spiegel/jpera"
)

func main() {
    flag.Parse()
    argsStr := flag.Args()

    if len(argsStr) < 4 {
        fmt.Fprintln(os.Stderr, "元号 年 月 日 を指定してください")
        return
    }
    name := argsStr[0]
    args := make([]int, 3)
    for i := 0; i < 3; i++ {
        num, err := strconv.Atoi(argsStr[i+1])
        if err != nil {
            fmt.Fprintln(os.Stderr, err)
            return
        }
        args[i] = num
    }
    te := jpera.Date(jpera.GetName(name), args[0], time.Month(args[1]), args[2], 0, 0, 0, 0, time.Local)
    fmt.Println(te.Format("西暦2006年1月2日"))
}
```

これを実行すると以下のような結果になります。

```
$ go run sample2/sample2.go 平成 31 4 30
西暦2019年4月30日

$ go run sample2/sample2.go 令和 1 5 1
西暦2019年5月1日
```

[jpera]: https://github.com/spiegel-im-spiegel/jpera
