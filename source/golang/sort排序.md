### 利用sort排序

#### 一、对数组排序
    1）、对基础类型数组（int、string、float64）排序
    var arr = []int{9, 7, 4, 8, 1, 3, 2}
    //正序
    sort.Sort(sort.IntSlice(arr)) //其他有StringSlice及Float64Slice默认结构体
    fmt.Println(arr)
    //倒序
    sort.Sort(sort.Reverse(sort.IntSlice(arr)))
    fmt.Println(arr)

    2）、对自定义结构体数组排序
    type person struct { 
        Name string
        Age int
    }
    //需要自定义Slice接口
    type personSlice []person 
    func (s personSlice) Len() int { return len(s) }
    func (s personSlice) Swap(i, j int) { s[i], s[j] = s[j], s[i] }
    func (s personSlice) Less(i, j int) bool { return s[i].Age < s[j].Age } //定义自己的排序规则
    //正序
    sort.Sort(personSlice(arr))
    fmt.Println(arr)
    //倒序
    sort.Sort(sort.Reverse(personSlice(arr)))
    fmt.Println(arr)

#### 二、对map排序
    1）、map按key排序
    //golang的map不保证有序性，所以按key排序需要取出key，对key排序，再遍历输出value。

    2）、map按value排序
    //要对golang map按照value进行排序，思路是直接不用map，用struct存放key和value，实现sort接口。