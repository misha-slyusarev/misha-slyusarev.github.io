---
layout: post
title:  "Travel API client in Elixir"
date:   2017-02-18 08:00:00 +0100
categories: elixir api
---

In this post I would like to show you how to start a travel project. In travel it is all about integration. You integrate various flights, hotels, and payments providers and build your service on top of this. So, to start I created a simple API client that you can use as a template in your future work.

Without going into much details about implementation, we will cover only main parts. You should be able to find the whole project in Github -- [travel_client](https://github.com/misha-slyusarev/travel_cllient). Try to download and run the client before further reading.

Consumed API provided by [Skypicker.com](http://docs.skypickerpublicapi.apiary.io/#reference) and you can freely use it to get flights information. You will have to become a partner to proceed with booking, though.

### Application workflow

The application downloads information about flights from A to B within required interval of time. That is, we will be asking, for example, how to get from Berlin to Paris with departure date in between June 21 through 28.

Then the tool converts received data to internal representation. And it could finally display or save this to a local database. That's our high level steps:

- _Fetch_ data from API
- _Convert_ to internal format
- _Output_ (or save to DB)

It's probably the most common workflow for API clients like this. I skipped the use of database, though, for sake of simplicity. And it will be displaying converted response right in the console. You can check on some wrappers like [ecto](https://github.com/elixir-ecto/ecto), for example, to make it talk to the database.

In Elixir you can outline the workflow in very clear manner (`lib/travel_client.ex`):

{% highlight elixir %}
def process({from, to, from_date, to_date}) do
  TravelClient.API.fetch(from, to, from_date, to_date)
  |> decode_response
  |> convert
  |> output
end
{% endhighlight %}

### Fetch flights

Interaction with the API is done in `lib/travel_client/api.ex`. We get the request data prepared and then pass it down to `HTTPoison` library.

{% highlight elixir %}
def fetch(from, to, from_date, to_date) do
  format_dates(from_date, to_date)
  |> flights_url(from, to)
  |> HTTPoison.get(@user_agent)
  |> handle_response
end
{% endhighlight %}

To process a response there is `handle_response` function that checks the status code and parses the body.

We limit a number of possible options providing constant duration for the trip.

{% highlight elixir %}
defmodule TravelClient.API do
  @default_duration 7 # week in heaven
...
{% endhighlight %}

### Convert data

Response from the flight API is basically, surprise, a list of flights. Every entity of the list represents a flight and can be handled separately. So it would be logical to create a struct in which we can keep flight related data. You can find such a struct inside `lib/flight.ex`:

{% highlight elixir %}
defmodule Flight do
  defstruct [:departue_time, :arrival_time, :duration, :price]
end

defimpl String.Chars, for: Flight do
  def to_string(f) do
    "#{f.departue_time} | #{f.arrival_time} | #{f.duration} | #{f.price}"
  end
end
{% endhighlight %}

For now we only interested in a small scope of fields, but you can explore the response and the API documentation to extend the struct. In the file you'll also find implementation of _String.Chars_ protocol. This lets us control the Flight representation when used in string interpolation.

Then conversion is mostly based on `extract_flights` function that goes recursively through the list of flights and creates another list containing instances of the Flight structure.

{% highlight elixir %}
def extract_flights([]), do: []
def extract_flights([head | tail]) do
  flight = %Flight{
    departue_time: DateTime.from_unix!(head["dTimeUTC"], :second),
    arrival_time: DateTime.from_unix!(head["aTimeUTC"], :second),
    duration: head["fly_duration"],
    price: head["price"]
  }

  [ flight | extract_flights(tail)]
end
{% endhighlight %}

### Output

Now we can simply iterate over this list and display flights in the console.

{% highlight bash %}
$ ./travel_client berlin paris 2017-06-21 2017-06-28

14:01:46.948 [info]  Successful request
Departue | Arrival | Duration | Price (EUR)
2017-06-23 14:10:00Z | 2017-06-23 16:05:00Z | 1h 55m | 92
2017-06-23 14:10:00Z | 2017-06-23 16:05:00Z | 1h 55m | 95
2017-06-22 14:10:00Z | 2017-06-22 16:05:00Z | 1h 55m | 97
...
{% endhighlight %}

### Summary

Obviously, there is much more room for functionality in this application. And it is just a basic implementation to show you the idea. So, feel free to fork the project and start building your very own API client.
