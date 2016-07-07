---
layout:                  post
title:                       简单的多线程应用
category:              多线程
tags:                    [多线程,随机数]

---

多线程运用的太少，先记录个最简单的，直到熟练!

{% highlight c# %}
1.随机数 winform窗体之间传递数据

 delegate void setdelegatenum(Label one,int a);
        private bool begin = false;
        private Thread td = null;
        private void setnum(Label one ,int a)
        {
           one.Text =a.ToString();
        }
        private void getnum( object label)
        {
            Label one = (Label)label;
            setdelegatenum a = this.setnum;
            Random random=new Random(unchecked((int)DateTime.Now.Ticks));
            while (true)
            {
                int num = random.Next(0, 9);
                this.Invoke(a, one, num);
                Thread.Sleep(500);
            }         
        }
        private void button1_Click(object sender, EventArgs e)
        {
            if (begin == false)
            {               
                Thread th = new Thread(new ParameterizedThreadStart(this.getnum));
                
                th.IsBackground = true;
                th.Start(label1);td = th;
                begin = true;
                button1.Text = "停止";
            }
            else
            {
                td.Abort();
                begin = false;
                button1.Text = "开始";
            }
        }
        
    /* Linq中asParallel() */
        public class Custom
        {
            public string Name { get; set; }
            public int Age { get; set; }
            public string Address { get; set; }
        }
        public static void TestPLinq()
        {
            var sw = Stopwatch.StartNew();
            List<Custom> customs = new List<Custom>();
            for (int i = 0; i < 2000000; i++)
            {
                customs.Add(new Custom() { Name = "Jack", Age = 21, Address = "NewYork" });
                customs.Add(new Custom() { Name = "Jime", Age = 26, Address = "China" });
                customs.Add(new Custom() { Name = "Tina", Age = 29, Address = "Shanghai" });
                customs.Add(new Custom() { Name = "Luo", Age = 30, Address = "Beijing" });
                customs.Add(new Custom() { Name = "Wang", Age = 23, Address = "Guangdong" });
            }
            sw.Start();
            var result = customs.Where<Custom>(c => c.Age > 26).ToList();
            sw.Stop();
            Console.WriteLine("Linq time is {0}.", sw.ElapsedMilliseconds);
            sw.Restart();
            sw.Start();
            var result2 = customs.AsParallel().Where<Custom>(c => c.Age > 26).ToList();
            sw.Stop();
            Console.WriteLine("Parallel Linq time is {0}.", sw.ElapsedMilliseconds);
        } 
     
       /* 并发Parallel */
        public class ParallelDemo
        {
            private Stopwatch stopWatch = new Stopwatch();
            public void Run1()
            {
                Thread.Sleep(2000);
                Console.WriteLine("Task 1 is cost 2 sec");
            }
            public void Run2()
            {
                Thread.Sleep(3000);
                Console.WriteLine("Task 2 is cost 3 sec");
            }
            public void ParallelInvokeMethod()
            {
                stopWatch.Start();
                Parallel.Invoke(Run1, Run2);
                stopWatch.Stop();
                Console.WriteLine("Parallel run " + stopWatch.ElapsedMilliseconds + " ms");
                stopWatch.Restart();
                Run1();
                Run2();
                stopWatch.Stop();
                Console.WriteLine("Normal run " + stopWatch.ElapsedMilliseconds + " ms");

                stopWatch.Restart();

                Parallel.For(0, 10000, item =>
                {
                    for (int j = 0; j < 60000; j++)
                    {
                        int sum = 0;
                        sum += item;
                    }
                });
                List<int> bag = new List<int>();
                Parallel.For(0, 1000, (i, state) =>
                {
                    if (bag.Count == 300)
                    {
                        state.Break();
                        return;
                    }
                    bag.Add(i);
                });
                foreach (var i in bag)
                {
                    Console.WriteLine(i.ToString());
                }
                stopWatch.Stop();
                Console.WriteLine("end run " + stopWatch.ElapsedMilliseconds + " ms");
            }
        } 
       

        /* LookUp分组 */
        public static void OrderByTest()
        {
            var sw = Stopwatch.StartNew();
            List<Custom> customs = new List<Custom>();
            for (int i = 0; i < 2000000; i++)
            {
                customs.Add(new Custom() { Name = "Jack", Age = 21, Address = "NewYork" });
                customs.Add(new Custom() { Name = "Jime", Age = 26, Address = "China" });
                customs.Add(new Custom() { Name = "Tina", Age = 29, Address = "Shanghai" });
                customs.Add(new Custom() { Name = "Luo", Age = 30, Address = "Beijing" });
                customs.Add(new Custom() { Name = "Wang", Age = 23, Address = "Guangdong" });
            }
            sw.Restart();
            var groupByAge = customs.GroupBy(item => item.Age).ToList();
            foreach (var item in groupByAge)
            {
                Console.WriteLine("Age={0},count={1}", item.Key, item.Count());
            }
            sw.Stop();
            Console.WriteLine("Linq group by time is: " + sw.ElapsedMilliseconds);
            sw.Restart();
            sw.Start();
            var lookupList = customs.ToLookup(i => i.Age);
            foreach (var item in lookupList)
            {
                Console.WriteLine("LookUP:Age={0},count={1}", item.Key, item.Count());
            }
            sw.Stop();
            Console.WriteLine("LookUp group by time is: " + sw.ElapsedMilliseconds);
        } 
        #endregion

        /* 普通Task */
        static void tryTask()
        {
            var task1 = new Task(() =>
            {

                Thread.Sleep(2000); Console.WriteLine("Hello,task");
            });
            task1.Start();

            var task2 = Task.Factory.StartNew(() =>
            {

                Thread.Sleep(3000); Console.WriteLine("Hello,task started task factory");
            });

            // Task.WaitAll(tasks,5000)         Task.WaitAny       Task.ContinueWith
            Task.WaitAny(task1, task2);
            Console.WriteLine("All Over");
            Console.Read();
        } 
        

        /* 继续ContinueTask */
        static void ContinueTask() 
       
        {
            var task1 = new Task(() =>
            {
                Console.WriteLine("Task 1 Begin");
                Thread.Sleep(2000);
                Console.WriteLine("Task 1 Finish");
            });
            var task2 = new Task(() =>
            {
                Console.WriteLine("Task 2 Begin");
                Thread.Sleep(3000);
                Console.WriteLine("Task 2 Finish");
            });
            task1.Start();
            task2.Start();
            // ContinueTask
            var result = task1.ContinueWith<string>(task =>
            {
                Console.WriteLine("task1 finished!");
                return "This is task result!";
            });
            Console.WriteLine(result.Result.ToString());
            Console.Read();
        } 
     

        /* 终端Task */
        static void BreakTask()
        {
            var tokenSource = new CancellationTokenSource();
            var token = tokenSource.Token;
            var task = Task.Factory.StartNew(() =>
                {
                    for (var i = 0; i < 1000; i++)
                    {
                        System.Threading.Thread.Sleep(1000);
                        if (token.IsCancellationRequested)
                        {
                            Console.WriteLine("Abort mission success!");
                            return;
                        }
                    }
                }, token);
            token.Register(() =>
            {
                Console.WriteLine("Canceled");
            });
            Console.WriteLine("Press enter to cancel task...");
            Console.ReadKey();
            tokenSource.Cancel();
        } 
        

        /* 父子关联task */
        static void ParentTask()
        {
            var pTask = Task.Factory.StartNew(() =>
                {
                    var cTask = Task.Factory.StartNew(() =>
                        {
                            Thread.Sleep(2000);
                            Console.WriteLine("Children task finished!");
                        }, TaskCreationOptions.AttachedToParent);
                    Console.WriteLine("Parent task finished!");
                });
            pTask.Wait();
            Console.WriteLine("Flag");
            Console.Read();
        } 
        
        /* Future----任务的结果 */
        public static Tuple<int,int> TaskWithResult(object division)
        {
            Tuple<int, int> div = (Tuple<int, int>)division;
            int result = div.Item1 / div.Item2;
            int reminder = div.Item1 % div.Item2;
            Console.WriteLine("task create a result...");
            return Tuple.Create<int, int>(result, reminder);
        }
         var t1 = new Task<Tuple<int, int>>(TaskWithResult, Tuple.Create<int, int>(8, 3));
          t1.Start();
          Console.WriteLine(t1.Result);
        


        /* 任务2和任务3要等待任务1完成后，取得任务1的结果，然后开始执行。任务4要等待任务2完成，取得其结果才能执行，最终任务3和任务4都完成了，合并结果，任务完成。*/
        static void GetResult()
        {
            Task.Factory.StartNew(() =>
                {
                    var t1 = Task.Factory.StartNew<int>(() =>
                        {
                            Console.WriteLine("Task 1 running...");
                            return 1;
                        });
                    t1.Wait(); //等待任务一完成
                    var t3 = Task.Factory.StartNew<int>(() =>
                        {
                            Console.WriteLine("Task 3 running...");
                            return t1.Result + 3;
                        });
                    var t4 = Task.Factory.StartNew<int>(() =>
                        {
                            Console.WriteLine("Task 2 running...");
                            return t1.Result + 2;
                        }).ContinueWith<int>(task =>
                        {
                            Console.WriteLine("Task 4 running...");
                            return task.Result + 4;
                        });
                    Task.WaitAll(t3, t4); //等待任务三任务四完成
                    var result = Task.Factory.StartNew(() =>
                    {
                        Console.WriteLine("Task Finished: The result is {0}", t3.Result + t4.Result);
                    });
                });
            Console.Read();
        } 
       

{% endhighlight %}
