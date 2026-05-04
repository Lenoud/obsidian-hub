# go-zero sqlx CRUD 操作

go-zero 框架内置 sqlx 数据库操作，无需额外 ORM，直接编写 SQL。

参考：https://go-zero.dev/

## 连接配置

```yaml
# etc/config.yaml
DataSource: "user:password@tcp(127.0.0.1:3306)/dbname?charset=utf8mb4&parseTime=true&loc=Local"
```

```go
// 在 ServiceContext 中初始化
type ServiceContext struct {
    Config  config.Config
    UserModel model.UserModel
}

func NewServiceContext(c config.Config) *ServiceContext {
    conn := sqlx.NewMysql(c.DataSource)
    return &ServiceContext{
        Config:     c,
        UserModel:  model.NewUserModel(conn, c.CacheRedis),
    }
}
```

## goctl 自动生成 CRUD

```bash
# 通过 DDL 文件生成 model 代码
goctl model mysql ddl -src user.sql -dir model

# 通过数据库连接直接生成
goctl model mysql datasource -url "user:password@tcp(127.0.0.1:3306)/dbname" -table "user" -dir model
```

生成的 model 自动包含 `Insert`、`FindOne`、`Update`、`Delete` 方法。

## 手动编写 Model

```go
type (
    UserModel interface {
        Insert(data *User) (sql.Result, error)
        FindOne(id int64) (*User, error)
        FindOneByName(name string) (*User, error)
        Update(data *User) error
        Delete(id int64) error
    }

    defaultUserModel struct {
        conn  sqlx.SqlConn
        table string
    }

    User struct {
        Id        int64     `db:"id"`
        Name      string    `db:"name"`
        Age       int64     `db:"age"`
        Email     string    `db:"email"`
        CreatedAt time.Time `db:"created_at"`
        UpdatedAt time.Time `db:"updated_at"`
    }
)
```

## 创建（Insert）

```go
// 插入单条
func (m *defaultUserModel) Insert(data *User) (sql.Result, error) {
    query := fmt.Sprintf("INSERT INTO %s (name, age, email) VALUES (?, ?, ?)", m.table)
    return m.conn.Exec(query, data.Name, data.Age, data.Email)
}
```

## 查询（Select）

```go
// 按主键查询
func (m *defaultUserModel) FindOne(id int64) (*User, error) {
    var resp User
    query := fmt.Sprintf("SELECT id, name, age, email FROM %s WHERE id = ? LIMIT 1", m.table)
    err := m.conn.QueryRow(&resp, query, id)
    return &resp, err
}

// 按条件查询
func (m *defaultUserModel) FindOneByName(name string) (*User, error) {
    var resp User
    query := fmt.Sprintf("SELECT id, name, age, email FROM %s WHERE name = ? LIMIT 1", m.table)
    err := m.conn.QueryRow(&resp, query, name)
    return &resp, err
}

// 查询多条
func (m *defaultUserModel) FindByAge(age int64) ([]*User, error) {
    var resp []*User
    query := fmt.Sprintf("SELECT id, name, age, email FROM %s WHERE age > ?", m.table)
    err := m.conn.QueryRows(&resp, query, age)
    return resp, err
}
```

## 更新（Update）

```go
func (m *defaultUserModel) Update(data *User) error {
    query := fmt.Sprintf("UPDATE %s SET name = ?, age = ?, email = ? WHERE id = ?", m.table)
    _, err := m.conn.Exec(query, data.Name, data.Age, data.Email, data.Id)
    return err
}
```

## 删除（Delete）

```go
func (m *defaultUserModel) Delete(id int64) error {
    query := fmt.Sprintf("DELETE FROM %s WHERE id = ?", m.table)
    _, err := m.conn.Exec(query, id)
    return err
}
```

## 在 Logic 层使用

```go
func (l *GetUserLogic) GetUser(req *types.IdReq) (resp *types.UserReply, err error) {
    user, err := l.svcCtx.UserModel.FindOne(req.Id)
    if err != nil {
        return nil, err
    }
    return &types.UserReply{
        Id:    user.Id,
        Name:  user.Name,
        Age:   user.Age,
        Email: user.Email,
    }, nil
}
```

## 说明

| 方法 | 用途 |
| --- | --- |
| `conn.Exec()` | 执行 INSERT/UPDATE/DELETE，返回 `sql.Result` |
| `conn.QueryRow()` | 查询单条，扫描到 struct |
| `conn.QueryRows()` | 查询多条，扫描到 struct 切片 |
| `conn.Transact()` | 事务操作 |
