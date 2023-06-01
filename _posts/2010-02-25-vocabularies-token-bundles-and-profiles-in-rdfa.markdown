---
layout: post
title: "Vocabularies, token bundles and profiles in RDFa"
date: 2010-02-25
comments: true
tags:
 - thought
 - rdfa
---

A number of discussions are taking place in the new [W3C RDFa Working Group][1]
about how to enable authors to use tokens in place of URIs. How do we avoid
conflicts if anyone can define their own tokens? This post looks at how this
might be achieved.

<!-- more -->

(The following discussion continues some of the themes from last year's post
[Tokenising the Semantic Web][2].)

### A contract between consumer and publisher

The first thing to say is that what we are talking about is allowing publishers
to establish a contract with the page consumer.

Looking at the vocabulary problem this way frees us from having to assume that
some document will be downloaded to find out how to process everything -- the
so-called *follow your nose* issue which I'll come back to.

Instead, at the most fundamental level we simply want to allow Google (for example)
to be able to say to authors, "if you use some previously agreed upon 'switch' in
your documents, then we'll interpret your RDFa in some agreed upon way".

I begin with this because it establishes that we are looking for two things:
primarily a way to identify a set of tokens (i.e., a way to provide that 'switch'),
and only secondarily are we looking for a way to set up retrievable tokens. I'm
emphasising this because I feel in previous discussions there has been a tendency
to look *only* for a solution to the second issue -- i.e., the 'follow-your-nose'
solution -- and this has the tendency to prematurely eliminate simpler solutions
form consideration.

### `@profile`

I've always favoured using the [HTML 4.01 profile attribute][3] for this contract,
since it already exists, and its meaning in HTML 4.01 is very similar to what we
need.

In particular, the HTML 4.01 spec says:

> User agents may use this URI in two ways:
> 
> *   As a globally unique name. [...]
> 
> *   As a link. [...]

This is ideal, since it says that we *may* do FYN, but we also might simply 'know'
intrinsically what to do. In other words, it gives us the opportunity to do both,
which is crucial as we'll see.

So what would a Google example look like, if we used `@profile` as the 'switch'?

Taking an example from the [Rich Snippets tutorial][4], it might look like this
(note that the original sample uses `@property="url"`, rather than `@rel` so I've
retained that here):

``` html
<div typeof="Person">
    <span property="name">John Smith</span>
    <span property="nickname">Smithy</span>
    <span property="url">http://www.example.com</span>
    <span property="affiliation">ACME</span>
    <span rel="address">
        <span property="locality">Albuquerque</span>
    </span>
    <span property="title">Engineer</span>
    <a href="http://darryl-blog.example.com/" rel="friend">Darryl</a>
</div>
```

Note that all I've done is to:

1.  make up a URI for a profile; of course the actual URI is up to Google, not me,
but as you can see, for the sake of discussion, I'm pretending that there are
different versions of a profile, and so I've built my imaginary URI accordingly;
2.  remove the namespace prefixes from the terms in @property and @rel. Of course
a profile could do all sorts of mappings to get back to the desired URIs.

Now, when Google retrieve a document that has this profile defined, they can assume
the contract I mentioned -- they can assume that the publisher is writing this:

``` html
@typeof="Person"
```

as a shorthand for this:

``` html
@typeof="http://rdf.data-vocabulary.org/#Person"
```

#### Omitting `@profile`

It's often asked in these discussions, 'but what if the author doesn't include the
profile attribute...aren't we left with unusable tokens?' It's a fair question,
because after all the tokens now really don't mean anything.

But from an RDFa standpoint it is no different to the situation when authors omit
namespace declarations. Here's the original rich snippets example, but without
the `v` prefix declaration:

``` html
<div typeof="v:Person">
    <span property="v:name">John Smith</span>
    <span property="v:nickname">Smithy</span>
    <span property="v:url">http://www.example.com</span>
    <span property="v:affiliation">ACME</span>
    <span rel="v:address">
        <span property="v:locality">Albuquerque</span>
    </span>
    <span property="v:title">Engineer</span>
    <a href="http://darryl-blog.example.com/" rel="friend">Darryl</a>
</div>
```

As you can see, it's no more meaningful than our 'missing profile' example.

#### Mixing profiles

So, we've established that `@profile` can be used as a 'switch' to enable a
whole set of tokens, and all that an organisation needs to do is to explain
clearly what mapping will take place between tokens and URIs, when that switch
is used; as long as authors know how their data is going to be interpreted,
then organisations like Google and Yahoo! can simply publish a profile URI and
indicate what it means.

But what if our document-publisher adds another profile to their document?
`@profile` is defined in HTML 4.01 to be a space-separated list of values,
so this is already legitimate, but what should happen when we get this:

``` html
<body
 xmlns:Person="http://rdf.data-vocabulary.org/#Person"
 xmlns:name="http://rdf.data-vocabulary.org/#name"
 .
 .
 .
>
    <!-- Some documentation about the profile here, to make it human readable. -->
</body>
```

Making a profile into an HTML document gives us the possibility of profiles loading
other profiles. For example, recall that I pretended that the 2010 version of this
profile included the CC profile; this 'import' might be achieved as easily as this:

``` html
<body
 xmlns:Person="http://rdf.data-vocabulary.org/#Person"
 xmlns:name="http://rdf.data-vocabulary.org/#name"
 .
 .
 .
```

If profiles could import other profiles, it would also make versioning incredibly easy.
For example, if our fictitious 2010 profile were to enhance the 2009 version by adding
both the CC profile and a new predicate called 'foo', we could simply declare the 2010
profile as including the 2009 profile, rather than expressing all of the tokens again:

``` html
<body
 xmlns:foo="http://rdf.data-vocabulary.org/#foo"
>
    <!-- Some documentation about the profile here, to make it human readable. -->
</body>
```

#### JSON profiles

For various reasons I think the basic level of conformance for expressing token bundles
should be to use JSON. Given that we are only concerned with name/value pairs, then all
we need is this:

``` javascript
{
    "Person": "http://rdf.data-vocabulary.org/#Person",
    "name": "http://rdf.data-vocabulary.org/#name",
    .
    .
    .
}
```

However, since the most common application of this will be to retrieve tokens in browsers,
we should really use [JSONP][5] so that the browser gets a chance to retrieve the data.
Something like this would do it:

``` javascript
document.meta.addMappings({
    "Person": "http://rdf.data-vocabulary.org/#Person",
    "name": "http://rdf.data-vocabulary.org/#name",
    .
    .
    .
});
```

This could easily be achieved by our theoretical parser calling out to a theoretical
server with the following URL:

    http://rdf.data-vocabulary.org/profile/2010.json?jsonp=document.meta.addMappings

### Handling conflicts

If a generic parser is able to load token bundles from all quarters, then we now have
a situation where the short-form tokens that authors are able to use can be ambiguous.

There are two issues here, one is to decide what should happen in a conflict situation,
and the other is how to let authors know.

#### Last wins

The first issue -- what should happen when a conflict occurs -- *could* be resolved by
saying that duplicate tokens are an error, but I think it's obvious that this is the
worst approach we could take. To avoid errors we'd need to establish central registries,
and then we'd have immediately flipped from a decentralised solution to a centralised one.

It would be better to say that tokens are loaded in the order in which they appear in the
document, and newer tokens simply override older ones.

Not only is this clear, but it would also allow for scenarios such as one organisation
creating a profile that makes a small number of modifications to someone else's profile,
by overriding certain tokens. And it would also allow minor changes to be made to a
profile without having to issue a whole new profile:

``` html
<div typeof="Person">
    <span property="http://rdf.data-vocabulary.org/#name">John Smith</span>
    <span property="nickname">Smithy</span>
    .
    .
    .
</div>
```

The author could of course reverse the order of the `@profile` URIs, but this might start
getting a little difficult to keep track of whilst editing.

A better solution is to make use of '@profile everywhere', and to scope the token bundle.
An author can then place their `@profile` declaration closer to the mark-up that it relates
to. In the following example we have the FOAF profile as our default throughout the document,
but when we insert a Google-specific notion of a `Person` we also put the profile identifier
on the root element:

``` html
<div typeof="Person" profile="http://rdf.data-vocabulary.org/profile/2009">
    <span property="name">John Smith</span>
    <span property="nickname">Smithy</span>
    .
    .
    .
</div>
```

Now it's very clear what is going on, since we've already said that 'last wins'.

Another scenario is the reverse of the one we've just seen; the author actually wants to use
the FOAF property inside *this* mark-up. To achieve this they could once again use `@profile`
on the element to override the previously set profile:

``` html
<div typeof="Person" profile="http://rdf.data-vocabulary.org/profile/2009">
    <span property="name" profile="http://xmlns.com/foaf/0.1/profile">John Smith</span>
    .
    .
    .
</div>
```

Alternatively, they could spell out the URI in full, as we saw before. But another interesting
approach is to use another token, and there are a couple of ways this could be done.

One way would be for authors to simply define their own token in the document. In this example
the author creates `foaf-name` to distinguish it from `name`:

``` html
<div typeof="Person" profile="http://rdf.data-vocabulary.org/profile/2009">
    <span property="foaf-name">John Smith</span>
    .
    .
    .
</div>
```

However, a better way would be for the profile's authors to have included a suitable prefix in
the profile itself. For example, Google's profile might look like this (note the declaration of
`v`):

``` html
<body
 xmlns:Person="http://rdf.data-vocabulary.org/#Person"
 xmlns:name="http://rdf.data-vocabulary.org/#name"
 .
 .
 .
>
    <!-- Some documentation about the profile here, to make it human readable. -->
</body>
```

Now not only are `Person` and `name` available to authors who use this profile, but so is `v`.
This means that authors can use `v:Person` if they ever need to be clear about what they are
doing.

### Conclusion

The `profile` attribute is an obvious way to allow data publishers and data consumers to agree
on what is being published. It is sufficiently open to allow it to be used in both RDFa and
Microformats, but its definition also leaves open a way to implement 'follow your nose' token
retrieval.

 [1]: http://www.w3.org/2010/02/rdfa/
 [2]: /blog/2009/04/30/tokenising-the-semantic-web
 [3]: http://www.w3.org/TR/html401/struct/global.html#profiles
 [4]: http://www.google.com/support/webmasters/bin/answer.py?answer=146646
 [5]: http://en.wikipedia.org/wiki/JSON#JSONP