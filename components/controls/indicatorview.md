---
description: This page describes how to implement an IndicatorView
---

# IndicatorView

> The .NET Multi-platform App UI (.NET MAUI) [IndicatorView](https://learn.microsoft.com/en-us/dotnet/api/microsoft.maui.controls.indicatorview) is a control that displays indicators that represent the number of items, and current position, in a [CarouselView](https://learn.microsoft.com/en-us/dotnet/api/microsoft.maui.controls.carouselview).

Official documentation:\
[https://learn.microsoft.com/en-us/dotnet/maui/user-interface/controls/indicatorview](https://learn.microsoft.com/en-us/dotnet/maui/user-interface/controls/indicatorview)\
\
MauiReactor sample app:\
[https://github.com/adospace/mauireactor-samples/tree/main/Controls/IndicatorViewTestApp](https://github.com/adospace/mauireactor-samples/tree/main/Controls/IndicatorViewTestApp)

The sample code below shows how to implement the IndicatorView control in MauiReactor:

```csharp
class MainPageState
{
    public Animal[] Animals { get; set; }
}

class MainPage : Component<MainPageState>
{
    protected override void OnMounted()
    {
        State.Animals = new Animal[] 
        {
            new Animal
            {
                Name = "American Black Bear",
                Location = "North America",
                Details = "The American black bear is a medium-sized bear native to North America. It is the continent's smallest and most widely distributed bear species. American black bears are omnivores, with their diets varying greatly depending on season and location. They typically live in largely forested areas, but do leave forests in search of food. Sometimes they become attracted to human communities because of the immediate availability of food. The American black bear is the world's most common bear species.",
                ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/0/08/01_Schwarzbär.jpg"
            },
            new Animal
            {
                Name = "Asian Black Bear",
                Location = "Asia",
                Details = "The Asian black bear, also known as the moon bear and the white-chested bear, is a medium-sized bear species native to Asia and largely adapted to arboreal life. It lives in the Himalayas, in the northern parts of the Indian subcontinent, Korea, northeastern China, the Russian Far East, the Honshū and Shikoku islands of Japan, and Taiwan. It is classified as vulnerable by the International Union for Conservation of Nature (IUCN), mostly because of deforestation and hunting for its body parts.",
                ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/b/b7/Ursus_thibetanus_3_%28Wroclaw_zoo%29.JPG/180px-Ursus_thibetanus_3_%28Wroclaw_zoo%29.JPG"
            },
            new Animal
            {
                Name = "Brown Bear",
                Location = "Northern Eurasia & North America",
                Details = "The brown bear is a bear that is found across much of northern Eurasia and North America. In North America the population of brown bears are often called grizzly bears. It is one of the largest living terrestrial members of the order Carnivora, rivaled in size only by its closest relative, the polar bear, which is much less variable in size and slightly larger on average. The brown bear's principal range includes parts of Russia, Central Asia, China, Canada, the United States, Scandinavia and the Carpathian region, especially Romania, Anatolia and the Caucasus. The brown bear is recognized as a national and state animal in several European countries.",
                ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/5/5d/Kamchatka_Brown_Bear_near_Dvuhyurtochnoe_on_2015-07-23.jpg/320px-Kamchatka_Brown_Bear_near_Dvuhyurtochnoe_on_2015-07-23.jpg"
            },
            new Animal
            {
                Name = "Grizzly-Polar Bear Hybrid",
                Location = "Canadian Artic",
                Details = "A grizzly–polar bear hybrid is a rare ursid hybrid that has occurred both in captivity and in the wild. In 2006, the occurrence of this hybrid in nature was confirmed by testing the DNA of a unique-looking bear that had been shot near Sachs Harbour, Northwest Territories on Banks Island in the Canadian Arctic. The number of confirmed hybrids has since risen to eight, all of them descending from the same female polar bear.",
                ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/7/7e/Grolar.JPG/276px-Grolar.JPG"
            },
        };

        base.OnMounted();
    }


    public override VisualNode Render()
    {
        return new ContentPage
        {
            new IndicatorView()
                .ItemsSource(State.Animals)
                .IndicatorColor(Colors.Red)
                .IndicatorSize(14)
                .IndicatorsShape(MauiControls.IndicatorShape.Circle)
                .VCenter()
                .HCenter()
        };
    }
}

```

{% hint style="info" %}
The IndicatorView control is almost always used in conjunction with the CarouselView to indicate its current view
{% endhint %}

## Custom IndicatorView

If you need more control over the indicator items, consider an alternative approach as shown below:

```csharp
public class TestModel
{
    public string Title { get; set; }
    public string SubTitle { get; set; }
    public string Description { get; set; }
}

class MainPageCustomState
{
    public List<TestModel> Models { get; set; } = new();

    public TestModel SelectedModel { get; set; }

}

class MainPageCustom : Component<MainPageCustomState>
{
    private MauiControls.CarouselView _carouselView;

    protected override void OnMounted()
    {
        State.Models = new List<TestModel>()
        {
            new TestModel
            {
                Title = "Lorem Ipsum Dolor Sit Amet",
                SubTitle = "Consectetur Adipiscing Elit",
                Description = "Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed in libero non quam accumsan sodales vel vel nunc."
            },
            new TestModel
            {
                Title = "Praesent Euismod Tristique",
                SubTitle = "Vestibulum Fringilla Egestas",
                Description = "Praesent euismod tristique vestibulum. Vivamus vestibulum justo in massa aliquam, id facilisis odio feugiat. Duis euismod massa id elit imperdiet."
            },
            new TestModel
            {
                Title = "Aliquam Erat Volutpat",
                SubTitle = "Aenean Feugiat In Mollis",
                Description = "Aliquam erat volutpat. Aenean feugiat in mollis ac. Nullam eget justo ut orci dictum auctor."
            },
            new TestModel
            {
                Title = "Suspendisse Tincidunt",
                SubTitle = "Faucibus Ligula Quis",
                Description = "Suspendisse tincidunt, arcu eget auctor efficitur, nulla justo tristique neque, et fermentum orci ante eget nunc."
            },
        };

        State.SelectedModel = State.Models.First();

        base.OnMounted();
    }

    public override VisualNode Render()
    {
        return new ContentPage
        {
            new VStack
            {
                new Grid("* 40", "*")
                {
                    new CarouselView(r => _carouselView = r)
                        .HeightRequest(450)
                        .HorizontalScrollBarVisibility(ScrollBarVisibility.Never)
                        .ItemsSource(State.Models, _=> new Grid("*", "*")
                        {
                            new VStack
                            {
                                  new Label(_.Title)
                                    .Margin(0,5),
                                  new Label(_.SubTitle)
                                    .Margin(0,5),
                                 new Label(_.Description)
                                    .Margin(0,8)
                            }
                        })
                        .CurrentItem(() => State.SelectedModel!)
                        .OnCurrentItemChanged((s, args) => SetState(s => s.SelectedModel = (TestModel)args.CurrentItem))
                        ,

                    new HStack(spacing: 5)
                    {
                        State.Models.Select(item =>
                            new Image(State.SelectedModel == item ? "sun.png" : "circle.png")
                                .WidthRequest(20)
                                .HeightRequest(20)
                                .OnTapped(()=>SetState(s=>s.SelectedModel = item, false))
                            )
                    }
                    .HCenter()
                    .VCenter()
                    .GridRow(1)
                }
            }
            .Padding(8,0)
            .VCenter()
        };
    }
}
```

<figure><img src="../../.gitbook/assets/image (1) (1).png" alt=""><figcaption><p>Custom indicator view sample</p></figcaption></figure>
