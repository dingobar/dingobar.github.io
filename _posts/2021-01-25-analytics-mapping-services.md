---
layout: post
title: Loosening monolithic dependencies with mapping microservices
subtitle: An idea for making the monoliths breakable
cover-img: /assets/img/path.jpg
thumbnail-img: /assets/img/thumb.png
share-img: /assets/img/path.jpg
tags: [crud, rest, modelling]
---

Bad data models can spread like a plague, and unfortunately us data engineers are often central
perpetrators. This is in particular the case when integrating with legacy systems, which I think
most enterprises that have been around since the early-2000's have at least a handful of. The
boundaries that caused formed these often monolithic applications have since disappeared, but
traditional BI with ETL-pipelines have made sure that their traces can be found in every corner of
the data landscape. Whereas these pipelines have undoubtedly paved the way for analytics and created
immense value, the continued perpetuation of dependencies to legacy data models gives huge issues
when developers want to make changes to monolithic legacy systems to accomodate more modern
microservice architecutres. Developers want to protect their inner data models in order to preserve
agility, because it really only takes a handful dependencies before changing even the tinyest aspect
of a data model triggers an organization-wide investigation (this never happens in practice).

I don't think there is a perfect way to reduce the complete grid-lock of data-dependencies that many
companies continue to face, but I came up with a very simple approach that I think could help
towards solving it over time. Before you arrest me, somebody else definitely had the same idea.
Maybe they even tried and failed, who knows. I have yet to find any similar approach documented
online, which is why I decided to do just that.

In the same way that master data works in the world of finance, we can implement field mapping or
look-up microservices through REST APIs, that can be used organization wide. In this way, we treat
data mapping as it's own micro-domain, which exists as a set of global microservices for anyone in
the organization to use. The primary identifiers of the mapping objects are the fields that we want
to perpetuate. This could be for example the UNLOCODE of sites, the registration number of vehicles,
or in general any entity that should be standardized.

By implementing the most common CRUD-operations, developers can handle mappings in a self-service
manner when onboarding datasets to the central data repository that is used for everything from
reporting to advanced analytics such as ML. The services must also produce events that document the
creation and evolution of mappings in an immutable manner. The API endpoints should be used by all
new consumer-oriented services that are using legacy datasets, so that they don't contribute to
spreading deprecated fields. Existing services should be modified to use the standardized mappings.

The mapping microservices have to loosen dependencies enough for the producer-oriented domains to be
able to regain their ability to be agile when working with their internal data models. In time, the
mapping services themselves can also be deprecated, when the fields that they map cease to be
relevant in the organization-wide scope (they can remain as implementational details in inner
architecture as long as needed, though).

One rule of thumb here should probably be to go only use this approach on fields that have a low
velocity, such as sites (warehouses, stores etc.), which are unlikely to change very often.

It's difficult to tell whether or not this is a good idea in practice. The success of the
microservices likely also depend on their actual implementation. If anyone reading this has any
experience trying something like this out I would love to hear about it.
