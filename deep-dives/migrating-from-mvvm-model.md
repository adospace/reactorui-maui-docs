---
description: Shows the difference between MVVM and MVU approach with a practical example
---

# Migrating from MVVM Model

#### Component-Based UI vs View-ViewModel

.NET MAUI promotes the separation between the View (usually written in XAML) and the Model (usually written in C#). This pattern is called View-ViewModel and has been historically adopted by a lot of UI frameworks like WPF/SL, Angular, etc.&#x20;

Recently Component based pattern has gained much popularity thanks to ReactJS and Flutter.

#### .NET MAUI View-ViewModel

Let's take for example a login page written in .NET MAUI composed of a MainPage (XAML) and a ViewModel MainPageViewModel (c#):

```markup
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:d="http://xamarin.com/schemas/2014/forms/design"
             xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
             mc:Ignorable="d"
             x:Class="XamarinApp1.MainPage">
    <StackLayout
        VerticalOptions="Center"
        HorizontalOptions="Center">
        <Entry Placeholder="Username" Text="{Binding Username}" />
        <Entry Placeholder="Password" Text="{Binding Password}" />
        <Button Text="Login" Command="{Binding LoginCommand}" />
    </StackLayout>
</ContentPage>
```

```csharp
public class MainPageViewModel: BindableObject
{
    private string _username;

    public string Username
    {
        get => _username;
        set
        {
            if (_username != value)
            {
                _username = value;
                OnPropertyChanged();
                LoginCommand.ChangeCanExecute();
            }
        }
    }

    private string _password;

    public string Password
    {
        get => _password;
        set
        {
            if (_password != value)
            {
                _password = value;
                OnPropertyChanged();
                LoginCommand.ChangeCanExecute();
            }
        }
    }

    private Command _loginCommand;

    public Command LoginCommand
    {
        get
        {
            _loginCommand = _loginCommand ?? new Command(OnLogin, () => !string.IsNullOrWhiteSpace(Username) && !string.IsNullOrWhiteSpace(Password));
            return _loginCommand;
        }
    }

    private void OnLogin()
    {
        //Username contains username and Password contains password
        //make login..
    }
}

```

{% hint style="info" %}
Nowadays, developers can take advantage of the latest [MVVM toolkit](https://learn.microsoft.com/en-us/windows/communitytoolkit/mvvm/introduction) package that reduces much of the verbosity in writing ViewModels class.
{% endhint %}

#### ReactorUI Component-Based

Following is the same login page but written using MauiReactor:

```csharp
public class MainPageState
{
    public string Username { get; set; }
    public string Password { get; set; }
}

public class MainPage : Component<MainPageState>
{
    public override VisualNode Render()
    {
        return new ContentPage()
        {
            new StackLayout()
            {
                new Entry()
                    .Placeholder("Username")
                    .OnTextChanged((s,e)=> SetState(_ => _.Username = e.NewTextValue)),
                new Entry()
                    .Placeholder("Password")
                    .OnTextChanged((s,e)=> SetState(_ => _.Password = e.NewTextValue)),
                new Button("Login")
                    .IsEnabled(!string.IsNullOrWhiteSpace(State.Username) && !string.IsNullOrWhiteSpace(State.Password))
                    .OnClick(OnLogin)
            }
            .VCenter()
            .HCenter()
        };
    }

    private void OnLogin()
    {
        //use State.Username and State.Password to login...
    }
}
```
