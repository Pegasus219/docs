### 计时器应用 & 协程超时

#### 1、timer计时器
    //从1输出到9，每秒一次
    func runTimer(wg *sync.WaitGroup) { 
        n := 0
        t := time.NewTicker(1 * time.Second)
        for {
            select {
            case <-t.C:
                n++
                fmt.Println(n)
                if n >= 9 {
                    t.Stop()
                    wg.Done()
                    return
                }
            }
        }
    }

#### 2、设置协程超时（两种方法）
    //方法一（timer）：
    func longProc(wg *sync.WaitGroup) {
        done := make(chan bool)
        go func() {
            time.Sleep(10 * time.Second)
            done <- true
        }()
        t := time.After(5 * time.Second)
        select {
        case <-done:
            fmt.Println("finished")
        case <-t:
            fmt.Println("timeout")
        }
        wg.Done()
    } 
 
    //方法二（context）：
    func longProc2(wg *sync.WaitGroup) {
        done := make(chan bool)
        go func() {
            time.Sleep(10 * time.Second)
            done <- true
        }()
        ctx, _ := context.WithTimeout(context.Background(), 5 * time.Second)
        select {
        case <-done:
            fmt.Println("finished")
        case <-ctx.Done():
            fmt.Println("timeout")
        }
        wg.Done()
    }