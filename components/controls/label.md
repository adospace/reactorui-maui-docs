---
description: This page descibes how to create a Label in MauiReactor
---

# Label

> The .NET Multi-platform App UI (.NET MAUI) [Label](https://learn.microsoft.com/en-us/dotnet/api/microsoft.maui.controls.label) displays single-line and multi-line text. Text displayed by a [Label](https://learn.microsoft.com/en-us/dotnet/api/microsoft.maui.controls.label) can be colored, spaced, and can have text decorations.

Creating a Label in MauiReactor is pretty easy:

```csharp
Label("My label text")
```

Formatted text can be created as well with a code like this:

```csharp
Label(
    FormattedString(
        Span("Red bold, ", Colors.Red, FontAttributes.Bold),
        Span("default, ",
            TapGestureRecognizer(async () => await ContainerPage!.DisplayAlert("Tapped", "This is a tapped Span.", "OK"))
            ),
        Span("italic small.", FontAttributes.Italic, 14)
        )
    )
```

If you are on MauiReactor 2, you have to provide a `FormattedString` object explicitly:

```csharp
Label()
    .FormattedText(()=>
    {
        //of course FomattedText here, being static, can be created as a static variable and passed to Label().FormattedText(myStaticFormattedText)
        FormattedString formattedString = new();
        formattedString.Spans.Add(new Span { Text = "Red bold, ", TextColor = Colors.Red, FontAttributes = FontAttributes.Bold });
    
        Span span = new() { Text = "default, " };
        span.GestureRecognizers.Add(new MauiControls.TapGestureRecognizer { Command = new Command(async () => await ContainerPage!.DisplayAlert("Tapped", "This is a tapped Span.", "OK")) });
        formattedString.Spans.Add(span);
        formattedString.Spans.Add(new Span { Text = "italic small.", FontAttributes = FontAttributes.Italic, FontSize = 14 });
    
        return formattedString;
    })
```

{% hint style="warning" %}
If the FormattedText object is static (i.e., not changed with the state), consider creating a static variable to pass to the `Label().FormattedText(myStaticVar)` method.
{% endhint %}

The above code produces a formatted text like the following:

<figure><img src="../../.gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>
