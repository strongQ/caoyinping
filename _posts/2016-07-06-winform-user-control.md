---
layout:           post
title:            Winform运用
description:      自定义控件
keywords:         winform
categories :      [winform]
tags :            [winform, 工作]
---

自定义控件的属性点击选择可执行程序是个难点。

------------------------
## 1.点击选择程序，再次点击执行程序。
       [Description("设置执行程序")]　//显示在属性设计视图中的描述
        [Editor(typeof(MyEditor), typeof(UITypeEditor))]
        public string SelectExe
        {
            get
            { return selectExe; }
            set
            {
               
                if (File.Exists(value))
                {
                    picExeImg.Image = GetIconByFileName(value, true).ToBitmap();
                    lblExeName.Text = Path.GetFileName(value);
                }
                    selectExe = value;
                    picExeImg.Tag = value;                                 
            }
        }


        public class MyEditor : UITypeEditor
        {
            private IWindowsFormsEditorService editorService = null;

            public override UITypeEditorEditStyle GetEditStyle(System.ComponentModel.ITypeDescriptorContext context)
            {
                return UITypeEditorEditStyle.Modal;
            }

            public override object EditValue(
                ITypeDescriptorContext context,
                IServiceProvider provider,
                object value)
            {
                if (provider != null)
                {
                    editorService =
                        provider.GetService(typeof(IWindowsFormsEditorService))
                        as IWindowsFormsEditorService;
                }

                if (editorService != null)
                {
                    OpenFileDialog ofd = new OpenFileDialog();
                    ofd.Title = "请选择执行程序";
                    ofd.Filter = "可执行文件 (*.exe)|*.exe";
                    ofd.FileName = value.ToString();
                    if (ofd.ShowDialog() == DialogResult.OK)
                    {
                        value = ofd.FileName;

                    }
                }
                return value;
            }

            public override bool GetPaintValueSupported(
                ITypeDescriptorContext context)
            {
                return false;
                //return true;
            }


            public override void PaintValue(PaintValueEventArgs e)
            {
                //TODO:
            }


        }
