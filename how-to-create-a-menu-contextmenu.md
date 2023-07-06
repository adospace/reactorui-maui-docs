---
description: How to create a menu or a context menu in Maui desktop app.
---

# How to create a Menu/ContextMenu?

.NET Maui allows developers to create Menus and ContextMenu for desktop apps.&#x20;

In MauiReactor you can easily add a Menu bar to a desktop application by adding the `MenuBarItem` control under a page like it's shown in the following example:

```csharp
new ContentPage
{
    new MenuBarItem("File")
    {
        new MenuFlyoutSubItem("Project")
        {
            new MenuFlyoutItem("Open...")
                .OnClicked(OnOpenProject),
        },
        new MenuFlyoutSeparator(),
        new MenuFlyoutItem("Exit")
            .OnClicked(OnExitApplication),
    },

    RenderBody()
}
```

For the context menu, you have to add the MenuFlyout under the control that should show the menu like it's shown below:

```csharp
new Label(person.Initial)
{
    new MenuFlyout
    {
        new MenuFlyoutItem("MenuItem1")
            .OnClicked(()=>OnClickMenuItem("MenuItem1")),
        new MenuFlyoutItem("MenuItem2")
            .OnClicked(()=>OnClickMenuItem("MenuItem2")),
        new MenuFlyoutItem("MenuItem3")
            .OnClicked(()=>OnClickMenuItem("MenuItem3")),
    }
}
```

{% hint style="warning" %}
Please note that not all controls support the context menu (like the ViewCell): for a list of unsupported controls please refer to official .NET MAUI documentation.
{% endhint %}
