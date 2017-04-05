# Building Decentralized Applications for the Social Web

## WWW 2016 Tutorial, Tuesday 12 April

### Introduction

This morning we will introduce you to the ideas behind personal datastores and
how to build applications that use data in people's personal datastores using
the Solid protocols. After the coffee break we'll do a walkthrough of an example
application, and this afternoon you're free to build your own applications.

### Decentralization: Motivation and Principles

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

Now imagine instead, if *users* had full control over their data (all of the
data that currently belongs to siloed service providers), and could select which
apps were allowed to access it (using standardized APIs). This is one of the
core goals of the Solid project.

#### Interoperability

Of course, separating applications from data (with the aim of giving users more
choices in terms of which apps they can use) is only possible when that data
is written in standard, mutually-intelligible formats.

By focusing on the re-use of standard protocols, formats and vocabularies, and
by providing a common data access API (as well as an Access Control List
format), Solid enables and encourages interoperability.

### Protocols and APIs

We mentioned that applications that read, create and manipulate data are
completely decoupled from where the data is stored. This means that applications
and datastores (servers) need a common protocol to speak to each other.

Datastores use a RESTful API, and store data as RDF. Every *resource* in the
data has a URL, whether this is a document, an image, a person, an abstract
concept, or a relationship between two resources. Applications can use HTTP to
create, read, update and delete resources in a datastore. Resources can be
organized hierarchically in *containers*, which can be thought of as
folders/directories.

Simply, to get the data in any resource (the attributes and their values),
an application does a GET request on the URL of the resource. For all of the
resources in a container, a GET request on the container returns a list. For an
application to create a new resource in a container, make an HTTP POST request
to the container itself with the attributes of that resource, and an optional
slug. To update a resource (or a container) use PUT or PATCH and to delete use
DELETE.

| Action | Path | Description |
|--------|------|------------------------|
| `GET` | `/path/to/resource` | Retrieve a resource |
| `GET` | `/path/to/container/` | List of resources in a container |
| `POST` | `/path/to/parent/` | Create a new resource in container |
| `PUT` | `/path/to/resource` | Replace a resource |
| `PATCH` | `/path/to/resource` | Update a resource |
| `DELETE` | `/path/to/resource` | Delete a resource |

The data that is sent and retrieved is RDF; the RDF serialization we use by
default is Turtle. Don't worry if you're not familiar with this format, we're
going to show you handy libraries for working with it. If you want to find out
more about Linked Data principles you can see **[Linked Data
101](http://rhiaro.github.io/sws/)** for a quick run down.

This API is a W3C standard called [Linked Data Platform
(LDP)](http://www.w3.org/TR/ldp/) (see also the
[LDP Primer](http://www.w3.org/TR/ldp-primer/)).

### Schemas and Vocabularies

You're used to describing data in a database with a schema, a set of terms that
describe attributes of a data item for which you can set values. For global
interoperability on the Web, we all need to find a way to cooperate when
choosing schemas to represent our data.

Fortunately many people publish RDF vocabularies which cover different domains,
and the idea is to reuse existing ones as much as possible. All terms in a
vocabulary (just like any other data item) have their own URL. This ensures that
when two people use the same term they can use a globally unique identifier to
show they mean the same thing.

* **Vocabularies** or ontologies are a set of words and phrases that someone has
decided to put together for the purposes of describing a particular topic. They
usually consist of *classes* and *properties*.

* **Classes** are used to say that `<some-thing>` is of type `<other-thing>`.
For example: `<http://rhiaro.co.uk/about#me> type <Person>`. (Person is the
class).

* **Properties** are the attributes. In the previous example `type` is
the property.

* **Anyone can publish** a vocabulary, and you can use vocabularies that anyone
else has published. There are lots out there on the web, some better than
others.

There are well-known, stable vocabularies. Examples include: RDF and RDFS
(which provide the basics for describing anything), FOAF (for describing people
and their friends), Dublin Core (for describing publications, mostly),
[schema.org](https://schema.org) (covers a broad variety of things).

A good place to start looking for domain-specific terms to reuse is
[LOV](http://lov.okfn.org).

Vocabularies specifically for social applications you might be interested in
include:

* [FOAF](http://xmlns.com/foaf/0.1/) (friend-of-a-friend) - people and the
  relations between them).
* SIOC (semantically-interlinked online communities) - some basic social stuff,
  simple but a little dated and based on forums / message board kinds of data.
* [ActivityStreams 2.0](https://www.w3.org/TR/activitystreams-core/) - an emerging W3C
  standard for describing most of the kinds of social data we see on the web
  today.

### Users, Authentication and Access Control

Solid uses WebID URLs as unique user IDs. This means that no matter where on the
web a user is interacting, they have one identifier which they can use to sign
into their personal datastore.

* Not just humans allowed: Apps and services can have their own WebIDs, or
  borrow a user's WebID when needed (through delegation)

* A WebID URL, when fetched, must return a valid WebID User Profile
  (a FOAF document in RDF format)

* Browser-side certificates are currently used for authentication, instead of
  passwords

* You need a WebID profile and related certificate on a Solid-compatible storage
  provider to use any Solid-based apps.

How can we decide who has access to a particular resource? Since we have
identities, we could use them to define who has read or write access to
resources. These lists are called ACL (Access Control List), and they follow the
[Web Access Control spec](https://github.com/solid/web-access-control-spec)

## Pastebin Example

This example will walk you through the steps required to build a typical Solid
app. You will learn how to:

- create new resources (pastebins)
- view created bins
- update existing bins

### Preparing the App

Before we start writing the functions that create and view bins, we need to
decide how the app will work. For instance, we know that we will use the app to
create new bins, to view existing ones and also to update them.

The easiest way to separate these different app functionalities, is to separate
the states by using query parameters -- i.e. `?view=...`, `?edit=...`.

Another important step we need to do right now is to add the required
dependencies. These are [rdflib.js](https://github.com/linkeddata/rdflib.js) and
[solid.js](https://github.com/solid/solid.js).

### Local Data Structure

Next, we can define how we want to structure our bins. For example, a bin could
have a title and body (content), but also a URI (useful later for updates).

```js
// Bin structure
var bin = {
    url: '',
    title: '',
    body: ''
}
```

### Picking the Vocabularies

For this particular app, we can go with two very common vocabularies, `SIOC` and
`Dublin Core Terms`. You can see their usage in the `load()` function, for
example. Specifically, we'll be using the `title` predicate (referred to as
`vocab.dct('title')` in the code), and the `content` predicate (as
`vocab.sioc('content')`).

### Deciding Where to Store New Bins

To create bins we have to define a default container to which we POST the bin.
For now we can create a global variable called `defaultContainer`. The value of
this variable can be a `bins`container on your account, which you have created
with Warp.

```js
var defaultContainer = 'https://user.databox.me/Public/bins/'
```

### UI/HTML

The UI is quite minimalistic. A couple of divs, one for the viewer and one for
the editor. The current example lacks CSS definitions for some classes (e.g.
`hidden`), which you can get from the Github repo (link below).

```html
<div class="content center-text hidden" id="view">
    <h1 id="view-title"></h1>
    <br>
    <div id="view-body"></div>
</div>

<div class="content center-text hidden" id="edit">
    <input type="text" id="edit-title" class="block" placeholder="Title...">
    <br>
    <textarea id="edit-body" class="block" placeholder="Paste text here..."></textarea>
    <br>
    <button id="submit" class="btn">Publish</button>
</div>
```

Now let's prepare a few functions that make the bread and butter of our app.

### Creating new bins

Creating new bins is easy. It involves using `solid.js` to post the bin data to
the defaultContainer. We can create a `publish()` function which will handle
this step. This function will read the value of the title and body elements from
the editor.

```js
function publish () {
    bin.title = document.getElementById('edit-title').value
    bin.body = document.getElementById('edit-body').value
}
```

Next, we will create a new graph object, in which we'll add the two triples, one
for the title and one for the body. We will also store the serialized value of
the graph in a new `data` variable.

```js
function publish () {
    bin.title = document.getElementById('edit-title').value
    bin.body = document.getElementById('edit-body').value

    var graph = $rdf.graph()
    var thisResource = $rdf.sym('')
    graph.add(thisResource, vocab.dct('title'), $rdf.lit(bin.title))
    graph.add(thisResource, vocab.sioc('content'), $rdf.lit(bin.body))
    var data = new $rdf.Serializer(graph).toN3(graph)
}
```

Finally, we will use `solid.js` to POST the new bin to the server. If the POST
succeeded, we will update the URL bar to the viewer. Else, we will have to deal
with the error.

Here is the final function.

```js
function publish () {
    bin.title = document.getElementById('edit-title').value
    bin.body = document.getElementById('edit-body').value

    var graph = $rdf.graph()
    var thisResource = $rdf.sym('')
    graph.add(thisResource, vocab.dct('title'), $rdf.lit(bin.title))
    graph.add(thisResource, vocab.sioc('content'), $rdf.lit(bin.body))
    var data = new $rdf.Serializer(graph).toN3(graph)

    solid.web.post(defaultContainer, data).then(function(meta) {
        // view
        window.location.search = "?view="+encodeURIComponent(meta.url)
    }).catch(function(err) {
        // do something with the error
        console.log(err)
    });
}
```

### Reading/viewing bins

Now that we have a way to create new bins, let's create a corresponding function
that reads and displays bins.

We can use `solid.js` to fetch the contents of a bin. Let's call this fetching
function `load`. Our function will take two parameters - one is the URL of the
bin, and the other one is a flag that indicates whether we need to display the
editor or not.

```js
function load (url, showEditor) {
    solid.web.get(url).then(function(response) {

    }).catch(function(err) {
        // do something with the error
        console.log(err)
    });
}
```

The function `solid.web.get()` returns a response on success, from which we
can get a parsed graph object (parsed via
[rdflib.js](https://github.com/linkeddata/rdflib.js/)). This graph object has a
few methods that allow us to query the graph for specific triples - i.e.
`graph.any()`. We'll use this method to get the title and body of a bin.

```js
function load (url, showEditor) {
    solid.web.get(url).then(function(response) {
        var graph = response.parsedGraph();
        // set url
        bin.url = response.url;
        var subject = $rdf.sym(response.url);
        // add title
        var title = graph.any(subject, vocab.dct('title'));
        if (title) {
            bin.title = title.value;
        }
        // add body
        var body = graph.any(subject, vocab.sioc('content'));
        if (body) {
            bin.body = body.value;
        }
    }).catch(function(err) {
        // do something with the error
        console.log(err)
    });
}
```

Now that we have set our `bin` object with the right values, we can update the
view, depending on whether or not we have to display the editor.

Here is the final version of our `load` function.

```js
function load (url, showEditor) {
    solid.web.get(url).then(function(g) {
        var graph = response.parsedGraph();
        // set url
        bin.url = response.url;
        var subject = $rdf.sym(response.url);
        // add title
        var title = graph.any(subject, vocab.dct('title'));
        if (title) {
            bin.title = title.value;
        }
        // add body
        var body = graph.any(subject, vocab.sioc('content'));
        if (body) {
            bin.body = body.value;
        }

        if (showEditor) {
            document.getElementById('edit-title').value = bin.title
            document.getElementById('edit-body').innerHTML = bin.body
            document.getElementById('submit').setAttribute('onclick', 'update()')
            document.getElementById('edit').classList.remove('hidden')
        } else {
            document.getElementById('view-title').innerHTML = bin.title
            document.getElementById('view-body').innerHTML = bin.body
            document.getElementById('view').classList.remove('hidden')
            document.getElementById('edit').classList.add('hidden')
        }
    }).catch(function(err) {
        // do something with the error
        console.log(err);
    });
}
```

### Updating bins

Updating bins is very similar to creating new ones. The only difference is that we use the URI of an existing bin, which we then pass to `solid.js`' `solid.web.put()` function.

```js
function update () {
    bin.title = document.getElementById('edit-title').value
    bin.body = document.getElementById('edit-body').value

    var graph = $rdf.graph();
    var thisResource = $rdf.sym('');
    graph.add(thisResource, vocab.dct('title'), bin.title);
    graph.add(thisResource, vocab.sioc('content'), bin.body);
    var data = new $rdf.Serializer(graph).toN3(graph);

    solid.web.put(bin.url, data).then(function(meta) {
        // view
        window.location.search = "?view="+encodeURIComponent(meta.url)
    }).catch(function(err) {
        // do something with the error
        console.log(err)
    });
}
```

### Routing different app states

Great, so now we have all the important functions in place. Let's put everything
together! We know we can use the app to create and also to view bins. The final
step now is to add the app logic which handles these different states. For that
we need a utility function that parses the current URL and returns different
query parameters.

You can go ahead and just paste the following function in your app.

```js
// Utility function to parse URL query string values
var queryVals = (function(a) {
    if (a == "") return {}
    var b = {}
    for (var i = 0; i < a.length; ++i)
    {
        var p=a[i].split('=', 2)
        if (p.length == 1) {
            b[p[0]] = ""
        } else {
            b[p[0]] = decodeURIComponent(p[1].replace(/\+/g, " "))
        }
    }
    return b
})(window.location.search.substr(1).split('&'))
```

You can call this function every time you need to check that a query parameter
is present and to get its value.

Next we can go ahead and create an `init()` function where we handle our
"routing". This function is in charge of parsing the current URL and either
display the editor or load the viewer. It also sets the `onclick()` event on the
submit button, depending on whether the editor is used to create a new bin or to
update an old one.

```js
function init() {
    if (queryVals['view'] && queryVals['view'].length > 0) {
        load(queryVals['view'])
    } else if (queryVals['edit'] && queryVals['edit'].length > 0) {
        load(queryVals['edit'], true)
    } else {
        document.getElementById('submit').setAttribute('onclick', 'publish()')
        document.getElementById('edit').classList.remove('hidden')
    }
}
```

This function will be called at the bottom of our app, after all the other
functions have been defined.

This is it, you're all set! You can use the following link for the source files
of the full example app:
[solid-tutorial-pastebin](https://github.com/solid/solid-tutorial-pastebin/)

See also ["user posts a note"
example](https://github.com/solid/solid-spec#example).
