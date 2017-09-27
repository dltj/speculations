# White paper: the FOLIO workflow engine

<!-- md2toc -l 2 folio-workflow-engine.md -->
* [Preface: what this document is](#preface-what-this-document-is)
* [Background](#background)
* [Terminology](#terminology)
* [Review of existing workflow systems](#review-of-existing-workflow-systems)
* [Workflow vs. automation](#workflow-vs-automation)
* [Example workflows](#example-workflows)
    * [Acquisition of a suggested book](#acquisition-of-a-suggested-book)
    * [Unboxing a delivery](#unboxing-a-delivery)
    * [Submitting a thesis](#submitting-a-thesis)
* [Using data in workflows](#using-data-in-workflows)
* [Front-end/back-end interaction](#front-endback-end-interaction)
* [Implementation analogies](#implementation-analogies)
    * [Finite state machine with transitions](#finite-state-machine-with-transitions)
    * [Makefile-like dependency tree](#makefile-like-dependency-tree)
    * [Connector Framework](#connector-framework)
    * [Very high-level DSL (like Okapi CLI)](#very-high-level-dsl-like-okapi-cli)
* [Implementation strategy](#implementation-strategy)
    * [Virtual Machine for running workflows](#virtual-machine-for-running-workflows)
    * [Expressing workflows](#expressing-workflows)
        * [XML or JSON](#xml-or-json)
        * [Human-readable/writeable DSL](#human-readablewriteable-dsl)
        * [VM](#vm)
        * [Visual](#visual)
        * [Discussion](#discussion)
    * [Tracking jobs](#tracking-jobs)
    * [Operations](#operations)
        * [Okapi calls](#okapi-calls)
        * [Transformations](#transformations)
        * [Control flow](#control-flow)
        * [Other](#other)
* [Error handling](#error-handling)
* [Appendix: using the notification system](#appendix-using-the-notification-system)
* [Appendix: object types](#appendix-object-types)



## Preface: what this document is

This is an overview of what workflow is or could be in FOLIO, how it breaks down into v1 and v2 features, what implications it has for the broader FOLIO architecture, and how we might go about implementing the v1 parts of it without circumscribing our options for the v2 parts when the time comes.

Much of what is contained herein was first discussed in the Workflow breakout group on the morning of Friday 20 September 2017, in the Montreal FOLIO developer's meeting. Contributors in that session included 
D Ellen Bonner,
Ian Ibbotson,
Heikki Levanto,
Peter Murray,
Nassib Nasar,
Jason Skomorowski,
and
Mike Taylor.

At this stage, the contents of this document should be considered as a foundation for discussion, rather than as a design for implementation.


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



## Workflow vs. automation

XXX Both pure automation (computer does every step) and human workflow. Workflows consisting entirely of the former can be thought of as macros.



## Example workflows


### Acquisition of a suggested book

XXX Peter's example - Acquisitions - request for book - one user finds source, but doesn’t have perms to approve spend, so queues task up for other person.


### Unboxing a delivery

XXX Formal definitions for Shelfmark, Call number, location

XXX Getting a file of records from a vendor to load. Vendor delivers box of books and file of bib records, eg shelf ready items with a barcode. For each barcode [Match on order number] you want to create an item record. Workflow “Go get file from ftp server [Or somewhere]”. Load file - for each item: “Match an existing record?” If Yes: “Overlay” If No: “Create Item”. Decide on location [to shelf level]. Shelf location [Or an indication thereof, such as fund code] may be on order information. -- Discussion of shelfmarks/call numbers/etc. Workflow[contd] : Check for holds/reservations on the new item.. 


### Submitting a thesis

XXX After Viva/Defence:: Receiving dissertation, Goes to cataloguing librarian, clean up abstract, upload to IR. 



## Using data in workflows

XXX Nassib's experience of workflow engines: pain comes in standardising inputs and outputs

XXX Should be avoidable here as data will be canonicalised on entering the system.

XXX Should workflow-step inuts and outputs be items (full objects) or IDs (references to them)?

XXX Does a workflow have its own state separate from the state of the objects it deals with? Does it suffice to mark a purchase as "needs authorization", or do we also need to make a workflow instance as "awaiting purchase authorization"? The latter allows us to tie together related operations after the event. Workflow is a great source of audit logging capability - Perhaps saving individual services from having to do excessive audit logging.



## Front-end/back-end interaction

XXX Goal is for most or all mechanism to be server-side. That is necessary for it to function in automation. Front-end involvement may be unavoidable in some cases.

XXX Workflow editor as UX'd by Filip is separate.

XXX Much will be done on back-end, though effects will be seen on front-end: e.g. partially pre-filled form that must be completed and submitted.



## Implementation analogies


### Finite state machine with transitions

XXX


### Makefile-like dependency tree

XXX


### Connector Framework

XXX


### Very high-level DSL (like Okapi CLI)

XXX Domain-specific loop control, e.g. `boxOfBooks.foreach(book) { ... }`.



## Implementation strategy

### Virtual Machine for running workflows

XXX Somewhat like the CF Engine

### Expressing workflows

XXX Bizarrely, there may be up to four of these.

#### XML or JSON

XXX Probably the persistent form that is stored in the FOLIO DB, exported and imported, etc.

#### Human-readable/writeable DSL

XXX May be needed before we have Visual editor, unless we want to hand-write XML or JSON

XXX Since this will be parseable, maybe we'll make it the persistent form and not bother with XML/JSON at all.

#### VM

XXX XML/JSON or DSL will be compiled to some representation that can be executed by the Workflow VM. We may store the compiled version (analogous to Java `.class` files), or maybe just compile and run from the internal representation as Perl and Python do.

#### Visual

XXX In v2, Filip's UK will guide us into a visual representation of workflows. The Workflow editor UI module will need to parse stores workflows (from XML/JSON or a DSL) to determine what to draw; and render edited drawings into a form to send back to the server.

#### Discussion

XXX Probably don't need all four representations. Which to omit?

XXX Programming language: since we will need to parse/render on the client side for the V2 workflow editor, we will need implementations in JS. That suggests we should use those JS implementations on the server side too. This may mean the first Okapi module written in JS, or may entail somehow calling out from an RMB-based module into the JS compiler/renderer.


### Tracking jobs

XXX A back-end module for CRUDding the status of jobs based on workflows. Will likely consist of a tree of pointers to job-step objects, each with its own state. Will not need to be transmitted, so no need for a serialised or human-readable form.


### Operations

#### Okapi calls

XXX CRUDding objects

#### Transformations

XXX Adding fields, modifying them, deleting them

#### Control flow

XXX if/then/else, while, foreach, do-in-parallel

#### Other

XXX What else?



## Error handling

XXX If a step fails, do we fail the whole job? We can report back to the person who initiated the job.

XXX In the unboxing example: "here’s a report of the three things we weren’t able to do anything with."

XXX The workflow may have multiple operations on the go at once (e.g. "when these three parallel tasks are complete, go on to Step X). How do we handle recovert if one of them fails after others have already succeeded?



## Appendix: using the notification system

XXX We will want to do all notification using the notification system: "please authorize this", "your request has been accepted/rejected", "your job failed", etc. Users will want different kinds of message communicated in different ways, e.g. some by email, some by text-message, some waiting on the system until the next login. So notifications need a "type" drawn from a small controlled vocabulary (as well as other extensions such as sender, date, subject)



## Appendix: object types

XXX May need to be made explicit, with preferences registered to each type as in the MacOS registry: e.g. "When I get an object of type `user` I want to use the `@someVendor/users` app to open it.


