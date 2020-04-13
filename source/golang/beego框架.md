### beego 框架

#### 一、下载 & 安装bee脚手架工具
     $ go get github.com/astaxie/beego
     $ go get github.com/beego/bee
     创建beego项目
     $ bee new <project-name>

#### 二、路由 & 控制器
      路由文件：routers/router.go
      添加新路由示例：
        beego.Router("/ajax", &controllers.AjaxController{},
            "get:GetAjax;post:PostAjax")
    
      添加新控制器文件
      基础控制器文件：controllers/base.go
      内容：
        type BaseController struct {
            beego.Controller
        }
        func (c *BaseController) GetParam(key, def string) string {
            param := c.Ctx.Request.FormValue(key)
            if param == "" {
                return def
            }
            return param
        }
        func (c *BaseController) PostParam(key, def string) string {
            param := c.Ctx.Request.PostFormValue(key)
            if param == "" {
                return def
            }
            return param
        }
        func (c *BaseController) JsonResp(data interface{}, encoding ...bool) {
            c.Data["json"] = data
            c.ServeJSON(encoding...)
        }
        func (c *BaseController) Prepare() {
            //此处放置hook中间件钩子
            middlewares.AccessAllow(c.Ctx)
        }

      业务控制器文件：controllers/ajax.go
      内容：
       type (
            AjaxController struct {
                BaseController
            }
            PostResponse struct {
                Resp interface{} `json:"resp"`
            }
       )
       //自定义get接口
       func (c *AjaxController) GetAjax() {
            key := c.GetParam("key", "未填写")
            ret := &PostResponse{
                Resp: "[GET]" + key + time.Now().Format("15:04:05"),
            }
            c.JsonResp(ret)
       }
       //自定义post接口
       func (c *AjaxController) PostAjax() {
            key := c.PostParam("key", "未填写")
            ret := &PostResponse{
                Resp: "[POST]" + key + time.Now().Format("15:04:05"),
            }
            c.JsonResp(ret)
       }

#### 三、中间件（例如：解决cors跨域访问不能）
      自定义中间件文件：controllers/middlewares/cors.go
      内容：
            import "github.com/astaxie/beego/context"
            func AccessAllow(ctx *context.Context) {
                ctx.ResponseWriter.Header().Set("Access-Control-Allow-Origin", "*")
            }

#### 四、更改web端口
    方法一、修改conf/app.conf文件下httpport=8080
    方法二、给main.go的beego.Run(":8080")增加参数

#### 五、数据库model
    1、初始化数据库连接
        文件：models/db/database.go
        内容：
        import (
            "github.com/astaxie/beego"
            "github.com/astaxie/beego/orm"
            _ "github.com/go-sql-driver/mysql" //勿忘
        )
            
        func init() {
            dbhost := beego.AppConfig.String("dbhost")
            dbport := beego.AppConfig.String("dbport")
            dbuser := beego.AppConfig.String("dbuser")
            dbpassword := beego.AppConfig.String("dbpassword")
            dbname := beego.AppConfig.String("dbname")
            //构造conn连接
            //用户名:密码@tcp(url地址)/数据库
            conn := fmt.Sprintf("%s:%s@tcp(%s:%s)/%s?parseTime=true&charset=utf8",
                dbuser, dbpassword, dbhost, dbport, dbname)
            //注册mysql Driver
            err := orm.RegisterDriver("mysql", orm.DRMySQL)
            if err != nil {
                panic(err)
            }
            //注册数据库连接
            err = orm.RegisterDataBase("default", "mysql", conn)
            if err != nil {
                panic(err)
            }
            fmt.Printf("数据库连接成功！%s\n", conn)
        }
        
    2、启动初始化连接
        文件：main.go
        内容：import _ "project-name/models/db"

    3、创建自定义model
        文件：models/ebk_tb_test.go（建议文件名与表名相同）
        内容：
        type (
            TbModel struct {
                Orm orm.Ormer
            }
            //☆这里的起名很关键，必须和表名一致
            TbTest struct {
                Id int `db:"id"`
                Name string `db:"name"`
                T1 int8 `db:"t1"`
                T2 int8 `db:"t2"`
                T3 int8 `db:"t3"`
                UpdateAt time.Time `db:"update_at"`
            }
        )
 
        //注册数据表模型
        func init() {
            orm.RegisterModelWithPrefix("ebk_", new(TbTest))
        }
        
        func NewTbModel() *TbModel {
            return &TbModel{
                Orm: orm.NewOrm(),
            }
        }
        
        //查询单条记录(找不到会报error)
        func (m *TbModel) Find(id int) (*TbTest, error) {
            row := &TbTest{
                Id: id,
            }
            err := m.Orm.Read(row)
            if err != nil {
                return nil, err
            }
            return row, nil
        }
        
        //查询多条数据
        func (m *TbModel) Select(minId, maxId int) ([]*TbTest, error) {
            result := []*TbTest{}
            query := fmt.Sprintf("SELECT * FROM `ebk_tb_test` WHERE id BETWEEN %d AND %d", 
                minId, maxId)
            _, err := m.Orm.Raw(query).QueryRows(&result)
            return result, err
        }
        
        //获取记录数量
        func (m *TbModel) Count(minId, maxId int) (int, error) {
            result := 0
            query := fmt.Sprintf("SELECT count(*) FROM `ebk_tb_test` WHERE id BETWEEN %d AND %d", 
                minId, maxId)
            err := m.Orm.Raw(query).QueryRow(&result)
            return result, err
        }
        
        //插入单条记录
        func (m *TbModel) Insert(name string, t1, t2, t3 int8) (int, error) {
            row := &TbTest{
                Name: name,
                T1: t1,
                T2: t2,
                T3: t3,
            }
            _, err := m.Orm.Insert(row)
            return row.Id, err
        }
        
        //批量插入记录
        func (m *TbModel) BatchInsert(data []*TbTest) error {
            _, err := m.Orm.InsertMulti(len(data), data)
            return err
        }
        
        //修改单条记录
        func (m *TbModel) Update(id int, name string) error {
            update := map[string]interface{}{
                "name": name,
            }
            _, err := m.Orm.QueryTable("ebk_tb_test").Filter("id", id).Update(update)
            return err
        }
        
        //删除单条记录
        func (m *TbModel) Delete(id int) error {
            _, err := m.Orm.QueryTable("ebk_tb_test").Filter("id", id).Delete()
            return err
        }
        
        //修改/删除多条记录
        func (m *TbModel) BatchUpdate(minId, maxId int, name string) error {
            query := fmt.Sprintf("UPDATE `ebk_tb_test` SET name='%s' WHERE id BETWEEN %d AND %d",
                name, minId, maxId)
            _, err := m.Orm.Raw(query).Exec()
            return err
        }