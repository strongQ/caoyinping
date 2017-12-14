---
layout:    post
title:     c# NPOI的运用
category:  excel操作
tags:      [excel,数据,c#]

---

回顾一下npoi的简单应用

## 首先引入dll

### 例子1

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

### 例子2

       //导出excel文档
        private async void ExportExcel()
        {
            string excelFile = DialogHelper.OpenSavePath("处置记录Excle保存路径", "处置记录");
            if (string.IsNullOrEmpty(excelFile)) return;

            HSSFWorkbook hssfWorkbook = new HSSFWorkbook();
            ISheet hssfSheet = hssfWorkbook.CreateSheet("sheet1");
            try
            {
                #region 列头

                //设置单元格字体格式
                ICellStyle headCellStyle = hssfWorkbook.CreateCellStyle();
                headCellStyle.Alignment = HorizontalAlignment.Center;
                headCellStyle.VerticalAlignment = VerticalAlignment.Center;
                headCellStyle.WrapText = true;
                IFont headFont = hssfWorkbook.CreateFont();
                headFont.FontName = "宋体";
                headFont.FontHeightInPoints = 14;
                headFont.IsBold = true;
                headCellStyle.SetFont(headFont);

                IRow headRow = hssfSheet.CreateRow(0);
                for (int i = 0; i < TitleColumnDic.Count; i++)
                {
                    // 设置列宽,excel列宽每个像素是1/256
                    hssfSheet.SetColumnWidth(i, TitleColumnDic.ElementAt(i).Value * 256);
                    ICell headCell = headRow.CreateCell(i);
                    headCell.SetCellType(CellType.String);
                    headCell.SetCellValue(TitleColumnDic.ElementAt(i).Key);
                    headCell.CellStyle = headCellStyle;
                }

                #endregion

                #region 填充数据

                //设置单元格字体格式
                ICellStyle contentCellStyle = hssfWorkbook.CreateCellStyle();
                contentCellStyle.Alignment = HorizontalAlignment.Left;
                contentCellStyle.VerticalAlignment = VerticalAlignment.Center;
                contentCellStyle.WrapText = true;
                IFont contentFont = hssfWorkbook.CreateFont();
                contentFont.FontName = "宋体";
                contentFont.FontHeightInPoints = 12;
                contentCellStyle.SetFont(contentFont);

                if (HandleRecordList != null && HandleRecordList.Count > 0)
                {
                    //图片后缀集合
                    List<string> picExt = new List<string>() { ".emf", ".wmf", ".pict", ".jpeg",
                                                               ".png", ".dib", ".gif", ".tiff",
                                                               ".eps", ".bmp", ".wpg", ".jpg" };
                    for (int i = 0; i < HandleRecordList.Count; i++)
                    {
                        //第0行为列头,从第1行开始
                        IRow contentRow = hssfSheet.CreateRow(i + 1);
                        //反馈时间
                        ICell firstCell = contentRow.CreateCell(0);
                        firstCell.SetCellValue(HandleRecordList[i].ReplyContentModel.ReplyTime.ToString("yyyy/MM/dd HH:mm:ss"));
                        firstCell.CellStyle = contentCellStyle;
                        //反馈部门
                        ICell secondCell = contentRow.CreateCell(1);
                        secondCell.SetCellValue(HandleRecordList[i].ReplyContentModel.ReplyStationName ?? "");
                        secondCell.CellStyle = contentCellStyle;
                        //反馈内容
                        ICell thirdCell = contentRow.CreateCell(2);
                        if (string.IsNullOrEmpty(HandleRecordList[i].ReplyContentModel.ReplyContent)
                            && HandleRecordList[i].ReplyContentModel.Accessorys.Count > 0)
                        {
                            int xOffset = 80;  //图片X轴偏移量
                            foreach (var accessory in HandleRecordList[i].ReplyContentModel.Accessorys)
                            {
                                string picPath = await FileDownloadHelper._fileHelper.GetFileLocalPath(accessory, MsgService);
                                if (!string.IsNullOrEmpty(picPath) && picExt.Contains(Path.GetExtension(picPath)))
                                {
                                    //设置行高 ,excel行高度每个像素点是1/20
                                    contentRow.Height = 30 * 20;

                                    HSSFPatriarch hssfPatriarch = (HSSFPatriarch)hssfSheet.CreateDrawingPatriarch();
                                    //255  与行高等高
                                    HSSFClientAnchor hssfClientAnchor = new HSSFClientAnchor(0, 0, xOffset, 255, 2, i + 1, 2, i + 1);
                                    IPicture hssfPicture = hssfPatriarch.CreatePicture(hssfClientAnchor, GetPictureIndex(picPath, hssfWorkbook));

                                    xOffset += xOffset;
                                }
                            }
                        }
                        else
                        {
                            //文本内容
                            thirdCell.SetCellValue(HandleRecordList[i].ReplyContentModel.ReplyContent ?? "");
                        }
                        thirdCell.CellStyle = contentCellStyle;
                        ////反馈类型
                        //ICell fourthCell = contentRow.CreateCell(3);
                        //fourthCell.SetCellValue(HandleRecordList[i].ReplyContentModel.ReplyId ?? "");
                        //fourthCell.CellStyle = contentCellStyle;
                        ////反馈内容类型
                        //ICell fifthCell = contentRow.CreateCell(4);
                        ////ReplyList | PersonAlarmEvent
                        //fifthCell.SetCellValue(HandleRecordList[i].EventName.ToString());
                        //fifthCell.CellStyle = contentCellStyle;
                    }
                }

                #endregion


                //文件保存
                using (FileStream fileStream = new FileStream(excelFile, FileMode.Create))
                {
                    hssfWorkbook.Write(fileStream);
                    fileStream.Close();
                }
                hssfWorkbook = null;
            }
            catch (Exception)
            {

            }
        }
