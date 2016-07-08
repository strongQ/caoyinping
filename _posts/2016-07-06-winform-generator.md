---
layout:           post
title:            界面生成器
description:      winform生成器
keywords:         winform
categories :      [winform]
tags :            [winform, 工作]
---

网上找的一个winform生成器

------------------------

## 1.根据model自动生成winform窗体，填写数据好点击ok触发获取全部对象的事件。
            WinFormGenerator.ClassForm a = WinFormGenerator.Generator.GenerateWinForm(typeof(User), null);
            a.GetAllEvent += a_GetAllEvent;
            
## 2.窗体部分代码
             void ClassForm_FormClosed(object sender, FormClosedEventArgs e)
        {
            instanceNum--;
        }
        private void btnOk_Click(object sender, EventArgs e)
        {
            if (Object == null && !ObjectType.IsValueType && ObjectType != typeof(string))
            {
                Object = Activator.CreateInstance(ObjectType);
            }
            if (ObjectType.IsValueType || ObjectType == typeof(string))
            {
                Object = Controls[2].Text;
                return;
            }
            var properties = ObjectType.GetProperties();
            int index = 0;
            for (int n = 0; n + 3 < Controls.Count; n += 2)
            {
                if (Controls[n + 3] is ComboBox)
                {
                    var combobox = Controls[n + 3] as ComboBox;
                    properties[ListOfIndex[index]].SetValue(Object,
                        Enum.Parse(properties[ListOfIndex[index]].PropertyType, combobox.SelectedItem.ToString()));
                }
                else if (Controls[n + 3] is CheckBox)
                {
                    var checkbox = Controls[n + 3] as CheckBox;
                    properties[ListOfIndex[index]].SetValue(Object, checkbox.Checked);
                }
                else if (Controls[n + 3] is Button)
                {
                    // dosomething
                }
              
                else
                {
                    if (!string.IsNullOrEmpty(Controls[n + 3].Text))
                        properties[ListOfIndex[index]].SetValue(Object, Convert.ChangeType(Controls[n + 3].Text, properties[ListOfIndex[index]].PropertyType));
                }
                index++;
            }
            if (instanceNum <= 1)
            {
                OnGetAllEvent();
                this.Close();
            }
        }
