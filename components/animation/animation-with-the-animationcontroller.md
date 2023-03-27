---
description: Describe how to create powerful animations with the AnimationController class
---

# Animation with the AnimationController

MauiReactor features a second powerful way to animate views inside a component through the `AnimationController` class and `Animation`-derived types.&#x20;

`AnimationController` is a standard `VisualNode`-derived class that you can render inside any component tree. Even if you can have more than one AnimationController inside a single component often just one is flexible enough to accomplish most of the animations.

Each `AnimationController` has internally a timer that you can control by playing it or putting it in Pause/Stop.

The `AnimationController` class itself host a list of Animation objects.

These are the types of `Animation` available in MauiReactor so far:

* `ParallelAnimation`: executes child animations in parallel
* `SequenceAnimation`: runs child animations in sequence (i.e. one after another)
* `DoubleAnimation`: Is a tween animation that fires an event containing a value between 2 doubles (From/To). You can customize how this value is generated using an Easing function.
* `CubicBezierPathAnimation`: is a tween animation that generates values as Points between StartPoint and EndPoint using a bezier function in which you can control setting ControlPoint1 and ControlPoint2
* `QuadraticBezierPathAnimation`: is a tween animation similar to the Bezier animation that generates a point between StartPoint and EndPoint using a quadratic bezier function which you can control by setting a ControlPoint

Each `Animation` has a duration and you can compose them as you like in a tree structure.

This is an example of an animation tree extracted from the MauiReactor sample app:

```csharp
new AnimationController
{
    new SequenceAnimation
    {
        new DoubleAnimation()
            .StartValue(0)
            .TargetValue(300)
            .Duration(TimeSpan.FromSeconds(2.5))
            .Easing(Easing.CubicOut)
            .OnTick(v => ....),

        new DoubleAnimation()
            .StartValue(0)
            .TargetValue(300)
            .Duration(TimeSpan.FromSeconds(1.5))
            .OnTick(v => ....)
    }

    new SequenceAnimation
    {
        new DoubleAnimation()
            .StartValue(0)
            .TargetValue(300)
            .Duration(TimeSpan.FromSeconds(2))                            
            .OnTick(v => ....),

        new CubicBezierPathAnimation()
            .StartPoint(new Point(0,100))
            .EndPoint(new Point(300,100))
            .ControlPoint1(new Point(0,0))
            .ControlPoint2(new Point(300,200))
            .OnTick(v => ....),

        new QuadraticBezierPathAnimation()
            .StartPoint(new Point(300,100))
            .EndPoint(new Point(0,100))
            .ControlPoint(new Point(150,200))
            .OnTick(v => ....)
    }
}
```

`SequenceAnimation` and `ParallelAnimation` are `Animation` containers that do not fire events (i.e. do not have `OnTick()` property) because their purpose is only to control child animations.

TweenAnimation types like `DoubleAnimation`, `CubicBezierPathAnimation`, and `QuadraticBezierPathAnimation` fire events that you can register with the `OnTick` callback where you can easily set component State properties.&#x20;

Moving objects is then as easy as connecting animated property values inside the component render to State properties.

{% hint style="info" %}
Even is technically doable, doesn't make much sense to use an `AnimationController` inside a State-less component
{% endhint %}

The `AnimationController` object can be paused (`IsPaused` = true/false): when an animation is paused it keeps internal values and restarts from the same point.

An `AnimationController` can also be stopped (`IsEnabled` = true/false) and in this case, the animations of all child objects are restored to the initial state.

Each `Animation` type has specific properties (for example `Animation` containers like SequenceAnimation and ParallelAnimation have InitalDelay or Loop properties) that help you describe exactly the generated values at the correct time.

`AnimationController` is correctly hot-reloaded and keeps its internal state (as any other MauiReactor object) between iterations.

MauiReactor GitHub repository contains several samples that show how to use `AnimationController` in different scenarios.
