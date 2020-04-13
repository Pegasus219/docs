### 常用time操作

    const LAYOUT_DATETIME = "2006-01-02 15:04:05"
    const LAYOUT_DATE = "2006-01-02"
    const LAYOUT_TIME = "15:04:05"
    
    // 当前时间戳
    func nowTimestamp() int64 {
        return time.Now().Unix()
    }
    
    // 当前格式化时间
    func nowDatetime() string {
        return time.Now().Format(LAYOUT_DATETIME)
    }
    
    // 给定时间戳 -> 格式化时间
    func theDatetime(sec int64) string {
        return time.Unix(sec, 0).Format(LAYOUT_DATETIME)
    }
    
    // 给定格式化时间 -> 时间戳
    func theTimestamp(datetime string) int64 {
        loc, _ := time.LoadLocation("Local")
        theTime, _ := time.ParseInLocation(LAYOUT_DATETIME, datetime, loc)
        return theTime.Unix()
    }
    
    // 获取当前的年、月、日、时、分、秒
    type Date struct {
        Year int
        Month time.Month
        Day int
        Hour int
        Minute int
        Second int
    }
    func getdate() *Date {
        theTime := time.Now()
        return &Date{
            Year: theTime.Year(),
            Month: theTime.Month(),
            Day: theTime.Day(),
            Hour: theTime.Hour(),
            Minute: theTime.Minute(),
            Second: theTime.Second(),
        }
    }
    
    // 获取当天零点时间
    func getTodayStart() time.Time {
        loc, _ := time.LoadLocation("Local")
        y, m, d := time.Now().Date()
        return time.Date(y, m, d, 0, 0, 0, 0, loc)
    }
    
    // 获取y年m月d天后的时间
    func getDatetimeAfter(y, m, d int) time.Time {
        return time.Now().AddDate(y, m, d)
    }