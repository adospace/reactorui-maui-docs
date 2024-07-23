---
description: This page describes how to create a CollectionView in MauiReactor
---

# CollectionView

> The .NET Multi-platform App UI (.NET MAUI) [CollectionView](https://learn.microsoft.com/en-us/dotnet/api/microsoft.maui.controls.collectionview) is a view for presenting lists of data using different layout specifications. It aims to provide a more flexible, and performant alternative to [ListView](https://learn.microsoft.com/en-us/dotnet/api/microsoft.maui.controls.listview).

Official documentation:\
[https://learn.microsoft.com/en-us/dotnet/maui/user-interface/controls/collectionview/](https://learn.microsoft.com/en-us/dotnet/maui/user-interface/controls/collectionview/)\
\
MauiReactor sample app:\
[https://github.com/adospace/mauireactor-samples/tree/main/Controls/CollectionViewTestApp](https://github.com/adospace/mauireactor-samples/tree/main/Controls/CollectionViewTestApp)

## Display data

Use the method `ItemsSource()` to link a collection of items and to define how each item is displayed.

{% hint style="info" %}
Bind the CollectionView to an IEnumerable (i.e. List or Array) you do not need to modify the collection (read-only collections). \
If you want to update the list, adding or removing items, use an ObservableCollection instead.
{% endhint %}

For example:&#x20;

```csharp
protected override void OnMounted()
{
    var person1 = new Person("John", "Doe", new DateTime(1980, 5, 10));
    var person2 = new Person("Jane", "Smith", new DateTime(1990, 6, 20));
    var person3 = new Person("Alice", "Johnson", new DateTime(1985, 7, 30));
    var person4 = new Person("Bob", "Williams", new DateTime(2000, 8, 15));
    var person5 = new Person("Charlie", "Brown", new DateTime(1995, 9, 25));

    State.Persons = new [] { person1, person2, person3, person4, person5 };
    base.OnMounted();
}

public override VisualNode Render()
{
    return ContentPage(
        CollectionView()
            .ItemsSource(State.Persons, RenderPerson)
    );
}

private VisualNode RenderPerson(Person person)
{
    return VStack(spacing: 5,
        Label($"{person.FirstName} {person.LastName}"),
        Label(person.DateOfBirth.ToShortDateString())
            .FontSize(12)
            .TextColor(Colors.Gray)
    )
    .Margin(5,10);
}
```

<figure><img src="../../../.gitbook/assets/image (9).png" alt=""><figcaption><p>CollectionView showing some data</p></figcaption></figure>

