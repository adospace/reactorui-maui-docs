---
description: >-
  This page describes how to trigger timed events using the MauiReactor Timer
  object
---

# Timer

When developing an app may happen that you need a timer that raises events regularly. In MauiReactor, as in .NET MAUI applications, you can use the timer types provided by the runtime (for example, the System.Timers.Timer class).

In MauiReactor, I recommend taking a look at `Timer` type provided by the library that is better suited to be integrated into MVU applications.

A MauiReactor Timer can be created as any other widget and added as a child of any other object:

```csharp
Grid(
    Timer()
        .IsEnabled(State.ShowCountdown)
        .Interval(1000) //interval in ms
        .OnTick(....)
)
```

Run the timer setting to true its IsEnabled property and pass an action to the OnTick property to implement your logic.

For example, in the following code, we create a timer and update a label every second:

```csharp
class CounterPageState
{
    public int Counter { get; set; }
    public bool TimerRunning { get; set; }
}

class CounterPage : Component<CounterPageState>
{
    public override VisualNode Render()
        => ContentPage("Counter Sample",
            VStack(
                Timer()
                    .IsEnabled(State.TimerRunning)
                    .Interval(1000)
                    .OnTick(() => SetState(s => s.Counter++)),

                Label($"Counter: {State.Counter}"),

                Button(State.TimerRunning ? "Pause" : "Run")
                    .OnClicked(() => SetState(s => s.TimerRunning = !s.TimerRunning))
            )
            .Spacing(10)
            .Center()
        );
}
```
