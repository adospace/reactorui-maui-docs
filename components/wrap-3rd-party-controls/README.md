---
description: >-
  Describe how to create a wrapper for any control in order to use it inside
  your component
---

# Wrap 3rd party controls

MauiReactor lets you build MAUI applications from components in an MVU environment. Often you need to integrate external controls that are provided by Microsoft itself, by community OSS projects, or by commercial vendors.

To use these controls in MauiReactor you have to create wrappers that can handle the initializations (i.e. Mount), update (Render), and finalization (Unmount) of the native widget.

For this, MauiReactor provides a source generator that is triggered by the attribute `ScaffoldAttribute`

{% hint style="info" %}
_Source generators_ are a powerful way to augment user source code. ScaffoldGenerator library creates a MauiReactor wrapper for a native control. You are not strictly required to know how _source generators_ work but if interested take a look [https://learn.microsoft.com/en-us/dotnet/csharp/roslyn-sdk/source-generators-overview](https://learn.microsoft.com/en-us/dotnet/csharp/roslyn-sdk/source-generators-overview)
{% endhint %}

You just need to create a partial class with the name you like and apply the attribute `Scaffold`with the type of native control that you'd like to embed.

For example, the following code creates a `VisualNode` for the LiveChartsCore.SkiaSharpView.Maui.CartesianChart control from the great charting library :

```csharp
[Scaffold(typeof(LiveChartsCore.SkiaSharpView.Maui.CartesianChart))]
partial class CartesianChart { }
```

{% hint style="info" %}
A VisualNode is a node in the Visual Tree that MauiReactor creates for each page. It's a lightweight class that handles the initialization, update, and release of a native control in the MVU framework. For more info please take a look at [native-tree-and-visual-tree.md](../../deep-dives/native-tree-and-visual-tree.md "mention")
{% endhint %}

{% hint style="warning" %}
MauiReactor can handle any kind of MAUI controls, but for some, the bare-bone wrapper is created automatically by the `ScaffoldGenerator` is not enough to handle every aspect of the native widget (for example when a `DataTemplate` is used to render child items). For those that are required to handle such behavior please take a look at the MauiReactor code (especially the implementation of wrappers for ItemsView and Shell) or open an issue [here](https://github.com/adospace/reactorui-maui/issues)
{% endhint %}

{% hint style="info" %}
Please take a look at the MauiReactor integrations repository [here](https://github.com/adospace/mauireactor-integration): you'll find many wrappers for most common 3rd party control vendors
{% endhint %}

As you have the wrapper you can use it in a component as any other control:

```csharp

[Scaffold(typeof(LiveChartsCore.SkiaSharpView.Maui.PieChart))]
partial class PieChart { }

[Scaffold(typeof(LiveChartsCore.SkiaSharpView.Maui.CartesianChart))]
partial class CartesianChart { }

[Scaffold(typeof(LiveChartsCore.SkiaSharpView.Maui.PolarChart))]
partial class PolarChart { }



class ChartPageState
{
    public double[] Values { get; set; } = new double[] { 2, 1, 2, 3, 2, 3, 3 };
}


class ChartPage : Component<ChartPageState>
{
    static readonly Random _rnd = new();

    public override VisualNode Render()
        => ContentPage("Chart Sample",
            Grid("*, *, Auto", "*",
               new PolarChart()
                    .Series(() => new ISeries[]
                    {
                        new PolarLineSeries<double>
                        {
                            Values = State.Values,
                            Fill = null,
                            IsClosed = false,
                            Stroke = new SolidColorPaint(SKColors.Blue) { StrokeThickness = 5 },
                        }
                    }),


                new CartesianChart()
                    .Series(() => new ISeries[]
                    {
                        new LineSeries<double>
                        {
                            Values = State.Values,
                            Fill = null,
                            Stroke = new SolidColorPaint(SKColors.Green) { StrokeThickness = 2 },

                        }
                    })
                    .GridRow(1),

                Slider()
                    .GridRow(2)
                    .Minimum(2)
                    .Maximum(25)
                    .Margin(5)
                    .Value(()=>State.Values.Length)
                    .OnValueChanged((s, args)=>
                    {
                        SetState(s => s.Values =
                            Enumerable.Range(1, (int)args.NewValue)
                            .Select(_=>_rnd.NextDouble()*20.0)
                            .ToArray(), false);
                    })
            )
        )
        .BackgroundColor(Colors.Black);
}
```
