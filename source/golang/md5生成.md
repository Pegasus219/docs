### 生成md5的几种方法

*以下三种方法，实测性能差异不大

#### 方法一
    func md5V(str string) string {
        h := md5.New()
        h.Write([]byte(str))
        return hex.EncodeToString(h.Sum(nil))
    }
    
#### 方法二
    func md5V2(str string) string {
        data := []byte(str)
        has := md5.Sum(data)
        return fmt.Sprintf("%x", has)
    }
    
#### 方法三
    func md5V3(str string) string {
        w := md5.New()
        io.WriteString(w, str)
        md5str := fmt.Sprintf("%x", w.Sum(nil))
        return md5str
    }