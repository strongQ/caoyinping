---
layout:  post
title:   WPF牢记知识
category:  WPF
tags:   [CS]
description:  工作中记录的一些WPF知识
---


### 1、命令
    <Window.Resources>
    <RoutedUICommand x:Key="TakeScreenshotCommand"   Text="TakeThumbnail"/>
    </Window.Resources>

    <Window.CommandBindings>
        <CommandBinding Command="{StaticResource TakeScreenshotCommand}"
                    Executed="CommandBinding_Executed" />
    </Window.CommandBindings>

    <Window.InputBindings>
        <KeyBinding Key="T" Modifiers="Ctrl" Command="{StaticResource TakeScreenshotCommand}" />
    </Window.InputBindings>


### 2、json序列化
    var serializer = new DataContractJsonSerializer(typeof(SampleStructureMap));
    var json = File.ReadAllText(metadataFilePath);
    var jsonInBytes = Encoding.UTF8.GetBytes(json);
            using (var stream = new MemoryStream(jsonInBytes))
            {
                // De-serialize sample model
                structureMap = serializer.ReadObject(stream) as SampleStructureMap;

               if (structureMap == null)

                throw new SerializationException("Couldn't create StructureMap from provided groups.json file stream.");
                structureMap.Samples = new List<SampleModel>();

            }

### 3、image字符串转换
    BitmapFrame.Create(
    new Uri(sampleImageName,UriKind.Absolute),
                        BitmapCreateOptions.DelayCreation,
                        BitmapCacheOption.OnLoad);

### 4、TextBox失去焦点后才更新
      <TextBox Text="{Binding ElementName=sampleText, Path=FontSize,
                            UpdateSourceTrigger=PropertyChanged,
                            Mode=TwoWay}"
                     Width="50" BorderThickness="1"></TextBox>

### 5、数据绑定
     <TextBlock FontSize="15" Margin="0 5"
                   FontFamily="Courier New"
                   Text="{Binding RelativeSource={RelativeSource Mode=Self}, Path=FontFamily}">
            </TextBlock>

            <TextBlock FontSize="12"
                   Text="{Binding RelativeSource={RelativeSource Mode=FindAncestor, AncestorType=Window}, Path=FontFamily}">
            </TextBlock>

            <TextBlock FontSize="15"
                   Text="{Binding RelativeSource={RelativeSource Mode=FindAncestor, AncestorType=StackPanel, AncestorLevel=1}, Path=Margin}">
            </TextBlock>

### 6、wcf
    public static ChannelFactory<T> GetFactory<T>(string address)
     {
         BasicHttpBinding binding = new BasicHttpBinding();
         ChannelFactory<T> myfactory = new ChannelFactory<T>(binding, new EndpointAddress(address));
         return myfactory;
     }
      ChannelFactory<IDuty> factory = ServiceFactory.GetFactory<IDuty>(ServiceFactory.Instance.dutyClientURIFormat);

### 7、TabControl
    <TabControl ItemsSource="{Binding Objects}" SelectedIndex="0" TabStripPlacement="Left" >
       <TabControl.Template>
           <ControlTemplate TargetType="TabControl">
               <Grid>
                   <Grid.ColumnDefinitions>
                       <ColumnDefinition Width="Auto"/>
                       <ColumnDefinition />
                   </Grid.ColumnDefinitions>
                   <ScrollViewer
           HorizontalScrollBarVisibility="Disabled"  
           VerticalScrollBarVisibility="Auto"
           FlowDirection="RightToLeft">
                       <TabPanel
                   x:Name="HeaderPanel"
                   Panel.ZIndex ="0"
                   KeyboardNavigation.TabIndex="1"
                   IsItemsHost="true"
               />
                   </ScrollViewer>
                   <ContentPresenter
           x:Name="PART_SelectedContentHost"
           SnapsToDevicePixels="{TemplateBinding SnapsToDevicePixels}"
           ContentSource="SelectedContent" Grid.Column="1"
       />
               </Grid>
           </ControlTemplate>
       </TabControl.Template>
       <TabControl.Resources>       
       <DataTemplate DataType="{x:Type vm:VM_DutyCheck}">
                   <v:DutyCheck DataContext="{Binding}"/>
               </DataTemplate>
               <DataTemplate DataType="{x:Type vm:VM_DutyCheck1}">
                   <v:DutyCheck1 DataContext="{Binding}"/>
               </DataTemplate>
               <DataTemplate DataType="{x:Type vm:VM_DutyExceptionCheck}">
                   <v:DutyExceptionCheck DataContext="{Binding}"/>
               </DataTemplate>
               <DataTemplate DataType="{x:Type vm:VM_ArtificialCheck}">
                   <v:ArtificialCheck DataContext="{Binding}"/>
               </DataTemplate>
               <DataTemplate DataType="{x:Type vm:VM_DispatchingCheckPresence}">
                   <v:DispatchingCheckPresence DataContext="{Binding}"/>
               </DataTemplate>
               <DataTemplate DataType="{x:Type vm:VM_DispatchingRecord}">
                   <v:DispatchingRecord DataContext="{Binding}"/>
               </DataTemplate>
               <DataTemplate DataType="{x:Type vm:VM_FailedRecord}">
                   <v:FailedRecord DataContext="{Binding}"/>
               </DataTemplate>

               <DataTemplate DataType="{x:Type vm:VM_ExceptionCase}">
                   <v:ExceptionCase DataContext="{Binding}"/>
               </DataTemplate>


               <DataTemplate DataType="{x:Type vm:VM_DutyCheck2}">
                   <v:DutyCheck2xaml DataContext="{Binding}"/>
               </DataTemplate>

               <DataTemplate DataType="{x:Type vm:VM_UnderFeedBackRecord}">
                   <v:UnderFeedBackRecord DataContext="{Binding}"/>
               </DataTemplate>

               <DataTemplate DataType="{x:Type vm:VM_UnSendRecord}">
                   <v:UnSendRecord DataContext="{Binding}"/>

               </DataTemplate>


               <DataTemplate DataType="{x:Type vm2:VM_CheckStatistics}">
                   <v2:CheckStatistics DataContext="{Binding}"/>
               </DataTemplate>
               <DataTemplate DataType="{x:Type vm3:VM_AttentionStatistics}">
                   <v3:AttentionStatistics DataContext="{Binding}"/>
               </DataTemplate>
               <!--勤务报备检查统计表-->
               <DataTemplate DataType="{x:Type vm6:VM_BeobeiStatistics}">
                   <v6:BeobeiStatistics DataContext="{Binding}"/>
               </DataTemplate>

               <!--勤务等级变更执行情况统计表-->
               <DataTemplate DataType="{x:Type vm6:VM_StartupStatistics}">
                   <v6:StartupStatistics DataContext="{Binding}"/>
               </DataTemplate>

               <DataTemplate DataType="{x:Type vm6:VM_WddStatistics}">
                   <v6:WddStatistics DataContext="{Binding}"/>

               </DataTemplate>

           </TabControl.Resources>
           <TabControl.ItemTemplate>
               <DataTemplate>
                   <StackPanel SnapsToDevicePixels="True" Orientation="Horizontal" Margin="25,3,1,6">
                       <TextBlock FontSize="{StaticResource HeaderFontSize}" Text="{Binding Header}"/>
                       <Decorator MinWidth="24">
                           <TextBlock FontSize="{StaticResource DefaultFontSize}" Name="tb" Margin="8,-2,0,2" Padding="5,0"
               Style="{StaticResource CountTips}" Text="{Binding ItemsCount}"/>
                       </Decorator>
                   </StackPanel>
                   <DataTemplate.Triggers>
                       <Trigger Property="Text" Value=""  SourceName="tb">
                           <Setter Property="Visibility" Value="Collapsed" TargetName="tb" />
                       </Trigger>
                   </DataTemplate.Triggers>
               </DataTemplate>
           </TabControl.ItemTemplate>

       </TabControl>

### 8、DataGrid
    <DataGridTemplateColumn HeaderStyle="{StaticResource CityNameStyle1}">
                            <DataGridTemplateColumn.CellTemplate>
                                <DataTemplate>
                                    <Grid Width="99">
                                        <TextBlock
                                            Foreground="{Binding IsQualified}"
                                            Style="{DynamicResource DateGridTextBlock2}"
                                            Text="{Binding Fj}" />
                                    </Grid>
                                </DataTemplate>
                            </DataGridTemplateColumn.CellTemplate>
                        </DataGridTemplateColumn>

### 9、在ViewModel中调用事件

 1、引用 System.Windows.Interactivity.dll 程序集之后，我们在 View 中添加xmlns的引用如下：

      xmlns:i="clr-namespace:System.Windows.Interactivity;assembly=System.Windows.Interactivity"


      <!--让Load事件触发LoadDataCommand命令-->
         <i:Interaction.Triggers>
             <i:EventTrigger EventName="Loaded">
                 <i:InvokeCommandAction Command="{Binding LoadDataCommand}" />
             </i:EventTrigger>
         </i:Interaction.Triggers>


### 10、数据模板选择器
    public class MessageTemplateSelector:DataTemplateSelector
      {

              public override DataTemplate SelectTemplate(object item, DependencyObject container)
              {
                    var message = item as ReplyModel;
              }

      }

   前台绑定：

     <local:MessageTemplateSelector x:Key="MsgSelector" MessageTemplate="{StaticResource MessageTemplate}" WordLeftTemplate="{StaticResource WordLeftTemplate}" AudioLeftTemplate="{StaticResource AudioLeftTemplate}"
                                     WordRightTemplate="{StaticResource WordRightTemplate}" FileLeftTemplate="{StaticResource FileLeftTemplate}" FileRightTemplate="{StaticResource FileRightTemplate}">


### 11、wpf窗体遮罩        
     /// <summary>
     /// 显示窗体,扩展方法
     /// </summary>
     /// <param name="box">父窗体</param>
     /// <param name="owner">子窗体</param>
     /// <returns></returns>
     public static bool? ShowWindow(this Window box,Window owner)
     {
         if (owner != null)
         {
             box.Owner = owner;
             //蒙板
             Grid layer = new Grid() { Background = new SolidColorBrush(Color.FromArgb(128, 0, 0, 0)) };
             //父级窗体原来的内容
             UIElement original = owner.Content as UIElement;
             owner.Content = null;
             //容器Grid
             Grid container = new Grid();
             container.Children.Add(original);//放入原来的内容
             container.Children.Add(layer);//在上面放一层蒙板
                                           //将装有原来内容和蒙板的容器赋给父级窗体
             owner.Content = container;
             box.WindowStartupLocation = WindowStartupLocation.CenterOwner;

         }
         return box.ShowDialog();
     }


     // 窗体关闭事件
     private void UCInstructSendDeptWindow_Closing(object sender, System.ComponentModel.CancelEventArgs e)
            {
                if (this.Owner != null)
                {
                    //容器Grid
                    Grid grid = this.Owner.Content as Grid;
                    //父级窗体原来的内容
                    UIElement original = VisualTreeHelper.GetChild(grid, 0) as UIElement;
                    //将父级窗体原来的内容在容器Grid中移除
                    grid.Children.Remove(original);
                    //赋给父级窗体
                    this.Owner.Content = original;
                }

                VisualStateManager.GoToState(this, "Closed", true);
                //e.Cancel = true;
            }
