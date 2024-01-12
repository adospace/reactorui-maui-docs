---
description: How to consume services with dependency injection
---

# Dependency injection

MAUI.NET is deeply integrated with the Microsoft Dependency injection extensions library and provides a structured way to inject services and consume them inside ViewModel classes.

MauiReactor works mainly the same except you can access services through the `Services` property inside your components.

For example, let's see how to consume a simple Calc service like this:

```csharp
public class CalcService
{
    public double Add(double x, double y) => x + y;
}
```

We, first, have to register the services during the startup of the app:

```csharp
public static MauiApp CreateMauiApp()
{
    var builder = MauiApp.CreateBuilder();
    builder
        .UseMauiReactorApp<MainPage>()
#if DEBUG
        .EnableMauiReactorHotReload()
#endif
        );
    
    builder.Services.AddSingleton<CalcService>()

    return builder.Build();
}
```

Then we can access it inside our components:

<pre class="language-csharp" data-line-numbers><code class="lang-csharp">public class MainPageState
{
    public double X { get; set; }
    public double Y { get; set; }
    public double Result { get; set; }
}

public class MainPage : Component&#x3C;MainPageState>
{
    public override VisualNode Render()
    {
        return new NavigationPage()
        {
            new ContentPage()
            { 
                new StackLayout()
                {
                    NumericEntry(State.X, v => SetState(s => s.X = v)),
                    new Label(" + ")
                        .VCenter(),
                    NumericEntry(State.Y, v => SetState(s => s.Y = v)),
                    new Button(" + ")
                        .OnClick(OnAdd),
                    new Label(State.Result)
                        .VCenter()
                }
                .Spacing(10)
                .VCenter()
                .HCenter()
                .WithHorizontalOrientation()
            }
            .Title("DI Container Sample")
        };
    }

    private void OnAdd()
    {
<strong>        var calcService = Services.GetRequiredService&#x3C;CalcService>();
</strong>        SetState(s => s.Result = calcService.Add(State.X, State.Y));
    }

    private Entry NumericEntry(double value, Action&#x3C;double> onSet)
        => new Entry(value.ToString())
            .OnAfterTextChanged(s => onSet(double.Parse(s)))
            .Keyboard(Keyboard.Numeric)
            .VCenter();
}
</code></pre>

At line 38 we get the reference to the singleton service and call its Add method().

{% hint style="info" %}
In MauiReactor, it's recommended to put service implementations and models in a project (assembly) different from the one containing the app.
{% endhint %}
