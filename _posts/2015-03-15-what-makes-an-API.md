---
layout: post
title:  "What makes an API"
---

Welcome to my first post!

I'm trying to promote the use of APIs to clearly separate and decouple functionality in end-to-end banking platorms. 

Rather than enforce a particular integration pattern or technology (always difficult in a diverse and large corporation), I'm trying to focus on the desirable traits of an API that a service provider should offer. My strawman list follows; I'm planning to update this following feedback.


Clarity of responsibility
------
an API normally has a Provider that provides and maintains the service, and a Consumer that uses it, and these roles are well defined and do not change in regard to the service.

Direction of dependency
------
it is normal for the Consumer to depend on the Provider (for as long as the Consumer needs the service), but the Provider should not be aware of individual Consumers, although it does need to be aware of them at an aggregated level for performance and capacity planning purposes.

Documentation
------
An API should be well documented - this is a pre-requisite for Self-Service (see below). The documentation should include usage examples, a formal API specification with inputs and outputs, documentation of allowable values, documentation of the data model and data relationships, usage notes etc. The acid test is whether it is possible for a Consumer that is skilled in the art to pick up the Provider's API and begin to use it without problems or questions.

Stability
------
An API should be relatively stable and slow-changing. An API represents a service contract definition between a Consumer and a Provider, and in order to minimise the dependency of a Provider on a Consumer (managing change communications during change), and minimise the overhead on the Consumer of modifying their own application to track the changing service, the API should be relatively stable. Of course, this does not mean that the internals of the Provider's application cannot be changed continuously, or that the Consumer cannot be agile in how they consume the service - just that the linkage between them changes slower than both.


Evolution
------
An API should be evolved to meet changing business needs. But the nature of the evolution is important - as far as possible, evolution should be non-breaking on the consumer, additive in nature, and the core concepts and interaction patterns maintained. Identifiers of data records and entities should be chosen carefully, as changing these causes particularly bad disruption. Additionally, the Provider should ensure that current and current-1 versions of the API are supported, so that Consumers are free to choose when they migrate within a supported time window.

Control
------
An API should be managed in a controlled way, ideally with a sense of direction and a product backlog or roadmap.

Anti-fragility
------
An API should use a semantically-rich serialisation format, where the model structure of data exchanged across the API is self-describing. Examples include the tree-structure and element names of XML and property names of JSON. Crucially, this allows Consumers to pick the data elements that they need, and allows Providers to evolve the API and include additional data elements that do not break the Consumer. By contrast, flat data files with data elements defined by column position are very brittle, since the addition of data elements in the middle of the record alter the column position of all subsequent elements.

Operational support
------
An API should be managed, with efficient and communications from the Provider to the Consumer in the event of service degradation, unavailability or evolutionary change.

Self-servicability
------
A Consumer should be able to discover an API, register through a series of automated processes to complete any steps necessary to authenticate themselves and be authorised to access resources, without "engaging" the Provider. This is critical, since too often the Provider becomes a bottleneck, and individual service to the Consumer breaks the Direction of Dependency trait, and makes the service much more expensive.

No further dependencies
------
Preferably, the infrastructure should be in place such that it is not necessary to engage a separate infrastructure team before the Consumer can use the service. A generic MQ routing service, or generic HTTP infrastructure (TCP, IP, DNS, proxy servers etc.) fulfil this trait.


I'm not prescribing an individual technology approach or stack for APIs - for example, I'm not saying that APIs should use JSON over HTTP in a RESTful style, although often these technologies are chosen since they make fulfilling the traits easier. There are definitely times when file or message-queue-based solutions are more appropriate - ultimately I want to encourage these traits regardless of the solution.

James