# Python 自动化办公 — class 形式

使用 Python openpyxl 库以面向对象方式统计 Excel 数据，按辅导员和班级分类汇总就业人数。

## 就业统计类

```python
import openpyxl

class EmploymentStatistics:
    def __init__(self, file_path, teacher_list, class_list, fdy_name_int, cla_name_int, biye_int):
        self.file_path = file_path
        self.teacher_list = teacher_list
        self.class_list = class_list
        self.fdy_name_int = fdy_name_int
        self.cla_name_int = cla_name_int
        self.biye_int = biye_int

    def calculate_employment_statistics(self):
        workbook = openpyxl.load_workbook(self.file_path)
        sheet = workbook.active
        employment_count = {}

        for row in sheet.iter_rows(min_row=1, max_row=906, min_col=1, max_col=23, values_only=True):
            b_cell_value = row[self.fdy_name_int]  # 辅导员姓名
            d_cell_value = row[self.cla_name_int]   # 班级列
            t_cell_value = row[self.biye_int]  # 毕业去向

            for i in range(len(self.teacher_list)):
                for j in range(len(self.class_list[i])):
                    if (
                        b_cell_value == self.teacher_list[i]
                        and d_cell_value == self.class_list[i][j]
                        and t_cell_value != "求职中"
                    ):
                        key = (self.teacher_list[i], self.class_list[i][j])
                        employment_count[key] = employment_count.get(key, 0) + 1

        return employment_count

    def print_employment_statistics(self):
        result = self.calculate_employment_statistics()
        sorted_result = sorted(result.items(), key=lambda item: self.teacher_list.index(item[0][0]))

        for key, count in sorted_result:
            teacher_name, class_name = key
            print(teacher_name, "的", class_name, "总就业人数:", count)

        total_count = sum(result.values())
        print("就业总人数:", total_count)
```

## 使用示例

```python
file_path = r'F:\桌面\就业统计\毕业生信息.XLSX'
teacher_list = ["王思思", "曾子衿", "袁丹", "范焕", "谭紫逸", "彭乐春"]
class_list = [
    ["信安2001班", "信安2002班"],
    ["大数据2001班", "大数据2002班", "大数据2003班", "云计算2001班", "云计算2002班"],
    ["计网2001班", "计网2002班", "计网2003班", "计网2004班"],
    ["计应2001班", "计应2002班", "信安2003班", "计应2005班"],
    ["计应2003班", "计应2004班", "大数据2004班", "计网2005班"],
    ["计网501801班"]
]
fdy_name_int = 1   # 辅导员列 B
cla_name_int = 3   # 班级列 D
biye_int = 19      # 毕业去向列 T

statistics = EmploymentStatistics(file_path, teacher_list, class_list, fdy_name_int, cla_name_int, biye_int)
statistics.print_employment_statistics()
```
