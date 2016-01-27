---

layout:    post
title:     冒泡排序等算法
category:  算法
tags:      [冒泡,md5]

---

记录下容易忘记的简单算法

- 冒泡排序
{% highlight c# %}
 class BubbleSorter
    {
        static public void Sort(IList sortArray, Func<T, T, bool> comparison)
        {
            bool swapped = true;
            do
            {
                swapped = false;
                for (int i = 0; i < sortArray.Count - 1; i++)
                {
                    if (comparison(sortArray[i + 1], sortArray[i]))
                    {
                        T temp = sortArray[i];
                        sortArray[i] = sortArray[i + 1];
                        sortArray[i + 1] = temp;
                    }
                }
            } while (swapped);
        }
    }
    class Employee
    {
        public Employee(string name, decimal salary)
        {
            this.Name = name;
            this.Salary = salary;
        }
        public string Name { get; private set; }
        public decimal Salary { get; private set; }
        public override string ToString()
        {
            return string.Format("{0}, {1:C}", Name, Salary);
        }
        public static bool CompareSalary(Employee e1, Employee e2)
        {
            return e1.Salary < e2.Salary;
        }
    }
    class Program
    {
        static void Main(string[] args)
        {
            Employee[] employees =
           {
               new Employee("Bugs bunny",2000),
               new Employee("Elmer Fudd",1000),
               new Employee("Daffy Duck",2500),
               new Employee("Wile Coyote",10000.38m),
               new Employee("Foghorn leghorn",23000),
               new Employee("RoadRunner",50000)
           };
            BubbleSorter.Sort(employees, Employee.CompareSalary);
            foreach (var employee in employees)
            {
                Console.WriteLine(employee);
            }
        }
    }

{% endhighlight %}

- md5加密
{% highlight c# %}
 public static string GetMD5(string sDataIn)
        {
            MD5CryptoServiceProvider md5 = 
                new MD5CryptoServiceProvider();
            byte[] bytValue, bytHash;
            bytValue = System.Text.Encoding.UTF8.GetBytes(sDataIn);
/*文件       using (FileStream fs = File.OpenRead(filepath))
            {
                md5ch = md5.ComputeHash(fs);
            }
*/
            bytHash = md5.ComputeHash(bytValue);
            md5.Clear();
            string sTemp = "";
            for (int i = 0; i < bytHash.Length; i++)
            {
                sTemp += bytHash[i].ToString("X").PadLeft(2, '0');
            }
            return sTemp.ToLower();
        }
{% endhighlight %}