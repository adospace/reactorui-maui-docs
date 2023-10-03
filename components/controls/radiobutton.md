---
description: This page describes how to create radio buttons in MauiReactor
---

# RadioButton

> The .NET Multi-platform App UI (.NET MAUI) [RadioButton](https://learn.microsoft.com/en-us/dotnet/api/microsoft.maui.controls.radiobutton) is a type of button that allows users to select one option from a set. Each option is represented by one radio button, and you can only select one radio button in a group.&#x20;

Official documentation:\
[https://learn.microsoft.com/en-us/dotnet/maui/user-interface/controls/radiobutton](https://learn.microsoft.com/en-us/dotnet/maui/user-interface/controls/radiobutton)\
\
MauiReactor sample app:\
[https://github.com/adospace/mauireactor-samples/tree/main/Controls/RadioButtonTestApp](https://github.com/adospace/mauireactor-samples/tree/main/Controls/RadioButtonTestApp)

## Group of radio buttons with string content

```csharp
new VStack(spacing: 5)
{
    new RadioButton("Radio 1"),
    new RadioButton("Radio 2"),
    new RadioButton("Radio 3"),
    new RadioButton("Radio 4")
        .IsChecked(true)
},
```

<figure><img src="../../.gitbook/assets/image (2) (1).png" alt=""><figcaption><p>Simple radio button group</p></figcaption></figure>

## Group of radio buttons with custom content

```csharp
new VStack
{
    new RadioButton
    {
        new Image("icon_email.png")
    },
    new RadioButton
    {
        new Image("icon_lock.png")
    }
}
```

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1).png" alt=""><figcaption><p>Radio buttons with custom content (iOS)</p></figcaption></figure>

{% hint style="warning" %}
On Android, you must define a ControlTemplate to show custom content for the radio button.

[https://learn.microsoft.com/en-us/dotnet/maui/user-interface/controls/radiobutton?view=net-maui-7.0#redefine-radiobutton-appearance](https://learn.microsoft.com/en-us/dotnet/maui/user-interface/controls/radiobutton?view=net-maui-7.0#redefine-radiobutton-appearance)

In MauiReactor you can place the control template in a XAML resource file
{% endhint %}

This is a sample control template for Android:

```xml
<Style TargetType="RadioButton">
    <Setter Property="Background" Value="Transparent"/>
    <Setter Property="TextColor" Value="{AppThemeBinding Light={StaticResource Black}, Dark={StaticResource White}}" />
    <Setter Property="FontFamily" Value="OpenSansRegular"/>
    <Setter Property="FontSize" Value="14"/>
    <Setter Property="VisualStateManager.VisualStateGroups">
        <VisualStateGroupList>
            <VisualStateGroup x:Name="CommonStates">
                <VisualState x:Name="Normal" />
                <VisualState x:Name="Disabled">
                    <VisualState.Setters>
                        <Setter Property="TextColor" Value="{AppThemeBinding Light={StaticResource Gray300}, Dark={StaticResource Gray600}}" />
                    </VisualState.Setters>
                </VisualState>
            </VisualStateGroup>
        </VisualStateGroupList>
    </Setter>
    <Setter Property="ControlTemplate">
        <ControlTemplate>
            <Grid Margin="4">
                <VisualStateManager.VisualStateGroups>
                    <VisualStateGroupList>
                        <VisualStateGroup x:Name="CheckedStates">
                            <VisualState x:Name="Checked">
                                <VisualState.Setters>
                                    <Setter TargetName="check"
                                            Property="Opacity"
                                            Value="1" />
                                </VisualState.Setters>
                            </VisualState>
                            <VisualState x:Name="Unchecked">
                                <VisualState.Setters>
                                    <Setter TargetName="check"
                                            Property="Opacity"
                                            Value="0" />
                                </VisualState.Setters>
                            </VisualState>
                        </VisualStateGroup>
                    </VisualStateGroupList>
                </VisualStateManager.VisualStateGroups>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="Auto"/>
                    <ColumnDefinition/>
                </Grid.ColumnDefinitions>
                <Grid Margin="4"
                      WidthRequest="18"
                      HeightRequest="18"
                      HorizontalOptions="End"
                      VerticalOptions="Start">
                    <Ellipse Stroke="Blue"
                             Fill="White"
                             WidthRequest="16"
                             HeightRequest="16"
                             HorizontalOptions="Center"
                             VerticalOptions="Center" />
                    <Ellipse x:Name="check"
                             Fill="Blue"
                             WidthRequest="8"
                             HeightRequest="8"
                             HorizontalOptions="Center"
                             VerticalOptions="Center" />
                </Grid>
                <ContentPresenter
                        Grid.Column="1"
                        VerticalOptions="Center"/>
            </Grid>
        </ControlTemplate>
    </Setter>
</Style>
```

Using the above style:

<figure><img src="../../.gitbook/assets/image (2) (1) (1).png" alt=""><figcaption><p>Radio buttons with custom style</p></figcaption></figure>

## Bonus tip: Radio buttons for an Enum

Say you have an Enum like this:

```csharp
enum VehicleType
{
    Car,
    Coach,
    Motorcycle
}
```

This code enumerates its values and displays them in a button group:

```csharp
public VisualNode RadioButtonsEnum<T>() where T : struct, System.Enum
{
    return new VStack
    {
        Enum.GetValues<T>()
            .Select(_=> new RadioButton(_.ToString()))
    };
}
```

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption><p>Radio buttons for an enum type</p></figcaption></figure>
