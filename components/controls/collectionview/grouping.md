---
description: This page shows how to bind a grouped list of items to a CollectionView
---

# Grouping

> Large data sets can often become unwieldy when presented in a continually scrolling list. In this scenario, organizing the data into groups can improve the user experience by making it easier to navigate the data.

In MauiReactor you can create a grouped view of your collection using an overload of the ItemsSource prop method as shown in the example below:

```csharp
public class Animal
{
    public string Name { get; set; }
    public string Location { get; set; }
    public string Details { get; set; }
    public string ImageUrl { get; set; }

    public override string ToString()
    {
        return Name;
    }
}

public class AnimalGroup : List<Animal>
{
    public string Name { get; private set; }

    public AnimalGroup(string name, List<Animal> animals) : base(animals)
    {
        Name = name;
    }

    public override string ToString()
    {
        return Name;
    }
}

class MainPageGroupingState
{
    public IEnumerable<AnimalGroup> Animals { get; set; }
}

class MainPageGrouping : Component<MainPageGroupingState>
{
    protected override void OnMounted()
    {
        State.Animals = CreateAnimalsCollection(false);

        base.OnMounted();
    }

    public override VisualNode Render()
    {
        return new ContentPage
        {
            new CollectionView()
                .IsGrouped(true)
                .ItemsSource<CollectionView, AnimalGroup, Animal>(State.Animals, RenderPerson, RenderHeader, RenderFooter)
        };
    }
    private VisualNode RenderPerson(Animal animal)
    {
        return new Grid("*,*", "64, *")
        {
            new Image(animal.ImageUrl)
                .GridRowSpan(2),

            new Label(animal.Name)
                .GridColumn(1),

            new Label(animal.Location)
                .GridRow(1)
                .GridColumn(1)
                .FontSize(12)
                .TextColor(Colors.Gray)
        }
        .HeightRequest(68)
        .Padding(4);
    }

    private VisualNode RenderHeader(AnimalGroup group)
    {
        return new Label(group.Name)
            .Padding(5)
            .FontSize(40)
            .BackgroundColor(Colors.Gray)
            .TextColor(Colors.White);
    }

    private VisualNode RenderFooter(AnimalGroup group)
    {
        return new Label($"Animals in the group: {group.Count}")
            .Padding(5);
    }


    private static IEnumerable<AnimalGroup> CreateAnimalsCollection(bool includeEmptyGroups)
    {
        ...

        return animals;
    }

}

```

<figure><img src="../../../.gitbook/assets/image (1).png" alt=""><figcaption><p>A CollectionView with group headers and footers</p></figcaption></figure>
