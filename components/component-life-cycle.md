---
description: Describes the component life-cycle
---

# Component life-cycle

Any MauiReactor application is composed of a logical tree of components. Each component renders its content using the overload function `Render()`. Inside it, the component can create a logical tree of Maui controls and/or other components.

It's important to understand how components are Created, Removed, or "Migrated" when a new logical tree is created after the current tree is invalidated (calling a method like `SetState` or `Invalidate`).

The key events in the life of a component are:

* **Mounted** (aka Created) is raised after the component is created and added to the logical tree.\
  It's the ideal place to initialize the state of the component, directly setting state properties or calling an external web service that will update the state. You can think of this event more or less like a class constructor.
* **WillUnmount** (aka Removing) is raised when the component is about to be removed from the logical tree. It's the perfect place to unsubscribe from service events or deallocate unmanaged resources (for example, use this overload to unsubscribe from a web socket event in a chat app).
* **PropsChanged** (aka Migrated) is raised when the component has been migrated to the next logical tree. It's the ideal place to verify whether it is required to update the state. Often this overload contains code similar to the code used for the Mounted event.

{% hint style="warning" %}
Each of these events is called under the main UI dispatcher thread so be sure to put any expensive call (for example calls to the network or file system) in a separate task using the async/await pattern.
{% endhint %}

To better understand how these events are fired we can create a sample like the following tracing down in the console when they are called:

```csharp
class MainPageState
{
    public bool Toggle { get; set; }
    public int CurrentValue { get; set; }
}

class MainPage : Component<MainPageState>
{
    public override VisualNode Render()
    =>  ContentPage(
            VStack(spacing: 5,
                Button($"Use {(!State.Toggle ? "increment" : "decrement")} button", ()=> SetState(s => s.Toggle = !s.Toggle)),
                State.Toggle ?
                    new IncrementalCounter()
                        .CurrentValue(State.CurrentValue)
                        .ValueChanged(v => SetState(s => s.CurrentValue = v))
                    :
                    new DecrementalCounter()
                        .CurrentValue(State.CurrentValue)
                        .ValueChanged(v => SetState(s => s.CurrentValue = v))
            )
            .Center()
        );
}

partial class IncrementalCounter : Component
{ 
    [Prop]
    int _currentValue;
    [Prop]
    private Action<int> _valueChanged;

    protected override void OnMounted()
    {
        Debug.WriteLine("[IncrementalCounter] OnMounted()");
        base.OnMounted();
    }

    protected override void OnPropsChanged()
    {
        Debug.WriteLine($"[IncrementalCounter] OnPropsChanged(_currentValue={_currentValue})");
        base.OnPropsChanged();
    }

    protected override void OnWillUnmount()
    {
        Debug.WriteLine("[IncrementalCounter] OnWillUnmount()");
        base.OnWillUnmount();
    }

    public override VisualNode Render()
    {
        Debug.WriteLine("[IncrementalCounter] Render()");

        return Button($"Increment from {_currentValue}!")
            .OnClicked(() => _valueChanged?.Invoke(++_currentValue));
    }
}

class DecrementalCounter : Component
{
    [Prop]
    int _currentValue;
    [Prop]
    private Action<int> _valueChanged;

    protected override void OnMounted()
    {
        Debug.WriteLine("[DecrementalCounter] OnMounted()");
        base.OnMounted();
    }

    protected override void OnPropsChanged()
    {
        Debug.WriteLine($"[DecrementalCounter] OnPropsChanged(_currentValue={_currentValue})");
        base.OnPropsChanged();
    }

    protected override void OnWillUnmount()
    {
        Debug.WriteLine("[DecrementalCounter] OnWillUnmount()");
        base.OnWillUnmount();
    }

    public override VisualNode Render()
    {
        Debug.WriteLine("[DecrementalCounter] Render()");

        return new Button($"Decrement from {_currentValue}!")
            .OnClicked(() => _valueChanged?.Invoke(--_currentValue));
    }
}
```

Running this code you should see an app like this:

<figure><img src="../.gitbook/assets/image (3) (1).png" alt="" width="327"><figcaption><p>Sample app tracing component life-cycle events</p></figcaption></figure>

Playing a bit with it, you should be able to see tracing lines like the following that could help to understand how events are sequenced:

```
[0:] [DecrementalCounter] OnMounted()
[0:] [DecrementalCounter] Render()

[0:] [DecrementalCounter] OnWillUnmount()
[0:] [IncrementalCounter] OnMounted()
[0:] [IncrementalCounter] Render()

[0:] [IncrementalCounter] OnPropsChanged(_currentValue=1)
[0:] [IncrementalCounter] Render()

[0:] [IncrementalCounter] OnPropsChanged(_currentValue=2)
[0:] [IncrementalCounter] Render()

[0:] [IncrementalCounter] OnPropsChanged(_currentValue=3)
[0:] [IncrementalCounter] Render()
```
