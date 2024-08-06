

# 基础应用

## 属性元素

在 .NET Multi-platform App UI (.NET MAUI) XAML 中，类的属性通常设置为 XML 属性：

```xml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="18"
       TextColor="Aqua" />
//或
<Label Text="Hello, XAML!"
       VerticalOptions="Center">
    <Label.FontAttributes>
        Bold
    </Label.FontAttributes>
    <Label.FontSize>
        Large
    </Label.FontSize>
    <Label.TextColor>
        Aqua
    </Label.TextColor>
</Label>
```



## 平台差异

.NET MAUI 应用可以按平台自定义 UI 外观。 这可以在 XAML 中使用 `OnPlatform` 和 `On` 类来实现：

```xml
<ContentPage xmlns="http://schemas.microsoft.com/dotnet/2021/maui"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">
    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="0,20,0,0" />
            <On Platform="Android" Value="10,20,20,10" />
        </OnPlatform>
    </ContentPage.Padding>
</ContentPage>
```



## 共享资源

某些 XAML 页面包含多个属性设置为相同值的视图。例如，这些[Button](https://learn.microsoft.com/en-us/dotnet/api/microsoft.maui.controls.button)对象的许多属性设置都是相同的：

```xml
<ContentPage xmlns="http://schemas.microsoft.com/dotnet/2021/maui"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SharedResourcesPage"
             Title="Shared Resources Page">
    <StackLayout>
        <Button Text="Do this!"
                HorizontalOptions="Center"
                VerticalOptions="Center"
                BorderWidth="3"
                Rotation="-15"
                TextColor="Red"
                FontSize="24" />
        <Button Text="Do that!"
                HorizontalOptions="Center"
                VerticalOptions="Center"
                BorderWidth="3"
                Rotation="-15"
                TextColor="Red"
                FontSize="24" />
        <Button Text="Do the other thing!"
                HorizontalOptions="Center"
                VerticalOptions="Center"
                BorderWidth="3"
                Rotation="-15"
                TextColor="Red"
                FontSize="24" />
    </StackLayout>
</ContentPage>
```

```xml
<ContentPage.Resources>
        <LayoutOptions x:Key="horzOptions"
                       Alignment="Center" />
        <LayoutOptions x:Key="vertOptions"
                       Alignment="Center" />
        <x:Double x:Key="borderWidth">3</x:Double>
        <x:Double x:Key="rotationAngle">-15</x:Double>
        <x:Double x:Key="fontSize">24</x:Double>        
</ContentPage.Resources>
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="{StaticResource borderWidth}"
        Rotation="{StaticResource rotationAngle}"
        TextColor="Red"
        FontSize="{StaticResource fontSize}" />
```

对于[Color](https://learn.microsoft.com/en-us/dotnet/api/microsoft.maui.graphics.color)类型的资源，您可以使用直接分配这些类型的属性时所使用的相同字符串表示形式。创建资源时会调用 .NET MAUI 中包含的类型转换器。还可以使用`OnPlatform`资源字典中的类来为平台定义不同的值。以下示例使用此类来设置不同的文本颜色：

```xml
<OnPlatform x:Key="textColor"
            x:TypeArguments="Color">
    <On Platform="iOS" Value="Red" />
    <On Platform="Android" Value="Aqua" />
</OnPlatform>
```

该`OnPlatform`资源获得一个`x:Key`属性，因为它是字典中的一个对象，也获得一个`x:TypeArguments`属性，因为它是一个泛型类。对象初始化时，`iOS`、 和属性`Android`将转换为[颜色值。](https://learn.microsoft.com/en-us/dotnet/api/microsoft.maui.graphics.color)

以下示例显示了访问六个共享值的三个按钮：

```xml
<ContentPage xmlns="http://schemas.microsoft.com/dotnet/2021/maui"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SharedResourcesPage"
             Title="Shared Resources Page">
    <ContentPage.Resources>
        <LayoutOptions x:Key="horzOptions"
                       Alignment="Center" />
        <LayoutOptions x:Key="vertOptions"
                       Alignment="Center" />
        <x:Double x:Key="borderWidth">3</x:Double>
        <x:Double x:Key="rotationAngle">-15</x:Double>
        <x:Double x:Key="fontSize">24</x:Double>    
        <OnPlatform x:Key="textColor"
                    x:TypeArguments="Color">
            <On Platform="iOS" Value="Red" />
            <On Platform="Android" Value="Aqua" />
            <On Platform="WinUI" Value="#80FF80" />
        </OnPlatform>
    </ContentPage.Resources>

    <StackLayout>
        <Button Text="Do this!"
                HorizontalOptions="{StaticResource horzOptions}"
                VerticalOptions="{StaticResource vertOptions}"
                BorderWidth="{StaticResource borderWidth}"
                Rotation="{StaticResource rotationAngle}"
                TextColor="{StaticResource textColor}"
                FontSize="{StaticResource fontSize}" />
        <Button Text="Do that!"
                HorizontalOptions="{StaticResource horzOptions}"
                VerticalOptions="{StaticResource vertOptions}"
                BorderWidth="{StaticResource borderWidth}"
                Rotation="{StaticResource rotationAngle}"
                TextColor="{StaticResource textColor}"
                FontSize="{StaticResource fontSize}" />
        <Button Text="Do the other thing!"
                HorizontalOptions="{StaticResource horzOptions}"
                VerticalOptions="{StaticResource vertOptions}"
                BorderWidth="{StaticResource borderWidth}"
                Rotation="{StaticResource rotationAngle}"
                TextColor="{StaticResource textColor}"
                FontSize="{StaticResource fontSize}" />
    </StackLayout>
</ContentPage>
```



## x:static 标记扩展

除了[`StaticResource`](https://learn.microsoft.com/en-us/dotnet/api/microsoft.maui.controls.xaml.staticresourceextension)标记扩展之外，还有一个`x:Static`标记扩展。但是， while[`StaticResource`](https://learn.microsoft.com/en-us/dotnet/api/microsoft.maui.controls.xaml.staticresourceextension)从资源字典返回对象，`x:Static`访问公共静态字段、公共静态属性、公共常量字段或枚举成员。

```C#
namespace XamlSamples
{
    static class AppConstants
    {
        public static readonly Color BackgroundColor = Colors.Aqua;
        public static readonly Color ForegroundColor = Colors.Brown;
    }
}
```

```xml
<ContentPage xmlns="http://schemas.microsoft.com/dotnet/2021/maui"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples"
             xmlns:sys="clr-namespace:System;assembly=netstandard"
             x:Class="XamlSamples.StaticConstantsPage"
             Title="Static Constants Page"
             Padding="5,25,5,0">
    <StackLayout>
       <Label Text="Hello, XAML!"
              TextColor="{x:Static local:AppConstants.BackgroundColor}"
              BackgroundColor="{x:Static local:AppConstants.ForegroundColor}"
              FontAttributes="Bold"
              FontSize="30"
              HorizontalOptions="Center" />
      <BoxView WidthRequest="{x:Static sys:Math.PI}"
               HeightRequest="{x:Static sys:Math.E}"
               Color="{x:Static local:AppConstants.ForegroundColor}"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand"
               Scale="100" />
    </StackLayout>
</ContentPage>
```



## 视图与视图绑定

可以定义数据绑定来链接同一页面上两个视图的属性。 在本例中，使用 `x:Reference` 标记扩展设置目标对象的 `BindingContext`。

以下示例包含一个 [Slider](https://learn.microsoft.com/zh-cn/dotnet/api/microsoft.maui.controls.slider) 和两个 [Label](https://learn.microsoft.com/zh-cn/dotnet/api/microsoft.maui.controls.label) 视图，其中一个视图按 [Slider](https://learn.microsoft.com/zh-cn/dotnet/api/microsoft.maui.controls.slider) 值旋转，另一个视图显示 [Slider](https://learn.microsoft.com/zh-cn/dotnet/api/microsoft.maui.controls.slider) 值：

```xml
<ContentPage xmlns="http://schemas.microsoft.com/dotnet/2021/maui"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SliderBindingsPage"
             Title="Slider Bindings Page">
    <StackLayout>
        <Label Text="ROTATION"
               BindingContext="{x:Reference slider}"
               Rotation="{Binding Path=Value}"
               FontAttributes="Bold"
               FontSize="18"
               HorizontalOptions="Center"
               VerticalOptions="Center" />
        <Slider x:Name="slider"
                Maximum="360"
                VerticalOptions="Center" />
        <Label BindingContext="{x:Reference slider}"
               Text="{Binding Value, StringFormat='The angle is {0:F0} degrees'}"
               FontAttributes="Bold"
               FontSize="18"
               HorizontalOptions="Center"
               VerticalOptions="Center" />
    </StackLayout>
</ContentPage>
```



## 绑定模式

单个视图可在其多个属性上创建数据绑定。 但每个视图只能有一个 `BindingContext`，因此视图上的所有数据绑定须引用同一对象的属性。

解决此问题和其他问题的方法涉及到 `Mode` 属性，该属性设置为 `BindingMode` 枚举的成员：

- `Default`
- `OneWay` - 值从源传输到目标
- `OneWayToSource` - 值从目标传输到源
- `TwoWay` - 值在源和目标之间双向传输
- `OneTime` - 只有在 `BindingContext` 更改时，数据才从源传输到目标

以下示例演示了 `OneWayToSource` 和 `TwoWay` 绑定模式的一种常见用法：

```xml
<ContentPage xmlns="http://schemas.microsoft.com/dotnet/2021/maui"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SliderTransformsPage"
             Padding="5"
             Title="Slider Transforms Page">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="Auto" />
        </Grid.ColumnDefinitions>

        <!-- Scaled and rotated Label -->
        <Label x:Name="label"
               Text="TEXT"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <!-- Slider and identifying Label for Scale -->
        <Slider x:Name="scaleSlider"
                BindingContext="{x:Reference label}"
                Grid.Row="1" Grid.Column="0"
                Maximum="10"
                Value="{Binding Scale, Mode=TwoWay}" />
        <Label BindingContext="{x:Reference scaleSlider}"
               Text="{Binding Value, StringFormat='Scale = {0:F1}'}"
               Grid.Row="1" Grid.Column="1"
               VerticalTextAlignment="Center" />

        <!-- Slider and identifying Label for Rotation -->
        <Slider x:Name="rotationSlider"
                BindingContext="{x:Reference label}"
                Grid.Row="2" Grid.Column="0"
                Maximum="360"
                Value="{Binding Rotation, Mode=OneWayToSource}" />
        <Label BindingContext="{x:Reference rotationSlider}"
               Text="{Binding Value, StringFormat='Rotation = {0:F0}'}"
               Grid.Row="2" Grid.Column="1"
               VerticalTextAlignment="Center" />

        <!-- Slider and identifying Label for RotationX -->
        <Slider x:Name="rotationXSlider"
                BindingContext="{x:Reference label}"
                Grid.Row="3" Grid.Column="0"
                Maximum="360"
                Value="{Binding RotationX, Mode=OneWayToSource}" />
        <Label BindingContext="{x:Reference rotationXSlider}"
               Text="{Binding Value, StringFormat='RotationX = {0:F0}'}"
               Grid.Row="3" Grid.Column="1"
               VerticalTextAlignment="Center" />

        <!-- Slider and identifying Label for RotationY -->
        <Slider x:Name="rotationYSlider"
                BindingContext="{x:Reference label}"
                Grid.Row="4" Grid.Column="0"
                Maximum="360"
                Value="{Binding RotationY, Mode=OneWayToSource}" />
        <Label BindingContext="{x:Reference rotationYSlider}"
               Text="{Binding Value, StringFormat='RotationY = {0:F0}'}"
               Grid.Row="4" Grid.Column="1"
               VerticalTextAlignment="Center" />
    </Grid>
</ContentPage>
```



## 绑定和集合

[ListView](https://learn.microsoft.com/zh-cn/dotnet/api/microsoft.maui.controls.listview) 定义 `IEnumerable` 类型的 `ItemsSource` 属性，并显示该集合中的项。 这些项可以是任何类型的对象。 默认情况下，[ListView](https://learn.microsoft.com/zh-cn/dotnet/api/microsoft.maui.controls.listview) 使用每个项的 `ToString` 方法来显示该项。 有时这正是你需要的，但在许多情况下，`ToString` 只返回对象的完全限定类名。

但是，可通过使用模板以任何方式显示 [ListView](https://learn.microsoft.com/zh-cn/dotnet/api/microsoft.maui.controls.listview) 集合中的项，该模板涉及派生自 [Cell](https://learn.microsoft.com/zh-cn/dotnet/api/microsoft.maui.controls.cell) 的类。 会为 [ListView](https://learn.microsoft.com/zh-cn/dotnet/api/microsoft.maui.controls.listview) 中的每个项克隆模板，并将模板上设置的数据绑定传输到每个克隆。 可使用 [ViewCell](https://learn.microsoft.com/zh-cn/dotnet/api/microsoft.maui.controls.viewcell) 类为项创建自定义单元格。

在 `NamedColor` 类的帮助下，[ListView](https://learn.microsoft.com/zh-cn/dotnet/api/microsoft.maui.controls.listview) 可以显示 .NET MAUI 中提供的所有已命名颜色的列表：

```c#
using System.Reflection;
using System.Text;

namespace XamlSamples
{
    public class NamedColor
    {
        public string Name { get; private set; }
        public string FriendlyName { get; private set; }
        public Color Color { get; private set; }

        // Expose the Color fields as properties
        public float Red => Color.Red;
        public float Green => Color.Green;
        public float Blue => Color.Blue;

        public static IEnumerable<NamedColor> All { get; private set; }

        static NamedColor()
        {
            List<NamedColor> all = new List<NamedColor>();
            StringBuilder stringBuilder = new StringBuilder();

            // Loop through the public static fields of the Color structure.
            foreach (FieldInfo fieldInfo in typeof(Colors).GetRuntimeFields())
            {
                if (fieldInfo.IsPublic &&
                    fieldInfo.IsStatic &&
                    fieldInfo.FieldType == typeof(Color))
                {
                    // Convert the name to a friendly name.
                    string name = fieldInfo.Name;
                    stringBuilder.Clear();
                    int index = 0;

                    foreach (char ch in name)
                    {
                        if (index != 0 && Char.IsUpper(ch))
                        {
                            stringBuilder.Append(' ');
                        }
                        stringBuilder.Append(ch);
                        index++;
                    }

                    // Instantiate a NamedColor object.
                    NamedColor namedColor = new NamedColor
                    {
                        Name = name,
                        FriendlyName = stringBuilder.ToString(),
                        Color = (Color)fieldInfo.GetValue(null)
                    };

                    // Add it to the collection.
                    all.Add(namedColor);
                }
            }
            all.TrimExcess();
            All = all;
        }
    }
}
```

使用 `x:Static` 标记扩展可以将静态 `NamedColor.All` 属性设置为 [ListView](https://learn.microsoft.com/zh-cn/dotnet/api/microsoft.maui.controls.listview) 的 `ItemsSource`：

```xml
<ContentPage xmlns="http://schemas.microsoft.com/dotnet/2021/maui"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples;assembly=XamlSamples"
             x:Class="XamlSamples.ListViewDemoPage"
             Title="ListView Demo Page">
    <ListView ItemsSource="{x:Static local:NamedColor.All}" />
</ContentPage>

//或者

<ContentPage xmlns="http://schemas.microsoft.com/dotnet/2021/maui"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples"
             x:Class="XamlSamples.ListViewDemoPage"
             Title="ListView Demo Page">
    <ContentPage.Resources>
        <x:Double x:Key="boxSize">50</x:Double>
        <x:Int32 x:Key="rowHeight">60</x:Int32>
        <local:FloatToIntConverter x:Key="intConverter" />
    </ContentPage.Resources>

    <ListView ItemsSource="{x:Static local:NamedColor.All}"
              RowHeight="{StaticResource rowHeight}">
        <ListView.ItemTemplate>
            <DataTemplate>
                <ViewCell>
                    <StackLayout Padding="5, 5, 0, 5"
                                 Orientation="Horizontal"
                                 Spacing="15">
                        <BoxView WidthRequest="{StaticResource boxSize}"
                                 HeightRequest="{StaticResource boxSize}"
                                 Color="{Binding Color}" />
                        <StackLayout Padding="5, 0, 0, 0"
                                     VerticalOptions="Center">
                            <Label Text="{Binding FriendlyName}"
                                   FontAttributes="Bold"
                                   FontSize="14" />
                            <StackLayout Orientation="Horizontal"
                                         Spacing="0">
                                <Label Text="{Binding Red,
                                                      Converter={StaticResource intConverter},
                                                      ConverterParameter=255,
                                                      StringFormat='R={0:X2}'}" />                                
                                <Label Text="{Binding Green,
                                                      Converter={StaticResource intConverter},
                                                      ConverterParameter=255,
                                                      StringFormat=', G={0:X2}'}" />                                
                                <Label Text="{Binding Blue,
                                                      Converter={StaticResource intConverter},
                                                      ConverterParameter=255,
                                                      StringFormat=', B={0:X2}'}" />
                            </StackLayout>
                        </StackLayout>
                    </StackLayout>
                </ViewCell>
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</ContentPage>
```



## 绑定值转换器

使用值转换器（也称为绑定转换器）解决此问题。 这是一个实现 [IValueConverter](https://learn.microsoft.com/zh-cn/dotnet/api/microsoft.maui.controls.ivalueconverter) 接口的类，这意味着它具有两个方法，分别名为 `Convert` 和 `ConvertBack`。 当值从源传输到目标时，将调用 `Convert` 方法。 在 `OneWayToSource` 或 `TwoWay` 绑定中从目标传输到源时将调用 `ConvertBack` 方法：

```C#
using System.Globalization;

namespace XamlSamples
{
    public class FloatToIntConverter : IValueConverter
    {
        public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
        {
            float multiplier;

            if (!float.TryParse(parameter as string, out multiplier))
                multiplier = 1;

            return (int)Math.Round(multiplier * (float)value);
        }

        public object ConvertBack(object value, Type targetType, object parameter, CultureInfo culture)
        {
            float divider;

            if (!float.TryParse(parameter as string, out divider))
                divider = 1;

            return ((float)(int)value) / divider;
        }
    }
}
```

绑定使用 `Converter` 属性引用绑定转换器。 绑定转换器还可以接受使用 `ConverterParameter` 属性指定的参数。 在某些多适应性场景中，会以此方式指定乘数。 绑定转换器会检查转换器参数是否为有效的 `float` 值。

转换器在页面的资源字典中实例化，因此可在多个绑定之间共享：

```xml
<local:FloatToIntConverter x:Key="intConverter" />
```

三个数据绑定引用此单一实例：

```xml
<Label Text="{Binding Red,
                      Converter={StaticResource intConverter},
                      ConverterParameter=255,
                      StringFormat='R={0:X2}'}" />
```



# 数据绑定和 MVVM

## 简单 MVVM

在 [XAML 标记扩展](https://learn.microsoft.com/zh-cn/dotnet/maui/xaml/fundamentals/markup-extensions?view=net-maui-8.0)中，你已了解如何定义新的 XML 命名空间声明，使 XAML 文件能够引用其他程序集中的类。 以下示例使用 `x:Static` 标记扩展从 `System` 命名空间中的静态 `DateTime.Now` 属性获取当前日期和时间：

```xml
<ContentPage xmlns="http://schemas.microsoft.com/dotnet/2021/maui"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:sys="clr-namespace:System;assembly=netstandard"
             x:Class="XamlSamples.OneShotDateTimePage"
             Title="One-Shot DateTime Page">

    <VerticalStackLayout BindingContext="{x:Static sys:DateTime.Now}"
                         Spacing="25" Padding="30,0"
                         VerticalOptions="Center" HorizontalOptions="Center">

        <Label Text="{Binding Year, StringFormat='The year is {0}'}" />
        <Label Text="{Binding StringFormat='The month is {0:MMMM}'}" />
        <Label Text="{Binding Day, StringFormat='The day is {0}'}" />
        <Label Text="{Binding StringFormat='The time is {0:T}'}" />

    </VerticalStackLayout>

</ContentPage>
```



## 交互式 MVVM

通常会基于基础数据模型将 MVVM 和双向数据绑定结合用于交互式视图。

以下示例演示了 `HslViewModel`，其中将 [Color](https://learn.microsoft.com/zh-cn/dotnet/api/microsoft.maui.graphics.color) 值转换为 `Hue`、`Saturation` 和 `Luminosity` 值，然后再转换回来：

```c#
using System.ComponentModel;
using System.Runtime.CompilerServices;

namespace XamlSamples;

class HslViewModel: INotifyPropertyChanged
{
    public event PropertyChangedEventHandler PropertyChanged;

    private float _hue, _saturation, _luminosity;
    private Color _color;

    public float Hue
    {
        get => _hue;
        set
        {
            if (_hue != value)
                Color = Color.FromHsla(value, _saturation, _luminosity);
        }
    }

    public float Saturation
    {
        get => _saturation;
        set
        {
            if (_saturation != value)
                Color = Color.FromHsla(_hue, value, _luminosity);
        }
    }

    public float Luminosity
    {
        get => _luminosity;
        set
        {
            if (_luminosity != value)
                Color = Color.FromHsla(_hue, _saturation, value);
        }
    }

    public Color Color
    {
        get => _color;
        set
        {
            if (_color != value)
            {
                _color = value;
                _hue = _color.GetHue();
                _saturation = _color.GetSaturation();
                _luminosity = _color.GetLuminosity();

                OnPropertyChanged("Hue");
                OnPropertyChanged("Saturation");
                OnPropertyChanged("Luminosity");
                OnPropertyChanged(); // reports this property
            }
        }
    }

    public void OnPropertyChanged([CallerMemberName] string name = "") =>
        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(name));
}
```

在此示例中，对 `Hue`、`Saturation` 和 `Luminosity` 属性的更改导致 `Color` 属性发生更改，而对 `Color` 属性的更改又导致另外三个属性发生更改。 这看上去像是一个无限循环，只不过视图模型在属性不发生更改时不会调用 `PropertyChanged` 事件。

```xml
<ContentPage xmlns="http://schemas.microsoft.com/dotnet/2021/maui"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples"
             x:Class="XamlSamples.HslColorScrollPage"
             Title="HSL Color Scroll Page">
    <ContentPage.BindingContext>
        <local:HslViewModel Color="Aqua" />
    </ContentPage.BindingContext>

    <VerticalStackLayout Padding="10, 0, 10, 30">
        <BoxView Color="{Binding Color}"
                 HeightRequest="100"
                 WidthRequest="100"
                 HorizontalOptions="Center" />
        <Label Text="{Binding Hue, StringFormat='Hue = {0:F2}'}"
               HorizontalOptions="Center" />
        <Slider Value="{Binding Hue}"
                Margin="20,0,20,0" />
        <Label Text="{Binding Saturation, StringFormat='Saturation = {0:F2}'}"
               HorizontalOptions="Center" />
        <Slider Value="{Binding Saturation}"
                Margin="20,0,20,0" />
        <Label Text="{Binding Luminosity, StringFormat='Luminosity = {0:F2}'}"
               HorizontalOptions="Center" />
        <Slider Value="{Binding Luminosity}"
                Margin="20,0,20,0" />
    </VerticalStackLayout>
</ContentPage>
```



## 命令

命令接口提供了另一种实现命令的方法，这种方法更适合 MVVM 体系结构。 视图模型可以包含命令，这些命令是针对视图中的特定活动（例如单击 [Button](https://learn.microsoft.com/zh-cn/dotnet/api/microsoft.maui.controls.button)）而执行的方法。 在这些命令和 [Button](https://learn.microsoft.com/zh-cn/dotnet/api/microsoft.maui.controls.button) 之间定义了数据绑定。

为了实现 [Button](https://learn.microsoft.com/zh-cn/dotnet/api/microsoft.maui.controls.button) 和视图模型之间的数据绑定，[Button](https://learn.microsoft.com/zh-cn/dotnet/api/microsoft.maui.controls.button) 定义了两个属性：

- [`System.Windows.Input.ICommand`](https://learn.microsoft.com/zh-cn/dotnet/api/system.windows.input.icommand) 类型的 `Command`
- `Object` 类型的 `CommandParameter`

[ICommand](https://learn.microsoft.com/zh-cn/dotnet/api/system.windows.input.icommand) 接口是在 [System.Windows.Input](https://learn.microsoft.com/zh-cn/dotnet/api/system.windows.input) 命名空间中定义的，由两个方法和一个事件组成：

- `void Execute(object arg)`
- `bool CanExecute(object arg)`
- `event EventHandler CanExecuteChanged`

视图模型可以定义 [ICommand](https://learn.microsoft.com/zh-cn/dotnet/api/system.windows.input.icommand) 类型的属性。 然后，可将这些属性绑定到每个 [Button](https://learn.microsoft.com/zh-cn/dotnet/api/microsoft.maui.controls.button) 或其他元素的 `Command` 属性，或绑定到实现此接口的自定义视图。 可以选择设置 `CommandParameter` 属性来标识绑定到此视图模型属性的各个 [Button](https://learn.microsoft.com/zh-cn/dotnet/api/microsoft.maui.controls.button) 对象（或其他元素）。 在内部，每当用户点击 [Button](https://learn.microsoft.com/zh-cn/dotnet/api/microsoft.maui.controls.button) 时，[Button](https://learn.microsoft.com/zh-cn/dotnet/api/microsoft.maui.controls.button) 都会调用 `Execute` 方法，并将其 `CommandParameter` 传递给 `Execute` 方法。

以下示例演示用于输入电话号码的简单键盘的视图模型:

```c#
using System.ComponentModel;
using System.Runtime.CompilerServices;
using System.Windows.Input;

namespace XamlSamples;

class KeypadViewModel: INotifyPropertyChanged
{
    public event PropertyChangedEventHandler PropertyChanged;

    private string _inputString = "";
    private string _displayText = "";
    private char[] _specialChars = { '*', '#' };

    public ICommand AddCharCommand { get; private set; }
    public ICommand DeleteCharCommand { get; private set; }

    public string InputString
    {
        get => _inputString;
        private set
        {
            if (_inputString != value)
            {
                _inputString = value;
                OnPropertyChanged();
                DisplayText = FormatText(_inputString);

                // Perhaps the delete button must be enabled/disabled.
                ((Command)DeleteCharCommand).ChangeCanExecute();
            }
        }
    }

    public string DisplayText
    {
        get => _displayText;
        private set
        {
            if (_displayText != value)
            {
                _displayText = value;
                OnPropertyChanged();
            }
        }
    }

    public KeypadViewModel()
    {
        // Command to add the key to the input string
        AddCharCommand = new Command<string>((key) => InputString += key);

        // Command to delete a character from the input string when allowed
        DeleteCharCommand =
            new Command(
                // Command will strip a character from the input string
                () => InputString = InputString.Substring(0, InputString.Length - 1),

                // CanExecute is processed here to return true when there's something to delete
                () => InputString.Length > 0
            );
    }

    string FormatText(string str)
    {
        bool hasNonNumbers = str.IndexOfAny(_specialChars) != -1;
        string formatted = str;

        // Format the string based on the type of data and the length
        if (hasNonNumbers || str.Length < 4 || str.Length > 10)
        {
            // Special characters exist, or the string is too small or large for special formatting
            // Do nothing
        }

        else if (str.Length < 8)
            formatted = string.Format("{0}-{1}", str.Substring(0, 3), str.Substring(3));

        else
            formatted = string.Format("({0}) {1}-{2}", str.Substring(0, 3), str.Substring(3, 3), str.Substring(6));

        return formatted;
    }


    public void OnPropertyChanged([CallerMemberName] string name = "") =>
        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(name));
}
```

在此示例中，命令的 `Execute` 和 `CanExecute` 方法在构造函数中定义为 Lambda 函数。 视图模型假定 `AddCharCommand` 属性绑定到多个按钮（或任何其他具有命令接口的控件）的 `Command` 属性，其中每个按钮都由 `CommandParameter` 标识。 这些按钮将字符添加到 `InputString` 属性，然后格式化为 `DisplayText` 属性的电话号码。 还有另一个 [ICommand](https://learn.microsoft.com/zh-cn/dotnet/api/system.windows.input.icommand) 类型的属性，名为 `DeleteCharCommand`。 该属性绑定到退格按钮，但如果没有要删除的字符，应禁用该按钮。

下面的示例显示使用 `KeypadViewModel` 的 XAML：

```xml
<ContentPage xmlns="http://schemas.microsoft.com/dotnet/2021/maui"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples"
             x:Class="XamlSamples.KeypadPage"
             Title="Keypad Page">
    <ContentPage.BindingContext>
        <local:KeypadViewModel />
    </ContentPage.BindingContext>

    <Grid HorizontalOptions="Center" VerticalOptions="Center">
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="80" />
            <ColumnDefinition Width="80" />
            <ColumnDefinition Width="80" />
        </Grid.ColumnDefinitions>

        <Label Text="{Binding DisplayText}"
               Margin="0,0,10,0" FontSize="20" LineBreakMode="HeadTruncation"
               VerticalTextAlignment="Center" HorizontalTextAlignment="End"
               Grid.ColumnSpan="2" />

        <Button Text="&#x21E6;" Command="{Binding DeleteCharCommand}" Grid.Column="2"/>

        <Button Text="1" Command="{Binding AddCharCommand}" CommandParameter="1" Grid.Row="1" />
        <Button Text="2" Command="{Binding AddCharCommand}" CommandParameter="2" Grid.Row="1" Grid.Column="1" />
        <Button Text="3" Command="{Binding AddCharCommand}" CommandParameter="3" Grid.Row="1" Grid.Column="2" />

        <Button Text="4" Command="{Binding AddCharCommand}" CommandParameter="4" Grid.Row="2" />
        <Button Text="5" Command="{Binding AddCharCommand}" CommandParameter="5" Grid.Row="2" Grid.Column="1" />
        <Button Text="6" Command="{Binding AddCharCommand}" CommandParameter="6" Grid.Row="2" Grid.Column="2" />

        <Button Text="7" Command="{Binding AddCharCommand}" CommandParameter="7" Grid.Row="3" />
        <Button Text="8" Command="{Binding AddCharCommand}" CommandParameter="8" Grid.Row="3" Grid.Column="1" />
        <Button Text="9" Command="{Binding AddCharCommand}" CommandParameter="9" Grid.Row="3" Grid.Column="2" />

        <Button Text="*" Command="{Binding AddCharCommand}" CommandParameter="*" Grid.Row="4" />
        <Button Text="0" Command="{Binding AddCharCommand}" CommandParameter="0" Grid.Row="4" Grid.Column="1" />
        <Button Text="#" Command="{Binding AddCharCommand}" CommandParameter="#" Grid.Row="4" Grid.Column="2" />
    </Grid>
</ContentPage>
```







































