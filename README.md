# Pragmatic Web API design

I worked on quite a few public and internal REST APIs both building and consuming them. REST is more of an architectural style than a well-defined framework. It's sometimes not easy to make sure everyone is on the same page when you talk about REST.

Reading Roy Fielding's famous [dissertation](http://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm) helps you understand the philosophy (especially chapter 5) and it's a wonderful piece of work. I published an [epub version](https://github.com/csabapalfi/rest-epub) on github (in case you would like to read it on your mobile) or you can grab the pdf or read in HTML [here](http://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm).

At the same time it's definitely worth having a look at what works in the wild and how others build web APIs. That's exactly what [Apigee](http://apigee.com/about/) did before publishing a short [ebook](http://info.apigee.com/Portals/62317/docs/web%20api.pdf) with their suggestions for good pragmatic web API design and I think they're straight to the point in most cases.

It's mainly addressed to companies building public APIs but lot of the sections are also relevant for internal APIs. They highlight good and bad examples from public APIs by companies like Facebook and Twitter.

Let me recollect the essence of the Apigee Web Api Design ebook here but please feel free to grab the original ebook for more details and the examples.

## Introduction

* public API design key performance metric: how quickly developers can get
up to speed and start enjoying success using your API
* API Craft Google Group is a good resource
* pragmatic and not dogmatic REST (RESTafarians)
* maximize developer productivity and success
* developer (API consumer) point of view should be the guiding principle

## Nouns are good, verbs are bad

* 2 base URLs per resource
* URL for a collection and for a specific element in the collection
* e.g /dogs, /dogs/1234
* keep verbs out of your base URLs
* use HTTP verbs to operate on the collections and elements
* e.g GET /dogs, POST /dogs/1234, DELETE dogs/1234

## Plural nouns and concrete names

* plural rather than singular nouns
* concrete rather than abstract names (videos, blogentries vs item)

## Associations

* simplifying the associations between resources: /resource/identifier/resource
* parameters and other complexities as query parameters

## Errors

* use HTTP status codes
* try to map them cleanly to relevant standard-based codes:
* 2xx Everything worked - success
* 4xx client did something wrong - client error
* 5xx API did something wrong - server error
* shouldn't need more than 5-8 (200, 201, 301, 304, 401, 404, 503)
* error message and detail in response body for people
* be verbose and use plain language descriptions
* add as many hints as your API team can think of about what's causing an error
* add a link in your description to more information

## Versioning

* never release an API without a version, make the version mandatory
* specify the version with a 'v' prefix
* move it all the way to the left in the URL so that it has the highest scope (e.g. /v1/dogs).
* use a simple ordinal number v1, v2, v3
* tipically granular versioning doesn't work well with APIs (it's an interface not an implementation)
* maintain at least one version back.
* application/vnd in Content-Type header is not recommended, less visible, less fun to hack in the browser

### General logic for parameters
* if it changes the logic you write to handle the response, put it in the URL so you can see it easily
* if it doesn't change the logic for each response, like OAuth information, put it in the header

## Partial response

* support partial response (e.g. by adding fields to return in a comma delimited list)
* e.g ?fields=name,photo

## Pagination

* make it easy for developers to paginate collection response
* it's almost always a bad idea to return every resource
* use limit and offset since it's well-understood
* e.g. /dogs?limit=25&offset=50
* indicate total number of records available in a header or response body
* default: limit=10&offset=0


## What about responses that don't involve resources?

* calculate, translate, convert etc.
* it's ok to use verbs but make it clear in docs

## Supporting multiple formats

* file system notation (.extension)
* dogs.json, e.g /dogs/1234.json
* developers and even casual users of any file system are familiar to this
* it also requires just one additional character (the period) to get the point across
* default format: JSON

## What about attribute names?

* follow JavaScript conventions for naming attributes (camelCaseStartingWithLowerCase)

## Tips for search

### Global search

* search is the verb; ?q represents the query
* e.g. /search?q=fluffy+fur

### Scoped search

* prepend with the scope of the search
* e.g. /owners/5678/dogs?q=fluffy+fur
* (dropped the explicit search in the URL and rely on the parameter 'q' to
indicate the scoped query)

### Formatted results

* format specified the same way for action oriented (non-resource) responses
* e.g. /search.xml?q=fluffy+fur

## Consolidate API requests in one subdomain

* api.yourtopleveldomain - API gateway
* developers.yourtopleveldomain - developer portal

## Tips for handling exceptional behaviour

### clients unable to handle non-200 reponse properly

* (e.g old Flash version):
* url?suppress_response_codes=true
* always return 200, error details in the response body

### client supports limited HTTP methods

* support ?method=POST (etc) parameter
* method is always GET
* but the developer can express rich HTTP verbs
* careful with e.g Google bot traffic and method=DELETE

## Authentication

* simply use OAuth 2.0

## Chatty APIs

RESTful APIs can end-up being a chatty easily

* imagine how developers will use your API
* be complete and RESTful and provide shortcuts (start with nice RESTful then e.g provide a composite response for a common use-case)
* take advantage of the partial response syntax
: /owners/5678?fields=name,dogs.name

## Complement with an SDK

* well-documented code samples are also a critical resource but an SDK might also be useful

Reasons to consider and SDK:

* in case of a really complex domain often, a lot of what's needed is on the client side and you can push that burden to an SDK.
* speed adoption on a specific platform. (For example Objective C SDK for iPhone.)
* simplify integration effort and maybe reduce bad or inefficient code
* good SDKs are a forcing function to create good source code examples and documentation
* market your API to a specific community

## The API facade pattern

When creating an API for an array of complementary systems:

### Antipatterns:
* buildup approach: exposes the core of one internal system to start with
* separate system implementing according to common 'standards doc'
* copycat: copying competitors successful API

### The API facade pattern

1. Design the ideal API - design the URLs, request parameters and responses, payloads, headers, query parameters, and so on. The API design should be self-consistent.
2. Implement the design with data stubs. This allows application developers to use your API and give you feedback even before your API is connected to internal systems.
3. Mediate or integrate between the façade and the systems
