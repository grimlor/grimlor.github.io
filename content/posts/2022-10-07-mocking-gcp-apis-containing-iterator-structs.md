---
title: "Mocking GCP APIs containing iterator structs"
date: 2022-10-07
slug: "mocking-gcp-apis-containing-iterator-structs"
tags: ["Go", "GCP", "testing", "mocking"]
description: "The astute amongst my readers may have noticed that I left Lightbend to go work with some friends who I'd built things with previously at another company...."
aliases:
  - "/2022/10/mocking-gcp-apis-containing-iterator.html"
---

## New company & language, who dis?

The astute amongst my readers may have noticed that I left Lightbend to go work with some friends who I'd built things with previously at another company. This new company presents some unique challenges, including another language to put under my belt. The new language is [Go, also referred to as Golang](https://go.dev/), and it has some interesting claims. I'll inspect those claims here on my blog as I become comfortable, or overly uncomfortable, with the language.

One of those claims that made me uncomfortable is that the consumer should define interfaces rather than the API. I recognize the reason for this thinking, but, in my opinion, it is making up for the laziness or ignorance of API developers. Too many don't practice [the SOLID principles](https://www.freecodecamp.org/news/solid-principles-explained-in-plain-english/), including coding to interfaces, not implementations, so the consumer takes up the burden of making their tests mockable. The easiest way to demonstrate the pain that causes is to show you the Go code I had to write to deal with this fact when mocking [Google Cloud Platform's Compute Engine APIs](https://cloud.google.com/go/docs/reference/cloud.google.com/go/compute/latest/apiv1#cloud_google_com_go_compute_apiv1_ZonesClient_List) containing iterators.

## What's the problem?

If I'm just writing code to use Google's APIs, what's the problem? Well, the problem is that I like to have my code tested without paying Google for the use of their resources. The only way to do that is to mock/stub/fake their APIs in my code so that I fulfill their interface with my basic implementation, enough to stand in for my tests. After all, I'm not testing their APIs but my code using them.

Great, so this is a pretty common need. Typically, I use the client library for the API provided by GCP and write code that fulfills their interface. This is where Go is unique, and I differ from their philosophy. See, they feel the API client developers aren't the ones who should be on the hook for the interface. Instead, that's on the consumer to create. I disagree because it means that the API client developers have broken the [Dependency Inversion Principle](https://www.freecodecamp.org/news/solid-principles-explained-in-plain-english/#:~:text=parking%20lot%20interface.-,Dependency%20Inversion%20Principle,-The%20Dependency%20Inversion), making their code unmockable for their own testing. Maybe Google engineers don't need to mock their code, preferring to exercise their actual systems. However, the typical consumer isn't going to want to exercise their actual resources except in integration testing. It's laziness to pass the buck like this.

## You have yet to convince me, Sir!

Let's pretend, though, that they're right. The API client authors aren't responsible for our needs. Let me show you where that still falls on its face by making its consumers' lives more complex than they should be. Google doesn't give us an interface for their API. Honestly, this isn't so uncommon, and I'm sure I have readers who are calling me out right about now. However, let me show you some code where the Google API Go client authors' disregard for the Dependency Inversion Principle bit me.

I recently had to work with GCP's Compute Engine APIs to fetch various data. I'll focus on fetching the Zones for a Region for this demo. Below is the Go code to do that.

```
import (
	"context"
	"fmt"
	"log"

	"github.com/samber/lo"
	"google.golang.org/api/iterator"
	"google.golang.org/genproto/googleapis/cloud/compute/v1"
)

func (t *TopologyFetcher) fetchComputeZones(
	ctx context.Context,
	regionShortName string,
	zones []*computepb.Zone,
) ([]*computepb.Zone, error) {
	var (
		err  error
		zone *computepb.Zone
	)

	client := t.zonesClientCreator(ctx)
	filter := "region eq .*" + regionShortName
	it := client.List(ctx, &computepb.ListZonesRequest{
		Project: t.projectID,
		Filter:  &filter,
	})

	defer func(client *ZonesClientWrapper) {
		_ = client.Close()
	}(client)

	for zone, err = it.Next(); err != iterator.Done; zone, err = it.Next() {
		if err != nil {
			returnErr := fmt.Errorf("could not fetch GCP zones: %q", err)
			log.Log.Error().Msg(returnErr.Error())
			return nil, returnErr
		}

		if !lo.Contains(zones, zone) {
			zones = append(zones, zone)
		}
	}

	return zones, nil
}
```

If you're new to Go having come from one of the modern OOP languages, like me, then the pointers will throw you a bit. The critical thing to note above is that the client library from GCP returns a struct by reference, not a value or an interface. What's most interesting about the code above is that I want to be able to mock the client, which returns an *iterator* when I request a List of zones.

Ok, that's a lie. Let me show you what the client really does:

```
func (c *ZonesClient) List(ctx context.Context, req *computepb.ListZonesRequest, opts ...gax.CallOption) *ZoneIterator {
	return c.internalClient.List(ctx, req, opts...)
}
```

That's the actual function of Google's compute.ZonesClient. Of note is that it returns a *pointer to an iterator*. That distinction makes all the difference, as I need to mock that iterator so I can return values from a slice (list or array in other languages). The problem is that they coded against a concretion, not an interface, so I now have to write my own interface to stub out my implementation.

## Here's what not to do...

My first thought was that I'll just create the [missing] iterator interface. Looking at the one method I needed from it, I could see in my code above that I needed the following interface.

```
type ZoneIterator interface {
    Next() (*Zone, error)
}
```

Pretty simple. My next intuition was to create an interface for the client.

```
type ZonesClient interface {
    Close() error
    List(ctx cotext.Context, req *computepb.ListZonesRequest, opts ...gax.CallOption) ZoneIterator
}
```

This proved to be wrong, thanks to the pointer. See, computepb.List does not conform with that interface. It wants a pointer, but the client interface returns an iterator interface. Why did I try this, then? Because I'm used to other languages that decided, correctly, in my opinion, that pointers were the root of too many bugs and difficulties for developers and unnecessary. 

## So, now what?

It took two pivots of my thinking to get this working. Thankfully, I wasn't going it alone. I took inspiration from [this SO post](https://stackoverflow.com/questions/72046805/go-create-a-mock-for-gcp-compute-sdk) and had the help of a Go pro, [Jessie Hernandez](https://medium.com/@jessiehernandez), to figure it out when I got stuck. 

First, I was right to create the iterator interface, but I needed to wrap the client, so I could use it.

```
type ZonesClientWrapper struct {
    Closer func() error
    Lister func(ctx cotext.Context, req *computepb.ListZonesRequest, opts ...gax.CallOption) ZoneIterator
}
```

It's just a few tweaks on my interface above. First, the wrapper is a struct, not an interface. Why? Because I am not stating that computepg.ZonesClient is fulfilling an interface. Instead, its functionality will be wrapped inside the ZonesClientWrapper.

The second tweak is that Closer and Lister are pointers to functions I need to specify before use. Instead of an interface, I'll fulfill with either my stub or GCP's implementation; I will flesh out the wrapper as follows.

```
func (w *ZonesClientWrapper) Close() error {
    return w.Closer()
}

func (*ZonesClientWrapper) List(ctx context.Context, req *computepb.ListZonesRequest, opts ...gax.CallOption) ZoneIterator {
    return w.Lister(ctx, req, opts...)
}
```

Now we're ready to use this wrapper. Here's the wrapper being populated using Google's API.

```
client, _ := computepb.NewZonesRESTClient(ctx)
wrapper := &ZonesClientWrapper{
    Closer: client.Close,
    Lister: func(ctx context.Context, req *computepb.ListZonesRequest, opts ...gax.CallOption) ZoneIterator {
        return client.List(ctx, req, opts...)
    }
}
```

And here it is with a test stub. First, we need to fulfill the iterator interface's promised functionality.

```
type ZoneIterator struct {
    iterCounter      int
    ReturnZones      []*computepb.Zone
    ReturnZonesError error
}

func (it *ZoneIterator) Next() (*computepb.Zone, error) {
    if it.ReturnZonesError != nil {
        return nil, it.ReturnZonesError
    }
    
    if it.iterCounter == len(it.ReturnZones) {
        return nil, iterator.Done
    }
    
    zone := it.ReturnZones[it.iterCounter]
    it.iterCounter++
    return zone, nil
}
```

Then, we create and wrap our stubbed client.

```
type ZonesClientStub struct {
    ZoneIterator *ZoneIterator
}

func (s *ZonesClientStub) List(_ context.Context, _ *computepb.ListZonesRequest, _ ...gax.CallOption) ZoneIterator {
    return s.ZoneIterator
}

func (s *ZonesClientStub) Close() error {
    return nil
}

// Wrap the stub before tests
BeforeEach(func() {
    zonesClient = &ZonesClientStub{}
    zonesClient.ItemIterator = &ZoneIterator{
        iterCounter:      0,
        ReturnZones:      []*computepb.Zone{},
        ReturnZonesError: nil
    }
    zonesClientCreator = func(ctx context.Context) *ZonesClientWrapper {
        return &ZonesClientWrapper{
            Closer: zonesClient.Close,
            Lister: zonesClient.List,
        }
    }
}
```

Now I just use either client via the wrapper as shown in my first listing. The client := t.zonesClientCreator(ctx) you see there does one of the two above, depending on if the code is being executed by the test or in production.

## My admittedly biased conclusion

This is a lot of work, and mind-bending around pointers, that I would not have to do in most other modern languages. Even in a traditional, garbage-collected OOP language, iterators are common enough to have their own interface. So, I'd just mock the interface and be done. In Go, I have to think about pointers and mock via a pointer function and a wrapper, adding a lot of complexity in my code just so I can test it. I'd be open to hearing a counter-argument but, to me, this is more work than necessary if only we coded consistently to interfaces and exposed those for external use.

## Bonus round

Fetching Zones was not my only use of GCP's compute API. As such, I was repeating myself a lot until Jessie introduced me to generics support in Go. In doing so, I was also able to apply the pattern to GCP's billing API by breaking up its functionality as if it were two different clients. Here is a complete listing of the generic implementation.

```
type GenericIterator[T any] interface {
	Next() (*T, error)
}

type GenericClientWrapper[R any, T any] struct {
	Closer func() error
	Lister func(ctx context.Context, req *R, opts ...gax.CallOption) GenericIterator[T]
}

func (w *GenericClientWrapper[R, T]) Close() error {
	return w.Closer()
}

func (w *GenericClientWrapper[R, T]) List(
	ctx context.Context,
	req *R,
	opts ...gax.CallOption,
) GenericIterator[T] {
	return w.Lister(ctx, req, opts...)
}

type SkusClientCreator = func(ctx context.Context) *SkusClientWrapper
type SkusClientWrapper = GenericClientWrapper[billing.ListSkusRequest, billing.Sku]

skusClientCreator = func(ctx context.Context) *gcpservice.SkusClientWrapper {
	// real client - uncomment to test against GCP's API
	//client, _ := gcpbilling.NewCloudCatalogClient(ctx)
	//return &gcpservice.SkusClientWrapper{
	//	Closer: client.Close,
	//	Lister: func(
	//		ctx context.Context,
	//		req *billing.ListSkusRequest,
	//		opts ...gax.CallOption,
	//	) gcpservice.GenericIterator[billing.Sku] {
	//		return client.ListSkus(ctx, req, opts...)
	//	},
	//}

	return &gcpservice.SkusClientWrapper{
		Closer: skusClient.Close,
		Lister: skusClient.List,
	}
}

type ZonesClientCreator = func(ctx context.Context) *ZonesClientWrapper
type ZonesClientWrapper = GenericClientWrapper[compute.ListZonesRequest, compute.Zone]

zonesClientCreator = func(ctx context.Context) *gcpservice.ZonesClientWrapper {
	// real client - uncomment to test against GCP's API
	//client, _ := gcpcompute.NewZonesRESTClient(ctx)
	//return &gcpservice.ZonesClientWrapper{
	//	Closer: client.Close,
	//	Lister: func(
	//		ctx context.Context,
	//		req *compute.ListZonesRequest,
	//		opts ...gax.CallOption,
	//	) gcpservice.GenericIterator[compute.Zone] {
	//		return client.List(ctx, req, opts...)
	//	},
	//}
    
    return &gcpservice.ZonesClientWrapper{
		Closer: zonesClient.Close,
		Lister: zonesClient.List,
	}
}
```
