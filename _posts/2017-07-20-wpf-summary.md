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
