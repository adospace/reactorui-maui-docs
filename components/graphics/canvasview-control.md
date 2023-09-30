---
description: >-
  The CanvasView is a MauiReactor control (not derived from a MAUI control) that
  is used to draw graphics objects with a declarative approach
---

# CanvasView control

MAUI developers can draw lines, shapes, or text (generally speaking called drawing objects) directly on a canvas through the `GraphicsView` control or the `SKCanvas` class provided by the [SkiaSharp library](https://github.com/mono/SkiaSharp) in an _imperative_ approach.&#x20;

MauiRector introduces another way to draw graphics and it's entirely _declarative._ In short, this method consists in describing the tree of the objects you want to show inside a canvas as child nodes of the CanvasView control.

For example, consider this code that declares a `CanvasView` control inside a `ContentPage`:

```csharp
class CanvasPage : Component
{
    public override VisualNode Render()
    {
        return new ContentPage("Canvas Test Page")
        {
            new CanvasView
            {
                
            }
        };
    }
}
```

Consider we want to draw a red rounded rectangle, then we just need to declare it as:

{% code lineNumbers="true" %}
```csharp
new CanvasView
{
    new Box()
        .Margin(10)
        .BackgroundColor(Colors.Red)
        .CornerRadius(10)
}
```
{% endcode %}

<figure><img src="../../.gitbook/assets/image (2) (1).png" alt=""><figcaption><p>Just a box inside a CanvasView</p></figcaption></figure>

As you can notice, we have also added a margin to distantiate it from the container (the ContentPage).

Of course, there is much more than this. As a starter, you are allowed to place more widgets inside a CanvasView and moreover arrange them in a Stack, Row, or Column layout similar to what you can do using normal MAUI controls and usual layout systems.

```csharp
new CanvasView
{
    new Column
    {
        new Box()
            .Margin(10)
            .BackgroundColor(Colors.Green)
            .CornerRadius(10),
        new Box()
            .Margin(10)
            .BackgroundColor(Colors.Red)
            .CornerRadius(10)
    }
}
```

<figure><img src="../../.gitbook/assets/image (5) (1).png" alt=""><figcaption><p>A Column layout with equally spaced children</p></figcaption></figure>

Column and Row layouts are the most common way to arrange CanvasView elements, and render children in a way much similar to MAUI Grid layout.&#x20;

For example, you can set a fixed size for one or more elements, giving proportional space for the other:

```csharp
new CanvasView
{
    new Column("100,*")
    {
        new Box()
            .Margin(10)
            .BackgroundColor(Colors.Green)
            .CornerRadius(10),
        new Box()
            .Margin(10)
            .BackgroundColor(Colors.Red)
            .CornerRadius(10)
    }
}
```

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
Row and Column layouts do not support currently Auto sizing for children (as happens instead for the standard Grid control) and it's an intentional design decision: CanvasView layout system is built without the render pass in order to be as fast as possible in rendering graphics. &#x20;
{% endhint %}

Many controls embeddable in MauiReactor CanvasView can contain a child. For example, the Box element can contain a Text, Image, or Row/Column controls like it's shown in the following code:

```csharp
new CanvasView
{
    new Column("100,*")
    {
        new Box()
        { 
            new Text("This a Text element!")
                .HorizontalAlignment(HorizontalAlignment.Center)
                .VerticalAlignment(VerticalAlignment.Center)
                .FontColor(Colors.White)
                .FontSize(24)                    
        }
        .Margin(10)
        .BackgroundColor(Colors.Green)
        .CornerRadius(10)
        ,
        new Box()
        { 
            new Column("*, 50")
            {
                new Picture("MauiReactor.TestApp.Resources.Images.Embedded.norway_1.jpeg"),
                new Text("Awesome Norway!")
                    .HorizontalAlignment(HorizontalAlignment.Center)
                    .VerticalAlignment(VerticalAlignment.Center)
                    .FontColor(Colors.White)
                    .FontSize(24)
            },
        }
        .Margin(10)
        .BackgroundColor(Colors.Red)
        .CornerRadius(10)
    }
}

```

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption><p>A bit more complex CanvasView element tree</p></figcaption></figure>

{% hint style="info" %}
CanvasView uses a GraphicsView control behind the scenes, this means that to load an image you have to embed it in your assembly: add the image under the Resources folder and set the build action to "Embedded resource"
{% endhint %}

Following is the list of elements you can embed inside a CanvasView currently:

* Box: Simple and efficient container that renders a rectangle that you can customize with properties like Background, Borders, and Padding
* Row/Column: Arrange children in a Row or Column using a layout system similar to the Grid with one Row or one Column.
* Group: container of elements that stacks children one on top of the other (sometimes called Z-Stack in other frameworks)
* Ellipse: draw an ellipse/circle that also contains a child
* Text: draw a string, customizable with properties like FontSize, FontColor, or Font.
* Align: it's one of the most useful elements because it aligns its child inside the rendered rectangle, for example to the borders of it.
* PointInteractionHandler: This is the element that triggers an event in the form of a callback function when the user taps inside its render area. Also supports events like mouse over and double click.
* Picture: draw an image loading it from an embedded resource

