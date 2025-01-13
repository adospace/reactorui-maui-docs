---
description: Describes what MauiReactor is and why you should be interested
---

# What is MauiReactor?

MauiReactor is a .NET library that implements a **component-based UI framework** on top of .NET MAUI.

{% hint style="info" %}
MauiReactor version 2 features a new way to write components.  Some code on this documentation site uses the classic format.\
The writing component using the old way is perfectly valid and working, and will be maintained/supported also in the coming versions.\
Choosing between the two formats is up to you and your preferences.
{% endhint %}

If you already have some experience in React Native, Flutter, or Swift you may find some similarities. MauiReactor borrows some implementation designs from them while maintaining the normal MAUI application development you're used to.

NO XAML needed, just using C# you can write fluent declarative UI with MauiReactor components.

Let's build our first Component based app from scratch, so you get the flavor of working with MauiReactor.  
This app will be focused on teaching, so is quite incomplete.  
For example, no exception handling, no reading of the user name or password entered.  Several useless buttons.

1. Install MauiReactor templates to Visual Studio 2022 *(if not already installed)*
   `dotnet new install Reactor.Maui.TemplatePack`

2. Install MauiReactor Hot Reload to Visual Studio 2022 *(if not already installed)*
   `dotnet tool install -g Reactor.Maui.HotReload`

3. Create vanilla MauiReactor solution

   1. Launch Visual Studio 2022 *(or newer)*

   2. Click: Create a new project

   3. In search box *(top center)*, type in: `mauireactor` *(Ensure lower case)*

   4. Select: `MauiReactor based app (Adolfo Marinucci)`, then click: `Next`

      1. Next time in, pin it down in `Recent project templates`, so it is easier to find.

   5. Configure your new project

      1. Project name: `FirstLook`
      2. Location:         `C:\dev\mauireactor` *(or other path that you choose)*  
      3. Click: Create
      4. Close: FirstLook: Overview page
      5. We will use `Windows Machine` as the target device.  *(Feel free to create an emulator or attach a physical Android or iPhone smartphone)*
         This results in the fastest turnaround as Maui and MauiReactor takes a while to compile and deploy.
      6. Press F5 to run it.  You should see a tablet size page with .NET 8 robot image, Hello World! and Welcome to MauiReactor: MAUI with superpowers, as well as a Counter Button.  *(Click the button as many times as you like)*
      7. Stop the app by closing the tablet sized window or Shift-F5 on Visual Studio app.
      8. Congratulations, you have created your first MauiReactor app.  *(Now lets make it our own)*

   6. Add `Components` folder at root level, sibling to `Pages` folder.  *(Our components go here)*

   7. Add `Components/GlobalsUsings.cs`, with content:

      ```
      global using MauiReactor;
      ```

      This will apply this using statement for all classes in `Components/` folder, resulting in less typing and smaller files

   8. Add `Components/StatelessLogin.cs` with content:

      ```
      namespace FirstLook.Components;
      
      class StatelessLogin : Component
      {
          public override VisualNode Render() =>
              VStack(         // Same as VerticalStackLayout
                  Label(text: "User:"),
                  Entry()
                      .Placeholder("User name"),
                  Label(text: "Password:"),
                  Entry(),
                  HStack(     // Same as HorizontalStackLayout
                      Button(text: "Login"),
                      Button(text: "Register"),
                      Button()
                          .Text("Click!")
                  )
              )
              .Center();
      }
      ```

      1. `StatelessLogin` inherits from non-generic `Component`, which makes it stateless.
      2. A `VisualNode` is a `MauiReactor` single element, or a single tree root element.
      3. For compactness
         -  `=>` is on same line as method signature.
         - VStack is indented two tabs.
      4. **Important.  Do not skip.**
         - Note the use of open parentheses and closed parentheses, to create a hierarchical tree.  *(They are easy to get wrong)*
         - Commas are quite sensitive.  Squiggles will display when a comma is missing or is extra.  *(You will figure it out with practice)*
      5. The benefit of this format is that it is tighter than XAML and quite readable.  *(It is similar in spirit to \*.jsx)*
         - Feel free to break up large Components into smaller Components so that you don't end up with a 3,000 line monolithic component. 
           *(T-SQL, I'm looking at you)*
      6. Components
         - VStack is a top-down display of VisualNode items.  *(It doesn't act like a LIFO stack)*
         - Label displays some text.  
         - Entry is a single line Text box.  (In this sample, there is no way to read what the user typed in)
           - Placeholder provides a hint when text is empty
         - HStack is a left-to-right display of VisualNode items.  *(It doesn't act like a LIFO stack)*
         - Button is your standard clickable button.
         - VStack.Center centers its contents as a block both Vertically and Horizontally
   
   9. Add `Components/StatefulButton.cs` with content:
   
   10. ```
       namespace FirstLook.Components;
       
       class StatefulButtonState
       {
           public string ButtonTitle { get; set; }
           public int Counter { get; set; } = 9;
       }
       
       class StatefulButton : Component<StatefulButtonState>
       {
           public override VisualNode Render() =>
               Button()
                   .Text(State.ButtonTitle)
                   .OnClicked(() => SetState(s =>
                       {
                           s.Counter = s.Counter + ((s.Counter % 5 == 0) ? 2 : 1);
                           s.ButtonTitle = $"Now I have a button title {State.Counter}!";
                       }
                   ));
       }
       ```
   
       1. `class StatefulButtonState` contains this Component's state.
   
          1. By *convention*, it has the name of the corresponding state `StatefulButton`, with `State` appended.
          2. Each stateful element is a public property with a public getter and public setter.
             1. It may also have a default value as well as constructor.
          3. Our ButtonTitle in this component starts with `null` value.  When the user clicks the button, the Button's Text is reactively set to ButtonTitle and automatically displays.
          4. Counter normally defaults to 0, but in this case we explicitly set the starter value of 9, where the first displayed value is 10.
          5. If `class StatefulButtonState` gets too long, move it to its own `StateButtonState.cs` file.
   
       2. `class StatefulButton` renders the Component's content, and handles events such as Click.
   
          1. `.Text(State.ButtonTitle)` 
             - `State` has reactive get/set access to `StatefulButtonState`, as it was passed in as a generic parameter in `StatefulButton` method signature.
             - Here, we are using a code-generated getter to read and write reactive `Counter` and`ButtonTitle` values.
   
       3. ```
                      .OnClicked(() => SetState(s =>
                          {
                              s.Counter = s.Counter + ((s.Counter % 5 == 0) ? 2 : 1);
                              s.ButtonTitle = $"Now I have a button title {State.Counter}!";
                          }
                      )
          ```
   
          - This is an extract of above `class StatefulButton`, so you don't need to scroll back and forth.
          - This is the boilerplate for changing the state reactivity.
          - `.OnClicked` is the method to handle clicks or taps.
          - It has one parameter, a function calling `SetState` function that makes your reactive changes.
          - Note that there is no ceremony in reading the "old" State values.  It acts just like standard C#
            - The reactivity is handled by MauiReactor internally.
            - If the Counter is at 5, 10, 15, ..., we increment by 2 else we increment by 1, just to show how straight forward it is.
   
   11. Finally, let's wrap up coding in `Pages/MainPage.cs`
   
       1. Change content to:
   
          ```
          using FirstLook.Components;
          
          namespace FirstLook.Pages;
          
          class MainPage : Component
          {
              public override VisualNode Render() => 
                  ContentPage(title: "Login",
                      VStack(
                          new StatelessLogin(),
                          new StatefulButton()
                      )
                      .Center()
                  );
          }
          ```
   
       2. Notice that some Components require `new` and others don't.  
   
          1. The rule of thumb is that most, but not all, built in components don't need new, but our custom Components always need new.
             And some Components such as `VStack` don't like `new` in front.  This may change in future versions.
          2. Some Components are relaxed and work either way, and some are insistent on one way or the other.
             1. The compiler will let you know what works in this regard.
   
       3. Note the clean layout using Components wired together like Lego Blocks.
   
   12. One last step *(no coding)*.  Let's see how `MainPage` gets called from the top.
       Here is an extract from `MauiProgram.cs`
   
       ```
           public static MauiApp CreateMauiApp()
           {
               var builder = MauiApp.CreateBuilder();
               builder
                   .UseMauiReactorApp<MainPage>(app =>
                   {
                       app.AddResource("Resources/Styles/Colors.xaml");
                       app.AddResource("Resources/Styles/Styles.xaml");
                   })
       #if DEBUG
                   .EnableMauiReactorHotReload()
       #endif
       ```
   
       - `.UseMauiReactorApp<MainPage>(app =>`
         This makes `MainPage` the Home Page.



ReactorMaui ports .NET MAUI controls to C#:

1.  Dependency properties that deal with simple types (i.e. excluding templates or views) are translated to Prop fluent methods:

    ```csharp
    Button()
         .Text("Click!")
    ```
2.  Events are translated to methods that accept a callback:

    ```csharp
     Button()
          .OnClicked(()=> ...)
    ```

You can read for examples and more complex controls at [Controls](https://adospace.gitbook.io/mauireactor/components/controls)

Also, you can opt to use ReactorMaui C# components within XAML files as described at [XAML Integration](https://adospace.gitbook.io/mauireactor/components/xaml-integration)

{% hint style="info" %}
**Good to know:** There are several design patterns you can choose when you build a UI. One of the most adopted is the MVVM (Model-View-ViewModel) approach, used for years to build desktop and mobile applications. React made famous the MVU (Model-View-Update) pattern that aims to solve some issues of the first one. MAUI app defaults to MVVM while MauiReactor lets you build a MAUI app using the MVU pattern.
{% endhint %}

GitHub repo: [https://github.com/adospace/reactorui-maui](https://github.com/adospace/reactorui-maui)

FirstLook.zip: https://mauireactor-bb.b-cdn.net/FirstLook.zip *(Ready to run Visual Studio 2022 solution)*
