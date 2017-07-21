---
type: api
---
> 从文件读取JSON数据

## json数据
```json
{
    "13918098715":"63120523",
    "13918098719":"73314985",
    "13918098718":"82752863",
    "13918098714":"92440329",
    "13918094183":"35875678",
    "13918094187":"45562314",
    "13918094186":"54292576",
    "13918094185":"63906263",
    "13918094189":"73460548",
    "13918094364":"45717870"
}
```

## 读取JSON数据

Go 读取文件，然后反序列化JSON代码
```go
package main
 
import (
    "encoding/json"
    "fmt"
    "io/ioutil"
)
 
var xxx = map[string]string{}
 
func readFile(filename string) (map[string]string, error) {
    bytes, err := ioutil.ReadFile(filename)
    if err != nil {
        fmt.Println("ReadFile: ", err.Error())
        return nil, err
    }
 
    if err := json.Unmarshal(bytes, &xxx); err != nil {
        fmt.Println("Unmarshal: ", err.Error())
        return nil, err
    }
 
    return xxx, nil
}
 
func main() {
    xxxMap, err := readFile("xxx.json")
    if err != nil {
        fmt.Println("readFile: ", err.Error())
        return nil, err
    }
 
    fmt.Println(xxxMap)
}
```