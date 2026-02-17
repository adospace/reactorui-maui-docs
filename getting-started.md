---
description: Describes how to setup a MauiReactor project
---

# Getting Started

## Creating a new MauiReactor project from CLI

MauiReactor provides a convenient dotnet project template you can install to easily create a new dotnet maui project with MauiReactor bindings:

```
dotnet new install Reactor.Maui.TemplatePack
```

To create a new project, just issue

```
dotnet new maui-reactor-startup -o my-new-project
```

To build the project, just move inside the project directory and run the usual dotnet build command like this (in the example below, we'll use the Android target; the same applies to the other targets like  net10.0-ios, net10.0-maccatalyst, or windows10.0.19041.0):

```
cd .\my-new-project\
dotnet build -f net10.0-android
```

To run the app under the Android platform, execute the following command:

```
dotnet build -t:Run -f net10.0-android
```

## Install MauiReactor hot-reload console

MauiReactor also provides a Hot-Reload console program that automatically hot reloads the project you are working on as you save changes to any file.

To install it, just type the following command in the terminal:

```
dotnet tool install -g Reactor.Maui.HotReloadConsole
```

If you want to upgrade the tool to the latest version, run the command:

```
dotnet tool update -g Reactor.Maui.HotReloadConsole
```

Ensure that Reactor.Maui.HotReload package is configured (here we're conditionally installing the package only for the debug configuration):

<pre class="language-xml" data-line-numbers><code class="lang-xml">	&#x3C;ItemGroup>
		&#x3C;PackageReference Include="Microsoft.Maui.Controls" Version="10.0.10" />
		&#x3C;PackageReference Include="Microsoft.Extensions.Logging.Debug" Version="10.0.0" />
<strong>		&#x3C;PackageReference Include="Reactor.Maui" Version="4.0.15" />
</strong>	&#x3C;/ItemGroup>

<strong>	&#x3C;ItemGroup Condition="'$(Configuration)'=='Debug'">
</strong><strong>		&#x3C;PackageReference Include="Reactor.Maui.HotReload" Version="4.0.15" />
</strong><strong>	&#x3C;/ItemGroup>
</strong></code></pre>

Finally, add the call to `UseMauiReactorHotReload()` call in `program.cs` :

<pre class="language-csharp" data-line-numbers><code class="lang-csharp">var builder = MauiApp.CreateBuilder();
builder
    .UseMauiReactorApp&#x3C;HomePage>()
<strong>#if DEBUG
</strong><strong>    .UseMauiReactorHotReload();
</strong><strong>#endif
</strong>    ...;

</code></pre>

## MauiReactor hot-reload console

MauiReactor hot reload can work in two different modes: **Simple** and **Full**

{% hint style="info" %}
**Simple Mode (Default)**: This allows you to hot-reload the app as you save changes to the project. It's faster than Full Mode because it uses in-memory compilation but has some drawbacks:

1\) Debugging is not supported: so for example, if you set a breakpoint it will not be hit after you hot-reload the app.\
2\) It's unable to build XAML files; so if you're planning to link XAML files for resources (like styles, brushes, etc) you may need to switch to the Full mode.

**Full Mode**: This mode uses the full-blown MS Build task to build the project after a file is changed/renamed/added. It's slower than Simple Mode but you can thoroughly debug your app even when you have hot-reloaded it. Also, it works better if you have XAML files or references to managed libraries in your project.
{% endhint %}

To start the hot-reload console in **Simple Mode**:

```
dotnet-maui-reactor -f [net10.0-android|net10.0-ios|net10.0-maccatalyst|net10.0-windows10.0.19041.0]
```

This is the command to start it in **Full Mode**:

```
dotnet-maui-reactor -f [net10.0-android|net10.0-ios|net10.0-maccatalyst|net10.0-windows10.0.19041.0] --mode Full
```

This is the typical startup messages from the hot-reload tool:

```
MauiReactor Hot-Reload CLI
Version 4.x.x.x
Press Ctrl+C or Ctrl+Break to quit
Setting up build pipeline for TrackizerApp project...done.
Monitoring folder 'C:\Source\github\mauireactor-samples\TrackizerApp'...
```

{% hint style="info" %}
MauiReactor hot-reload keeps listening to file changes in the directory, but doesn't run the application: you need to open another console to run the application or run/debug it using an IDE like Visual Studio.
{% endhint %}

{% hint style="warning" %}
Under MacOS, ensure that your app is NOT executed in "sandbox mode"; otherwise, hot-reload won't work.

Go to Platforms->MacCatalyst->Entitlements.plist and set/update this value:

```
        <key>com.apple.security.app-sandbox</key>
        <false/>
```

Please note that before publishing to the App Store this value must be set to true again.       &#x20;
{% endhint %}

{% hint style="info" %}
You can also hot-reload applications running in an iOS emulator using Windows and Visual Studio: use the `-h/--host` command line arguments to set the remote Mac host.

For example:\
`dotnet-maui-reactor -f net10.0-ios -h ip-of-the-mac`

For more info on how to debug iOS applications from Visual Studio under Windows please get a look at the  .NET MAUI official documentation:\
[https://learn.microsoft.com/en-us/dotnet/maui/ios/remote-simulator?view=net-maui-8.0](https://learn.microsoft.com/en-us/dotnet/maui/ios/remote-simulator?view=net-maui-8.0)
{% endhint %}

{% hint style="info" %}
Hot-reloading of an Android application requires the presence of the **adb** tool.

Check that the adb tool is installed and working by listing the devices with the command:

`adb devices`

If the command is not recognized then you could install it with `brew`:

1.  Install the [homebrew](http://brew.sh/) package manager if not installed

    ```bash
     /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"
    ```
2.  Install adb

    ```
     brew install android-platform-tools
    ```
3.  Start using adb

    ```
     adb devices
    ```
{% endhint %}

## .NET built-in hot-reload

MauiReactor also supports .NET built-in hot-reload. This feature is enabled by default when using the hot-reload.

To enable the hot-reload for MAUI projects and an updated list of supported edits, please look at the official documentation [here](https://learn.microsoft.com/en-us/visualstudio/debugger/hot-reload?view=vs-2022).

## Create a new project in Visual Studio 2022+

After you have installed the dotnet project template, you should see it in the Visual Studio project creation dialog:

<figure><img src=".gitbook/assets/image (4) (1).png" alt=""><figcaption><p>Select the MauiReactor based app template</p></figcaption></figure>

## Migrate from the default MAUI project template

It's fine to start from a standard MAUI project template: below, we'll see what is required to migrate a brand-new project to MauiReactor. This short guide also helps to make a port from an existing MVVM project to MauiReactor.

#### Step 1

Include the latest version of the MauiReactor package (select the latest version):

```
<PackageReference Include="Reactor.Maui" Version="4.0.*" />
```

Even if not strictly required, I suggest removing the ImplicitUsings directive in the csproj file:

```xml
<ImplicitUsings>disable</ImplicitUsings>
```

and add a GlobalUsings.cs file containing these global usings:

```csharp
global using System;

global using Microsoft.Maui;
global using Microsoft.Maui.Hosting;
global using Microsoft.Maui.Graphics;
global using MauiControls = Microsoft.Maui.Controls;
```

This will avoid problems with namespacing conflicts between MAUI and MauiReactor.

#### Step 2

Remove the file App.xaml, AppShell.xaml, and MainPage.xaml (including the code-behind files).

#### Step 3

Create a MainPage component: add a HomePage.cs file in the project root folder with this code:

```csharp
class MainPage : Component
{
    public override VisualNode Render()
    => ContentPage(
           Label("Hi!")
            .Center()
        );
}
```

#### Step 4

Finally, replace the Program.cs main content:

```csharp
public static MauiApp CreateMauiApp()
{
    var builder = MauiApp.CreateBuilder();
    builder
        .UseMauiReactorApp<MainPage>()
        .ConfigureFonts(fonts =>
        {
            fonts.AddFont("OpenSans-Regular.ttf", "OpenSansRegular");
            fonts.AddFont("OpenSans-SemiBold.ttf", "OpenSansSemiBold");
        });

    return builder.Build();
}
```

## Best practices

When the app is hot-reloaded, a new assembly is compiled on the fly and injected into the running app. This means that the new component lives in a different assembly from the original one. It's recommended to follow these best practices to avoid type mismatch issues:

* The component state class should contain only public properties whose types are value-type or string.
* If you need a component state with properties other than value-type/string (i.e., classes), host them in a separate assembly (project) so that it's not hot reloaded.
