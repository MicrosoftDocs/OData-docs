---
title: "ODataLib changelog"
description: "ODataLib 7.x"
author: madansr7
ms.author: madansr7
ms.date: 02/19/2019
ms.topic: article
---

To briefly summarize the breaking changes, most of them fall into one of four categories:

## Improved Performance ##

We will get better writer performance across the board. 

## Introducing Dependency Injection ##

This feature will substantially increase extensibility, like allowing customers to replace entire components such as the `UriPathParser` with their own implementation. Introducing DI make it much easier to use the same reader/writer settings across the board. 

## Removed Legacy Code ##

There was a lot of vestigial code left around from the OData v1-3 days that we’ve removed. 

## Improved API Design ##

Most of our API improvements fall into the category of namespace simplifications or updating verbiage. The single most impactful change that we made was deciding to merge entity type and complex type in ODataLib. We did this because complex type and entity type are becoming more and more similar in the protocol, but we continue to pay overhead to make things work for both of them.

More detailed information on new changes in ODL and WebApi in their respective changelogs.
