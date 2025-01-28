---
description: Describes the list of changes present in Version 3 of MauiReactor
---

# What's new in Version 3

Version 3 of MauiReactor targets MAUI .NET 9. If you're using an earlier version of MauiReactor, please note the following short-step list of changes required to move forward to version 3.

{% hint style="info" %}
For an up-to-date list of new features for MauiReactor 3 please head to [https://github.com/adospace/reactorui-maui/issues/263](https://github.com/adospace/reactorui-maui/issues/263)
{% endhint %}

## Hot-reload changes

Hot-reload must be enabled using the new Feature switch available in .NET 9 ([https://github.com/dotnet/designs/blob/main/accepted/2020/feature-switch.md](https://github.com/dotnet/designs/blob/main/accepted/2020/feature-switch.md)).

Remove the EnableMauiReactorHotReload() call in `program.cs` :

<pre class="language-csharp" data-line-numbers><code class="lang-csharp">var builder = MauiApp.CreateBuilder();
builder
    .UseMauiReactorApp&#x3C;HomePage>()
<strong>#if DEBUG
</strong><strong>    .EnableMauiReactorHotReload()
</strong><strong>#endif
</strong>    ...;

</code></pre>

In the project definition add the following lines:

```xml
<ItemGroup Condition="'$(Configuration)'=='Debug'">
  <RuntimeHostConfigurationOption Include="MauiReactor.HotReload" Value="true" Trim="false" />
</ItemGroup>
```

## XAML resources

Due to an internal change of the .NET Maui 9 framework, loading the resources (i.e. styles, brushes, etc) from XAML dictionaries is slightly less straightforward.

First, create an App.xaml and App.xaml.cs (or copy them from the standard .NET MAUI template) and link your resources as merged dictionaries of the app:

```xml
<?xml version = "1.0" encoding = "UTF-8" ?>
<local:MauiReactorApplication xmlns="http://schemas.microsoft.com/dotnet/2021/maui"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:MauiReactor.TestApp"
             x:Class="MauiReactor.TestApp.App">
    <local:MauiReactorApplication.Resources>
        <ResourceDictionary>
            <ResourceDictionary.MergedDictionaries>
                <ResourceDictionary Source="Resources/Styles/Colors.xaml" />
                <ResourceDictionary Source="Resources/Styles/Styles.xaml" />
            </ResourceDictionary.MergedDictionaries>
        </ResourceDictionary>
    </local:MauiReactorApplication.Resources>
</local:MauiReactorApplication>
```

{% hint style="warning" %}
Ensure that the file build action is set to MauiXaml:\
![](.gitbook/assets/image.png)
{% endhint %}

In the App.xaml.cs file, create a derived class of the standard MAUI Application like the following:

```csharp
public partial class App: ReactorApplication<HomePage>
{
    public App(IServiceProvider serviceProvider)
        :base(serviceProvider)
    {
        InitializeComponent();
    }
}
```

Note that the class injects the IServiceProvider object and passes it to the base class. `HomePage` class represents the root component of the application.

Finally, use the standard UseMauiApp method in your Program.cs file load the application:

```csharp
var builder = MauiApp.CreateBuilder();
builder
    .UseMauiApp<App>()
    ...

```

If you need also to customize the MauiReactor application, i.e. also adding your c# styles from the [application theming ](components/theming.md)feature, you need to create an additional class that derives from ReactorApplication and set your application theme as shown below:

```csharp
public partial class App : MauiReactorApplication
{
    public App(IServiceProvider serviceProvider)
        :base(serviceProvider)
    {
        InitializeComponent();
    }
}


public abstract class MauiReactorApplication : ReactorApplication<HomePage>
{
    public MauiReactorApplication(IServiceProvider serviceProvider)
        :base(serviceProvider)
    {
        this.UseTheme<AppTheme>();
    }
}
```

## AOT compliance

MauiReactor 3 is fully AOT compatible: to compile your application to a native iOS or Mac Catalyst application you have to add these lines to your project definition:

```xml
<ItemGroup Condition="'$(Configuration)'=='Release'">
    <RuntimeHostConfigurationOption Include="MauiReactor.HotReload" Value="false" Trim="true" />
</ItemGroup>
```

{% hint style="info" %}
Please follow official guidelines to enable the AOT compilation in your .NET 9 MAUI app: [https://learn.microsoft.com/en-us/dotnet/maui/deployment/nativeaot?view=net-maui-9.0](https://learn.microsoft.com/en-us/dotnet/maui/deployment/nativeaot?view=net-maui-9.0)
{% endhint %}

