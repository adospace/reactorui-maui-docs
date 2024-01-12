---
description: Describes the list of changes present in Version 2 of MauiReactor
---

# What's New in Version 2

{% hint style="warning" %}
MauiReactor 2 is currently in beta if you want to access new features you need to reference packages with version 2.0.X-beta.\
This site contains code and information updated to Version 2.\
MauiReactor 2 will be released next week, stay tuned!
{% endhint %}

MauiReactor 2 is the new version of MauiReactor targeting .NET 8. It contains some good improvements, many of which are under the hood. \
\
If you're coming from version 1 there is good news: your code is mostly working as it is. There are just a couple of breaking changes you need to be aware of.

Source code: [https://github.com/adospace/reactorui-maui](https://github.com/adospace/reactorui-maui)\
License: MIT (i.e. you can use it for your closed-source commercial projects)\
\
Let's start with what is new.

## A new simplified way to write components

In Version 1, components are generally written with code like this:

```csharp
class CounterPageState
{
    public int Counter { get; set; }
}

class CounterPage : Component<CounterPageState>
{
    public override VisualNode Render()
    {
        return new ContentPage("Counter Sample")
        {
            new VStack(spacing: 10)
            {
                new Label($"Counter: {State.Counter}")
                    .VCenter()
                    .HCenter(),

                new Button("Click To Increment", () =>
                    SetState(s => s.Counter++))
            }
            .VCenter()
            .HCenter()
        };
    }
}
```

In Version 2, you _can_ use the new format that is less verbose and more easy to read

```csharp
class CounterPageState
{
    public int Counter { get; set; }
}

class CounterPage : Component<CounterPageState>
{
    public override VisualNode Render()
        => ContentPage("Counter Sample",
            VStack(spacing: 10,
                Label($"Counter: {State.Counter}")
                    .HCenter(),

                Button("Click To Increment", () =>
                    SetState(s => s.Counter++))
            )
            .Center()
        );    
}
```

{% hint style="info" %}
The new code style is **optional**. You can use the other way to write your component or leave the code you may have already written as it will work in Version 2+.
{% endhint %}

## \[Prop], \[Param], and \[Inject] attributes

Version 2 provides a few convenient source generators that should remove some boilerplate code in your components.

The PropAttribute lets specify a component prop without writing the method.

Before:

```csharp
class BusyComponent : Component
{
    string _message;
    bool _isBusy;

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
    ...
}
```

After (version 2+):

```csharp
partial class BusyComponent : Component
{
    [Prop]
    string _message;    
    [Prop]
    bool _isBusy;
    
    ...
}
```

Please note that the component class must be partial to use the new feature.

The `ParamAttribute` allows you to specify a parameter that is inherited from the parent component without the boilerplate code `Create/GetParameter`.

Before

```csharp
class CustomParameter
{
    public int Numeric { get; set; }
}

class ParametersPage: Component
{
    IParameter<CustomParameter> _customParameter;

    public ParametersPage()
    {
        _customParameter = CreateParameter<CustomParameter>();
    }

    ...
}

class ParameterChildComponent: Component
{
    IParameter<CustomParameter> _customParameter;

    public ParameterChildComponent()
    {
        _customParameter = GetParameter<CustomParameter>();
    }
    
    ...
}
```

After (version 2+):

```csharp
class CustomParameter
{
    public int Numeric { get; set; }
}

partial class ParametersPage: Component
{
    [Param]
    IParameter<CustomParameter> _customParameter;

    ...
}

class ParameterChildComponent: Component
{
    [Param]
    IParameter<CustomParameter> _customParameter;
    
    ...
}
```

Finally, you may also find useful the InjectAttribute that lets you remove some repetitive code to inject services from the DI container

Before:

<pre class="language-csharp"><code class="lang-csharp"><strong>class MyComponent : Component
</strong>{
     IMyService _service;
     
     public MyComponent()
     {
         _service = Services.GetRequiredService&#x3C;IMyService>();
     }

}
</code></pre>

After (version 2+):

```csharp
partial class MyComponent : Component
{
     [Inject]
     IMyService _service;

}
```

{% hint style="info" %}
All the new attributes are **optional** and require the component class to be partial.
{% endhint %}

## Breaking Changed: Simplified creation of inline components

Version 2 provides a more straightforward method for creating Inline Components that is easier to read requiring less boiler code.

{% hint style="warning" %}
Version 2+ doesn't support the old way of creating Inline Component, modifying old code to the new format should be a matter of changing a few lines of code.
{% endhint %}

Before:

```csharp
static VisualNode RenderMudEntry(string label, Action<string> textChangedAction)
    => Component.Render(context =>
    {
        MauiControls.Entry _entryRef = null;
        var state = context.UseState<(bool IsFocused, bool IsFilled)>();

        return new Grid("Auto", "*")
        {
            new Entry(entryRef => _entryRef = entryRef)
                .OnAfterTextChanged(text =>
                {
                    state.Set(s => (s.IsFocused, IsFilled: !string.IsNullOrWhiteSpace(text)));
                    textChangedAction?.Invoke(text);
                })
                .VCenter()
                .OnFocused(()=>state.Set(s => (IsFocused: true, s.IsFilled)))
                .OnUnfocused(()=>state.Set(s => (IsFocused: false, s.IsFilled))),

            new Label(label)
                .OnTapped(() =>_entryRef?.Focus())
                .Margin(5,0)
                .HStart()
                .VCenter()
                .TranslationY(state.Value.IsFocused || state.Value.IsFilled ? -20 : 0)
                .ScaleX(state.Value.IsFocused || state.Value.IsFilled ? 0.8 : 1.0)
                .AnchorX(0)
                .TextColor(!state.Value.IsFocused || !state.Value.IsFilled ? Colors.Gray : Colors.Red)
                .WithAnimation(duration: 200),
        }
        .VCenter();
    });
```

After (version 2+):&#x20;

```csharp
static VisualNode RenderMudEntry(string label, Action<string> textChangedAction)
    => Render<(bool IsFocused, bool IsFilled)>(state =>
    {
        MauiControls.Entry _entryRef = null;
        
        return Grid("Auto", "*",
            Entry(entryRef => _entryRef = entryRef)
                .OnAfterTextChanged(text =>
                {
                    state.Set(s => (s.IsFocused, IsFilled: !string.IsNullOrWhiteSpace(text)));
                    textChangedAction?.Invoke(text);
                })
                .VCenter()
                .OnFocused(()=>state.Set(s => (IsFocused: true, s.IsFilled)))
                .OnUnfocused(()=>state.Set(s => (IsFocused: false, s.IsFilled))),

            Label(label)
                .OnTapped(() =>_entryRef?.Focus())
                .Margin(5,0)
                .HStart()
                .VCenter()
                .TranslationY(state.Value.IsFocused || state.Value.IsFilled ? -20 : 0)
                .ScaleX(state.Value.IsFocused || state.Value.IsFilled ? 0.8 : 1.0)
                .AnchorX(0)
                .TextColor(!state.Value.IsFocused || !state.Value.IsFilled ? Colors.Gray : Colors.Red)
                .WithAnimation(duration: 200),
        )
        .VCenter();
    });
```

## Breaking changed: Reactor.Maui.ScaffoldGenerator package dismissed

When you authored components wrapping native controls coming from external libraries you were required to install an additional packaged Reactor.Maui.ScaffoldGenerator.

This is no longer required as the source generators library is now directly linked by the main package Reactor.Maui.
