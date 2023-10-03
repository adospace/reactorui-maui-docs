---
description: >-
  This page provides instructions on how scroll to a specific item in the
  collection
---

# Scrolling

> The .NET Multi-platform App UI (.NET MAUI) [CollectionView](https://learn.microsoft.com/en-us/dotnet/api/microsoft.maui.controls.collectionview) defines two `ScrollTo` methods, that scroll items into view. One of the overloads scrolls the item at the specified index into view, while the other scrolls the specified item into view. Both overloads have additional arguments that can be specified to indicate the group the item belongs to, the exact position of the item after the scroll has completed, and whether to animate the scroll.

In MauiReactor, you need a reference to the native `CollectionView` and call the `ScrollTo` method on it.

The following sample code shows how to scroll to a specific position index:

```csharp
public record IndexedPersonWithAddress(int Index, string FirstName, string LastName, int Age, string Address);

class MainPageScrollingState
{
    public List<IndexedPersonWithAddress> Persons { get; set; }

    public MauiControls.ItemsViewScrolledEventArgs LatestEventArgs { get; set; }

    public int ScrollToIndex {  get; set; }

    public MauiControls.ScrollToPosition ScrollToPosition { get; set; }

    public bool IsAnimationDisabled { get; set; }
}

class MainPageScrolling : Component<MainPageScrollingState>
{
    private MauiControls.CollectionView _collectionViewRef;

    private static List<IndexedPersonWithAddress> GenerateSamplePersons(int count)
    {
        var random = new Random();
        var firstNames = new[] { "John", "Jim", "Joe", "Jack", "Jane", "Jill", "Jerry", "Jude", "Julia", "Jenny" };
        var lastNames = new[] { "Brown", "Green", "Black", "White", "Blue", "Red", "Gray", "Smith", "Doe", "Jones" };
        var cities = new[] { "New York", "London", "Sidney", "Tokyo", "Paris", "Berlin", "Mumbai", "Beijing", "Cairo", "Rio" };

        var persons = new List<IndexedPersonWithAddress>();

        for (int i = 0; i < count; i++)
        {
            var firstName = firstNames[random.Next(0, firstNames.Length)];
            var lastName = lastNames[random.Next(0, lastNames.Length)];
            var age = random.Next(20, 60);
            var city = cities[random.Next(0, cities.Length)];
            var address = $"{city} No. {random.Next(1, 11)} Lake Park";

            persons.Add(new IndexedPersonWithAddress(i, firstName, lastName, age, address));
        }

        return persons;
    }

    protected override void OnMounted()
    {
        State.Persons = GenerateSamplePersons(100);
        base.OnMounted();
    }

    public override VisualNode Render()
    {
        return new ContentPage
        {
            new Grid("Auto, Auto,*", "*")
            {
                new VStack(spacing: 5)
                {
                    new Label($"CenterItemIndex: {State.LatestEventArgs?.CenterItemIndex}"),
                    new Label($"LastVisibleItemIndex: {State.LatestEventArgs?.LastVisibleItemIndex}"),
                    new Label($"FirstVisibleItemIndex: {State.LatestEventArgs?.FirstVisibleItemIndex}"),
                    new Label($"VerticalDelta: {State.LatestEventArgs?.VerticalDelta}"),
                    new Label($"VerticalOffset: {State.LatestEventArgs?.VerticalOffset}"),
                },

                new VStack(spacing: 5)
                {
                    new Entry()
                        .Keyboard(Keyboard.Numeric)
                        .OnTextChanged(_ =>
                        {
                            if (int.TryParse(_, out var scrollToIndex) &&
                                scrollToIndex >= 0 &&
                                scrollToIndex < State.Persons.Count)
                            {
                                SetState(s => s.ScrollToIndex = scrollToIndex);
                            }
                        }),

                    new Picker()
                        .ItemsSource(Enum.GetValues<MauiControls.ScrollToPosition>().Select(_=>_.ToString()).ToArray())
                        .OnSelectedIndexChanged(index => SetState(s => s.ScrollToPosition = (MauiControls.ScrollToPosition)index)),                        

                    new HStack(spacing: 5)
                    {
                        new CheckBox()
                            .OnCheckedChanged((sender, args) => SetState(s => s.IsAnimationDisabled = args.Value)),

                        new Label("Disable animation")
                            .HFill()
                            .VCenter(),
                    },

                    new Button("Scroll To")
                        .OnClicked(()=> _collectionViewRef?.ScrollTo(State.ScrollToIndex, position: State.ScrollToPosition, animate: !State.IsAnimationDisabled))
                }
                .GridRow(1),

                new CollectionView(collectionViewRef => _collectionViewRef = collectionViewRef)
                    .ItemsSource(State.Persons, RenderPerson)
                    .GridRow(2)
                    .OnScrolled(OnScrolled)
            }
        };
    }

    private void OnScrolled(object sender, MauiControls.ItemsViewScrolledEventArgs args)
    {
        SetState(s => s.LatestEventArgs = args);
    }

    private VisualNode RenderPerson(IndexedPersonWithAddress person)
    {
        return new VStack(spacing: 5)
        {
            new Label($"{person.Index}. {person.FirstName} {person.LastName} ({person.Age})"),
            new Label(person.Address)
                .FontSize(12)
        }
        .HeightRequest(56)
        .VCenter();
    }
}

```

<figure><img src="../../../.gitbook/assets/image (1) (1).png" alt=""><figcaption><p>Scrolling collection view to a specific position index</p></figcaption></figure>

The above sample could be easily modified to scroll to a specific item as well.
