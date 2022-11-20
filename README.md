# Watcher

## Initial Note

I have chosen to do this task for two reasons. First of all, I’ve actually done this before, in two different systems. Water Ledger is built heavily on a system of watching for contract events to create an event sourcing model. This is not custom per user, but the skeleton is similar. The other reason is that I’ve actually considered building something to do almost exactly this as a commercial service previously.

In taking on this task as an architectural design challenge I have two conflicting choices. One is to design the system as I would build it, but the other is to build it to impress.

The most honest solution is to build it as I would build it from the start. Though there are more impressive technical architectures I think it is important that a technical solution solves the problems in front of it, rather than optimizing prematurely for future needs. Though it’s tempting to create a distributed system with event sourcing and serverless calls for notifications, the key requirement is simply to create something as quickly as possible, launch a working service and then iterate once the idea is proven. This is not solely to facilitate rapid development, but also to constrain costs and complexity until needed.

The developed architecture components required for an event watcher system are relatively simple.

## Scoping

The project as documented is a minimum viable product, rather than extensively featured. It is intended to be useful and usable, but not highly optimized. For example, the solution assumes a “monolith” or standard web app, which might be enhanced later as a series of micro-services. The solution assumes implementation as a RESTful API, but may later be enhanced as GraphQL.

The specified architecture makes assumptions of overall technology but not specific choices, such as backend programming languages, frontend frameworks, database vendor or cloud provider.

There are four key workflows.

1. A user signs up
2. The user adds a contract
3. The user flags specific events on a contract
4. Event watchers are created to watch for the above events
5. Events are triggered in watched contract, causing handlers to execute

The architecture as designed only includes the functionality of the first three elements. Though the eventHandlers can be created, their actual functionality would be a separate process. They might send an email, sms, push notification, slack message, or any other form of notification or action. This functionality is essentially “stubbed”.

## Key Components

Database: A standard SQL database will store data for the application including all of the events to be watched and the associated user. The assumption is of a PostgresSQL database, but equivalents such as MariaDB or MySQL would also be appropriate.

REST API: The backend application for storing data can be built as a standard RESTful API, which gives and takes JSON as requests. Specified in the diagrams provided below, the REST API is written as a distinct service from the event watcher service. This would not initially be a separate service in a micro-service sense, but likely a separate domain within the same core application. The same applies to a less clearly defined notification service, which would handle the messaging of an event to the user per their requirements. These domain considerations are likely candidates for separation to micro services and an implementation of an event-driven architecture, but that separation is premature.

Frontend application: JavaScript based SPA, or Single Page App. This could be created in a number of different technology options, but would create the interface that calls the API.

Blockchain Provider: given the intensity of usage and the number of likely watchers created it would be most appropriate to create a full Ethereum node to correct with directly. The initial proposed application will just use an service like Infura.

## Architectural Diagrams

### Sequence Diagram

Sequence Diagram showing key interactions from either the user or the event. Note again that user management, event watchers and notification processes are treated as separate services here, but are merely distinct domains in a shared architecture (including a shared database, etc).

![Sequence Diagram](https://github.com/smallbatch-apps/watcher-architecture/sequence-diagram.png)

### Entity Relationship Diagram

Database tables and the relationships between these tables. This database is not necessarily exhaustive - it omits lookup tables, user login logging, token resets, and other “housekeeping” tables and fields. It is also only includes a proposed simplified notification process. There could be any number of additional tables depending on added features - for example it would be possible to extract the events from the ABI to facilitate the interface for selection of events.

![Entity Relationship Diagram](https://github.com/smallbatch-apps/watcher-architecture/entity-relationship-diagram.png)

<!--
### AWS Architecture diagram
This is not intended to dictate service vendors, and any competing cloud provider such as Azure or Digital Ocean could provide an equivalent implementation. -->
