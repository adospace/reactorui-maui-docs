---
description: Describes the list of changes present in Version 3 of MauiReactor
---

# What's new in Version 4

Version 4 of MauiReactor targets MAUI .NET 10, and even though it is highly compatible with the previous version, targeting .NET 9, it has some notable breaking changes on some tooling (hot-reload).

{% hint style="info" %}
For an up-to-date list of new features for MauiReactor4, please head to [https://github.com/adospace/reactorui-maui/issues/346](https://github.com/adospace/reactorui-maui/issues/263)
{% endhint %}

## Hot-reload changes

Hot-reload is now available with a separate package `Reactor.Maui.HotReload`. Be sure to reference the package in your project; otherwise, the EnableMauiReactorHotReload() won't compile.

Add the call to EnableMauiReactorHotReload() call in `program.cs` :

<pre class="language-csharp" data-line-numbers><code class="lang-csharp">var builder = MauiApp.CreateBuilder();
builder
    .UseMauiReactorApp&#x3C;HomePage>()
<strong>#if DEBUG
</strong><strong>    .EnableMauiReactorHotReload()
</strong><strong>#endif
</strong>    ...;

</code></pre>

In the project definition, add the following lines:

```xml
```



