---
description: How to consume services with dependency injection
---

# Dependency injection

{% hint style="warning" %}
In MauiReactor, it's recommended to put service implementations and models in a project (assembly) different from the one containing the app.\
Hot-reloading a project that contains dependency-injected services requires them to be hosted in a different assembly/project.
{% endhint %}

MAUI.NET is deeply integrated with the Microsoft Dependency injection extensions library and provides a structured way to inject services and consume them inside ViewModel classes.

MauiReactor works mainly the same except you can access services through the `Services` property inside your components.

For example, let's see how to consume a simple Calc service like this (created in a class library referenced by the main MAUI project):

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
    [Inject]
    CalcService _calcService;

    public override VisualNode Render()
        => NavigationPage(
             ContentPage("DI Container Sample",
                HStack(spacing: 10,
                    NumericEntry(State.X, v => SetState(s => s.X = v)),
                    Label(" + ")
                        .VCenter(),
                    NumericEntry(State.Y, v => SetState(s => s.Y = v)),
                    Button(" + ")
                        .OnClick(OnAdd),
                    Label(State.Result)
                        .VCenter()
                )
                .Center()
            )
            .Title("DI Container Sample")
        );

    private void OnAdd()
    {
<strong>        SetState(s => s.Result = _calcService.Add(State.X, State.Y));
</strong>    }

    private Entry NumericEntry(double value, Action&#x3C;double> onSet)
        => Entry(value.ToString())
            .OnAfterTextChanged(s => onSet(double.Parse(s)))
            .Keyboard(Keyboard.Numeric)
            .VCenter();
}
</code></pre>

At line 38 we get the reference to the singleton service and call its Add method().
