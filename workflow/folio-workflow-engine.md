# The FOLIO workflow engine: a straw-man proposal

<!-- md2toc -l 2 folio-workflow-engine.md -->
* [Preface](#preface)
* [Background](#background)
* [Terminology](#terminology)
* [Review of existing workflow systems](#review-of-existing-workflow-systems)
* [Workflow data](#workflow-data)
* [Front-end/back-end interaction](#front-endback-end-interaction)
* [What workflow includes](#what-workflow-includes)
* [Example workflows](#example-workflows)
    * [Acquisition of a suggested book](#acquisition-of-a-suggested-book)
    * [Unboxing a delivery](#unboxing-a-delivery)
    * [Submitting a thesis](#submitting-a-thesis)
* [Implementation analogies](#implementation-analogies)
    * [Finite state machine with transitions](#finite-state-machine-with-transitions)
    * [Makefile-like dependency tree](#makefile-like-dependency-tree)
    * [Connector Framework](#connector-framework)
    * [Very high-level DSL (like Okapi CLI)](#very-high-level-dsl-like-okapi-cli)
* [Error handling](#error-handling)
* [Appendix: using the notification system](#appendix-using-the-notification-system)
* [Appendix: object types](#appendix-object-types)



## Preface

XXX Emerges from Montreal breakout with D, Peter, Nassib, Heikki, Jason, Ian, Mike.



## Background

XXX Workflow as orhestration for a multi-app system.

XXX Workflow and automation are the same thing.

XXX Why to think about this now. (How much reengineering will we need to do later on if we don't?)

XXX UX-oriented peespective: see https://issues.folio.org/browse/UX-52



## Terminology

XXX A "workflow" is something that we author

XXX A "workflow instance" or "job" is a running instance of a workflow that has its own state.

XXX Like class and instance in OOP.



## Review of existing workflow systems

XXX Nassib volunteered to do this: no issue filed yet AFAIK.



## Workflow data

XXX Nassib's experience of workflow engines: pain comes in standardising inputs and outputs

XXX Should be avoidable here as data will be canonicalised on entering the system.

XXX Should workflow-step inuts and outputs be items (full objects) or IDs (references to them)?

XXX Does a workflow have its own state separate from the state of the objects it deals with? Does it suffice to mark a purchase as "needs authorization", or do we also need to make a workflow instance as "awaiting purchase authorization"? The latter allows us to tie together related operations after the event. Workflow is a great source of audit logging capability - Perhaps saving individual services from having to do excessive audit logging.



## Front-end/back-end interaction

XXX Goal is for most or all mechanism to be server-side. That is necessary for it to function in automation. Front-end involvement may be unavoidable in some cases.

XXX Workflow editor as UX'd by Filip is separate.

XXX Much will be done on back-end, though effects will be seen on front-end: e.g. partially pre-filled form that must be completed and submitted.



## What workflow includes

XXX Both pure automation (computer does every step) and human workflow. Workflows consisting entirely of the former can be thought of as macros.



## Example workflows


### Acquisition of a suggested book

XXX Peter's example - Acquisitions - request for book - one user finds source, but doesn’t have perms to approve spend, so queues task up for other person.


### Unboxing a delivery

XXX Formal definitions for Shelfmark, Call number, location

XXX Getting a file of records from a vendor to load. Vendor delivers box of books and file of bib records, eg shelf ready items with a barcode. For each barcode [Match on order number] you want to create an item record. Workflow “Go get file from ftp server [Or somewhere]”. Load file - for each item: “Match an existing record?” If Yes: “Overlay” If No: “Create Item”. Decide on location [to shelf level]. Shelf location [Or an indication thereof, such as fund code] may be on order information. -- Discussion of shelfmarks/call numbers/etc. Workflow[contd] : Check for holds/reservations on the new item.. 


### Submitting a thesis

XXX After Viva/Defence:: Receiving dissertation, Goes to cataloguing librarian, clean up abstract, upload to IR. 



## Implementation analogies


### Finite state machine with transitions

XXX


### Makefile-like dependency tree

XXX


### Connector Framework

XXX


### Very high-level DSL (like Okapi CLI)

XXX Domain-specific loop control, e.g. `boxOfBooks.foreach(book) { ... }`.



## Error handling

XXX If a step fails, do we fail the whole job? We can report back to the person who initiated the job.

XXX In the unboxing example: "here’s a report of the three things we weren’t able to do anything with."

XXX The workflow may have multiple operations on the go at once (e.g. "when these three parallel tasks are complete, go on to Step X). How do we handle recovert if one of them fails after others have already succeeded?



## Appendix: using the notification system

XXX We will want to do all notification using the notification system: "please authorize this", "your request has been accepted/rejected", "your job failed", etc. Users will want different kinds of message communicated in different ways, e.g. some by email, some by text-message, some waiting on the system until the next login. So notifications need a "type" drawn from a small controlled vocabulary (as well as other extensions such as sender, date, subject)



## Appendix: object types

XXX May need to be made explicit, with preferences registered to each type as in the MacOS registry: e.g. "When I get an object of type `user` I want to use the `@someVendor/users` app to open it.


