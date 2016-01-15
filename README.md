# Introductory Solid Tutorial

## Introduction

[Solid](https://github.com/solid/solid) (derived from "**so**cial **li**nked **d**ata")
is a proposed set of conventions and tools for building *decentralized social
applications* based on [Linked Data](http://www.w3.org/DesignIssues/LinkedData.html)
principles. Solid is modular and extensible. It relies as much as possible on existing
[W3C](http://www.w3.org/) standards and protocols.

Specifically, Solid is:

* A tech stack -- a set of complementary
  [standards](https://github.com/solid/solid-spec#standards-used) and
  [data formats/vocabularies](https://github.com/solid/vocab) that together
  provide capabilities that are currently available only through centralized
  social media services (think Facebook/Twitter/LinkedIn/many others), such as
  *identity*, *authentication and login*, *authorization and permission lists*,
  *contact management*, *messaging and notifications*, *feed aggregation and
  subscription*, *comments and discussions*, and more.
* A **[Specifications document](https://github.com/solid/solid-spec)**
  that describes a REST API that extends those existing
  standards, contains design notes on the individual components used, and is
  intended as a guide for developers who plan to build servers or applications.
  See also the **[Solid API](#solid-api)** section of this tutorial.
* A set of [servers](https://github.com/solid/solid-platform#servers) that
  implement this specification.
* A [test suite](https://github.com/solid/solid-tests) for testing and validating
  Solid implementations.
* An ecosystem of [social apps](https://github.com/solid/solid-apps),
  [identity providers](https://github.com/solid/solid-idp-list) and helper
  libraries (such as [solid.js](https://github.com/solid/solid.js)) that run on
  the Solid platform.
* A community providing documentation,
  [tutorials](https://github.com/solid/solid-tutorials) and
  [talks/presentations](https://github.com/solid/talks).

## Solid Concepts

Before going into the details of the Solid tech stack and API, it helps to
understand the underlying concepts the project was built on, its motivation and
context.

#### Why decentralized applications?

Developing a technology stack that enables truly *user-centric* de-centralized
applications can drastically improve the life of both users and app developers:

* Avoids vendor lock in (and the tendencies towards monopolies, walled gardens,
    surveillance, and other abuses of privilege that go along with that)

* Promotes healthy competition and diversity in app development --
  because the user's data is separated from a particular service provider or
  application, users are free to choose their preferred way of interacting with
  their data

* The user, instead of the service provider, owns all of their own data

* Helps prevent data loss (in case a provider gets acquired, or decides to
    discontinue a particular service)

* Encourages the use of standard inter-operable data formats, which
    provides a richer user experience

#### Logic Separated from Data

Consider the flowering of innovative and interesting features that resulted from
the various [mashups](http://www.programmableweb.com/mashups) and the general
"Web 2.0" trend of having service providers offer access to *some* subset of
their users' data as simple REST APIs. And that was with non-standard APIs,
proprietary and frequently incompatible data formats, using only the tightly
controlled and metered functionality that the service providers allowed.

Now imagine instead, if *users* had full control over their data (all of the data
that currently belongs to siloed service providers), and could select
which apps were allowed to access it (using standardized APIs).
This is one of the core goals of the Solid project.

#### Interoperability

Of course, separating applications from data (with the aim of giving users more
choices in terms of which apps they can use) is only possible when that data
is written in standard, mutually-intelligible formats.

By focusing on the re-use of standard protocols, formats and vocabularies, and
by providing a common data access API (as well as an Access Control List format),
Solid enables and encourages interoperability.

#### De-centralized identity

Any proposal for a de-centralized application infrastructure needs to solve the
problem of de-centralizing user identity.

Solid uses [WebID](http://www.w3.org/2005/Incubator/webid/spec/identity/) URLs
to provide universal usernames/IDs for Solid apps, and to refer to unique Agents
(people, organizations, devices). (See the [Authentication and User
Profiles](#authentication-and-user-profiles) section below, for more details).

This provides several benefits:

* The convenience of a persistent, user-controlled reusable sign-in
* Can be as descriptive or anonymous as the user wants
* Users have the option of creating multiple identities/logins,
    to protect separation of concerns (work-life, etc)

* Persistent de-centralized identities mean that a user has to assemble their
    social graph (friends/subscription list) only *once*, and then re-use it on
    many social networks. Prevents the "ghost town" network effect that
    prevents the formation of new social apps.

* An important step towards building a Web of Trust

#### REST API for resources

Solid uses the [Linked Data Platform (LDP)](http://www.w3.org/TR/ldp/) standard
(see also [LDP Primer](http://www.w3.org/TR/ldp-primer/)) extensively, as a
standard way of reading and writing generic Linked Data resources.
See the **[Solid API](#solid-api)** section below, for API listings and examples.

## Linked Data

> Linked Data is simply about using the Web to create typed links between data
  from different sources. These may be as diverse as databases maintained by two
  organizations in different geographical locations, or simply heterogeneous
  systems within one organization that, historically, have not easily
  interoperated at the data level. Technically, Linked Data refers to data
  published on the Web in such a way that it is machine-readable, its meaning is
  explicitly defined, it is linked to other external data sets, and can in turn be
  linked to from external data sets.
  *(from [Linked Data - Bizer, Heath and Berners-Lee
  (2009)](http://tomheath.com/papers/bizer-heath-berners-lee-ijswis-linked-data.pdf))*

The Solid project is based on
[Linked Data](https://en.wikipedia.org/wiki/Linked_data)
principles. These can be roughly summarized as:

* HTTP URLs make fantastic globally unique IDs (and because they're HTTP, people and
  machines can easily look up/dereference those links)

* You should publish structured data in a way that's machine-readable and inter-linked
  (because linking to other data increases the value of all data involved).

* It would be helpful if your data structure was standardized, composable and
  have a self-describing, extensible schema.

* In fact, RDF provides just such a generic, graph-based data model with which
  to structure and link data that describes things in the world. So you should
  strongly consider using it.

### Introduction to RDF (for Data Representation)

RDF, the Resource Description Framework, is one of the key ingredients of Linked
Data, and provides a generic graph-based data model for describing things,
including their relationships with other things. Another way to think about this
that's familiar to many developers, is that RDF allows you to create
[Entity-Attribute-Value
models](https://en.wikipedia.org/wiki/Entity%E2%80%93attribute%E2%80%93value_model)
in a standard and machine-readable way.

RDF data can be written down in a number of different interoperable ways, known
as *serialisations*:

- the original XML/RDF format
- Turtle RDF (easier to read and write, used by default in Solid)
- RDFa (RDF embedded into HTML attributes)
- JSON-LD

See **[Linked Data 101](http://rhiaro.github.io/sws/)** for a quick introduction
to RDF structure and concepts.

### Schemas and Vocabularies

As much as possible, Solid apps should aim to use existing schemas and vocabularies
to promote [interoperability](#interoperability).

**Vocabularies** or ontologies are a set of words and phrases that someone has decided to put together for the
purposes of describing a particular topic. They usually consist of classes and properties.

**Classes** are used to say that `<some-thing>` is of type `<other-thing>`.
For example:
`<http://rhiaro.co.uk/about#me> type <Person>`.
(Person is the class). Classes start with an uppercase letter.

**Properties** are just the describing words. In the previous example `type` is
the property, and before that name was the property. Properties are camelCased.

**Anyone can publish** a vocabulary, and you can use vocabularies that anyone
else has published. There are lots out there on the web, some better than others.

There are well-known, *stable* vocabularies. Examples include: RDF and RDFS
(which provide the basics for describing anything), FOAF (for describing people
and their friends), Dublin Core (for describing publications, mostly).

Vocabulary **reuse** is good practice, that is: look up if someone else has
already coined terms to describe what you want to describe, before thinking
about creating your own. A good place to start is http://lov.okfn.org

Social vocabularies you might be interested in for your applications include:

* [FOAF](http://xmlns.com/foaf/0.1/) (friend-of-a-friend; people and the
  relations between them).
* SIOC (semantically-interlinked online communities; some basic social stuff,
  simple but a little dated and based on forums / message board kinds of data).
* (AS2) ActivityStreams 2.0 (an emerging W3C standard for describing most of the
  kinds of social data we see on the web today).

#### ActivityStreams 2.0 (AS2)

AS2 comes in two parts: the [core](https://www.w3.org/TR/activitystreams-core/),
and the [vocabulary](https://www.w3.org/TR/activitystreams-vocabulary). The core
is about the structure of the data, and the vocabulary is all the terms (classes
and properties) you use in that structure. AS2 consists of 4 core types of
thing:

* Actors (people, robots, ..)
* Objects (eg. articles, media, events ...)
* Activities (eg. create, like, follow, accept an invitation, ...)
* Collections (a list of any of the above)

As well as lots of properties to describe these things, and to link them
together. For example:

**An object** (a note with some content):

```
<http://rhiaro.co.uk/2015/12/theres-nothing> a as:Note ;

        as:content "If there's nothing wrong with me... maybe there's something wrong with the universe." ;

        as:actor <http://rhiaro.co.uk/about#me> ;

        as:published "2015-12-29T19:49+00:00"^^xsd:dateTime ;

        as:tag "star trek", "life" .
```

**An activity** (the act of creating the above note, e.g. imagine it as it would appear in a notifications stream):

```
<http://rhiaro.co.uk/activities/12345> a as:Create ;

    as:object <http://rhiaro.co.uk/2015/12/theres-nothing> ;

    as:actor <http://rhiaro.co.uk/about#me> ;

    as:published "2015-12-29T19:49+00:00"^^xsd:dateTime .
```

A reply can be represented as a `Note` object, with a value in the `inReplyTo`
property.

A like can be represented as an Activity with a `Like` type, and the object
being liked in the `object` property.

More complex things can be represented too, e.g. adding a photo to an album
(which is a collection):

```
<http://img.amy.gy/food> a as:Collection ;

      as:name "My food photos" ;

      as:items

    <http://img.amy.gy/files/food/151129_chocpie2.jpg>,

    <http://img.amy.gy/files/food/151129_chocpie3.jpg> .


<http://img.amy.gy/files/food/151129_chocpie4.jpg> a as:Photo .


<http://activities.example/12345> a as:Add ;

    as:actor <http://rhiaro.co.uk/about#me> ;

    as:object <http://img.amy.gy/files/food/151129_chocpie4.jpg> ;

    as:target <http://img.amy.gy/food> .
```

And if AS2 doesn't cover something you want to model data about, you can extend
it simply by using your own vocabulary terms alongside.

## Solid

The sections below provide an overview of what you need to know to
use the Solid platform.
See also the [Solid specs document](https://github.com/solid/solid-spec)
for more details.

### Authentication and User Profiles

Authentication is the process of an app determining a user’s identity, “How do I
know you are who you say?”. (This is related to but also different from
Authorization (see Web Access Control below), which is “Now that I know who you
are, are you *allowed* to perform this action?”)

How do web applications typically authenticate users (that is, how do they
verify identity)? The most common method is usernames and passwords. A username
uniquely identifies a user (and ties them with a user *profile*, usually), and a
password verifies that the user is who they say they are.

In the Solid world, users don’t have to authenticate to applications, because
applications are decoupled from the data they produce. Instead, this means that
users will authenticate themselves to the servers on which data resides.

Continuing with the username and password analogy, Solid uses a URL as the
username, as a globally unique identifier. This URL is referred to as the **Web
ID**, and has a couple of required properties.

While the exact shape of the WebID URL will depend on which server you're
hosting it on, on many of the current Solid implementations, it will look
something like this:

`https://user.example.com/profile/card#me`

Fetching this WebID URL results in a **WebID Profile Document**
(which you can think of as an extensible universal user profile). This profile is a
[web document](https://en.wikipedia.org/wiki/Web_document), by default returned
in [Turtle](https://www.w3.org/TR/turtle/) RDF format, that describes some useful
facts about the user. (While the Turtle format is required by the spec, other profile
serialization formats such as RDFa and JSON-LD are often supported by servers.)

Solid stores WebID profile documents using the
[FOAF (Friend-of-a-Friend) RDF vocabulary](http://xmlns.com/foaf/0.1/).
A profile can optionally have a number of properties, such as names, pictures,
organization membership, socia media profiles, and so on. (Or, in the time honored
tradition of user profiles, all of these can be left blank.)

For example, this is what a part of a WebID Profile looks like:

```
@prefix foaf: <http://xmlns.com/foaf/0.1/> .

<> a foaf:PersonalProfileDocument ;
      foaf:maker <#me> ;
      foaf:primaryTopic <#me> .

<#me> a foaf:Person ;
      foaf:name "Tim Berners-Lee" ;
      foaf:img <picture.jpg>.
```

So far, we've discussed how Solid handles user IDs and user profiles. What about
passwords? Instead of passwords, the platform uses **WebID browser
certificates** that are stored client-side (in their browsers' secure key
stores).

Think back to the typical web app user registration workflow. You register with
a particular app or service, which asks you to pick a username and password
(which you can later use to log in), and generates a user profile.

Similarly, to use most Solid apps, you will need to register *somewhere*, but
unlike most web apps, the choice of which server or storage provider you want to
host your user identity and profile is yours (see the [Getting a WebID
Profile](#getting-a-webid-profile) section below). When you register for a WebID
profile for use with Solid, the signup app will typically will do 3 things:

1. prompt you for a username to use as a sub-domain (which will influence your
  WebID URL)
2. create a WebID Profile at that URL, and
3. generate a browser-side *WebID security certificate* that goes with that
  profile

From that point on, when you access a Solid web app that needs to authenticate
you, instead of a pop-up form asking for your username and password, the browser
prompts you to select your WebID security certificate that points to a profile
you want to log in with.

To put it in other words, the certificate stored in your browser serves both as
a username (since it contains inside it a link to your WebID) *and* a password
(in the form of a public/private cryptographic key pair that it also stores).

#### Getting a WebID account

As mentioned before, you will need a WebID account (with a corresponding browser
certificate) in order to use most Solid apps. The *good* news is that you only
need to sign up for it once, and afterwards re-use it anywhere on the . In that
regard, WebIDs are conveniently reusable (although nothing prevents you from
having multiple WebIDs and profiles, for purposes of avoiding linkability and
increased privacy).

To get a WebID profile and certificate, you have two options:

1. Sign up for a free account from one of the Solid-compliant
    [identity providers](https://solid.github.io/solid-idps/), such as
    [databox.me](https://databox.me/).

2. Or, if you are a hands-on dev type, set up one of the
  [Solid servers](https://github.com/solid/solid-platform#servers), and launch
  it pointing to a [signup app](https://solid.github.io/solid-signup/),
  which will walk you through creating a WebID profile and certificate.

#### Solid Authentication and User Profile Summary

* Solid uses WebID URLs as unique user IDs

* Not just humans allowed: Apps and services can have their own WebIDs, or borrow
  a user's WebID when needed (through delegation)

* A WebID URL, when fetched, must return a valid WebID User Profile
  (a FOAF document in RDF format)

* Browser-side certificates are currently used for authentication, instead of
  passwords

* You need a WebID profile and related certificate on a Solid-compatible storage
  provider to use any Solid-based apps.

### Access Control

How can we decide who has access to a particular resource? Since we have
identities, we could use them to define who has read or write access to
resources. These lists are called ACL (Access Control List), and they follow the
[Web Access Control spec](https://www.w3.org/wiki/WebAccessControl)

### Solid API

Let’s think of the web as a file system, in which every path leads to either a
resource or to a container (`https://example.tld/container/resource.json`).
These can be created, updated and deleted using CRUD operations.

| Action | Path | Description |
|--------|------|------------------------|
| `GET` | `/path/to/resource` | Retrieve a resource or list of resources in a container |
| `POST` | `/path/to/parent/` | Create a new resource in container |
| `PUT` | `/path/to/resource` | Replace a resource |
| `PATCH` | `/path/to/resource` | Update triples of a resource |
| `DELETE` | `/path/to/resource` | Delete a resource |

You can use simple Ajax requests to perform these operations. However, to make
it easier, we have written a Javascript library
[solid.js](https://github.com/solid/solid.js). You can continue the tutorial on
how to use the library to make web apps:

https://github.com/solid/solid.js.

### Pastebin Example

This example will walk you through the steps required to build a typical Solid app. 
You will learn how to:
- create new resources (paste bins)
- view created bins
- update existing bins

**Preparing the app**

Before we start writing the functions that create and view bins, we need to decide how the app will work. For instance, we know that we will use the app to create new bins, to view existing ones and also to update them.

The easiest way to separate these different app functionalities, is to separate the states by using query parameters -- i.e. `?view=...`, `?edit=...`. 

**Picking the vocabularies**

For this particular app, we can go with two very common vocabularies, `SIOC` and `Dublin Core Terms`. Using them is just a matter of defining the rdflib.js namespaces.

```Javascript
// common RDF vocabs
var RDF = $rdf.Namespace("http://www.w3.org/1999/02/22-rdf-syntax-ns#");
var DCT = $rdf.Namespace("http://purl.org/dc/terms/");
var SIOC = $rdf.Namespace("http://rdfs.org/sioc/ns#");
```

**Local data structure**

Next, we can define how we want to structure our bins. For example, a bin could have a title and body (content), but also a URI (useful later for updates).

```Javascript
// Bin structure
var bin = {
    url: '',
    title: '',
    body: ''
};
```

**Default container for new bins**

To create bins we have to define a default container to which we POST the bin. For now we can create a global variable called `defaultContainer`. The value of this variable can be a `bins`container on your account, which you have created with Warp.

```Javascript
var defaultContainer = 'https://user.databox.me/Public/bins/';
```

Now let's prepare a few functions that make the bread and butter of our app.

**Creating new bins**

Creating new bins is easy. It involves using Solid.js to post the bin data to the defaultContainer. We can create a `publish()` function which will handle this step. This function will read the value of the title and body elements from the editor.

```Javascript
function publish () {
    bin.title = document.getElementById('edit-title').value;
    bin.body = document.getElementById('edit-body').value;
}
```

Next, we will create a new graph object, in which we'll add the two triples, one for the title and one for the body. We will also store the serialized value of the graph in a new `data` variable.

```Javascript
function publish () {
    bin.title = document.getElementById('edit-title').value;
    bin.body = document.getElementById('edit-body').value;
    
    var g = new $rdf.graph();
    g.add($rdf.sym(''), DCT('title'), bin.title);
    g.add($rdf.sym(''), SIOC('content'), bin.body);
    var data = new $rdf.Serializer(g).toN3(g);
}
```

Finally, we will use Solid.js to POST the new bin to the server. If the POST succeeded, we will update the URL bar to the viewer. Else, we will have to deal with the error.

Here is the final function.


```Javascript
function publish () {
    bin.title = document.getElementById('edit-title').value;
    bin.body = document.getElementById('edit-body').value;

    var g = new $rdf.graph();
    g.add($rdf.sym(''), DCT('title'), bin.title);
    g.add($rdf.sym(''), SIOC('content'), bin.body);
    var data = new $rdf.Serializer(g).toN3(g);

    Solid.web.post(defaultContainer, undefined, data).then(function(meta) {
        // view
        window.location.search = "?view="+encodeURIComponent(meta.url);
    }).catch(function(err) {
        // do something with the error
        console.log(err);
    });
}
```

**Reading/viewing bins** 

Now that we have a way to create new bis, let's create a corresponding function that reads and displays bins.

We can use Solid.js to fetch the contents of a bin. Let's call this fetching function `loadBin`. Our function will take two parameters - one is the URL of the bin, and the other one is a flag that indicates whether we need to display the editor or not.

```Javascript
function loadBin (url, showEditor) {
    Solid.web.get(url).then(function(g) {
    
    }).catch(function(err) {
        // do something with the error
        console.log(err);
    });
}
```

The function `Solid.web.get()` returns a graph object `g` in case of success. This object has a few methods that allow us to query the graph for specific triples - i.e. `g.any()`. We'll use this method to get the title and body of a bin.

```Javascript
function loadBin (url, showEditor) {
    Solid.web.get(url).then(function(g) {
        // set url
        bin.url = url;
        // add title
        var title = g.any($rdf.sym(url), DCT('title'));
        if (title) {
            bin.title = title.value;
        }
        // add body
        var body = g.any($rdf.sym(url), SIOC('content'));
        if (body) {
            bin.body = body.value;
        }
    }).catch(function(err) {
        // do something with the error
        console.log(err);
    });
}
```    

Now that we have set our `bin` object with the right values, we can update the view, depending on whether or not we have to display the editor.

Here is the final version of our `loadBin` function.

```Javascript
function loadBin (url, showEditor) {
    Solid.web.get(url).then(function(g) {
        // set url
        bin.url = url;
        // add title
        var title = g.any($rdf.sym(url), DCT('title'));
        if (title) {
            bin.title = title.value;
        }
        // add body
        var body = g.any($rdf.sym(url), SIOC('content'));
        if (body) {
            bin.body = body.value;
        }

        if (edit) {
            document.getElementById('edit-title').value = bin.title;
            document.getElementById('edit-body').innerHTML = bin.body;
            document.getElementById('submit').setAttribute('onclick', 'update()');
            document.getElementById('edit').classList.remove('hidden');
        } else {
            document.getElementById('view-title').innerHTML = bin.title;
            document.getElementById('view-body').innerHTML = bin.body;
            document.getElementById('view').classList.remove('hidden');
            document.getElementById('edit').classList.add('hidden');
        }
    }).catch(function(err) {
        // do something with the error
        console.log(err);
    });
}
```

**Updating bins**

Updating bins is very similar to creating new ones. The only difference is that we use the URI of an existing bin, which we then pass to Solid.js' `Solid.web.put` function.

```Javascript
function update () {
    bin.title = document.getElementById('edit-title').value;
    bin.body = document.getElementById('edit-body').value;

    var g = new $rdf.graph();
    g.add($rdf.sym(''), DCT('title'), bin.title);
    g.add($rdf.sym(''), SIOC('content'), bin.body);
    var data = new $rdf.Serializer(g).toN3(g);

    Solid.web.put(bin.url, data).then(function(meta) {
        // view
        window.location.search = "?view="+encodeURIComponent(meta.url);
    }).catch(function(err) {
        // do something with the error
        console.log(err);
    });
}
```

**Routing different app states**

Great, so now we have all the important functions in place. Let's put everything together! We know we can use the app to create and also to view bins. The final step now is to add the app logic which handles these different states. For that we need a utility function that parses the current URL and returns different query parameters.

You can go ahead and just paste the following function in your app.

```Javascript
// Utility function to parse URL query string values
var queryVals = (function(a) {
    if (a == "") return {};
    var b = {};
    for (var i = 0; i < a.length; ++i)
    {
        var p=a[i].split('=', 2);
        if (p.length == 1)
            b[p[0]] = "";
        else
            b[p[0]] = decodeURIComponent(p[1].replace(/\+/g, " "));
    }
    return b;
})(window.location.search.substr(1).split('&'));
```

You can call this function every time you need to check that a query parameter is present and to get its value.

Next we can go ahead and create an `init()` function where we handle our "routing". This function is in charge of parsing the current URL and either display the editor or load the viewer. It also sets the `onclick()` event on the submit button, depending on whether the editor is used to create a new bin or to update an old one.

```Javascript
function init() {
    if (queryVals['view'] && queryVals['view'].length > 0) {
        loadBin(queryVals['view']);
    } else if (queryVals['edit'] && queryVals['edit'].length > 0) {
        loadBin(queryVals['edit'], true);
    } else {
        document.getElementById('submit').setAttribute('onclick', 'publish()');
        document.getElementById('edit').classList.remove('hidden');
    }
}
```

This function will be called at the bottom of our app, after all the other functions have been defined.

**UI/HTML**

The UI is quite minimalistic. A couple of divs, one for the editor and one for the editor. The current example lacks CSS definitions for some classes (e.g. `hidden`), which you can get from the Github repo (link below).

```html
<div class="content center-text" id="view" class="hidden">
    <h1 id="view-title"></h1>
    <br>
    <div id="view-body"></div>
</div>

<div class="content center-text" id="edit" class="hidden">
    <input type="text" id="edit-title" class="block" placeholder="Title...">
    <br>
    <textarea id="edit-body" class="block" placeholder="Paste text here..."></textarea>
    <br>
    <button id="submit" class="btn">Publish</button>
</div>
```

This is it, you're all set! You can use the following link for the source files of the full example app - [https://github.com/solid/solid-tutorial-pastebin/](https://github.com/solid/solid-tutorial-pastebin/)

See also ["user posts a note" example](https://github.com/solid/solid-spec#example).
