---
description: This page describes how to create buttons in MauiReactor
---

# Button

> The .NET Multi-platform App UI (.NET MAUI) [Button](https://learn.microsoft.com/en-us/dotnet/api/microsoft.maui.controls.button) displays text and responds to a tap or click that directs the app to carry out a task.

You can create a Button in MauiReactor just like any other widget:

```csharp
Button("Button")
    .OnClicked(...)
```

## Visual states

To modify the look of the button when it assumes a specific state, append one or more calls to VisualState() method specifying which state to link and which property and value to set.

For example, the following code changes the background color of a button when it's pressed:

<pre class="language-csharp"><code class="lang-csharp">Button("Button")
<strong>    .VisualState(nameof(CommonStates), CommonStates.Normal)
</strong>    .VisualState(nameof(CommonStates), "Pressed", MauiControls.VisualElement.BackgroundColorProperty, Colors.Aqua)

</code></pre>

Use [theming](../theming.md) to configure the visual states of all the buttons of your application, as shown below:

<pre class="language-csharp"><code class="lang-csharp"><strong>ButtonStyles.Default = _ => _
</strong>    .VisualState(nameof(CommonStates), CommonStates.Normal)
    .VisualState(nameof(CommonStates), "Pressed", MauiControls.VisualElement.BackgroundColorProperty, Colors.Aqua);
</code></pre>
