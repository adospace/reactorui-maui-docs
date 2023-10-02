---
description: This page describe how you handle selction of items in a CollectionView
---

# Selection

MauiReactor provides 2 ways to be notified when the user selects an item:

1. OnSelected\<CollectionView, T> is called when a new item is selected, T is the type of the model bound to the ItemsSource property of the CollectionView
2. &#x20;OnSelectionChanged() reports old and new selections, particularly useful when you have SelectionMode set to Multiple

{% hint style="info" %}
By default selection is disabled, set SelectionMode to either Single or Multiple to enable it
{% endhint %}

In the following sample code we see how selection mode affects the selection of the items of a CollectionView:

```csharp
class MainPageSelectionState
{
    public Person[] Persons { get; set; }

    public ObservableCollection<string> EventMessages = new();

    public MauiControls.SelectionMode SelectionMode { get; set; }
}

class MainPageSelection : Component<MainPageSelectionState>
{
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
        return new ContentPage
        {
            new Grid("Auto,*,*", "*")
            {
                new Picker()
                    .ItemsSource(Enum.GetValues<MauiControls.SelectionMode>().Select(_=>_.ToString()).ToList())
                    .SelectedIndex((int)State.SelectionMode)
                    .OnSelectedIndexChanged(index => SetState(s => s.SelectionMode = (MauiControls.SelectionMode)index)),

                new CollectionView()
                    .ItemsSource(State.Persons, RenderPerson)
                    .SelectionMode(State.SelectionMode)
                    //use either the OnSelectionChanged callback or OnSelected<CollectionView, Person>
                    .OnSelectionChanged(OnSelectionChanged)
                    //.OnSelected<CollectionView, Person>(OnSelectedSinglePerson)
                    .GridRow(1),

                new CollectionView()
                    .ItemsSource(State.EventMessages, message => new Label(message).Margin(4,8))
                    .GridRow(2),
            }
        };
    }

    private void OnSelectedSinglePerson(Person person)
    {
        State.EventMessages.Add($"Selected: {person}");
    }

    private void OnSelectionChanged(object sender, MauiControls.SelectionChangedEventArgs args)
    {
        State.EventMessages.Add($"Previous selection: {string.Join(",", args.PreviousSelection)}");
        State.EventMessages.Add($"Current selection: {string.Join(",", args.CurrentSelection)}");
    }

    private VisualNode RenderPerson(Person person)
    {
        return new VStack(spacing: 5)
        {
            new Label($"{person.FirstName} {person.LastName}"),
            new Label(person.DateOfBirth.ToShortDateString())
                .FontSize(12)
                .TextColor(Colors.Gray)
        }
        .Margin(5,10);
    }
}

```

<figure><img src="../../../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

You can change the selected item using the method SelectedItem() shown below:

```csharp
class MainPageSelection2State
{
    public List<Person> Persons { get; set; }

    public Person SelectedPerson { get; set; }
}

class MainPageSelection2 : Component<MainPageSelection2State>
{
    protected override void OnMounted()
    {
        var person1 = new Person("John", "Doe", new DateTime(1980, 5, 10));
        var person2 = new Person("Jane", "Smith", new DateTime(1990, 6, 20));
        var person3 = new Person("Alice", "Johnson", new DateTime(1985, 7, 30));
        var person4 = new Person("Bob", "Williams", new DateTime(2000, 8, 15));
        var person5 = new Person("Charlie", "Brown", new DateTime(1995, 9, 25));

        State.Persons = new List<Person>(new[] { person1, person2, person3, person4, person5 });
        base.OnMounted();
    }

    public override VisualNode Render()
    {
        return new ContentPage
        {
            new Grid("Auto,*", "*")
            {
                new Grid("*", "*,*,*")
                {
                    new Button("Up")
                        .OnClicked(MoveUp),
                    new Button("Down")
                        .GridColumn(1)
                        .OnClicked(MoveDown),
                    new Button("Reset")
                        .GridColumn (2)
                        .OnClicked(()=>SetState(s => s.SelectedPerson = null)),
                },

                new CollectionView()
                    .ItemsSource(State.Persons, RenderPerson)
                    .SelectedItem(State.SelectedPerson)
                    .SelectionMode(MauiControls.SelectionMode.Single)
                    .OnSelected<CollectionView, Person>(OnSelectedSinglePerson)
                    .GridRow(1),
            }
        };
    }

    private void MoveUp()
    {
        if (State.SelectedPerson == null)
        {
            return;
        }

        var indexOfPerson = State.Persons.IndexOf(State.SelectedPerson);
        if (indexOfPerson > 0)
        {
            indexOfPerson--;
            SetState(s => s.SelectedPerson = State.Persons[indexOfPerson]);
        }
    }

    private void MoveDown()
    {
        if (State.SelectedPerson == null)
        {
            return;
        }

        var indexOfPerson = State.Persons.IndexOf(State.SelectedPerson);
        if (indexOfPerson < State.Persons.Count - 1)
        {
            indexOfPerson++;
            SetState(s => s.SelectedPerson = State.Persons[indexOfPerson]);
        }
    }

    private void OnSelectedSinglePerson(Person person)
    {
        SetState(s => s.SelectedPerson = person);
    }

    private VisualNode RenderPerson(Person person)
    {
        return new VStack(spacing: 5)
        {
            new Label($"{person.FirstName} {person.LastName}"),
            new Label(person.DateOfBirth.ToShortDateString())
                .FontSize(12)
                .TextColor(Colors.Gray)
        }
        .Margin(5,10);
    }
}

```

<figure><img src="../../../.gitbook/assets/image (2).png" alt=""><figcaption><p>Move the selected item up and down</p></figcaption></figure>

Selection background colors can be modified using an XAML resource as shown [here](https://learn.microsoft.com/en-us/dotnet/maui/user-interface/controls/collectionview/selection#change-selected-item-color). You can also modify the render of each item accordnly to the state of the component.

For example:

```csharp
private VisualNode RenderPerson(Person person)
{
    return new VStack(spacing: 5)
    {
        new Label($"{person.FirstName} {person.LastName}"),
        new Label(person.DateOfBirth.ToShortDateString())
            .FontSize(12)
            .TextColor(Colors.Gray)
    }
    .BackgroundColor(State.SelectedPerson == person ? Colors.Green : Colors.White)
    .Padding(5,10);
}
```
