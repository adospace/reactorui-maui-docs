---
description: >-
  Component Parameters is a way to share data between parent and child
  components
---

# Component Parameters

MauiReactor has a nice feature that developers can integrate into their app to share data between a component and its tree of children.&#x20;

There are 2 primary API functions for this:

1. Call `CreateParameter<T>()` to create a parameter on the parent component
2. Call `GetParameter<T>()` to access a parameter value from a child component

{% hint style="info" %}
You can access a parameter created from any ancestor, not just the direct parent component
{% endhint %}

For example, in the sample code, we're going to define a parameter in a component:

```csharp
class CustomParameter
{
    public int Numeric { get; set; }
}

class ParametersPage: Component
{
    private readonly IParameter<CustomParameter> _customParameter;

    public ParametersPage()
    {
        _customParameter = CreateParameter<CustomParameter>();
    }

    public override VisualNode Render()
    {
        return new ContentPage("Parameters Sample")
        {
            new VStack(spacing: 10)
            {
                new Button("Increment from parent", () => _customParameter.Set(_=>_.Numeric += 1   )),
                new Label(_customParameter.Value.Numeric),

                new ParameterChildComponent()
            }
            .VCenter()
            .HCenter()
        };
    }
}
```

To access the component from a child, just call the GetParameter\<T>():

```csharp
partial class ParameterChildComponent: Component
{
    public override VisualNode Render()
    {
        var customParameter = GetParameter<CustomParameter>();

        return new VStack(spacing: 10)
        {
            new Button("Increment from child", ()=> customParameter.Set(_=>_.Numeric++)),

            new Label(customParameter.Value.Numeric),
        };
    }
}
```

{% hint style="info" %}
When you modify a parameter value, MauiReactor update any component starting from the parent one that has defined it down to its children. You can control this behaviour using the overload  `void Set(Action setAction, bool invalidateComponent = true)` of the `IParameter<T>` interface
{% endhint %}
