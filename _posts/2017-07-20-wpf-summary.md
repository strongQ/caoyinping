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
         //msmq绑定
         NetMsmqBinding binding = new NetMsmqBinding();
         // 设定非事务
         binding.ExactlyOnce = false;
         binding.Security = new NetMsmqSecurity() { Mode = NetMsmqSecurityMode.None };
         binding.QueueTransferProtocol = QueueTransferProtocol.Native;

         ChannelFactory<T> myfactory = new ChannelFactory<T>(binding, new EndpointAddress(address));
         return myfactory;




         // msmq wcf配置
          <system.serviceModel>
          <bindings>

           <netMsmqBinding>
             <binding name="msmqBinding" exactlyOnce="false">
              <security mode="None" />
            </binding>
           </netMsmqBinding>

          </bindings>
          <client>


            <endpoint address="net.msmq://localhost/private/fjtosj_mq" binding="netMsmqBinding"
             bindingConfiguration="msmqBinding" contract="JP.HCZZP.WPFAPP.WCF.Contracts.ISj"
             name="NetMsmqBinding_ISj">
            <identity>
              <dns value="localhost" />
             </identity>
             </endpoint>
          </client>
          <services>
              <service name="ConsoleApp1.SjService">
                <endpoint address="net.msmq://localhost/private/fjtosj_mq" binding="netMsmqBinding" bindingConfiguration="msmqBinding"
               contract="JP.HCZZP.WPFAPP.WCF.Contracts.ISj"/>
          </service>
         </system.serviceModel>

     }


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


                        <Style TargetType="{x:Type uc:PageSortingDataGrid}" >
                            <Setter Property="AutoGenerateColumns" Value="False"/>
                            <Setter Property="AlternatingRowBackground" Value="{DynamicResource AlternatingRowBackground}"/>
                            <Setter Property="Background" Value="Transparent"/>
                            <Setter Property="BorderThickness" Value="0"/>
                            <Setter Property="CanUserAddRows" Value="False"/>
                            <Setter Property="CanUserDeleteRows" Value="False"/>
                            <Setter Property="CanUserResizeColumns" Value="False"/>


                            <Setter Property="EnableRowVirtualization" Value="True"/>
                            <Setter Property="FontFamily" Value="{DynamicResource FontFamily}"/>
                            <Setter Property="FontSize" Value="{DynamicResource DefaultFontSize}"/>
                            <Setter Property="GridLinesVisibility" Value="Vertical"/>
                            <Setter Property="IsReadOnly" Value="True"/>
                            <Setter Property="RowBackground" Value="{DynamicResource RowBackground}"/>
                            <Setter Property="RowHeaderWidth" Value="0"/>
                            <Setter Property="SelectionMode" Value="Single"/>
                            <Setter Property="VerticalGridLinesBrush" Value="Transparent"/>
                            <!--网格线颜色-->

                        </Style>
                        <Style TargetType="DataGridColumnHeader">
                            <Setter Property="SnapsToDevicePixels" Value="True" />
                            <Setter Property="MinWidth" Value="0" />
                            <Setter Property="MinHeight" Value="32" />
                            <Setter Property="Foreground" Value="{DynamicResource DefaultForeground}" />
                            <Setter Property="FontSize" Value="{DynamicResource DefaultFontSize}" />
                            <Setter Property="Cursor" Value="Hand" />
                            <Setter Property="Background" Value="{DynamicResource HeaderBackground}"/>
                            <Setter Property="Template">
                                <Setter.Value>
                                    <ControlTemplate TargetType="DataGridColumnHeader">
                                        <Border Background="{TemplateBinding Background}" x:Name="BackgroundBorder" BorderThickness="0"
                                                BorderBrush="Transparent"
                                                Width="Auto">
                                            <Grid >
                                                <Grid.ColumnDefinitions>
                                                    <ColumnDefinition Width="*" />
                                                </Grid.ColumnDefinitions>
                                                <ContentPresenter Name="txt"  Margin="0,0,0,0" VerticalAlignment="Center" HorizontalAlignment="Center"/>
                                                <Path x:Name="SortArrow" Visibility="Collapsed" Data="M0,0 L1,0 0.5,1 z" Stretch="Fill"   Width="8" Height="6" Fill="White" Margin="0,0,50,0"
                                                      VerticalAlignment="Center" RenderTransformOrigin="1,1" />
                                                <Rectangle Width="0" Fill="#d6c79b" HorizontalAlignment="Right" Grid.ColumnSpan="1" />

                                            </Grid>

                                        </Border>
                                    </ControlTemplate>
                                </Setter.Value>

                            </Setter>
                            <Style.Triggers>

                                <Trigger Property="IsMouseOver" Value="true">
                                    <Setter Property="Foreground" Value="Yellow"></Setter>
                                </Trigger>
                                <Trigger Property="IsPressed" Value="true">
                                    <Setter Property="Background" Value="#0188FB"></Setter>
                                </Trigger>
                            </Style.Triggers>
                         </Style>
                        <!--行样式触发-->
                        <!--背景色改变必须先设置cellStyle 因为cellStyle会覆盖rowStyle样式-->
                        <Style  TargetType="DataGridRow">

                            <Setter Property="Height" Value="30"/>
                            <Setter Property="FontSize" Value="{DynamicResource DefaultSmalFontSize}"/>
                            <Setter Property="Foreground" Value="{DynamicResource DefaultForeground}" />
                            <Style.Triggers>
                                <!--隔行换色-->
                                <!--<Trigger Property="AlternationIndex" Value="0" >
                                    <Setter Property="Background" Value="Transparent" />
                                </Trigger>
                                <Trigger Property="AlternationIndex" Value="1" >
                                    <Setter Property="Background" Value="{DynamicResource RowBackground}" />
                                </Trigger>-->

                                <Trigger Property="IsMouseOver" Value="True">
                                    <Setter Property="Background" Value="#005277"/>
                                    <!--<Setter Property="Foreground" Value="White"/>-->
                                </Trigger>

                                <Trigger Property="IsSelected" Value="True">
                                    <Setter Property="Background" Value="#005277"/>
                                </Trigger>
                            </Style.Triggers>
                        </Style>
                        <!--单元格样式触发-->
                        <Style TargetType="DataGridCell">
                            <Setter Property="Template">
                                <Setter.Value>
                                    <ControlTemplate TargetType="DataGridCell">
                                        <TextBlock TextAlignment="Center" HorizontalAlignment="Center" VerticalAlignment="Center"  >
                                            <ContentPresenter />
                                        </TextBlock>
                                    </ControlTemplate>
                                </Setter.Value>
                            </Setter>
                            <Style.Triggers>
                                <Trigger Property="IsSelected" Value="True">
                                    <Setter Property="Foreground" Value="White"/>
                                </Trigger>
                            </Style.Triggers>
                        </Style>   


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
             //new SolidColorBrush((Color)ColorConverter.ConvertFromString("#FFD3CFC7"));
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

### 12、wpf窗体固定位置

       var desktopWorkingArea = System.Windows.SystemParameters.WorkArea;
       logoWindow.Left = desktopWorkingArea.Right - logoWindow.Width-50;
       logoWindow.Top = desktopWorkingArea.Bottom - logoWindow.Height-100;

### 13、listBox样式
    <Style TargetType="{x:Type ListBox}">
      <Setter Property="Background" Value="{DynamicResource ContentBackground}"/>
      <Setter Property="FontSize" Value="{DynamicResource DefaultFontSize}"/>
      <Setter Property="Foreground" Value="{DynamicResource NormalForeground}"/>
      <Setter Property="OverridesDefaultStyle" Value="True"/>
      <Setter Property="Padding" Value="0"/>
     <Setter Property="Margin" Value="0"/>
     <Setter Property="ClipToBounds" Value="False"/>
     <Setter Property="BorderThickness" Value="0"/>
     <Setter Property="ScrollViewer.HorizontalScrollBarVisibility" Value="Auto"/>
     <Setter Property="ScrollViewer.VerticalScrollBarVisibility" Value="Auto"/>
     <Setter Property="ScrollViewer.CanContentScroll" Value="true"/>
     <Setter Property="ScrollViewer.PanningMode" Value="Both"/>
     <Setter Property="Stylus.IsFlicksEnabled" Value="False"/>
      <Setter Property="ScrollViewer.CanContentScroll" Value="False"/>
      <Setter Property="Template">
         <Setter.Value>
          <ControlTemplate TargetType="{x:Type ListBox}">
              <Border x:Name="Bd" Background="{TemplateBinding Background}" BorderBrush="{TemplateBinding BorderBrush}"
      BorderThickness="{TemplateBinding BorderThickness}" SnapsToDevicePixels="true">
                  <ScrollViewer Focusable="false" Margin="0" Padding="0" ContextMenu="{x:Null}">
                      <ItemsPresenter Margin="0" SnapsToDevicePixels="{TemplateBinding SnapsToDevicePixels}"/>
                  </ScrollViewer>
              </Border>
              <!--<ControlTemplate.Triggers>
                  <Trigger Property="IsEnabled" Value="false">
                      <Setter Property="Opacity" TargetName="Bd" Value="0.5"/>
                  </Trigger>
                  <MultiTrigger>
                      <MultiTrigger.Conditions>
                          <Condition Property="IsGrouping" Value="true"/>
                      </MultiTrigger.Conditions>
                      <Setter Property="ScrollViewer.CanContentScroll" Value="True"/>
                  </MultiTrigger>
              </ControlTemplate.Triggers>-->
          </ControlTemplate>
         </Setter.Value>
       </Setter>
    </Style>

         <Style  TargetType="{x:Type ListBoxItem}" >
        <Style.Triggers>
         <!--<MultiTrigger>
          <MultiTrigger.Conditions>
              <Condition Property="IsSelected" Value="True"/>
              <Condition Property="IsMouseOver" Value="True"/>
          </MultiTrigger.Conditions>
          <Setter Property="Background" Value="Red"/>
      </MultiTrigger>
      <MultiTrigger>
          <MultiTrigger.Conditions>
              <Condition Property="IsSelected" Value="True"/>
              <Condition Property="IsMouseOver" Value="False"/>
          </MultiTrigger.Conditions>
          <Setter Property="Background" Value="Transparent"/>
      </MultiTrigger>-->
      <Trigger Property="IsMouseOver" Value="True">
          <Setter Property="Background" Value="Transparent"/>
      </Trigger>
    </Style.Triggers>
   </Style>

   ### 14、全局通用命令
   后台：
   public class TestCommand:ContentControl
    {
        public static RoutedCommand OpenDialogCommand = new RoutedCommand();
        public TestCommand()
        {
            CommandBindings.Add(new CommandBinding(OpenDialogCommand, OpenDialogHandler));
        }

        private void OpenDialogHandler(object sender, ExecutedRoutedEventArgs e)
        {
            MessageBox.Show("ok");
        }
    }
   前台：
    <domain:TestCommand></domain:TestCommand>

### 15、word模板引擎
   TemplateEngine.Docx

   配合Spire读取显示


 ### 16、主题设置
 1、添加资源类，方便xaml中定位资源

    public class CustomResources
    {

     public static ComponentResourceKey OneGradientForeKey
     {
         get
         {
             return new ComponentResourceKey(
                 typeof(CustomResources), "OneGradientFore");
         }
     }
    }

 2、添加资源字典和后台cs文件，方便直接调用

    ResourceDictionary x:Class="JP.HCZZP.WPFApp.Instruct.Modules.ResourceLibrary.Themes.Generic" xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation" xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml">
    <ResourceDictionary.MergedDictionaries>

        <ResourceDictionary  Source="Images.xaml"/>
        <ResourceDictionary Source="Colors.xaml"/>
        <ResourceDictionary Source="Controls/InstructListBox.xaml"/>
    </ResourceDictionary.MergedDictionaries>
   </ResourceDictionary>

    public partial class Generic: ResourceDictionary
     {
        public Generic()
        {
            InitializeComponent();
        }
     }


   3、xaml中应用资源

    BorderBrush="{DynamicResource {x:Static res:CustomResources.OneForeKey}}"




 ### 16、Wpf中Cef网页加载

     // 等待加载完成
     public Task LoadPageAsync(string address=null)
        {
            var tcs = new TaskCompletionSource<bool>(TaskCreationOptions.RunContinuationsAsynchronously);

            EventHandler<LoadingStateChangedEventArgs> handler = null;
            handler = (sender, args) =>
            {       
                    //Wait for while page to finish loading not just the first frame
                    if (!args.IsLoading)
                    {              
                    //Important that the continuation runs async using TaskCreationOptions.RunContinuationsAsynchronously
                    var result=  tcs.TrySetResult(true);       
                    }                         
            };
            this.WebBrowser.LoadingStateChanged -= handler;
            this.WebBrowser.LoadingStateChanged += handler;
            if (!string.IsNullOrEmpty(address))
            {
                
                this.WebBrowser.Load(address);
            }
            else
            {
                this.GetBrowser()?.Reload();
            }
            return tcs.Task;
        }