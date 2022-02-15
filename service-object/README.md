# ServiceObject API User Guide

Open source documentation of Idun products

## System Overview

![ServiceObject overview](./assets/service-object-overview.png)

There are 4 entities in the application

### ServiceObject

ServiceObject is the main actor. ServiceObject could be produced directly via
API or by configured alert based on sensor observation stream. Read more
about [ProptechOS Alerts](proptechOS/alerts/README.md)

### Route

Routes provide the ability to specify which service objects should be dispatched by selected dispatchers. Therefore, the dispatcher itself is configured once with required/sensitive information, and then routing is responsible for leveraging how many service objects should be dispatched. Routing can be updated multiple times without the need to re-configure dispatcher information.

### Dispatcher

Dispatchers introduce an abstraction layer on top of the existing ServiceObject stream that allows to dispatch items to 3-party systems: Email, SMS, EstateLogs, Webhook etc. Dispatcher aggregates information about how to dispatch specific ServiceObject and stores any sensitive information: API keys, credentials, etc., so applications that produce ServiceObject don't need to know nor provide this information during ServiceObject creation.

Some dispatchers are provided by ProptechOS as a service: Email, SMS, so clients don't need to have their Email/SMS integration services. Other dispatchers will require sensitive information provided by clients like EstateLogs service credentials, those will be stored securely and used only during the dispatching process.

#### Dispatcher types

ServiceObject API develops integration with different dispatcher types and the list of supported types will grow in the future. Clients that want to create an instance of a dispatcher have to submit a request with required information depending on the type of the dispatcher and then set up routing for ServiceObjects that will instruct the dispatcher which service objects should be dispatched by them. One ServiceObject could be dispatched by multiple dispatchers and a single dispatcher can dispatch multiple service objects.

### Dispatch Log

Dispatch Log contains the information about the ServiceObject dispatching.
Whenever ServiceObject is processed by Router, each dispatcher creates
a log entry with dispatch status and details. This information can be used as
debug information to analyze unhealty behavior of 3-party service.

## Architecture Overview

### Processing pipeline

Every ServiceObject goes into a dispatching queue after creation, where routing is taken into account. Router gathers all available routes and tests each service object in the queue by the route filter speficied during route creation and if there is a match, the router puts the ServiceObject into a dedicated dispatching queue, therefore malfunctioning dispatcher won't affect other dispatchers and each dispatcher will have their own dispatching time, retry on failure and all dispatchers will be processing items in parallel.

### Conceptual diagram

![image info](./assets/service-object-processing-pipeline.png)

#### Processing pipeline steps

1. ServiceObject is created and put into the routing queue.
1. Router picks up a ServiceObject and tests for existing route filters.
1. ServiceObject put into the specific dispatcher queue(s).
1. Dedicated Dispatcher picks up ServiceObject and dispatches based on the provided information.
