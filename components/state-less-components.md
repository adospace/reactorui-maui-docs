---
description: Describes what are State-less components and how to create them
---

# State-less Components

Each MauiReactor page is composed of one or more `Component`s which is described by a series of `VisualNode` and/or other `Component`s organized in a tree.

The root component is the first created by the application in the `Program.cs` file with the call to the `UseMauiReactorApp<TComponent>()`.

```csharp
    public static MauiApp CreateMauiApp()
    {
        var builder = MauiApp.CreateBuilder();
        builder
            .UseMauiReactorApp<MainPage>(app =>
            {
                app.AddResource("Resources/Styles/Colors.xaml");
                app.AddResource("Resources/Styles/Styles.xaml");

                app.SetWindowsSpecificAssectDirectory("Assets");
            })
#if DEBUG
            .EnableMauiReactorHotReload()
#endif

            .ConfigureFonts(fonts =>
            {
                fonts.AddFont("OpenSans-Regular.ttf", "OpenSansRegular");
                fonts.AddFont("OpenSans-SemiBold.ttf", "OpenSansSemiBold");
            });

        return builder.Build();
    }
```

The following code creates a component that renders a `ContentPage` with title "Home Page":

{% code lineNumbers="true" %}
```csharp
class MainPage : Component
{
    public override VisualNode Render()
    {
        return new ContentPage()
            .Title("Home Page")
            ;
    }
}
```
{% endcode %}

Line 3\. Every component must override the Render method and return the visual tree of the component

Line 5\. The ContentPage visual node pairs with the ContentPage native control.

Line 6\. The Title property sets the title of the page and updates the Title dependency property on the native page.

You can also pass the title to the Constructor:

{% code lineNumbers="true" %}
```csharp
class MainPage : Component
{
    public override VisualNode Render()
    {
        return new ContentPage("Home Page");
    }
}
```
{% endcode %}

Line 5\. The title of the page is set by passing it to the `ContentPage` constructor.

Running the app you should see an empty page titled "Home Page"

You can build any complex UI in the render method of the component but often it's better to compose more than one component to create a page or app.

For example, consider this component:

```csharp
class MainPage : Component
{
    public override VisualNode Render()
    {
        return new ContentPage()
        {
            new VStack
            {
                new Label("User:"),
                new Entry(),
                new Label("Password:"),
                new Entry(),
                new HStack()
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

We could create a component like this:

```csharp
class EntryWithLabel : Component
{
    string _label;
    public EntryWithLabel Label(string label)
    {
        _label = label;
        return this;
    }
    
    public override VisualNode Render()
    {
        return new VStack
        {
            new Label(_label),
            new Entry()
        };
    }   
}
```

and reuse it on the main page as shown in the following code:

```csharp
class MainPage : Component
{
    public override VisualNode Render()
    {
        return new ContentPage()
        {
            new VStack
            {
                new EntryWithLabel()
                    .Label("User:"),
                new EntryWithLabel()
                    .Label("Password:"),
                new HStack()
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

Reusing components is a key feature in MauiReactor: decomposing a large page into small components that are easier to test is also beneficial to the overall performance of the application.
