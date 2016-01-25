---
layout:    post
title:     c# NPOI的运用
category:  excel操作
tags:      [excel,数据,c#]

---

回顾一下npoi的简单应用

## 首先引入dll

### 然后看代码哦

{% highlight c# %}
using (FileStream stream = File.OpenRead(@"c:\cust.xls"))
            {
                HSSFWorkbook workbook = new HSSFWorkbook(stream);
                HSSFSheet sheet = workbook.GetSheetAt(0);
                //第一行是表头，所以不导入
                for (int i = 1; i <= sheet.LastRowNum; i++)
                {
                    T_Customers cust = new T_Customers();
                    HSSFRow row = sheet.GetRow(i);
                    HSSFCell cell门店编号 = row.GetCell(0);
                    cust.DeptNo = cell门店编号.StringCellValue;
                    //因为电话号码可能不存在，所以需要判断
                    if (row.GetCell(2) == null)
                    {
                        cust.TelPhone = "";//todo
                    }
                    else
                    {
                        cust.TelPhone = row.GetCell(2).StringCellValue;
                    }
                    HSSFCell cellMobilePhone = row.GetCell(3);
                    //文件中有的电话错误的设置为了数字类型
                    if (cellMobilePhone.CellType == HSSFCell.CELL_TYPE_STRING)
                    {
                        cust.MobilePhone = cellMobilePhone.StringCellValue;
                    }
                    else
                    {
                        cust.MobilePhone = cellMobilePhone.NumericCellValue.ToString();
                    }                   
                    cust.CarNum = row.GetCell(4).StringCellValue;
                    cust.BracketNum = row.GetCell(5).StringCellValue;
                    cust.BuyDate = row.GetCell(6).DateCellValue;
                    cust.Brand = row.GetCell(7).StringCellValue;
                    cust.TypeNum = row.GetCell(8).StringCellValue;
                    cust.PostCode = "";
                    cust.Email = "";
                    cust.Remarks = "";
                    cust.Suggestion = "";
                    cust.Address = "";
                    new T_CustomersBLL().AddNew(cust);
                }
            }           
{% endhighlight %}