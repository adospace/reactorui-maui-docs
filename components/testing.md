---
description: Describes how test MauiReactor components
---

# Testing

Testing an application usually involves 3 different kinds of tests.

1. **Unit Tests**: Tests of single functions or classes aiming to prove the correctness of single behavior or feature or absence of a specific issue. You can create unit tests for your maui app like you would do for any other c# program.
2. **Component or Widget Tests**: These kinds of tests are specific to UI apps that use an MVU framework and serve to prove the quality of single components. \
   MauiReactor provides some neat classes and functions that let you test MauiReactor components: this article describes how to use them.
3. **Integration Tests**: This kind of test, generally, involves the loading of external tools that simulate the real environment, the execution of the app with different settings, and the simulation of a user interacting with the app. \
   You could create integration tests for MauiReactor using tools like [https://appium.io/](https://appium.io/)

## Preliminary steps

First of all, you need to modify your .NET MAUI application project so that can be linked to a test project.&#x20;

Open your project definition; it should be something like the following:

```xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <TargetFrameworks>net7.0-android;net7.0-ios;net7.0-maccatalyst</TargetFrameworks>
    <TargetFrameworks Condition="$([MSBuild]::IsOSPlatform('windows'))">$(TargetFrameworks);net7.0-windows10.0.19041.0</TargetFrameworks>
    <OutputType>Exe</OutputType>
    <RootNamespace>KeeMind</RootNamespace>
    <UseMaui>true</UseMaui>
    <SingleProject>true</SingleProject>
    <Nullable>enable</Nullable>
    ....   
  </PropertyGroup>
</Project>

```

Add the target framework `net7.0` (or the current one the app is targeting) and put a condition on the `OutputType` header so that the MSBuild task doesn't produce an exe under the plain `net7.0` framework.

<pre class="language-xml"><code class="lang-xml">&#x3C;Project Sdk="Microsoft.NET.Sdk">
  &#x3C;PropertyGroup>
<strong>    &#x3C;TargetFrameworks>net7.0;net7.0-android;net7.0-ios;net7.0-maccatalyst&#x3C;/TargetFrameworks>
</strong>    &#x3C;TargetFrameworks Condition="$([MSBuild]::IsOSPlatform('windows'))">$(TargetFrameworks);net7.0-windows10.0.19041.0&#x3C;/TargetFrameworks>
<strong>    &#x3C;OutputType Condition="'$(TargetFramework)' != 'net7.0'">Exe&#x3C;/OutputType>
</strong>    &#x3C;RootNamespace>KeeMind&#x3C;/RootNamespace>
    &#x3C;UseMaui>true&#x3C;/UseMaui>
    &#x3C;SingleProject>true&#x3C;/SingleProject>
    &#x3C;Nullable>enable&#x3C;/Nullable>
    ....  
  &#x3C;/PropertyGroup>
&#x3C;/Project>
</code></pre>

This way the app project can be referenced in the test project.

Now let's create a test project, and choose the framework you like most (MSTest, xUnit, nUnit, etc).

As a final step, you have to reference the MAUI controls library in the test project adding the `<UseMaui>` header in the project definition:

<pre class="language-xml"><code class="lang-xml">  &#x3C;PropertyGroup>
    &#x3C;TargetFramework>net7.0&#x3C;/TargetFramework>
    &#x3C;ImplicitUsings>enable&#x3C;/ImplicitUsings>
    &#x3C;Nullable>enable&#x3C;/Nullable>
<strong>    &#x3C;UseMaui>true&#x3C;/UseMaui>
</strong>
    &#x3C;IsPackable>false&#x3C;/IsPackable>
  &#x3C;/PropertyGroup>
</code></pre>

## Test components

The `TemplateHost` is the key class to use when you want to test a component: it creates a virtual tree of nodes starting from the component you pass to it as the constructor parameter.

To create a `TemplateHost` just use a code like this:\
`TemplateHost.Create(new MyComponent());`

As you have the template host you can use some helper methods on it to traverse the tree to access native controls, check properties and raise events.

For example, say we want to test this component:

```csharp
new ContentPage
{
    new VStack
    {
        new Label($"Counter: {State.Counter}"),

        new Button("Click To Increment", () =>
            SetState(s => s.Counter++))
    }
}
```

First, we need to add a way for each control to be identified, one easy way is to augment the code with the AutomationId() method like it's shown in the following snippet:

```csharp
new ContentPage
{
    new VStack
    {
        new Label($"Counter: {State.Counter}")
            .AutomationId("Counter_Label"),

        new Button("Click To Increment", () =>
            SetState(s => s.Counter++))
            .AutomationId("Counter_Button")
    }
}
```

We can finally create a test the verify that the counter is working:

```csharp
var mainPageNode = TemplateHost.Create(new CounterWithServicePage());

// Check that the counter is 0
mainPageNode.Find<MauiControls.Label>("Counter_Label")
    .Text
    .ShouldBe($"Counter: 0");

// Click on the button
mainPageNode.Find<MauiControls.Button>("Counter_Button")
    .SendClicked();

// Check that the counter is 1
mainPageNode.Find<MauiControls.Label>("Counter_Label")
    .Text
    .ShouldBe($"Counter: 1");

```

TemplateHost has many find overloads that let you traverse the tree of controls to find the one you need to test.

## Components that inject services

In case your components need services from DI you have to inject them before creating the TemplateHost through the ServiceContext as shown below:

```csharp
using var serviceContext = new ServiceContext(services => services.AddSingleton<IncrementService>());
var mainPageNode = TemplateHost.Create(new CounterWithServicePage());
...
```

{% hint style="warning" %}
You have to always dispose of the `ServiceContext` object at the end of the test, even better if you wrap it with the `using` clause
{% endhint %}

## Attach components created with a page or modal

Often your components are hosted on a page that is created at runtime for example when the user pushes a button.\
In this case, you need to "attach" the new component using the NavigationContainer class as it's shown in the following sample code:

```csharp
using var navigationContainer = new NavigationContainer();

var mainPageNode = TemplateHost.Create(new NavigationMainPage());

// Verify that initially the value is 0
mainPageNode.Find<MauiControls.Label>("MainPage_Label")
    .Text
    .ShouldBe("Value: 0");

// Click the button to open the second page
mainPageNode.Find<MauiControls.Button>("MoveToChildPage_Button")
    .SendClicked();

// here we attach the new component created after the button is clicked
var childPageNode = navigationContainer.AttachHost();

// se entry text to 12
childPageNode.Find<MauiControls.Entry>("ChildPage_Entry")
    .Text = "12";

// click the button to go back to main page
childPageNode.Find<MauiControls.Button>("MoveToMainPage_Button")
    .SendClicked();

// Verify that now the label reports the updated text
mainPageNode.Find<MauiControls.Label>("MainPage_Label")
    .Text
    .ShouldBe("Value: 12");

```

{% hint style="warning" %}
As for the `ServiceConteiner`also the `NavigationContainer`object must be disposed of when the test ends; again it's a good idea to wrap it with the `using` keyword
{% endhint %}

