以下是一些常用的 HBase Shell 命令

```markdown
# 常用 HBase Shell 命令
hbase shell
```

## 查看命令帮助
```bash
help
```

## 列出所有表
```bash
list
```

## 创建表
```bash
create '<table_name>', '<column_family>'
```

示例：

```bash
create 'my_table', 'cf1'
```

## 描述表
```bash
describe '<table_name>'
```

## 查看表的详细信息
```bash
show_filters
```

## 启用表
```bash
enable '<table_name>'
```

## 禁用表
```bash
disable '<table_name>'
```

## 删除表
```bash
disable '<table_name>'
drop '<table_name>'
```

## 插入数据
```bash
put '<table_name>', '<row_key>', '<column_family:column>', '<value>'
```

示例：

```bash
put 'my_table', 'row1', 'cf1:col1', 'value1'
```

## 获取数据
```bash
get '<table_name>', '<row_key>'
```

示例：

```bash
get 'my_table', 'row1'
```

## 扫描表
```bash
scan '<table_name>'
```

示例：

```bash
scan 'my_table'
```

## 删除列
```bash
delete '<table_name>', '<row_key>', '<column_family:column>'
```

示例：

```bash
delete 'my_table', 'row1', 'cf1:col1'
```

## 删除行
```bash
deleteall '<table_name>', '<row_key>'
```

示例：

```bash
deleteall 'my_table', 'row1'
```

## 清空表
```bash
truncate '<table_name>'
```

## 创建命名空间
```bash
create_namespace '<namespace_name>'
```

示例：

```bash
create_namespace 'my_namespace'
```

## 列出命名空间
```bash
list_namespace
```

## 删除命名空间
```bash
drop_namespace '<namespace_name>'
```

示例：

```bash
drop_namespace 'my_namespace'
```

## 查询表的所有 regions
```bash
show_filters '<table_name>'
```

```text
你可以根据具体需求修改这些命令。
```
