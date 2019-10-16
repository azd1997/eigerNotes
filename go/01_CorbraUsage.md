---
title: "Cobra简介"
date: 2019-10-16T03:11:59+08:00
draft: false
categories: ["go"]
tags: ["cobra"]
keywords: ["cobra"]
---

## Cobra官方文档

- https://github.com/spf13/cobra/blob/master/README.md

本文主要译自官方文档，并加以着重和个人理解。
本文假设读者已有go语言flag包、os包编写命令行程序经验

## Cobra

### 应用结构

```
  ▾ appName/
    ▾ cmd/
        add.go
        your.go
        commands.go
        here.go
      main.go
```

`main.go`一般非常简洁，只做Cobra的初始化工作。

```go
package main

import (
  "{pathToYourApp}/cmd"
)

func main() {
  cmd.Execute()
}
```

### 使用Cobra库创建命令行

一个Cobra程序最少需要一个`main.go`和一个`rootCmd`文件（比如说单独放到·root.go·）。当然也可以根据需要添加新的命令。

#### 创建rootCmd

```go
var rootCmd = &cobra.Command{
  Use:   "hugo",
  Short: "Hugo is a very fast static site generator",
  Long: `A Fast and Flexible Static Site Generator built with
                love by spf13 and friends in Go.
                Complete documentation is available at http://hugo.spf13.com`,
  Run: func(cmd *cobra.Command, args []string) {
    // Do Stuff Here
  },
}

func Execute() {
  if err := rootCmd.Execute(); err != nil {
    fmt.Println(err)
    os.Exit(1)
  }
}
```

也可以额外再创建`init()`用来添加一些flag，处理配置文件.
例如

```go
import (
  "fmt"
  "os"

  homedir "github.com/mitchellh/go-homedir"
  "github.com/spf13/cobra"
  "github.com/spf13/viper"
)

func init() {
  cobra.OnInitialize(initConfig)
  rootCmd.PersistentFlags().StringVar(&cfgFile, "config", "", "config file (default is $HOME/.cobra.yaml)")
  rootCmd.PersistentFlags().StringVarP(&projectBase, "projectbase", "b", "", "base project directory eg. github.com/spf13/")
  rootCmd.PersistentFlags().StringP("author", "a", "YOUR NAME", "Author name for copyright attribution")
  rootCmd.PersistentFlags().StringVarP(&userLicense, "license", "l", "", "Name of license for the project (can provide `licensetext` in config)")
  rootCmd.PersistentFlags().Bool("viper", true, "Use Viper for configuration")
  viper.BindPFlag("author", rootCmd.PersistentFlags().Lookup("author"))
  viper.BindPFlag("projectbase", rootCmd.PersistentFlags().Lookup("projectbase"))
  viper.BindPFlag("useViper", rootCmd.PersistentFlags().Lookup("viper"))
  viper.SetDefault("author", "NAME HERE <EMAIL ADDRESS>")
  viper.SetDefault("license", "apache")
}

func initConfig() {
  // Don't forget to read config either from cfgFile or from home directory!
  if cfgFile != "" {
    // Use config file from the flag.
    viper.SetConfigFile(cfgFile)
  } else {
    // Find home directory.
    home, err := homedir.Dir()
    if err != nil {
      fmt.Println(err)
      os.Exit(1)
    }

    // Search config in home directory with name ".cobra" (without extension).
    viper.AddConfigPath(home)
    viper.SetConfigName(".cobra")
  }

  if err := viper.ReadInConfig(); err != nil {
    fmt.Println("Can't read config:", err)
    os.Exit(1)
  }
}
```

#### 创建main.go

`root command`需要被主函数执行，且是在主函数根目录下执行，即便其可能被任何`command`调用。

```go
package main

import (
  "{pathToYourApp}/cmd"
)

func main() {
  cmd.Execute()
}
```

#### 添加其他命令

通常，一个命令及其相关代码被放到`appName/cmd/mycommand.go`中。例如，创建一个用来展示软件版本的命令。创建并编辑`cmd/version.go`

```go
package cmd

import (
  "fmt"

  "github.com/spf13/cobra"
)

func init() {
  rootCmd.AddCommand(versionCmd)
}

var versionCmd = &cobra.Command{
  Use:   "version",
  Short: "Print the version number of Hugo",
  Long:  `All software has versions. This is Hugo's`,
  Run: func(cmd *cobra.Command, args []string) {
    fmt.Println("Hugo Static Site Generator v0.9 -- HEAD")
  },
}
```

### Flag相关操作

Flag标志用来向动作命令提供参数修改，进而控制其行为

#### 为命令指定Flag

Flag变量需要在合适的范围内初始化，他们可能在不同的文件不同的文件夹中使用。通常我们将其初始化为包级变量。

```go
var Verbose bool
var Source string
```

- 持久Flag(Persistent Flag)
持久标志可以作用于其定义处命令，及其子命令。例如在rootCmd定义的持久标志，可以被其所有子命令使用
```go
rootCmd.PersistentFlags().BoolVarP(&Verbose, "verbose", "v", false, "verbose output")
```

- 局部变量（Local Flag）
只能被定义处的或者叫指定的命令使用
```go
localCmd.Flags().StringVarP(&Source, "source", "s", "", "Source directory to read from")
```

- 父命令上的局部标志（LOcal Flag on Parent Command）
可以通过使能TraverseChildren属性来让子命令也可以使用父命令的局部标志
```go
command := cobra.Command{
  Use: "print [OPTIONS] [COMMANDS]",
  TraverseChildren: true,  // 父命令中使能
}
```

#### 将Flag与Config绑定

这里指使用viper将标志与配置绑定
```go
var author string

func init() {
  rootCmd.PersistentFlags().StringVar(&author, "author", "YOUR NAME", "Author name for copyright attribution")
  viper.BindPFlag("author", rootCmd.PersistentFlags().Lookup("author"))
}
```
这个例子中就将标志author与viper绑定（这里viper指的是通过viper读取配置文件得到的一个viper实例）。
**注意！如果在使用命令行时，不提供`--author`标志的话，·author·变量并不会从配置文件读取并被赋值**

#### 要求Flags （Required Flags）

可以通过下面这个方式设定某标志是必须的，这样的话，当在命令行中输入而不指定该flag的参数，会发生报错
```go
rootCmd.Flags().StringVarP(&Region, "region", "r", "", "AWS region (required)")
rootCmd.MarkFlagRequired("region")
```

### 位置和自定义参数（Positional and Custom Arguments）

可以在一个命令的Args域中设置不同的参数校验器，内置的校验器及其作用：

- `NoArgs` - the command will report an error if there are any positional args.
- `ArbitraryArgs` - the command will accept any args.
- `OnlyValidArgs` - the command will report an error if there are any positional args that are not in the `ValidArgs` field of `Command`.
- `MinimumNArgs(int)` - the command will report an error if there are not at least N positional args.
- `MaximumNArgs(int)` - the command will report an error if there are more than N positional args.
- `ExactArgs(int)` - the command will report an error if there are not exactly N positional args.
- `ExactValidArgs(int)` - the command will report an error if there are not exactly N positional args OR if there are any positional args that are not in the `ValidArgs` field of `Command`
- `RangeArgs(min, max)` - the command will report an error if the number of args is not between the minimum and maximum number of expected args.

也可以自定义校验器，自定义校验器的例子：
```go
var cmd = &cobra.Command{
  Short: "hello",
  Args: func(cmd *cobra.Command, args []string) error {
    if len(args) < 1 {
      return errors.New("requires a color argument")
    }
    if myapp.IsValidColor(args[0]) {
      return nil
    }
    return fmt.Errorf("invalid color specified: %s", args[0])
  },
  Run: func(cmd *cobra.Command, args []string) {
    fmt.Println("Hello, World!")
  },
}
```

### 一个完整的Cobra例子

```go
package main

import (
  "fmt"
  "strings"

  "github.com/spf13/cobra"
)

func main() {
  var echoTimes int

  var cmdPrint = &cobra.Command{
    Use:   "print [string to print]",
    Short: "Print anything to the screen",
    Long: `print is for printing anything back to the screen.
For many years people have printed back to the screen.`,
    Args: cobra.MinimumNArgs(1),
    Run: func(cmd *cobra.Command, args []string) {
      fmt.Println("Print: " + strings.Join(args, " "))
    },
  }

  var cmdEcho = &cobra.Command{
    Use:   "echo [string to echo]",
    Short: "Echo anything to the screen",
    Long: `echo is for echoing anything back.
Echo works a lot like print, except it has a child command.`,
    Args: cobra.MinimumNArgs(1),
    Run: func(cmd *cobra.Command, args []string) {
      fmt.Println("Print: " + strings.Join(args, " "))
    },
  }

  var cmdTimes = &cobra.Command{
    Use:   "times [string to echo]",
    Short: "Echo anything to the screen more times",
    Long: `echo things multiple times back to the user by providing
a count and a string.`,
    Args: cobra.MinimumNArgs(1),
    Run: func(cmd *cobra.Command, args []string) {
      for i := 0; i < echoTimes; i++ {
        fmt.Println("Echo: " + strings.Join(args, " "))
      }
    },
  }

  cmdTimes.Flags().IntVarP(&echoTimes, "times", "t", 1, "times to echo the input")

  var rootCmd = &cobra.Command{Use: "app"}
  rootCmd.AddCommand(cmdPrint, cmdEcho)
  cmdEcho.AddCommand(cmdTimes)
  rootCmd.Execute()
}
```

假设这个应用名叫“app”，这个例子中总共有root/print/echo/time四个命令，但root命令没有指定操作，那么它必须有子命令。而且这在命令行的结果是：输入`$ app`后没有动作（当然，cobra默认给你加了个usage命令，会在这时候提示你参数有误，并展示所有用法）.

这个程序的命令有这么几条：
```shell
$ app print [string to print]
$ app echo [string to echo]
$ app times [string to echo]
```

## 其他细节参阅官方文档
