---
description: >-
  This page describe how you can use the MauiReactor TemplateHost class to
  create custom native DataTemplates
---

# Provide DataTemplate to native controls

When integrating external libraries in MauiReactor, sometimes you need to provide a DataTemplate to the native control in order to render its content.&#x20;

For example, consider the SfPopup control from Syncfusion vendor: if you want to customize its content you need to set a DataTemplate to the ContentTemplate property ([https://help.syncfusion.com/maui/popup/layout-customizations](https://help.syncfusion.com/maui/popup/layout-customizations)).

In MauiReactor you can use a code like this:

```csharp
[Scaffold(typeof(Syncfusion.Maui.Popup.SfPopup))]
public partial class SfPopup 
{
    public SfPopup Content(Func<VisualNode> render)
    {
        this.Set(Syncfusion.Maui.Popup.SfPopup.ContentTemplateProperty, 
            new MauiControls.DataTemplate(() => TemplateHost.Create(render()).NativeElement));

        return this;
    }
}

[Scaffold(typeof(Syncfusion.Maui.Core.SfView))]
public abstract class SfView { }


class MainPageState
{
    public bool IsPopupOpen { get; set; }
}

class MainPage : Component<MainPageState>
{
    public override VisualNode Render()
    {
        return new ContentPage("Pagina uno")
        {
            new VStack() {
                new Label("Main page"),
                new Button().Text("Apri popup").OnClicked(ApriPopup),

                new SfPopup()
                    .Content(()=>
                        new VStack()
                        {
                            new Label("my label")
                        })
                    .IsOpen(State.IsPopupOpen)
                    .OnClosed(()=>SetState(s => s.IsPopupOpen = false, false))

            }.VCenter().HCenter()
        };
    }

    private void ApriPopup()
    {
        SetState(s => s.IsPopupOpen = true);
    }
}
```

{% hint style="warning" %}
Integrating native controls that require DataTemplate to render their content (for example dealing with CollectionView-like controls) could be a complex task.

Often the best approach is following what is already realized in MauiReactor itself, for example looking at CollectionView or ListView implementations.

If you encounter a problem, please look for similar issues or open a new one in MauiReactor GitHub repository.
{% endhint %}
