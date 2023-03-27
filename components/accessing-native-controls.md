---
description: Describes how access the underling native control
---

# Accessing native controls

Sometimes you need a reference to the native control (i.e. MAUI control); for example, say you need to move focus to Entry control when the component starts up.

In MauiReactor, you can get a reference to the underlying widget passing an Action argument when constructing the visual object accepting the reference to the native control: you can easily save it in a private field and reuse it wherever.&#x20;

For example, in this code when a button is clicked, the textbox (i.e. Entry) is focused:

```csharp
public class MainPage : Component
{
    private MauiControls.Entry _entryRef;

    public override VisualNode Render()
    {
        return new ContentPage()
        {
            new VStack(spacing: 10)
            {
                new Entry(entryRef => _entryRef = entryRef),
            
                new Button("Click here!")
                    .OnClick(OnButtonClicked)
                    .VCenter()
                    .HCenter()
            }
        };
    }

    private void OnButtonClicked()
        => _entryRef?.Focus();
}
```

{% hint style="info" %}
The page containing the current component is also accessible with the convenient `ContainerPage` property
{% endhint %}

Sometimes you need to set a specific dependency property of the native control:

```csharp
new Button()
    .Set(BindableProperty property, object? value);
```

If you want to set an attached dependency property:

```csharp
new Button()
    .SetAttachedProperty(BindableProperty property, object? value);
```
