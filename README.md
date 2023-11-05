---
description: Describes what MauiReactor is and why you should be interested
---

# What is MauiReactor?

MauiReactor is a .NET library that implements a **component-based UI framework** on top of the .NET MAUI.

If you already have some experience in React Native, Flutter, or Swift you may find some similarities. Actually, MauiReactor borrows some implementation designs from them while maintaining the normal MAUI application development you're used to.

NO XAML needed, just using C# you can write fluent declarative UI with MauiReactor components:

```csharp
class MainPage : Component
{
    public override VisualNode Render()
    {
        return new ContentPage()
        {
            new VStack // Vertical Stack
            {
                new Label("User:"),
                new Entry(),
                new Label("Password:"),
                new Entry(),
                new HStack() // Horizontal Stack
                {
                    new Button("Login"),
                    new Button("Register")
                }
            }
            .HCenter()
            .VCenter()
        }
        .Title("Login");
    }
}
```

ReactorMaui ports .NET MAUI controls to C#:

1.  Dependency properties that deal with simple types (i.e. excluding templates, or views) are translated to Prop fluent methods:

    ```csharp
    new Button()
         .Text("Click!")
    ```
2.  Events are translated to methods that accept a callback:

    ```csharp
     new Button()
          .OnClicked(()=> ...)
    ```

You can read for examples and more complex controls at [Controls](https://adospace.gitbook.io/mauireactor/components/controls)

Also, you can opt for use ReactorMaui C# components within XAML files as described at [XAML Intregration](https://adospace.gitbook.io/mauireactor/components/xaml-integration)

{% hint style="info" %}
**Good to know:** There are several design patterns you can choose when you build a UI. One of the most adopted is the MVVM (Model-View-View-Model) approach, used for years to build desktop and mobile applications. React made famous the MVU (Model-View-Update) pattern that aims to solve some issues of the first one. MAUI app defaults to MVVM while MauiReactor lets you build a MAUI app using the MVU pattern.
{% endhint %}

GitHub repo: [https://github.com/adospace/reactorui-maui](https://github.com/adospace/reactorui-maui)
