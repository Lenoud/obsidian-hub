# GORM CRUD 操作（Go 语言）

GORM 是 Go 语言最流行的 ORM 库，支持 MySQL、PostgreSQL、SQLite、SQL Server。

官方文档：https://gorm.io/zh_CN/docs/

## 连接数据库

```go
import (
    "gorm.io/driver/mysql"
    "gorm.io/gorm"
    "gorm.io/gorm/clause"
)

dsn := "user:password@tcp(127.0.0.1:3306)/dbname?charset=utf8mb4&parseTime=True&loc=Local"
db, err := gorm.Open(mysql.Open(dsn), &gorm.Config{})
```

## 模型定义

```go
type User struct {
    ID        uint           `gorm:"primarykey"`
    Name      string         `gorm:"column:name;type:varchar(100)"`
    Age       int            `gorm:"column:age"`
    Email     string         `gorm:"column:email;uniqueIndex"`
    CreatedAt time.Time
    UpdatedAt time.Time
    DeletedAt gorm.DeletedAt `gorm:"index"` // 软删除
}
```

## 创建（Create）

```go
// 创建单条
user := User{Name: "张三", Age: 25, Email: "zs@example.com"}
result := db.Create(&user)
fmt.Println(user.ID)         // 获取自增ID
fmt.Println(result.RowsAffected) // 影响行数

// 批量创建
users := []User{{Name: "李四"}, {Name: "王五"}}
db.CreateInBatches(&users, 100) // 每批100条

// Upsert（冲突更新）
db.Clauses(clause.OnConflict{
    Columns:   []clause.Column{{Name: "email"}},
    DoUpdates: clause.AssignmentColumns([]string{"name", "age"}),
}).Create(&user)

// 指定字段创建
db.Select("Name", "Age").Create(&user)
```

## 查询（Read）

```go
// 单条查询
var user User
db.First(&user, 1)                    // 按主键
db.First(&user, "name = ?", "张三")    // 按条件
db.Take(&user)                         // 不排序取一条
db.Last(&user)                         // 按主键降序取一条

// 多条查询
var users []User
db.Find(&users)                                    // 全部
db.Where("age > ?", 20).Find(&users)               // 条件
db.Where("name LIKE ?", "%张%").Find(&users)        // 模糊
db.Where("age IN ?", []int{20, 25, 30}).Find(&users)

// 排序 & 分页
db.Order("age desc").Limit(10).Offset(0).Find(&users)

// Select 指定字段
db.Select("name", "age").Find(&users)

// Count 统计
var count int64
db.Model(&User{}).Where("age > ?", 20).Count(&count)

// FirstOrCreate（不存在则创建）
db.Where(User{Name: "张三"}).FirstOrCreate(&user)
```

## 更新（Update）

```go
// 更新单个字段
db.Model(&user).Update("name", "新名字")

// 更新多个字段（struct）
db.Model(&user).Updates(User{Name: "新名字", Age: 30})

// 更新多个字段（map，推荐，可更新零值）
db.Model(&user).Updates(map[string]interface{}{"name": "新名字", "age": 0})

// 批量更新
db.Model(&User{}).Where("age < ?", 18).Update("status", "minor")

// 表达式更新
db.Model(&user).Update("age", gorm.Expr("age + ?", 1))
```

## 删除（Delete）

```go
// 删除单条（需有主键）
db.Delete(&user)

// 按主键删除
db.Delete(&User{}, 1)
db.Delete(&User{}, []int{1, 2, 3})

// 条件删除
db.Where("name LIKE ?", "%test%").Delete(&User{})

// 软删除（模型需有 DeletedAt 字段，查询时自动排除）
db.Unscoped().Find(&users)           // 包含软删除记录
db.Unscoped().Delete(&user)          // 永久删除
```

## 常用链式方法

| 方法 | 用途 |
| --- | --- |
| `db.Select()` | 选择字段 |
| `db.Where()` | 条件过滤 |
| `db.Or()` | OR 条件 |
| `db.Not()` | 排除条件 |
| `db.Order()` | 排序 |
| `db.Limit()` / `Offset()` | 分页 |
| `db.Group()` / `Having()` | 分组聚合 |
| `db.Joins()` | 关联查询 |
| `db.Preload()` | 预加载关联 |
| `db.Scopes()` | 复用条件 |
| `db.Raw()` / `db.Exec()` | 原生 SQL |

## 原生 SQL

```go
// 查询
var users []User
db.Raw("SELECT * FROM users WHERE age > ?", 20).Scan(&users)

// 执行
db.Exec("UPDATE users SET status = ? WHERE age < ?", "minor", 18)
```
