---
type: api
---

## Go 读取JSON文件

Go按照RFC 4627的标准实现了一个json编解码的标准库
---
### 反序列化

```go
// 反序列化函数
func Unmarshal(data []byte, v interface{}) error
```

Unmarshal用于反序列化json的函数 根据data将数据反序列化到传入的对象中
仔细查看代码中的四种情况：
1. 将json反序列化成struct对象
1. 将json反序列化成可以存储struct的slice中
1. 将json反序列化到map中
1. 将json反序列化到slice中

示例代码：
```go
package main

import (
	"encoding/json"
	"fmt"
)

func main() {
	type Person struct {
		Name   string
		Age    int
		Gender bool
	}
	//unmarshal to struct
	var p Person
	var str = `{"Name":"junbin", "Age":21, "Gender":true}`
	json.Unmarshal([]byte(str), &p)
	//result --> junbin : 21 : true
	fmt.Println(p.Name, ":", p.Age, ":", p.Gender)

	// unmarshal to slice-struct
	var ps []Person
	var aJson = `[{"Name":"junbin", "Age":21, "Gender":true},
				{"Name":"junbin", "Age":21, "Gender":true}]`
	json.Unmarshal([]byte(aJson), &ps)
	//result --> [{junbin 21 true} {junbin 21 true}] len is 2
	fmt.Println(ps, "len is", len(ps))

	// unmarshal to map[string]interface{}
	var obj interface{} // var obj map[string]interface{}
	json.Unmarshal([]byte(str), &obj)
	m := obj.(map[string]interface{})
	//result --> junbin : 21 : true
	fmt.Println(m["Name"], ":", m["Age"], ":", m["Gender"])

	//unmarshal to slice
	var strs string = `["Go", "Java", "C", "Php"]`
	var aStr []string
	json.Unmarshal([]byte(strs), &aStr)
	//result --> [Go Java C Php]  len is 4
	fmt.Println(aStr, " len is", len(aStr))
}
```
---
### 序列化

```go
// 序列化函数
func Marshal(v interface{}) ([]byte, error)
```

Marshal 用于将struct对象序列化到json对象中
下面这个例子讲解了以下几个例子

1. 给field指定别名
1. 序列化时忽略字段
1. 序列化时忽略值为zero value的字段
1. 序列化时 将int类型的值 转换成string
1. slice序列化为json
1. map序列化为json

示例代码：
```go
package main

import (
	"encoding/json"
	"fmt"
)

//tag中的第一个参数是用来指定别名
//比如Name 指定别名为 username `json:"username"`
//如果不想指定别名但是想指定其他参数用逗号来分隔
//omitempty 指定到一个field时
//如果在赋值时对该属性赋值 或者 对该属性赋值为 zero value
//那么将Person序列化成json时会忽略该字段
//- 指定到一个field时
//无论有没有值将Person序列化成json时都会忽略该字段
//string 指定到一个field时
//比如Person中的Count为int类型 如果没有任何指定在序列化
//到json之后也是int 比如这个样子 "Count":0
//但是如果指定了string之后序列化之后也是string类型的
//那么就是这个样子"Count":"0"
type Person struct {
	Name        string `json:"username"`
	Age         int
	Gender      bool `json:",omitempty"`
	Profile     string
	OmitContent string `json:"-"`
	Count       int    `json:",string"`
}

func main() {
	var p *Person = &Person{
		Name:        "brainwu",
		Age:         21,
		Gender:      true,
		Profile:     "I am Wujunbin",
		OmitContent: "OmitConent",
	}
	if bs, err := json.Marshal(&p); err != nil {
		panic(err)
	} else {
		//result --> {"username":"brainwu","Age":21,"Gender":true,"Profile":"I am Wujunbin","Count":"0"}
		fmt.Println(string(bs))
	}

	var p2 *Person = &Person{
		Name:        "brainwu",
		Age:         21,
		Profile:     "I am Wujunbin",
		OmitContent: "OmitConent",
	}
	if bs, err := json.Marshal(&p2); err != nil {
		panic(err)
	} else {
		//result --> {"username":"brainwu","Age":21,"Profile":"I am Wujunbin","Count":"0"}
		fmt.Println(string(bs))
	}

	// slice 序列化为json
	var aStr []string = []string{"Go", "Java", "Python", "Android"}
	if bs, err := json.Marshal(aStr); err != nil {
		panic(err)
	} else {
		//result --> ["Go","Java","Python","Android"]
		fmt.Println(string(bs))
	}

	//map 序列化为json
	var m map[string]string = make(map[string]string)
	m["Go"] = "No.1"
	m["Java"] = "No.2"
	m["C"] = "No.3"
	if bs, err := json.Marshal(m); err != nil {
		panic(err)
	} else {
		//result --> {"C":"No.3","Go":"No.1","Java":"No.2"}
		fmt.Println(string(bs))
	}
}
```