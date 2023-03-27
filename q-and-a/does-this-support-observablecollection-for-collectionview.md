# Does this support ObservableCollection for CollectionView?

MauiReactor wraps standard .NET MAUI controls, so everything works exactly in the same way it behaves in the .NET MAUI framework.

The CollectionView control doesn't make exceptions. The only difference is that you should not create a Binding between the CollectionView ItemsSource property and the underlying collection of models.

## Read-Only CollectionView

When you're dealing with read-only collections of items to render or you are going to just replace the entire collection during the lifetime of the application you can just connect the CollectionView ItemsSource property providing a Render function used by MauiReactor to actually render each item.

For example, in this code the CollectionView renders its items as labels:

<pre class="language-csharp" data-line-numbers><code class="lang-csharp">class CollectionViewPage: Component
{
    static CollectionViewPage()
    {
        ItemsSource = Enumerable.Range(1, 200)
            .Select(_ => new Tuple&#x3C;string, string>($"Item{_}", $"Details{_}"))
            .ToArray();
    }
    
    static Tuple&#x3C;string, string>[] ItemsSource { get; }

    public override VisualNode Render()
    {
        return new ContentPage("CollectionView")
        {
            new CollectionView()
<strong>                .ItemsSource(ItemsSource, RenderItem)
</strong>        };
    }

    private VisualNode RenderItem(Tuple&#x3C;string, string> item) 
        => new VStack(spacing: 5)
        {
            new Label(item.Item1),
            new Label(item.Item2)
        };
}
</code></pre>

More often, the list of items is stored inside the State of the component and read/updated by a call to an external service (like a rest API).

{% hint style="info" %}
The Render method passed to the ItemsSource property (row 17 above) is used by MauiReactor to create a DataTemplate used by the native CollectionView to render its items. The DataTemplate is cached and reused by MauiReactor every time is requested by the native control.
{% endhint %}

## Read-Write CollectionView

In case you are going to modify the collection of items without just resetting it, for example, adding/removing single or multiple items at runtime, you can opt for the ObservableCollection type.

For example, this code lets the user add/remove items from the underlying collection and the CollectionView control updates accordnly:

```csharp
class EditableCollectionViewState
{
    public ObservableCollection<string> Items { get; set; } = new();
}

class EditableCollectionView: Component<EditableCollectionViewState>
{
    static readonly Random _random = new();
    public override VisualNode Render()
    {
        return new ContentPage("CollectionView")
        {
            new Grid("48, 44, *", "*, *")
            {
                new Button("Add Item")
                    .OnClicked(()=>SetState(s => s.Items.Insert(_random.Next(0, s.Items.Count), $"Item {DateTime.Now}"), invalidateComponent: false)),

                new Button("Remove Item")
                    .OnClicked(()=>SetState(s => s.Items.RemoveAt(_random.Next(0, s.Items.Count - 1)), invalidateComponent: false))
                    .GridColumn(1)
                    .IsEnabled(()=>State.Items.Count > 0),

                new Label()
                    .Text(()=> $"{State.Items.Count} Items")
                    .GridRow(1)
                    .GridColumnSpan(2)
                    .HCenter()
                    .VCenter(),


                new CollectionView()
                    .ItemsSource(State.Items, _ => new Label(_))
                    .GridRow(2)
            }
        };
    }
}
```

{% hint style="warning" %}
It's not required (actually it's discouraged) to get a reference to the native CollectionView and forcibly set its ItemsSource property.
{% endhint %}
