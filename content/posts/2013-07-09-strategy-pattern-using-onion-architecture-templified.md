---
title: "Strategy Pattern using Onion Architecture Templified"
date: 2013-07-09
tags: ["Onion Architecture Templified", ".NET", "strategy pattern", "design patterns"]
slug: "strategy-pattern-using-onion-architecture-templified"
description: "*The following article was drafted 2013/07/09 shortly after a presentation I had made at the Orlando .NET Users Group. Unfortunately, I neglected to hit the..."
aliases:
  - "/2013/07/strategy-pattern-using-onion.html"
---

*The following article was drafted 2013/07/09 shortly after a presentation I had made at the Orlando .NET Users Group. Unfortunately, I neglected to hit the Post button and left it to collect dust without being published until now. Apologies for anyone who was looking forward to this article.*  

  

[Download demo code](https://dl.dropboxusercontent.com/u/2241127/CodeCampDemo.7z)  

  

[On 2013/07/09] I presented the [strategy pattern](http://en.wikipedia.org/wiki/Strategy_pattern) at the [Orlando .NET Users Group](http://onetug.org/Home.aspx) monthly meeting. The gist of it is that if you find yourself starting to write a switch statement or a cascading series of if/else statements, you need to stop for a moment and ask this question: "Will I be having to revisit this someday? If so, how often?" Your answer will dictate if you should proceed merrily along or implement the [strategy pattern](http://en.wikipedia.org/wiki/Strategy_pattern).
  

I had the opportunity to ask myself this very question recently when I was required to write a set of web scrapers to get price and availability information off of various retailers' product pages. Some retailers implement the [schema.org](http://schema.org/) standard for encoding this product data and others use a proprietary encoding. When my process gets the work item, it includes the URL and the encoding type so I started writing the if/else statement to handle this. Then I re-read the spec and noticed that other schemas were coming down the pike so I started writing a switch statement to handle the ones listed. It was at that moment when I realized that I was introducing a maintenance headache and knew the strategy pattern was the solution.
  

So, why did I decide that? After all, the strategy pattern doesn't prevent the need for maintenance. What it does very well, though, is moves the maintenance out of the business or (*shudder*) UI logic, the logic which isn't likely to change often, and promotes it to its own set of classes which actually do the work. The business logic simply asks a context object for whichever class will fulfill the need of the piece of work being handled.
  

Let me demonstrate using a trivial example for simplicity. I have a web page for doing product searches. On that page, I allow the user to decide if they want to use Bing or Google. When they hit the Search button, the `PerformSearch` action is called.
  

```
private readonly IShoppingSearchAPI _shoppingSearch;

[HttpPost]
public ActionResult PerformSearch(QueryModel queryModel)
{
    _shoppingSearch.Context = queryModel.SearchEngine;
    var searchResults = _shoppingSearch.Search(queryModel.Query, 10);
    var content = "";
    content += "";

    foreach (var product in searchResults)
    {
        var inventoryData = product.InventoriesData != null ? product.InventoriesData.First() : new InventoryData();
        var buyUrl = product.Link;
        content += string.Format("", 
            product.Brand, product.Title, product.Description, inventoryData.Price, inventoryData.Shipping, inventoryData.Tax, buyUrl);
    }

    content += "...creates a table...";

    return Content(content, "text/html");
}

```

  

Key takeaways here:  

- The code here knows nothing about how many search engines there are to choose from.
- The code doesn't make any decisions about the search engines.
- The code does not need to be maintained when a new search engine is added.

Now, I want to make it clear that this is a contrived example. Only two choices exist and I doubt you'd be adding new search engines to a web page for the user to pick at runtime. However, this clearly demonstrates that such runtime choices needn't be handled in the UI or business logic. Rather, it can be pushed to the outside ring of the [Onion Architecture](https://github.com/grimlor/OnionArchitecture) so that maintenance can be managed much more easily.

  

So, if the code isn't in the UI or business "layers" (Core in my unlayered architecture, name TBD but currently still [Onion Architecture Templified](https://github.com/grimlor/OnionArchitecture)) then where does it live? It lives where all code expecting to change often lives, in Infrastructure with an interface in Core. Here's the interface:
  

```
public interface IShoppingSearchAPI
{
    SearchEngine Context { get; set; }
    Image Logo { get; set; }
    IEnumerable<Product> Search(string query, int maxResults);
}

```

  

And here is an implementation for Google:

  

```
public class GoogleShoppingSearchAPI : IShoppingSearchAPI
{
    public SearchEngine Context { get; set; }

    private Image _logo;
    public Image Logo
    {
        get { return _logo ?? (_logo = Image.FromFile(@"%HOMEDRIVE%%HOMEPATH%\Documents\Visual Studio 2012\Projects\CodeCampDemo\Infrastructure\Services\Google\logo4w.png")); } 
        set { _logo = value; }
    }

    public IEnumerable<Product> Search(string query, int maxResults)
    {
        var products = new List<Product>();

        var service = new ShoppingService { Key = "AIzaSyBcWCofOByUzp-EekjUCMxa30h-D9U95eE" };
        var request = service.Products.List("public");
        request.Country = "us";
        request.Q = query;

        var startIndex = 1;
        while (products.Count < maxResults)
        {
            request.StartIndex = startIndex;
            var response = request.Fetch();
            if (response.CurrentItemCount == 0)
            {
                break;
            }

            foreach (var item in response.Items)
            {
                var images = item.ProductValue.Images;
                var image = string.Empty;
                if (images != null)
                {
                    image = images[0].Link;
                }
                var product = new Product
                {
                    Id = item.Id,
                    Brand = item.ProductValue.Brand,
                    Author = item.ProductValue.Author.Name,
                    Title = item.ProductValue.Title,
                    Description = item.ProductValue.Description,
                    Image = image,
                    Link = item.ProductValue.Link,
                    Gtins = item.ProductValue.Gtins
                };

                if (item.ProductValue.Inventories != null)
                {
                    product.InventoriesData = new List<InventoryData>();
                    foreach (var inventoryData in item.ProductValue.Inventories)
                    {
                        var inventory = new InventoryData
                        {
                            Price = inventoryData.Price,
                            Tax = inventoryData.Tax,
                            Shipping = inventoryData.Shipping,
                            Availability = inventoryData.Availability
                        };
                        product.InventoriesData.Add(inventory);
                    }
                }

                products.Add(product);

                if (products.Count == maxResults)
                {
                    break;
                }
            }
            startIndex++;
        }

        return products;
    }
}

```

  

Finally, here is a typical context implementation, the place where your switch statement lives:

  

```
public class SearchEngineContext : IShoppingSearchAPI
{
    public SearchEngine Context { get; set; }

    public Image Logo { 
        get { throw new NotImplementedException(); }
        set { throw new NotImplementedException(); }
    }

    public IEnumerable<Product> Search(string query, int maxResults)
    {
        IShoppingSearchAPI searchEngineInstance;

        switch(Context)
        {
            case SearchEngine.Bing:
                searchEngineInstance = new BingShoppingSearchAPI();
                break;
            case SearchEngine.Google:
            default:
                searchEngineInstance = new GoogleShoppingSearchAPI();
                break;
        }

        return searchEngineInstance.Search(query, maxResults);
    }
}

```

  

This implementation of the context likely differs from the examples you'll find on the 'net due to the fact that it implements the same interface as the classes it serves. This simplifies execution by making it unnecessary to ask for an instance before executing the action, doing so immediately instead. I use this improvement in the [sample code](https://dl.dropboxusercontent.com/u/2241127/CodeCampDemo.7z) to leverage .NET reflection features so that I don't even have to maintain the switch statement. Be sure to [download the sample code](https://dl.dropboxusercontent.com/u/2241127/CodeCampDemo.7z) to see how that was done.
  

  

So, the next time you find yourself creating a lengthy switch statement or a list of cascading if/else statements, ask yourself if you'll ever have to modify that code. If the answer is yes, then consider spending a little time implementing the strategy pattern and, if you want maximum flexibility, take a look at [the demo code](https://dl.dropboxusercontent.com/u/2241127/CodeCampDemo.7z) for how to do so in such a way that you could just add new classes to your infrastructure code and nothing more for new, related functionality to automagically appear.  

  

*Note: Technically, the demo also requires an Enum to be updated in Core but it would be a simple matter to use a string rather than an Enum to enable the same functionality.*
