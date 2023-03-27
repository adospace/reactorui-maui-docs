# How to deal with state shared across Components?

Hi, given that you can choose the best approach that fits better for your project here are 3 different ways to achieve it in MauiReactor/C# that I can think of:

1. Pass global state/parameters down to your component tree. For example, say you have a class that resembles your settings pass it down to your components:

```csharp
class Settings
{
  public int MySettingValue {get;set;}
}

class MyComponent
{
   Settings? _settings;
    public MyComponent Settings(Settings settings)
    {
       _settings = settings;
      return this;
    }

    public override VisualNode Render()
    {
      //use _settings and pass down to components used here _settings object
    }
}
```

2. Use dependency injection to "inject" your global state/parameters:

```csharp
interface IGlobalSettings //->implemented somewhere and registered in MauiProgram.cs
{
    int MySettingValue {get;set;}
}

class MyComponent
{   
    public override VisualNode Render()
    {
          var settings = Services.GetRequiredService<IGlobalSettings>();
         //use settings, but no need to pass down to components used here (they can easily access the settings class with DI as well)
    }
}
```

3. Use the "Parameters" feature provided by MauiReactor: it allows you to create a Parameter in a parent component and access it in read-write mode from child components:

```csharp
class CustomParameter
{
    public int Numeric { get; set; }
}

class ParametersPage: Component
{
    private readonly IParameter<CustomParameter> _customParameter;

    public ParametersPage()
    {
        _customParameter = CreateParameter<CustomParameter>();
    }

    public override VisualNode Render()
    {
        return new ContentPage("Parameters Sample")
        {
            new VStack(spacing: 10)
            {
                new Button("Increment from parent", () => _customParameter.Set(_=>_.Numeric += 1   )),
                new Label(_customParameter.Value.Numeric),

                new ParameterChildComponent()
            }
            .VCenter()
            .HCenter()
        };
    }
}

class ParameterChildComponentc: Component
{
    public override VisualNode Render()
    {
        var customParameter = GetParameter<CustomParameter>();

        return new VStack(spacing: 10)
        {
            new Button("Increment from child", ()=> customParameter.Set(_=>_.Numeric++)),

            new Label(customParameter.Value.Numeric),
        };
    }
}
```

for more info on this specific feature please take a look at:

* [https://github.com/adospace/reactorui-maui/blob/main/samples/MauiReactor.TestApp/Pages/ParametersPage.cs](https://github.com/adospace/reactorui-maui/blob/main/samples/MauiReactor.TestApp/Pages/ParametersPage.cs)
* [https://github.com/adospace/kee-mind](https://github.com/adospace/kee-mind) (KeeMind app makes extensive use of this feature)
* [https://app.gitbook.com/s/h1eh1igwiwRzrw2kbxSp/\~/changes/55/components/component-parameters](https://app.gitbook.com/s/h1eh1igwiwRzrw2kbxSp/\~/changes/55/components/component-parameters)



