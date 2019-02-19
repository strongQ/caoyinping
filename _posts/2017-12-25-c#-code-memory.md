---

layout:     post
title:      C#代码记录
description: 整理部分
keywords:   C#
categories: [C#]
tags:       [C#]

---

### 1、根据进程获取程序路径
       #region 获取进程路径

       private enum ProcessAccessFlags : uint
        {
            All = 0x001F0FFF,

            Terminate = 0x00000001,

            CreateThread = 0x00000002,

            VMOperation = 0x00000008,

            VMRead = 0x00000010,

            VMWrite = 0x00000020,

            DupHandle = 0x00000040,

            SetInformation = 0x00000200,

            QueryInformation = 0x00000400,

            Synchronize = 0x00100000,

            PROCESS_QUERY_LIMITED_INFORMATION= 0x1000
        }
       private static string GetExecutablePath(Process Process)
       {
           //If running on Vista or later use the new function
           if (Environment.OSVersion.Version.Major >= 6)
           {
               return GetExecutablePathAboveVista(Process.Id);
           }

           return Process.MainModule.FileName;
       }
       private static string GetExecutablePathAboveVista(int ProcessId)
       {
           var buffer = new StringBuilder(1024);
           IntPtr hprocess = OpenProcess(ProcessAccessFlags.PROCESS_QUERY_LIMITED_INFORMATION,
                                         false, ProcessId);
           if (hprocess != IntPtr.Zero)
           {
               try
               {
                   int size = buffer.Capacity;
                   if (QueryFullProcessImageName(hprocess, 0, buffer, out size))
                   {
                       return buffer.ToString();
                   }
               }
               finally
               {
                   CloseHandle(hprocess);
               }
           }
           throw new Win32Exception(Marshal.GetLastWin32Error());
       }

       [DllImport("kernel32.dll")]
       private static extern bool QueryFullProcessImageName(IntPtr hprocess, int dwFlags,
                      StringBuilder lpExeName, out int size);
       [DllImport("kernel32.dll")]
       private static extern IntPtr OpenProcess(ProcessAccessFlags dwDesiredAccess,
                      bool bInheritHandle, int dwProcessId);

       [DllImport("kernel32.dll", SetLastError = true)]
       private static extern bool CloseHandle(IntPtr hHandle);
       #endregion



### 2、获取xml信息
    private static Hashtable getSettings(string path)
        {
            Hashtable _ret = new Hashtable();
            if (File.Exists(path))
            {
                StreamReader reader = new StreamReader
                (
                    new FileStream(
                        path,
                        FileMode.Open,
                        FileAccess.Read,
                        FileShare.Read)
                );
                XmlDocument doc = new XmlDocument();
                string xmlIn = reader.ReadToEnd();
                reader.Close();
                doc.LoadXml(xmlIn);
                foreach (XmlNode child in doc.ChildNodes)
                    if (child.Name.Equals("configuration"))
                        foreach(XmlNode mchild in child.ChildNodes)
                        foreach (XmlNode node in mchild.ChildNodes)
                            if (node.Name.Equals("add"))
                                _ret.Add
                                (
                                    node.Attributes["key"].Value.ToUpper(),
                                    node.Attributes["value"].Value
                                );
            }
            return (_ret);
        }

  ### 3、正确启动进程

       private void ExecuteAsAdmin(string fileName,string dirPath)
       {
         ProcessStartInfo proc = new ProcessStartInfo();
         proc.FileName = fileName;
         //必须设置工作路径
         proc.WorkingDirectory = dirPath;
         proc.UseShellExecute = false;
        //proc.Verb = "runas";
        Process.Start(proc);
      }

 ### 4、根据属性动态排序

    var param = "Address";    
    var propertyInfo = typeof(Student).GetProperty(param);    
    var orderByAddress = items.OrderBy(x => propertyInfo.GetValue(x, null));
