---
description: Describes what are State-less components and how to create them
---

# State-less Components

Each MauiReactor page is composed of one or more `Component`s which is described by a series of `VisualNode` and/or other `Component`s organized in a tree.

The root component is first created by the application in the `MauiProgram.cs` file with the call to the `UseMauiReactorApp<MainPage>()`.

A stateless component is where we don't need reactive features.  Maui and MauiReactor each allow you to go "manual", but that is not why you are using Maui with MVVM or MauiReactor with MVU.

Stateless Components are used when you just want to display something such as a brochure allowing the user to follow a link or click a button.  Stateless Components are useful for small components without state.

If you want to capture checkbox state, radio button state, entry textbox state, Stateful components are the better choice *(see: next lesson Stateful Components)*.

**Create an empty titled page**

1. Create StatelessComponents MauiReactor project.  *(not AppShell)*
   If you need instructions, [](https://adospace.gitbook.io/mauireactor)

2. Replace MainPage.cs with

   {% code lineNumbers="true" %}

   ```
   namespace StatelessComponents.Pages;
   
   class MainPage : Component
   {
       public override VisualNode Render() => 
           ContentPage()
               .Title("Home Page");
   }
   ```

​	{% endcode %}

​	Line 5. Every component must override the Render method and return the visual tree of the component

​	Line 6. The ContentPage visual node pairs with the ContentPage native control.

​	Line 7. The Title property sets the title of the page and updates the Title dependency property on the native page.

​	Run it with F5, you will see an empty page.


**Edit an empty titled page by passing the title to the Constructor**

{% code lineNumbers="true" %}

```
namespace StatelessComponents.Pages;

class MainPage : Component
{
    public override VisualNode Render() => 
        ContentPage("Home Page");
}
```

{% endcode %}

Run the app with F5, you should see an empty page titled "Home Page"



**Edit adding some non DRY (Don't Repeat Yourself) Components**

You can build any complex UI in the render method of the component but often it's better to compose more than one component to create a page or app.

For example, consider this component:

{% code lineNumbers="true" %}

```
using StatelessComponents.Components;
namespace StatelessComponents.Pages;

class MainPage : Component
{
    public override VisualNode Render() =>
        ContentPage("Login",
            VStack(
                Label("User:"),
                Entry(),
                Label("Password:"),
                Entry(),
                HStack(
                    Button("Login"),
                    Button("Register")
                )
            )
            .Center()
        );
}
```

{% endcode %}

Run it with F5.  You should see a VStack of Label, Entry, Label, Entry, then a HStack of two buttons
But the observant developer notices that we are repeating VStack(Label, Entry) twice

**DRY it out by adding our first custom Component**

 1. Create `Components` folder

 2. Add `Components/GlobalUsings.cs` with content:

    ```
    global using MauiReactor;
    ```

    By adding `global using`, we can save typing for all Component classes in Components folder.
    We don't want to add it to root `GlobalUsings.cs` as we want it focused on the `Components` folder, and not be used in every class in the project.

3. Add Components/EntryWithLabel.cs

{% code lineNumbers="true" %}

```
namespace StatelessComponents.Components;

partial class EntryWithLabel : Component
{
    [Prop]
    string _labelText;

    public override VisualNode Render() =>
        VStack(
            Label(_labelText),
            Entry()
        );

}
```

{% endcode %}

- A MauiReactor code generator will refactor this code for you behind the scenes.
  - There are **several rules** to follow when coding`[Prop] string _labelText;`
    - `[Prop]` attribute is required to make the **field** a parameter.  
      *This may be one of the rare times you must use a **field** rather than a property in your C# experience.*
    - `[Prop]` will not compile for properties with `{ get; set; }`
    - `[Prop] string _labelText = "will be ignored";` will compile, run and be ignored by the code generator, so don't do it.  
      That would add **dead code** which needs to be reviewed and read by current and future developers.
    - **Never** name your `_variable` as a Component name, such as `[Prop] string _label;`
      Doing so will break the project with an infinite loop, never seeing your page render.
    - The component `class` must be `partial` and inherit from `Component`.
    - The `[Prop]` name must start with underscore and be `_camelCase`.
    - Within the Component, use `_labelText` throughout as the example does.
    - Each property that will be passed in has the `[Prop]` attribute.
    - Each `[Prop]` should skip the access part such as `public`/`protected`/`internal`/`private`, which will be ignored by the MauiReactor code generator.

4. Modify `Pages/MainPage.cs`

{% code lineNumbers="true" %}

```
using StatelessComponents.Components;

namespace StatelessComponents.Pages;

class MainPage : Component
{
    public override VisualNode Render() =>
        ContentPage("Login",
            VStack(
                new EntryWithLabel()
                    .LabelText("User:"),
                new EntryWithLabel()
                    .LabelText("Password:"),
                HStack(
                    Button("Login"),
                    Button("Register")
                )
            )
            .Center()
        );
}
```

{% endcode %}

- Note the shortening of the source code, as we are making this **DRY**.
- As we are consuming a custom component that you created, use:  `new EntryWithLabel()`
  The `new` keyword is required with custom components.
- Then `.LabelText("User:"),`
  - Recall that in `EntryWithLabel.cs`, the `[Prop]` was `string _labelText`.
    - The code generator converts `_labelText` into `LabelText` method.
      Now you understand why `_label` converts to `Label` which causes an infinite loop.
- Thus `"User:"` is passed as parameter `[Prop] _labelText` to `EntryWithLabel()` where it is passed to built-in `Label` as the `text` parameter.



Reusing components is a key feature in MauiReactor: decomposing a large page into small components that are easier to test is also beneficial to the overall performance of the application.

StatelessComponents.zip: https://mauireactor-bb.b-cdn.net/StatelessComponents.zip *(Ready to run Visual Studio 2022 solution)*

