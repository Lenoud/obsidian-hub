# Python Excel 操作

使用 openpyxl 库遍历 Excel 表格数据，按条件筛选统计。

## 代码

```python
import openpyxl

# 打开 Excel 文件
workbook = openpyxl.load_workbook(r'F:\桌面\就业统计\毕业生信息.XLSX')
sheet = workbook.active

# 老师和班级分类
teacher = ["王思思", "曾子衿", "袁丹", "范焕", "谭紫逸", "彭乐春"]
banji = [
    ["信安2001班", "信安2002班"],
    ["大数据2001班", "大数据2002班", "大数据2003班", "云计算2001班", "云计算2002班"],
    ["计网2001班", "计网2002班", "计网2003班", "计网2004班"],
    ["计应2001班", "计应2002班", "信安2003班", "计应2005班"],
    ["计应2003班", "计应2004班", "大数据2004班", "计网2005班"],
    ["计网501801班"]
]

i = 0
zong = 0
for tc in range(len(teacher)):
    for bj in range(len(banji[i])):
        local_data = []

        for row in sheet.iter_rows(min_row=1, max_row=906, min_col=1, max_col=23, values_only=True):
            b_cell_value = row[1]   # 辅导员姓名
            d_cell_value = row[3]   # 班级列
            e_cell_value = row[4]   # 学生姓名
            t_cell_value = row[19]  # 毕业去向

            laoshi = b_cell_value == teacher[tc]
            bji = d_cell_value == banji[i][bj]
            biyequxiang = t_cell_value != "求职中"

            if laoshi and bji and biyequxiang:
                local_data.append((b_cell_value, row[21], e_cell_value))

        bd = len(local_data)
        zong = zong + bd
        print(teacher[tc], "的", banji[i][bj], "总就业人数：", bd)
    i = i + 1

print("就业总人数:", zong)
```

## 说明

| 列索引 | 字段 |
|--------|------|
| `row[1]` (B列) | 辅导员姓名 |
| `row[3]` (D列) | 班级 |
| `row[4]` (E列) | 学生姓名 |
| `row[19]` (T列) | 毕业去向 |
| `row[21]` (V列) | 单位所在地 |
