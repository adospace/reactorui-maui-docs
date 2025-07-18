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

This way you can access all the properties of the window and receive all the callbacks you need attaching events like the SizeChanged.

## Application lifetime events

You can handle application lifetime events attaching to the specific handler as shown below:

```csharp
class MainPage : Component
{
    public override VisualNode Render()
    {
        return new Window
        {
            new ContentPage
            {
            }
        }
        .OnCreated(() => Debug.WriteLine("Created"))
        .OnActivated(() => Debug.WriteLine("Activated"))
        .OnDeactivated(() => Debug.WriteLine("Deactivated"))
        .OnStopped(() => Debug.WriteLine("Stopped"))
        .OnResumed(() => Debug.WriteLine("Resumed"))
        .OnDestroying(() => Debug.WriteLine("Destroying"))
        ;
    }
}
```

## Window Titlebar

> The .NET Multi-platform App UI (.NET MAUI) [TitleBar](https://learn.microsoft.com/en-us/dotnet/api/microsoft.maui.controls.titlebar) is a view that enables you to add a custom title bar to a [Window](https://learn.microsoft.com/en-us/dotnet/api/microsoft.maui.controls.window) to match the personality of your app.

This is how you can create and customize the TitleBar in MauiReactor:

```csharp
Window(
    Shell(shellRef => _shellRef = shellRef,
        FlyoutItem("Number Generator", new ThemeSwitcherPage().ThemeChanged(Invalidate)),
        FlyoutItem("Decision Maker", new ContentPage().Title("Decision Maker")),
        FlyoutItem("Email Generator", new ContentPage().Title("Email Generator"))
    )
    .FlyoutFooter(
        Label()
            .Text($"Version: {Microsoft.Maui.ApplicationModel.AppInfo.Current.VersionString}")
            .HorizontalTextAlignment(TextAlignment.Center)
    )
    .ItemTemplate(RenderItemTemplate),

    TitleBar()
        .Title("Random Generator")
        .TrailingContent(
            Button("Settings", async () => await _shellRef!.DisplayAlert("Test App", "Open Settings page!", "OK"))
                .BackgroundColor(Colors.Transparent)
                .BorderWidth(0)
                .VerticalOptions(LayoutOptions.Center)
        )
)
```
