---
description: Describes how to specify properties for a component
---

# Component Properties

When creating a component you almost always need to pass props (or parameters/property values) in order to customize its appearance or behavior. In MauiReactor you can use plain properties.

Take for example this component that implements an activity indicator with a label:

```csharp
public class BusyComponent : Component
{
    private string _message;
    private bool _isBusy;

    public BusyComponent Message(string message)
    {
        _message = message;
        return this;
    }

    public BusyComponent IsBusy(bool isBusy)
    {
        _isBusy = isBusy;
        return this;
    }

    public override VisualNode Render()
    {
        return new StackLayout()
        {
            new ActivityIndicator()
                .IsRunning(_isBusy),
            new Label()
                .Text(_message)
        };
    }
}
```

and this is how we can use it on a page:

```csharp
public class BusyPageState : IState
{
    public bool IsBusy { get; set; }
}

public class BusyPageComponent : Component<BusyPageState>
{
    protected override void OnMounted()
    {
        SetState(_ => _.IsBusy = true);

        //OnMounted is called on UI Thread, move the slow code to a background thread
        Task.Run(async () =>
        {
            //Simulate lenghty work
            await Task.Delay(3000);

            SetState(_ => _.IsBusy = false);
        });

        base.OnMounted();
    }

    public override VisualNode Render()
    {
        return new ContentPage()
        {
            State.IsBusy ?
            new BusyComponent()
                .Message("Loading")
                .IsBusy(true)
            :
            RenderPage()
        };
    }

    private VisualNode RenderPage()
        => new Label("Done!")
                .VCenter()
                .HCenter();
}
```

{% hint style="info" %}
If you need to set properties of components hosted on a different page you should use a Props object (see [Navigation](navigation/navigation.md))
{% endhint %}
