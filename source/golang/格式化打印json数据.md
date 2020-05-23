### 格式化打印json数据

    func PrintJson(obj interface{}) {
        jsonByte, err := json.Marshal(obj)
        if err != nil {
            fmt.Println(err)
            return
        }
        tags := 0
        length := len(jsonByte)
        for i := 0; i < length; i++ {
            char := string(jsonByte[i])
            if i == 0 && char != "{" && char != "[" {
                fmt.Println(string(jsonByte))
                return
            }
            switch char {
            case "{","[":
                fmt.Println(strings.Repeat("  ", tags * 2) + char)
                tags++
            case "}","]":
                tags--
                fmt.Println(strings.Repeat("  ", tags * 2) + char)
            case ",":
                continue
            default:
                for {
                    i++
                    nextChar := string(jsonByte[i])
                    switch nextChar {
                    case "{","[","]","}":
                        i--
                        goto BreakHere
                    case ",":
                        goto BreakHere
                    }
                    char += nextChar
                }
            BreakHere:
                fmt.Println(strings.Repeat("  ", tags * 2) + char)
            }
        }
    }