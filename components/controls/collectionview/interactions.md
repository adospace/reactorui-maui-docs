---
description: This page describes how to handle user interaction of the CollectionView
---

# Interactions

## Context menus

Use the SwipeView to define custom commands for each item. Below it's shown an example:

{% hint style="info" %}
In the following example, the `CollectionView` is bound to an `ObservableCollection` so that when an item is removed from the list the control automatically refreshes without reloading the entire list.\
Using a simple `List` or `Array` it would be necessary to update the entire list using a call to `SetState()`
{% endhint %}

```csharp
class MainPageSwipeState
{
    public ObservableCollection<Person> Persons { get; set; }
}

class MainPageSwipe : Component<MainPageSwipeState>
{
    protected override void OnMounted()
    {
        var person1 = new Person("John", "Doe", new DateTime(1980, 5, 10));
        var person2 = new Person("Jane", "Smith", new DateTime(1990, 6, 20));
        var person3 = new Person("Alice", "Johnson", new DateTime(1985, 7, 30));
        var person4 = new Person("Bob", "Williams", new DateTime(2000, 8, 15));
        var person5 = new Person("Charlie", "Brown", new DateTime(1995, 9, 25));

        State.Persons = new ObservableCollection<Person>(new[] { person1, person2, person3, person4, person5 });
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
        return SwipeView(
            VStack(spacing: 5,
                Label($"{person.FirstName} {person.LastName}"),
                Label(person.DateOfBirth.ToShortDateString())
                    .FontSize(12)
            )
            .VCenter()
        )
        .LeftItems(new SwipeItems
        {
            new SwipeItem()
                .IconImageSource("archive.png")
                .Text("Archive")
                .BackgroundColor(Colors.Green),
            new SwipeItem()
                .IconImageSource("delete.png")
                .Text("Delete")
                .BackgroundColor(Colors.Red)
                .OnInvoked(()=>State.Persons.Remove(person))
        })
        .HeightRequest(60);
    }
}
```

<figure><img src="../../../.gitbook/assets/image (10).png" alt=""><figcaption><p>CollectionView with context commands</p></figcaption></figure>

## Pull to refresh

The "pull to refresh" feature allows you to execute a command when the user pulls down the collection view control to refresh the list.

{% hint style="info" %}
Please note again that we're using an ObservableCollection instead of a List or Array so that control can subscribe to collection change events using the System.Collections.Specialized.INotifyCollectionChanged interface and update itself accordingly and efficiently
{% endhint %}

For example:

```csharp
class MainPagePullToRefreshState
{
    public ObservableCollection<Person> Persons { get; set; }
    
    public bool IsRefreshing {  get; set; }
}

class MainPagePullToRefresh : Component<MainPagePullToRefreshState>
{
    protected override void OnMounted()
    {
        var person1 = new Person("John", "Doe", new DateTime(1980, 5, 10));
        var person2 = new Person("Jane", "Smith", new DateTime(1990, 6, 20));
        var person3 = new Person("Alice", "Johnson", new DateTime(1985, 7, 30));
        var person4 = new Person("Bob", "Williams", new DateTime(2000, 8, 15));
        var person5 = new Person("Charlie", "Brown", new DateTime(1995, 9, 25));

        State.Persons = new ObservableCollection<Person>(new[] { person1, person2, person3, person4, person5 });
        base.OnMounted();
    }

    public override VisualNode Render()
    {
        return new ContentPage(
            RefreshView(
                CollectionView()
                    .ItemsSource(State.Persons, RenderPerson)
            )
            .IsRefreshing(State.IsRefreshing)
            .OnRefreshing(OnRefresh)
        );
    }

    private void OnRefresh()
    {
        SetState(s => s.IsRefreshing = true);

        Task.Run(async () =>
        {
            await Task.Delay(2000);

            var person6 = new Person("Daniel", "Robinson", new DateTime(1982, 10, 2));
            var person7 = new Person("Ella", "Martin", new DateTime(1992, 11, 13));
            var person8 = new Person("Frank", "Garcia", new DateTime(1987, 3, 19));

            State.Persons.Insert(0, person6);
            State.Persons.Insert(0, person7);
            State.Persons.Insert(0, person8);

            SetState(s => s.IsRefreshing = false);
        });
    }

    private VisualNode RenderPerson(Person person)
    {
        return new VStack(spacing: 5)
        {
            new Label($"{person.FirstName} {person.LastName}"),
            new Label(person.DateOfBirth.ToShortDateString())
                .FontSize(12)
        }
        .VCenter();
    }
}

```

<figure><img src="../../../.gitbook/assets/image (11).png" alt=""><figcaption><p>Pull to refresh in action</p></figcaption></figure>

## Load data incrementally

The below example shows how you can load data incrementally as the user scrolls down the list:

```csharp
public record PersonWithAddress(string FirstName, string LastName, int Age, string Address);

class MainPageLoadDataIncrementallyState
{
    public ObservableCollection<PersonWithAddress> Persons { get; set; }
    
    public bool IsLoading {  get; set; }
}

class MainPageLoadDataIncrementally : Component<MainPageLoadDataIncrementallyState>
{
    private static IEnumerable<PersonWithAddress> GenerateSamplePersons(int count)
    {
        var random = new Random();
        var firstNames = new[] { "John", "Jim", "Joe", "Jack", "Jane", "Jill", "Jerry", "Jude", "Julia", "Jenny" };
        var lastNames = new[] { "Brown", "Green", "Black", "White", "Blue", "Red", "Gray", "Smith", "Doe", "Jones" };
        var cities = new[] { "New York", "London", "Sidney", "Tokyo", "Paris", "Berlin", "Mumbai", "Beijing", "Cairo", "Rio" };

        var persons = new List<PersonWithAddress>();

        for (int i = 0; i < count; i++)
        {
            var firstName = firstNames[random.Next(0, firstNames.Length)];
            var lastName = lastNames[random.Next(0, lastNames.Length)];
            var age = random.Next(20, 60);
            var city = cities[random.Next(0, cities.Length)];
            var address = $"{city} No. {random.Next(1, 11)} Lake Park";

            persons.Add(new PersonWithAddress(firstName, lastName, age, address));
        }

        return persons;
    }

    protected override void OnMounted()
    {
        State.Persons = new ObservableCollection<PersonWithAddress>(GenerateSamplePersons(100));
        base.OnMounted();
    }

    public override VisualNode Render()
    {
        return new ContentPage
        {
            new Grid("*", "*")
            {
                new CollectionView()
                    .ItemsSource(State.Persons, RenderPerson)
                    .RemainingItemsThreshold(50)
                    .OnRemainingItemsThresholdReached(LoadMorePersons),

                new ActivityIndicator()
                    .IsRunning(State.IsLoading)
                    .VCenter()
                    .HCenter()
            }
        };
    }

    private void LoadMorePersons()
    {
        if (State.IsLoading == true)
        {
            return;
        }

        SetState(s => s.IsLoading = true);

        Task.Run(async () =>
        {
            await Task.Delay(3000);

            foreach (var newPerson in GenerateSamplePersons(50))
            {
                //is not required here to call set state because we don't want to refresh the entire component
                //the collection view is bound to State.Persons
                State.Persons.Add(newPerson);
            }

            SetState(s => s.IsLoading = false);
        });
    }

    private VisualNode RenderPerson(PersonWithAddress person)
    {
        return new VStack(spacing: 5)
        {
            new Label($"{person.FirstName} {person.LastName} ({person.Age})"),
            new Label(person.Address)
                .FontSize(12)
        }
        .HeightRequest(56)
        .VCenter();
    }
}
```

{% hint style="warning" %}
You may be tempted to use code like SetState(s => s.Persons.Add(person)) but, in this specific case, it's not required: the CollectionView is bound to the Persons ObservableCollection and it's automatically refreshed at every change of the collection without requiring the component refresh.

Please note that, in all other cases (i.e. excluding ObservableCollection properties) you must update the state using the SetState() method when you need to update the view.
{% endhint %}
