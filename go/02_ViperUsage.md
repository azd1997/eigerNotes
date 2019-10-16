---
title: "Viper简介"
date: 2019-10-16T03:14:47+08:00
draft: false
categories: ["go"]
tags: ["viper"]
keywords: ["viper"]
---


## Viper官方文档

- https://github.com/spf13/viper/blob/master/README.md

## 将值存入Viper

### 设置默认值

如果没有通过配置文件、环境变量等设置值，那么将使用默认值。默认值以如下方式设置：

```go
viper.SetDefault("ContentDir", "content")
viper.SetDefault("LayoutDir", "layouts")
viper.SetDefault("Taxonomies", map[string]string{"tag": "tags", "category": "categories"})
```

### 读取配置文件

一个Viper实例只支持单个配置文件路径。读取配置文件的例子如下：

```go
viper.SetConfigName("config") // name of config file (without extension)
viper.AddConfigPath("/etc/appname/")   // path to look for the config file in
viper.AddConfigPath("$HOME/.appname")  // call multiple times to add many search paths
viper.AddConfigPath(".")               // optionally look for config in the working directory
err := viper.ReadInConfig() // Find and read the config file
if err != nil { // Handle errors reading the config file
    panic(fmt.Errorf("Fatal error config file: %s \n", err))
}
```
当然也可以自己指定当找不到配置文件时怎么处理：

```go
if err := viper.ReadInConfig(); err != nil {
    if _, ok := err.(viper.ConfigFileNotFoundError); ok {
        // Config file not found; ignore error if desired
    } else {
        // Config file was found but another error was produced
    }
}

// Config file found and successfully parsed
```

### 写入配置文件

运行时也可以通过下列方式将配置的修改写入配置文件：

* WriteConfig - writes the current viper configuration to the predefined path, if exists. Errors if no predefined path. Will overwrite the current config file, if it exists.
* SafeWriteConfig - writes the current viper configuration to the predefined path. Errors if no predefined path. Will not overwrite the current config file, if it exists.
* WriteConfigAs - writes the current viper configuration to the given filepath. Will overwrite the given file, if it exists.
* SafeWriteConfigAs - writes the current viper configuration to the given filepath. Will not overwrite the given file, if it exists.

带有safe前缀的两个写方法不会覆盖原本配置文件而是新建。

```go
viper.WriteConfig() // writes current config to predefined path set by 'viper.AddConfigPath()' and 'viper.SetConfigName'
viper.SafeWriteConfig()
viper.WriteConfigAs("/path/to/my/.config")
viper.SafeWriteConfigAs("/path/to/my/.config") // will error since it has already been written
viper.SafeWriteConfigAs("/path/to/my/.other_config")
```

### 监视并重加载配置文件（热更新配置）

Viper支持在程序运行时重新读配置文件并加载进来。操作方式如下：

```go
viper.WatchConfig()  // 1.这一句保证热更新配置
// 2.下面这句是可选的，可以用来设定当配置改变时执行什么动作
viper.OnConfigChange(func(e fsnotify.Event) {
	fmt.Println("Config file changed:", e.Name)
})
```

**切记！WatchConfig()必须在添加所有配置路径完成之后！！！**

### 从io.Reader中读取配置

Viper还可以从io.Reader读取配置。Viper可以从配置文件（.yaml;.toml;.json;...）、环境变量、远程K/V存储（如etcd）。

也可以实现自己需要的配置源，并将其赋给Viper。

```go
viper.SetConfigType("yaml") // or viper.SetConfigType("YAML")

// any approach to require this configuration into your program.
var yamlExample = []byte(`
Hacker: true
name: steve
hobbies:
- skateboarding
- snowboarding
- go
clothing:
  jacket: leather
  trousers: denim
age: 35
eyes : brown
beard: true
`)

viper.ReadConfig(bytes.NewBuffer(yamlExample))  // 从io.Reader中读取配置

viper.Get("name") // this would be "steve"
```

### 设置覆盖

可以根据命令行指令或者程序逻辑对配置做修改（覆盖原本的值）

```go
viper.Set("Verbose", true)
viper.Set("LogFile", LogFile)
```

### 注册别名及使用别名

别名可以为一个值提供多个键。

```go
viper.RegisterAlias("loud", "Verbose")

viper.Set("verbose", true) // same result as next line
viper.Set("loud", true)   // same result as prior line

viper.GetBool("loud") // true
viper.GetBool("verbose") // true
```

### 操作环境变量

以下5个函数用以操作环境变量：

* `AutomaticEnv()`
* `BindEnv(string...) : error`
* `SetEnvPrefix(string)`
* `SetEnvKeyReplacer(string...) *strings.Replacer`
* `AllowEmptyEnv(bool)`

**注意！Viper对环境变量的处理是大小写敏感的**

`SetEnvPrefix(string)`设置环境变量前缀之后，`BindEnv(string...) : error`和`AutomaticEnv()`都会使用这个前缀。

*关于这几个API的细节用法详见官方文档。*

一个简单的例子如下：

```go
SetEnvPrefix("spf") // will be uppercased automatically
BindEnv("id")

os.Setenv("SPF_ID", "13") // typically done outside of the app

id := Get("id") // 13
```

### 与Flags操作

`BindPFlag()`像前边的`BindEnv(string...) : error`类似。需要知道的是：绑定的时候并没有设置值。这意味着绑定操作可以尽可能早，甚至放到`init()`

```go
serverCmd.Flags().Int("port", 1138, "Port to run Application server on")
viper.BindPFlag("port", serverCmd.Flags().Lookup("port"))
```

不仅可以绑定单个的Flag，也可以绑定Flags(集合)

```go
pflag.Int("flagname", 1234, "help message for flagname")

pflag.Parse()
viper.BindPFlags(pflag.CommandLine)

i := viper.GetInt("flagname") // retrieve values from viper instead of pflag
```

也可以添加官方flag包的flag集合：

```go
package main

import (
	"flag"
	"github.com/spf13/pflag"
)

func main() {

	// using standard library "flag" package
	flag.Int("flagname", 1234, "help message for flagname")

	// 将官方标志集给添加到pflag包的commandline实例
	pflag.CommandLine.AddGoFlagSet(flag.CommandLine)
	pflag.Parse()
	viper.BindPFlags(pflag.CommandLine)

	i := viper.GetInt("flagname") // retrieve value from viper

	...
}
```

### Flag接口

如果不使用Pflags的话，Viper也提供了两个Go接口来绑定其他flag系统

`FlagValue`接口表示单个Flag：

```go
type myFlag struct {}
func (f myFlag) HasChanged() bool { return false }
func (f myFlag) Name() string { return "my-flag-name" }
func (f myFlag) ValueString() string { return "my-flag-value" }
func (f myFlag) ValueType() string { return "string" }
```

只要自选的或者自建的flag实现了这个接口，就可以了和Viper绑定。

```go
viper.BindFlagValue("my-flag-name", myFlag{})
```

另一个接口是`FlagValueSet`：

```go
type myFlagSet struct {
	flags []myFlag
}

func (f myFlagSet) VisitAll(fn func(FlagValue)) {
	for _, flag := range flags {
		fn(flag)
	}
}
```

绑定方法：

```go
fSet := myFlagSet{
	flags: []myFlag{myFlag{}, myFlag{}},
}
viper.BindFlagValues("my-flags", fSet)
```

### 远程K/V存储的支持

*参阅官方文档*


## 从Viper取值

有以下方法取用Viper实例的值：

 * `Get(key string) : interface{}`
 * `GetBool(key string) : bool`
 * `GetFloat64(key string) : float64`
 * `GetInt(key string) : int`
 * `GetIntSlice(key string) : []int`
 * `GetString(key string) : string`
 * `GetStringMap(key string) : map[string]interface{}`
 * `GetStringMapString(key string) : map[string]string`
 * `GetStringSlice(key string) : []string`
 * `GetTime(key string) : time.Time`
 * `GetDuration(key string) : time.Duration`
 * `IsSet(key string) : bool`
 * `AllSettings() : map[string]interface{}`

**谨记！** 如果没有找到key对应的配置值，则会默认返回该数据类型的0值。
如果要检查key存不存在，可以使用`IsSet(key string) bool`方法。

Example:
```go
viper.GetString("logfile") // case-insensitive Setting & Getting
if viper.GetBool("verbose") {
    fmt.Println("verbose enabled")
}
```

### 访问嵌套键值数据

以访问如下json配置为例：

```json
{
    "host": {
        "address": "localhost",
        "port": 5799
    },
    "datastore": {
        "metric": {
            "host": "127.0.0.1",
            "port": 3099
        },
        "warehouse": {
            "host": "198.0.0.1",
            "port": 2112
        }
    }
}

```

第一种访问方式是使用'.'分隔符。如下：

```go
GetString("datastore.metric.host") // (returns "127.0.0.1")
```

该访问方式与文件路径访问类似。需要知道的是：
- 查找会一级一级向下寻找，直到寻找到；
- 如果某一级比如说"datastore.metric"被环境变量、Flag等等set方法覆盖了，那么其下级的所有键值都会被隐藏掉无法访问到。
- 如果有某个键直接匹配了带分隔的路径，那么其值将覆盖原本带分隔的方式得到的值，例如：

```json
{
    "datastore.metric.host": "0.0.0.0",
    "host": {
        "address": "localhost",
        "port": 5799
    },
    "datastore": {
        "metric": {
            "host": "127.0.0.1",
            "port": 3099
        },
        "warehouse": {
            "host": "198.0.0.1",
            "port": 2112
        }
    }
}

GetString("datastore.metric.host") // returns "0.0.0.0"
```

### 从Viper抽取子树

例如，Viper读取配置文件后代表了：

```json
app:
  cache1:
    max-items: 100
    item-size: 64
  cache2:
    max-items: 200
    item-size: 80
```

可以作抽取：

```go
subv := viper.Sub("app.cache1")
```

`subv`代表着

```json
max-items: 100
item-size: 64
```

这个特性可以方便多套配置的切换或者是同时使用。例如有个创建Cache的方法：

```go
func NewCache(cfg *Viper) *Cache {...}
```

那么可以做到：

```go
cfg1 := viper.Sub("app.cache1")
cache1 := NewCache(cfg1)

cfg2 := viper.Sub("app.cache2")
cache2 := NewCache(cfg2)
```

### Unmashaling 数据编出

Viper可以将其存的配置编出给结构体、表等。

有两个方法：

 * `Unmarshal(rawVal interface{}) : error`
 * `UnmarshalKey(key string, rawVal interface{}) : error`

 例如：

```go
type config struct {
	Port int
	Name string
	PathMap string `mapstructure:"path_map"`
}

var C config

err := viper.Unmarshal(&C)
if err != nil {
	t.Fatalf("unable to decode into struct, %v", err)
}
```

### Marshaling 数据编入

Viper可以将Viper实例中保存的配置信息编入到字符串，而不是编入到文件。用例如下：

```go
import (
    yaml "gopkg.in/yaml.v2"
    // ...
)

func yamlStringSettings() string {
    c := viper.AllSettings()
    bs, err := yaml.Marshal(c)
    if err != nil {
        log.Fatalf("unable to marshal config to YAML: %v", err)
    }
    return string(bs)
}
```

## Viper还是Vipers

一般情况下采用单例模式足以应付应用配置需求。

*参阅官方文档*

