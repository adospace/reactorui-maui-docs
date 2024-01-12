---
description: >-
  Component Parameters is a way to share data between parent and child
  components
---

# Component Parameters

MauiReactor has a nice feature that developers can integrate into their app to share data between a component and its tree of children.&#x20;

A parameter is a way to automatically transfer an object from the parent component to its children down the hierarchy up to the lower ones.

Each component accessing a parameter can read and write its value freely.

When a parameter is modified all the components referencing it are automatically invalidated so that they can re-render according to the new value.

{% hint style="info" %}
You can access a parameter created from any ancestor, not just the direct parent component
{% endhint %}

For example, in the following code, we're going to define a parameter in a component:

```csharp
class CustomParameter
{
    public int Numeric { get; set; }
}

partial class ParametersPage: Component
{
    [Param]
    IParameter<CustomParameter> _customParameter;

    public override VisualNode Render()
     => ContentPage("Parameters Sample",
        => VStack(spacing: 10,
                Button("Increment from parent", () => _customParameter.Set(_=>_.Numeric += 1   )),
                Label(_customParameter.Value.Numeric),

                new ParameterChildComponent()
            )
            .Center()
        );
}
```

To access the component from a child, just reference it:

```csharp
partial class ParameterChildComponent: Component
{
    [Param]
    IParameter<CustomParameter> _customParameter;
    
    public override VisualNode Render()
      => VStack(spacing: 10,
            Button("Increment from child", ()=> _customParameter.Set(_=>_.Numeric++)),

            Label(customParameter.Value.Numeric)
        );
}
```

{% hint style="info" %}
When you modify a parameter value, MauiReactor updates any component starting from the parent one that has defined it down to its children. \
You can control this behavior using the overload`void Set(Action setAction, bool invalidateComponent = true)` of the `IParameter<T>` interface\
Passing false to the `invalidateComponent` MauiReactor doesn't invalidate the components referencing the `Parameter` but it just updates the properties that are referencing it inside the callback ()=>...
{% endhint %}
