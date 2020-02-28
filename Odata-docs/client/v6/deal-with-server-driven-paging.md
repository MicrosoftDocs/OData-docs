---
title: "Deal with server-driven paging"
description: ""

author: madansr7
ms.author: madansr7
ms.date: 7/1/2019
ms.topic: article
 
---
# Server driven paging
**Applies To**: [!INCLUDE[appliesto-odataclient](../../includes/appliesto-odataclient-v6.md)]

The OData Client for .NET deals with server-driven paging with the help of `DataServiceQueryContinuation` and `DataServiceQueryContinuation<T>`. They are classes that contain the next link of the partial set of items.

Example:

``` csharp
var context = new DefaultContainer(new Uri("https://services.odata.org/v4/TripPinServiceRW/"));

// DataServiceQueryContinuation<T> contains the next link
DataServiceQueryContinuation<Person> nextLink = null;

// Get the first page
var response = await context.People.ExecuteAsync() as QueryOperationResponse<Person>;

// Loop if there is a next link
while ((nextLink = response.GetContinuation()) != null)
{
    // Get the next page
    response = context.Execute<Person>(nextLink);
}
```

## Nested Pagination
There are instances where we need to load pages of entities as well as pages of their related entities.
The example below returns related Trips for each Person entity from the data service. It uses a do...while loop to paginate through Person entities and a nested while loop to paginate through the related Trips.
We need to enumerate the response before calling `GetContinuation` on it.

``` csharp
var context = new DefaultContainer(new Uri("https://services.odata.org/v4/TripPinServiceRW/"));
var pageCount = 0;
var innerPageCount = 0;
DataServiceQueryContinuation<Person> nextLink = null;

try
{
    // Execute the query for all people and related trips,
    // and get the response object.
    var response = await
        context.People.Expand("Trips")
        .ExecuteAsync() as QueryOperationResponse<Person>;

    // With a paged response from the service, use a do...while loop
    // to enumerate the results before getting the next link.
    do
    {
        // Write the page number.
        Console.WriteLine("Person Page {0}:", ++pageCount);

        // If nextLink is not null, then there is a new page to load.
        if (nextLink != null)
        {
            // Load the new page from the next link URI.
            response = await context.ExecuteAsync<Person>(nextLink)
                as QueryOperationResponse<Person>;
        }

        // Enumerate the Person(s) in the response.
        foreach (Person p in response)
        {
            Console.WriteLine("\tPerson Name: {0}", p.FirstName);
            Console.WriteLine("\tTrips Page {0}:", ++innerPageCount);
            // Get the next link for the collection of related Trips.
            DataServiceQueryContinuation nextTripsLink =
                response.GetContinuation(p.Trips);

            foreach (Trip t in p.Trips)
            {
                // Print out the trips in the first page.
                Console.WriteLine("\t\tTripID: {0} - Name: ${1}",
                    t.TripId, t.Name);
            }

            while (nextTripsLink != null)
            {
                Console.WriteLine("\t\t******Load Trips******");

                // Load the next page of Trips.
                var tripsResponse = await context.LoadPropertyAsync(p, "Trips", nextTripsLink);
                nextTripsLink = tripsResponse.GetContinuation();
                foreach (Trip t in tripsResponse)
                {
                    // Print out the trips.
                    Console.WriteLine("\t\tTripID: {0} - Name: ${1}",
                        t.TripId, t.Name);
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
        Trips Page 2:
                TripID: 0 - Name: $Trip in US
                TripID: 2004 - Name: $Trip in Beijing
        .............................................
        .............................................
        .............................................

Person Page 2:
        Person Name: Marshall
        Trips Page 3:
               .............................................
               ............................................

        Person Name: Ryan
        Trips Page 4:
               .............................................
               .............................................

        Trips Page 5:
               .............................................
               .............................................
```
