---
title: "Pagination"
description: "This tutorial describes how to deal with server-driven and client-driven pagination."

author: mumbi-o
ms.author: mowambug
ms.date: 7/1/2019
ms.topic: article
 
---
# Pagination
**Applies To**: [!INCLUDE[appliesto-odataclient](../includes/appliesto-odataclient-v7.md)]
Loading large datasets can be slow. Services often rely on pagination to load the data incrementally to improve the response times and the user experience. Paging can be server-driven or client-driven.

## Server-driven paging
In Server-driven paging, the server returns the first page of results. If total number of results is greater than the page size, the server returns the first page along with a `nextlink` that can be used to fetch the next page of results.

The OData Client deals with server-driven paging with the help of [DataServiceQueryContinuation](/dotnet/api/microsoft.odata.client.dataservicequerycontinuation) and [DataServiceQueryContinuation&lt;T&gt;](/dotnet/api/microsoft.odata.client.dataservicequerycontinuation-1). These classes contain the `nextLink` of the partial set of items.

### Top Level Pagination

Example:

``` csharp
DefaultContainer context = new DefaultContainer(new Uri("https://services.odata.org/v4/TripPinServiceRW/"));

// DataServiceQueryContinuation<T> contains the next link
DataServiceQueryContinuation<Person> nextLink = null;

// Get the first page
QueryOperationResponse<Person> response = await context.People.ExecuteAsync() as QueryOperationResponse<Person>;
int pageCount = 0;

do
{
    Console.WriteLine($"Page {++pageCount}");
    if (nextLink != null)
    {
        response = await context.ExecuteAsync<Person>(nextLink) as QueryOperationResponse<Person>;
    }

    // You must enumerate the response before calling GetContinuation below.
    foreach (Person person in response)
    {
        Console.WriteLine($"\tPerson Name: {person.FirstName}");
    }

}
// Loop if there is a next link
while ((nextLink = response.GetContinuation()) != null);
```

### Nested Pagination
There are instances where we need to load pages of entities as well as pages of their related entities.
The example below returns related `Trips` for each `Person` entity from the data service. It uses a do...while loop to paginate through `Person` entities and a nested while loop to paginate through the related `Trips`.
We need to enumerate the response before calling `GetContinuation` on it.

``` csharp
DefaultContainer context = new DefaultContainer(new Uri("https://services.odata.org/v4/TripPinServiceRW/"));
int pageCount = 0;
DataServiceQueryContinuation<Person> nextLink = null;

try
{
    // Execute the query for all people and related trips,
    // and get the response object.
    QueryOperationResponse<Person> response = await
        context.People.Expand("Trips")
        .ExecuteAsync() as QueryOperationResponse<Person>;

    // With a paged response from the service, use a do...while loop
    // to enumerate the results before getting the next link.
    do
    {
        // Write the page number.
        Console.WriteLine($"Person Page {++pageCount}:");

        // If nextLink is not null, then there is a new page to load.
        if (nextLink != null)
        {
            // Load the new page from the next link URI.
            response = await context.ExecuteAsync<Person>(nextLink)
                as QueryOperationResponse<Person>;
        }

        // Enumerate the Person(s) in the response.
        foreach (Person person in response)
        {
            var innerPageCount = 0;
            Console.WriteLine($"\tPerson Name: {person.FirstName}");

            // Get the next link for the collection of related Trips.
            DataServiceQueryContinuation tripsNextLink =
                response.GetContinuation(person.Trips);

            if (person.Trips.Count > 0)
            {
                Console.WriteLine($"\t\tTrips Page {++innerPageCount}:");
            }
            foreach (Trip trip in person.Trips)
            {
                // Print out the trips in the first page.
                Console.WriteLine($"\t\t\tTripID: {trip.TripId} - Name: {trip.Name}");
            }

            while (tripsNextLink != null)
            {
                // Load the next page of Trips.
                var tripsResponse = await context.LoadPropertyAsync(person, "Trips", tripsNextLink);
                tripsNextLink = tripsResponse.GetContinuation();

                if (tripsResponse.Count > 0)
                {
                    Console.WriteLine($"\t\tTrips Page {++innerPageCount}:");
                }
                foreach (Trip trip in tripsResponse)
                {
                    // Print out the trips.
                    Console.WriteLine($"\t\t\tTripID: {trip.TripId} - Name: {trip.Name}");
                }
            }
        }
    }

    // Get the next link, and continue while there is a next link.
    while ((nextLink = response.GetContinuation()) != null);
}
catch (DataServiceQueryException ex)
{
    throw new ApplicationException(
        "An error occurred during query execution.", ex);
}
```

Below is the sample output (Assuming the Trippin service had a pagesize of 2):
```html
Person Page 1:
        Person Name: Russell
            Trips Page 1:
                TripID: 0 - Name: $Trip in US
                TripID: 1003 - Name: $Trip in Beijing

            Trips Page 2:
                TripID: 1007 - Name: $Honeymoon

        Person Name: Scott
            Trips Page 1:
                TripID: 0 - Name: $Trip in US
                TripID: 2004 - Name: $Trip in Beijing
        .............................................
        .............................................
        .............................................

Person Page 2:
        Person Name: Marshall
            Trips Page 1:
               .............................................
               ............................................

        Person Name: Ryan
            Trips Page 1:
               .............................................
               .............................................

            Trips Page 2:
               .............................................
               .............................................
```

## Client-driven paging
In client-driven paging, we request the server to return the specified number of results. There is no `nextLink` that is returned.

The OData Client deals with client-driven paging using `$skip` and `$top` query options.

The `$top` query option requests the number of items in the queried collection to be included in the result. 

The `$skip` query option requests the number of items in the queried collection that are to be skipped and not included in the result.

For `GET https://host/service/People?$skip=3&$top=5`

``` csharp
DefaultContainer context = new DefaultContainer(new Uri("https://services.odata.org/V4/(S(uvf1y321yx031rnxmcbqmlxw))/TripPinServiceRW/"));
IQueryable<Person> people =
    context.People
        .Skip(3)
        .Take(5);
foreach (Person person in people)
{
    Console.WriteLine($"Username: {person.UserName} First Name: {person.FirstName}");
}
```