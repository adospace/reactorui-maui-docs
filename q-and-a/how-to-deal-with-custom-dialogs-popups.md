---
description: >-
  This article explains how to integrate Popup control from the
  CommunityToolkit.Maui
---

# How to deal with custom dialogs/popups?

## Imperative approach

You can show a Popup using code like this:

```csharp
public static class PopupManager
{
    class PopupVisualContainer : ContentView
    {
        public void Unmount()
        {
            //this is only required if you want to attach some code when the popup is closed
            base.OnUnmount();
        }
    }

    static Popup? _sheet;
    static PopupVisualContainer? _popupVisualContainer;
    private static ITemplateHost? _templateHost;

    public static async Task ShowPopupAsync(this MauiControls.Page? page, 
        Func<VisualNode> contentRender, 
        Action<PopupOptions>? configureOptionsAction = null, 
        CancellationToken cancellationToken = default)
    {
        if (page == null)
        {
            return;
        }

        if (_sheet != null)
        {
            return;
        }

        _popupVisualContainer = new PopupVisualContainer()
        {
            contentRender()
        };

        _templateHost = TemplateHost.Create(_popupVisualContainer);

        _sheet = new Popup
        {
            Content = (MauiControls.View)_templateHost.NativeElement!
        };

        PopupOptions? options = null;
        if (configureOptionsAction != null)
        {
            options = new PopupOptions();
            configureOptionsAction(options);
        }

        await page.ShowPopupAsync((MauiControls.View)_templateHost.NativeElement!, options, cancellationToken);

        _sheet = null;
        _templateHost = null;
    }
}
```

And, in the following example, we use it to create Alert and Confirmation dialogs:

```csharp
public static class Popups
{
    public static async Task Alert(MauiControls.Page? page, string title, string message, string cancel = "OK")
    {
        if (page == null)
        {
            return;
        }

        await page.ShowPopupAsync(() =>
        {
            return
                Component.VStack(
                    Component.Label(title),

                    Component.Label(message),

                    Component.Button(cancel)
                        .OnClicked(() => page.ClosePopupAsync())
                )
            ;
        },
        configureOptionsAction: options =>
        {
            options.CanBeDismissedByTappingOutsideOfPopup = false;
        });
    }

    public static async Task<bool> Confirm(MauiControls.Page? page, string title, string message, string accept = "Yes", string cancel = "No")
    {
        if (page == null)
        {
            return false;
        }
        
        bool result = false;
        await page.ShowPopupAsync(() =>
        {
            return
                Component.VStack(
                    Component.Label(title),

                    Component.Label(message),

                    Component.HStack(AppTheme.SpacingNormal,
                        Component.Button(cancel)
                            .OnClicked(() => page.ClosePopupAsync()),
                        Component.Button(accept)
                            .HEnd()
                            .OnClicked(() =>
                            {
                                result = true;
                                page.ClosePopupAsync();
                            })
                    )
                    .HEnd()
                    .Margin(0, AppTheme.BaseSize, 0, 0)
                )
            ;
        },
        configureOptionsAction: options =>
        {
            options.CanBeDismissedByTappingOutsideOfPopup = false;
        });
        return result;
    }
}

```

## Declarative approach

This solution is for the CommunityToolkit.Maui Popup:

```csharp
[Scaffold(typeof(CommunityToolkit.Maui.Views.Popup))]
partial class Popup 
{
    protected override void OnAddChild(VisualNode widget, MauiControls.BindableObject childNativeControl)
    {
        if (childNativeControl is MauiControls.View content)
        {
            Validate.EnsureNotNull(NativeControl);
            NativeControl.Content = content;
        }    

        base.OnAddChild(widget, childNativeControl);
    }

    protected override void OnRemoveChild(VisualNode widget, MauiControls.BindableObject childNativeControl)
    {
        Validate.EnsureNotNull(NativeControl);

        if (childNativeControl is MauiControls.View content &&
            NativeControl.Content == content)
        {
            NativeControl.Content = null;
        }
        base.OnRemoveChild(widget, childNativeControl);
    }
}

class PopupHost : Component
{
    private CommunityToolkit.Maui.Views.Popup? _popup;
    private bool _isShown;
    private Action<object?>? _onCloseAction;
    private readonly Action<CommunityToolkit.Maui.Views.Popup?>? _nativePopupCreateAction;

    public PopupHost(Action<CommunityToolkit.Maui.Views.Popup?>? nativePopupCreateAction = null)
    {
        _nativePopupCreateAction = nativePopupCreateAction;
    }

    public PopupHost IsShown(bool isShown)
    {
        _isShown = isShown;
        return this;
    }

    public PopupHost OnClosed(Action<object?> action)
    {
        _onCloseAction = action;
        return this;
    }

    protected override void OnMounted()
    {
        InitializePopup();
        base.OnMounted();
    }

    protected override void OnPropsChanged()
    {
        InitializePopup();
        base.OnPropsChanged();
    }

    void InitializePopup()
    { 
        if (_isShown && MauiControls.Application.Current != null)
        {
            MauiControls.Application.Current?.Dispatcher.Dispatch(() =>
            {
                if (ContainerPage == null ||
                    _popup == null)
                {
                    return;
                }

                ContainerPage.ShowPopup(_popup);
            });
        }
    }

    public override VisualNode Render()
    {
        var children = Children();
        return _isShown ?
            new Popup(r =>
            {
                _popup = r;
                _nativePopupCreateAction?.Invoke(r);
            })
            {
                children[0]
            }
            .OnClosed(OnClosed)
            : null!;
    }

    void OnClosed(object? sender, PopupClosedEventArgs args)
    {
        _onCloseAction?.Invoke(args.Result);
    }
}
```

and this is how you can use it in your components:

```csharp
class ShowPopupTestPage : Component<ShowPopupTestPageState>
{
    private CommunityToolkit.Maui.Views.Popup? _popup;

    public override VisualNode Render()
    {
        return new ContentPage()
        {
            new Grid
            {
                new Button(State.Result == null ? "Show popup" : $"Result: {State.Result.GetValueOrDefault()}")
                    .HCenter()
                    .VCenter()
                    .OnClicked(ShowPopup),

                new PopupHost(r => _popup = r)
                {
                    new VStack(spacing: 10)
                    {
                        new Label("Hi!"),

                        new HStack(spacing: 10)
                        {
                            new Button("OK", ()=> _popup?.Close(true)),

                            new Button("Cancel", ()=> _popup?.Close(false)),
                        }
                    }
                }
                .IsShown(State.IsShown)
                .OnClosed(result => SetState(s =>
                {
                    s.IsShown = false;
                    s.Result = (bool?)result;
                }))
            }
        };
    }

    private void ShowPopup()
    {
        SetState(s => s.IsShown = true);
    }
}
```

<figure><img src="https://user-images.githubusercontent.com/10573253/223133271-30c626ed-1fae-4e44-80f3-b351e0618ffb.gif" alt=""><figcaption><p>CommunityToolkit MAUI Popup</p></figcaption></figure>
