---
description: >-
  This page describes how is possible to integrate MauiReactor components in a
  classic XAML-C# MAUI application
---

# XAML Integration

Sometimes, you may want to adopt the MVU approach only for a portion of the application. For example, when you have already developed an XAML-based app and are not ready to completely rewrite it using a different framework like MauiReactor.

XAML integration in MauiReactor allows you to run a MauiReactor component inside a standard MAUI page or view. You can even use a MauiReactor component for an entire page while the rest of the app uses a classic MVVM approach.

{% hint style="warning" %}
Using MauiReactor (i.e., MVU) for the entire application remains recommended.

Consider this feature if you are facing the following scenarios:

1\) You have completed the development of a standard MAUI application or are in an advanced stage of the process where moving to MauiReactor is not feasible, but you're interested in adopting MauiReactor in the future

2\) You may want to adopt/experiment with the MVU approach for some specific portion or page of the UI

Do not mix MVU and MVVM code/approaches but use the dependency inject to share services.
{% endhint %}

1\) Let's start by adding the MauiReactor Nuget package to the project (pick the latest version):

`dotnet add package Reactor.Maui`

2\) Configure the application to use MauiReactor:

<pre class="language-csharp" data-line-numbers><code class="lang-csharp">var builder = MauiApp.CreateBuilder();
builder
    .UseMauiApp&#x3C;App>()
<strong>    .UseMauiReactor() //&#x3C;--- ADD THIS LINE
</strong>    .ConfigureFonts(fonts =>
    {
        fonts.AddFont("OpenSans-Regular.ttf", "OpenSansRegular");
        fonts.AddFont("OpenSans-Semibold.ttf", "OpenSansSemibold");
    });
</code></pre>

## Host a MauiReactor component

To host a MauiReactor component on your page, please use the `ComponenHost` class like it's shown in the following snippet code:

<pre class="language-xml" data-line-numbers><code class="lang-xml">&#x3C;?xml version="1.0" encoding="utf-8" ?>
&#x3C;ContentPage xmlns="http://schemas.microsoft.com/dotnet/2021/maui"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
<strong>             xmlns:MauiReactor="clr-namespace:MauiReactor.Integration;assembly=MauiReactor"
</strong>             xmlns:components="clr-namespace:IntegrationTest.Components"
             x:Class="IntegrationTest.MainPage">

    &#x3C;ScrollView>
        &#x3C;VerticalStackLayout
            Spacing="25"
            Padding="30,0"
            VerticalOptions="Center">

            &#x3C;Image
                Source="dotnet_bot.png"
                SemanticProperties.Description="Cute dot net bot waving hi to you!"
                HeightRequest="200"
                HorizontalOptions="Center" />

            &#x3C;Label
                Text="Hello, World!"
                SemanticProperties.HeadingLevel="Level1"
                FontSize="32"
                HorizontalOptions="Center" />

            &#x3C;Label
                Text="Welcome to .NET Multi-platform App UI"
                SemanticProperties.HeadingLevel="Level2"
                SemanticProperties.Description="Welcome to dot net Multi platform App U I"
                FontSize="18"
                HorizontalOptions="Center" />

<strong>            &#x3C;MauiReactor:ComponentHost
</strong><strong>                Component="{x:Type components:Counter}"/>
</strong>
            &#x3C;Button
                Text="Click Me!"
                Clicked="Button_Clicked"
                />

        &#x3C;/VerticalStackLayout>
    &#x3C;/ScrollView>

&#x3C;/ContentPage>

</code></pre>

In line 4, you have to specify the MauiReactor namespace and assembly containing the `ComponentHost` class.

In lines 33 and 34, we're going to create an instance of the control `ComponentHost` passing the `Counter` component type as a parameter: the `ComponentHost` class will instantiate and run the component.

Of course, the same can be done in code:

```csharp
var componentHost = new MauiReactor.ComponentHost
{
    Component = typeof(Counter)
}
```

## Host a MauiReactor page

Another way to integrate a MauiReactor component is to navigate to another page passing a component as its root using the overload of the `Navigation` class `Navigation.PushAsync<Component-Type>()`

```csharp
await Navigation.PushAsync<ChildPage>();
```

Where ChildPage is defined as:

```csharp
class ChildPage : Component
{
    public override VisualNode Render()
    {
        return new MauiReactor.ContentPage()
        {
            new MauiReactor.Label("Hello from MauiReactor!")
        };
    }
}
```

The same works with the Shell as well. You need to register the route using the static method `Routing.RegisterRoute` and navigate to it with the usual `Shell.Current.GoToAsync` call:

```csharp
Routing.RegisterRoute<Page2>("page-2");
...
await MauiControls.Shell.Current.GoToAsync("page-2");
```
