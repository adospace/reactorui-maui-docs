---
description: >-
  This article describes how you can manage properties of the window containing
  the app
---

# Window

When you start an app in a desktop environment like Windows or Mac, .NET MAUI creates under the hood the main window that contains the root page.

MauiReactor has some nice features that allow you to change its properties like the title or position and interact with it for example responding to the SizeChanged event.

## Change the title of the window

Often all you need for your app is to change its Title. The easiest method to modify basic properties on the parent window is to use some helper function on the containing page.

For example, this is the code if you want to change the window title:

```csharp
new ContentPage
{
...
}
.WindowTitle("My Window Title")
```

## Working with the Window

If you need more control over the parent window you can create a Window visual node and host the page inside it:

```csharp
public override VisualNode Render()
{
    return new Window
    {
        new ContentPage
        {
            ...
        }
    }
    .Title("My Window Title")
    .OnSizeChanged(OnSizeChanged)
    ;
}

private void OnSizeChanged(object sender, EventArgs args)
{
    var window = ((MauiControls.Window)sender);
    System.Diagnostics.Debug.WriteLine($"Window size changed to {window.Width}x{window.Height}");
    System.Diagnostics.Debug.WriteLine($"Window position changed to {window.X},{window.Y}");
}

```

This way you can access all the properties of the window and receive all the callback you need attaching events like the SizeChanged.
