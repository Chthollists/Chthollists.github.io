---
title: Python高阶语法系列CP5——Excel文件操作
toc: true
reward: true
declare: true
gitalkenable: true
categories:
  - Python
  - Advanced
tags:
  - Python
  - Excel
abbrlink: d497e4d0
date: 2020-08-20 13:14:18
top:
---

---

* Python高阶语法系列CP5——Excel文件操作
* CP5主要分享了
  * Python操作Excel文件的相关模块
  * xlrd模块 ：读xls文件
  * xlwt模块：写xls文件，只能新建一个文件写入，无法追加内容
  * xlutils.copy()函数：复制Excel文件，复制后可用xlwt来覆盖写入
  * xlsxwriter模块：写入xlsx文件
  * openpyxl模块：读、写、追加xlsx、xlsm文件

<!-- more -->

# Python高阶语法系列CP5——Excel文件操作

## 四、Excel操作

* 时间：2020.08.22~2020.08.23

* Excel操作相关模块
  * xlrd
  * xlwt
  * xlsxwriter
  * openpyxl

### 1. xlrd模块

* 时间：2020.08.22

#### 1.1 读取、导入Excel

* 读取文件

  > 保留原格式打开：formatting_info = True 
  >
  > ```python
  > import xlrd
  > 
  > # 返回 xlrd.book.Book() 对象——book
  > book = xlrd.open_workbook('xxx.xlsx', formatting_info = True)
  > ```

* 读取指定sheet

  > ```python
  > # 根据索引获取
  > sheet = book.sheet_by_index(0)
  > # 根据名字获取
  > sheet = book.sheet_by_name(name)  
  > # 根据索引获取
  > sheet =  book.sheets()[0] 
  > 
  > # 返回 xlrd.sheet.Sheet() 对象——sheet
  > ```

#### 1.2 获取Excel的信息和数据

* 获取表名、行数、列数

  > ```python
  > sheet.name
  > sheet.nrows 
  > sheet.ncols
  > ```

* 获取某单元格、一行、一列的数据：

  * 返回单元格(m,n)的数据：sheet.cell(m,n) 

    > ```python
    > # 返回单元格(m,n)的数据
    > sheet.cell(m,n) 
    > ```
    >
    > 返回数据类型为: 'xlrd.sheet.Cell' ，格式为: text:数据

  * 返回第m行，或者第m列的数据：sheet.row(m) 、sheet.col(m)

    > ```python
    > # 返回第m行的数据
    > sheet.row(m)
    > # 返回第m列的数据
    > sheet.col(m)
    > ```
    >
    > 返回数据类型为: list，list中的每个元素为'xlrd.sheet.Cell' 

  * sheet.cell_value(m,n) 、sheet.row_values(m, col_strat, col_end)、sheet.clo_values(m, row_strat, row_end)

    > ```python
    > sheet.cell_value(m,n) 
    > sheet.row_values(m, col_strat, col_end)
    > sheet.clo_values(m, row_strat, row_end)
    > ```
    >
    > 返回单元格、某一列、某一行的数据，数据类型为: str

---

### 2. xlwt模块

* 时间：2020.08.22

#### 2.1 写入Excel

* 创建Excel空对象

  > encoding 是编码，默认是ascii，即不能写中文。
  >
  > style_compression表示 是否压缩，0代表否，1代表是
  >
  > ```python
  > # 创建、获取excel空对象
  > work_book = xlwt.Workbook(encoding = "utf-8", style_compression = 0) 
  > ```

* 创建表单sheet

  > cell_overwrite_ok 表示单元格是否可以被覆盖，默认是False
  >
  > ```python
  > # 创建表单sheet
  > sheet = work_book .add_sheet(sheet名称, cell_overwrite_ok = True) 
  > ```

* 写入表头

  > 前两个参数，依次为行和列的序号设置——接着最后一个参数为label，单元填充
  >
  > ```python
  > # 第一列标签
  > sheet.write(0, 0, label = '标签') 
  > # 第二列标签
  > sheet.write(0, 1, label = '标签')  
  > ```

* 写入正文数据

  > ```python
  > # 在第m+1行，n+1列写入值value
  > sheet.write(m, n, value)  
  > 
  > # 保存文件
  > work_book.save(文件名---可以包含路径)
  > ```


#### 2.2 注意

* xlrd和xlwt模块都只能处理xls文件，不能处理xlsx文件
* 两者都只能处理小文件，不能处理大文件
* xlwt模块每次写入文件只能创建一个新文件，不能修改原文件(在原文件上追加内容)

#### 2.3 xlutils模块的copy函数

> 弥补xlwt不能修改原文件的问题
>
> 先读取原文件，再用xlutils.copy复制一份
>
> 最后对复制的文件实现写入

* 导入模块

  > ```python
  > import xlwt
  > import xlrd
  > import xlutils.copy
  > ```

* xlrd读取文件

  > formatting_info = True  表示保留原格式打开文件
  >
  > ```python
  > # 打开文件
  > wb = xlrd.open_workbook("xxx.xls", formatting_info = True)   
  > ```

* xlutils复制文件

  > ```python
  > # 复制
  > wb_copy = xlutils.copy.copy(wb) 
  > ```

* 获取文件

  > ```python
  > # 根据索引获取
  > sheet = wb_copy .sheet_by_index(0)  
  > # get_sheet()疑似会报错
  > sheet = wb_copy .get_sheet(0)   
  > ```

* xlwt写入数据---覆盖修改

  > ```python
  > sheet.write(m, n, "I love you!")   
  > ```

* 保存

  > ```python
  > # 保存
  > wb_copy .save("xxx.xls")   
  > ```

---

### 3. xlsxwriter模块

* 时间：2020.08.23

* 特点

  * 写入xlsx文件，不能处理xls文件
  * 可处理大文件

* 创建一个Excel文件

  > ```python
  > workbook = xlsxwriter.Workbook('xxx.xlsx')
  > ```

* 创建一个工作表sheet对象

  > ```python
  > worksheet = workbook.add_worksheet()
  > ```

* 格式设置

  > ```python
  > # 设定第一列（A）宽度为20像素
  > worksheet.set_column('A:A',20)
  > 
  > # 定义一个加粗的格式对象
  > bold = workbook.add_format({'bold':True})
  > ```

* 向单元格写入数据

  > ```python
  > # 向A1单元格写入'Hello'
  > worksheet.write('A1','Hello')
  > 
  > # 用行列表示法写入
  > worksheet.write(2,0,10)
  > 
  > # 求A3、A4单元格的和并写入A5单元格，由此可见可以直接使用公式
  > worksheet.write(4,0,'=SUM(A3:A4)')
  > ```

* 保存文件xlsx

  > ```python
  > # 保存
  > workbook.close()
  > ```

---

### 4. openpyxl模块

* 时间：2020.08.23

* 特点
  * 既可以读取文件，也可以写文件，还可以修改文件
  * 对象为xlsx、xlsm文件，不能处理xls文件
  * 可以处理大文件

#### 4.1 读取

* 读取xlsx文件

  > ```python
  > wb = openpyxl.load_workbook('xxx.xlsx') 
  > ```

* 获得指定名称的页

  > ```python
  > # 根据表名
  > ws = wb.get_sheet_by_name('Sheet') 
  > # 根据索引
  > ws = wb.worksheets[0]
  > # 类似下标的概念
  > ws = wb['Sheet1']
  > ```

* 获得行数、列数，类型为迭代器

  > ```python
  > #  获得行数、列数
  > nrows = ws.rows
  > ncols = ws.columns
  > # nrows 、ncols   cell 分别为页名，行或列的值
  > # 类型为迭代器
  > 如：
  > ((<Cell Sheet1.A1>, <Cell Sheet1.B1>, <Cell Sheet1.C1>), 
  > 
  > (<Cell Sheet1.A2>, <Cell Sheet1.B2>, <Cell Sheet1.C2>), 
  > ...)
  > ```

* 读取单元格数据

  > ```python
  > # 读取一行   ws.rows是个元组
  > data = [cell.value for cell in ws.rows[0]]          
  > # list(ws.rows)
  > 
  > # 读取一列
  > data = [cell.value for cell in ws.columns[0]] 
  > 
  > # 读取某单元格
  > # 根据名称获取、访问——可以切片操作
  > ws['A1':'B3']    
  > # 根据索引，读取第m+1行、第n+1列的数据
  > ws.rows[m] [n].value  
  > 
  > # 读取第2行，第1列数据
  > ws.cell('a2').value    
  > # 读取第m行，第n列数据
  > ws.cell(rows=m, columns=n).value  
  > ```

* 读取整个sheet的数据

  > ```python
  > # 读取整个sheet
  > for row in nrows:
  > # row 每一行
  > line = [col.value for col in row] # 取值
  > print(line)
  > ```

* 复制sheet

  > ```python
  > ws_source = wb.active
  > ws_target = wb.copy_worksheet(ws_source)
  > ```

#### 4.2 写入数据

* 新建工作簿

  > ```python
  > wb_new = openpyxl.Workbook()
  > ```

* 添加 sheet 页

  > ```python
  > # 默认插到最后
  > wb_new.create_sheet('Sheet1')  
  > # 插入到最开始的位置
  > wb_new.create_sheet('Sheet1', 0)  
  > ```

* 获得当前活跃的工作页，默认为第一个工作页

  > ```python
  > ws_new = wb_new.active 
  > 
  > ws_new = wb_new.get_activeg_sheet()
  > ```

* 写入数据——行，列，值 这里是从1开始计数的

  > ```python
  > ws_new.cell(rows=m,columns=n,value)  
  > # 在第m行，第n列写入value值数据
  > ws_new.cell(rows=m,columns=n,value=xxx) 
  > 
  > # 在第2行，第1列直接赋值----value值数据
  > ws_new['a2'] = value  
  > # 公式赋值
  > ws_new['a2'] = '=SUM(A1:A10)'  
  > ```

* 保存文件

  > ```python
  > ws_new.save('xxx.xlsx')
  > ```

* 追加数据

  > ```python
  > # 获得行数
  > nrows = table.max_row 
  > # 获得列数
  > ncolumns = table.max_column 
  > # 在末尾追加数据
  > ws.cell(nrows+1,ncolumns 1).value = value
  > ```

---