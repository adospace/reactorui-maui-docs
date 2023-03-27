---
description: This page describes ho to deal with resources stored in XAML files
---

# Using XAML Resources

MauiReactor supports the XAML styling system. You can specify your resources in the startup stage with code as shown below:

```csharp
var builder = MauiApp.CreateBuilder();
builder
    .UseMauiReactorApp<ShellPage>(app => 
    {
        app.AddResource("Resources/Styles/DefaultTheme.xaml");
    })
```

{% hint style="info" %}
Hot-reloading an application that links XAML files may require you to set the Full mode option: `dotnet-maui-reactor -f net6.0-android --mode Full`
{% endhint %}

Every resource found in the specified files is directly accessible inside components just by accessing the Application.Resources dictionary:

```csharp
new Button()
.Set(VisualElement.StyleProperty, Application.Current.Resources["MyStyle"])
```

{% hint style="info" %}
Even if technically doable it's not possible to store or retrieve resources from the `VisualElement.Resources` dictionary. Resource lookup (from the current VisualElement up to the Application) is actually discouraged in MauiReactor while it's perfectly fine to have all the resources in files that are linked at startup as shown above.
{% endhint %}

It's even possible (but not recommended) to create a DynamicResource link between a DependencyProperty and a Resource using code like shown below:

```csharp
new Button(buttonRef => buttonRef?.SetDynamicResource(VisualElement.StyleProperty, "MyStyle"))
```

{% hint style="warning" %}
DynamicResource in .NET MAUI allows you to "link" a dependency property to a Resource (like a Style for example) so that when you change the resource the dependency property is updated accordingly. MauiReactor doesn't play well with it because it directly refreshes dependency properties after component invalidation (for more info get a look at the [Native vs Visual Tree page](native-tree-and-visual-tree.md).
{% endhint %}

MauiReactor repo on GitHub contains the WeatherTwentyOne sample project that is a direct porting from a classic .NET MAUI project that makes heavy usage of XAML resource files (this is the [link](https://github.com/adospace/reactorui-maui/tree/main/samples/MauiReactor.WeatherTwentyOne)).
