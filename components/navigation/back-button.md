---
description: How to handle the back button visibility and pressed event
---

# Back button

The back button in MAUI has different behavior and settings based on whether you're hosting the child page under a NavigationPage or Shell.

## Shell

Under the Shell, you can use the BackButtonBehavior class to customize the BackButton.

To handle back button behavior you have to provide a call-back action in the `OnBackButtonPressed`

```csharp
new ContentPage()
    .OnBackButtonPressed(()=>...);
```

You can also set visibility, icon source etc directly to the Page, like this:

```csharp
new ContentPage()
    .BackButtonIsVisible(true/false)
    .BackButtonIsEnabled(true/false)
    .BackButtonText(...)
    .BackButtonIcon(...)
```

## NavigationPage

If the child page is hosted under a NavigationPage the BackButton can be disabled using the AttachedProperty HasBackButtonDisable dependency property.\
In MauiReactor use this code:



For the BackButton pressed event you have instead to hack it a bit as there isn't a BackButtonPressed event out of the box and the OnBackButtonPressed can't work.\


To handle such an event we have to create a Page-derived custom class and override the OnBackButtonPressed method:

```csharp
public class CustomContentPage : MauiControls.ContentPage
{
    public event EventHandler BackButtonPressed;

    protected override bool OnBackButtonPressed()
    {
        BackButtonPressed?.Invoke(this, EventArgs.Empty);
        return base.OnBackButtonPressed();
    }
}
```

{% hint style="info" %}
Put the above code in a separate file/namespace different from the namespace containing the component that will scaffold it.
{% endhint %}

Finally, scaffold and use it in your component:

```csharp
[Scaffold(typeof(Project24.Controls.CustomContentPage))]
public partial class CustomContentPage
{
}

public class MainPage : Component
{
    public override VisualNode Render()
    {
        return new NavigationPage()
        {
            new ContentPage()
            {
                new Button("Move To Page")
                    .VCenter()
                    .HCenter()
                    .OnClicked(OpenChildPage)
            }
            .Title("Main Page")
        }
            ;
    }

    private async void OpenChildPage()
    {
        await Navigation.PushAsync<ChildPage>();
    }
}

public class ChildPage : Component
{
    public override VisualNode Render()
    {
        return new CustomContentPage()
        {
            new Button("Back")
                .VCenter()
                .HCenter()
                .OnClicked(GoBack)
        }
        .Set(MauiControls.NavigationPage.HasBackButtonProperty, true)
        .OnBackButtonPressed(OnBack)
        .Title("Child Page");
    }


    async void OnBack()
    {
        await ContainerPage.DisplayAlert("Hey", "hey", "cancel");
    }

    private async void GoBack()
    {
        await Navigation.PopAsync();
    }
}
```
