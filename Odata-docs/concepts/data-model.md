---
title: "Data Model"
description: "Data model basics"
author: madansr7
ms.author: madansr7
ms.date: 02/19/2019
ms.topic: article
 
---
# Data model

This section provides a high-level description of the Entity Data Model (EDM): the abstract data model that is used to describe the data exposed by an OData service. An OData Metadata Document is a representation of a service's data model exposed for client consumption.

The central concepts in the EDM are entities, relationships, entity sets, actions, and functions.

## Entities

Entities** are instances of entity types (e.g. Customer, Employee, etc.).

## Entity types

Entity types are named structured types with a key. They define the named properties and relationships of an entity. Entity types may derive by single inheritance from other entity types.

The key of an entity type is formed from a subset of the primitive properties (e.g. CustomerId, OrderId, LineId, etc.) of the entity type.

## Complex types

Complex types are keyless named structured types consisting of a set of properties. These are value types whose instances cannot be referenced outside of their containing entity. Complex types are commonly used as property values in an entity or as parameters to operations.

## Properties

Properties declared as part of a structured type's definition are called declared properties. Instances of structured types may contain additional undeclared dynamic properties. A dynamic property cannot have the same name as a declared property. Entity or complex types which allow clients to persist additional undeclared properties are called open types.

## Relationships

Relationships from one entity to another are represented as navigation properties. Navigation properties are generally defined as part of an entity type, but can also appear on entity instances as undeclared dynamic navigation properties. Each relationship has a cardinality.

## Enumeration

Enumeration types are named primitive types whose values are named constants with underlying integer values.

Type definitions are named primitive types with fixed facet values such as maximum length or precision. Type definitions can be used in place of primitive typed properties, for example, within property definitions.

## Entity sets

Entity sets are named collections of entities (e.g. Customers is an entity set containing Customer entities). An entity's key uniquely identifies the entity within an entity set. If multiple entity sets use the same entity type, the same combination of key values can appear in more than one entity set and identifies different entities, one per entity set where this key combination appears. Each of these entities has a different entity-id. Entity sets provide entry points into the data model.

## Operations

Operations allow the execution of custom logic on parts of a data model. Functions are operations that do not have side effects and may support further composition, for example, with additional filter operations, functions or an action. Actions are operations that allow side effects, such as data modification, and cannot be further composed in order to avoid non-deterministic behavior. Actions and functions are either bound to a type, enabling them to be called as members of an instance of that type, or unbound, in which case they are called as static operations. Action imports and function imports enable unbound actions and functions to be called from the service root.

## Singletons

Singletons are named entities which can be accessed as direct children of the entity container. A singleton may also be a member of an entity set.

An ***OData resource*** is anything in the model that can be addressed (an entity set, entity, property, or operation).
