### golang module包管理

*版本要求：>=go1.11（建议>=go1.13）

#### 一、使用步骤：
    在main根目录下：
    1、$ go mod init <项目名>
    ——打开生成的go.mod文件，添加replace（作用相当于glide下repo）
    2、$ go mod vendor 
    ——自动生成go.sum文件，并修改替换go.mod中replace的包的版本号，并加载必要的包到vendor（这仅是为了方便IDE中开发使用）
    3、$ go build main.go
    ——成功

#### 二、一些关键的环境变量
    1、查看go的环境变量：$ go env
    2、设置go的环境变量：$ go env -w <变量名>=<变量值>
        GO111MODULE=on （*go1.13版本开始，默认auto）
        GOPROXY=https://goproxy.cn,direct （国内镜像代理）
        GOPRIVATE=*.gitlab.com （针对本地仓库的包，设置不走代理）
        GONOPROXY=*.gitlab.com（针对本地仓库的包，设置不走代理）
        GOSUMDB=sum.golang.org （镜像的hash校验）
        GONOSUMDB=*.gitlab.com （将本地仓库的包，跳过hash校验）

#### 三、如何加载本地仓库的包
    修改go.mod文件，例：
    replace (
        github.com/kataras/iris => *.gitlab.com/backend/iris latest
        xxxx.com/cloud/sdk => *.gitlab.com/cloud/sdk latest
    )
    *注、latest（在go1.12版本以上可用）表示最新版本，也可用hash git版本号指定某个版本

#### 四、一些常见报错的处理
    1、404 not found
    ——指定仓库的包在goproxy.cn镜像地址中未找到
    ——解决：检查GOPRIVATE、GONOPROXY、GONOSUMDB设置
    
    2、cannot find module providing package ......
    ——可能是你的项目名称和加载的某个包的仓库名重名了
    ——解决：修改go.mod，把package设置成src下完整项目名xxxx.com/cloud/sdk