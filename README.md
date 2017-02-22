
# Auth-Boss
Become an Auth Boss. Learn about different Auth methodologies.

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents** 

- [Introduction](#introduction)
  - [Who](#who)
  - [Why](#why)
  - [How](#how)
- [Examples cases](#examples-cases)
- [General Best Practices](#general-best-practices)
- [Terminology](#terminology)
  - [Terms](#terms)
    - [Encryption](#encryption)
    - [HTTP](#http)
    - [HTTPS](#https)
    - [TLS / SSL](#tls--ssl)
    - [Cookies](#cookies)
    - [Sessions / Session Management](#sessions--session-management)
- [Methodologies](#methodologies)
  - [HTTP Basic Authentication](#http-basic-authentication)
  - [HTTP digital access authentication](#http-digital-access-authentication)
  - [Session based Authentication](#session-based-authentication)
  - [OAuth](#oauth)
  - [OpenId](#openid)
  - [Token based Authentication](#token-based-authentication)
  - [JWT](#jwt)
- [Resources and Footnotes](#resources-and-footnotes)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->
# Introduction

The intention of this document is to chronicle and catalogue the methodologies of _authentication_ on the web. By _authentication_ I am referring to, in the simplest sense, the process of creating a system through which user's can "login" to an online service and be given access to otherwise protected resources.

This quote might better sum up what I'm trying to cover:

> Client authentication involves proving the identity of a client (or user) to a server on the Web. We will use the term “authentication” to refer to this problem.[1]

***

## Who
I am a self-taught developer with a passion for open source tech, learning, mentoring, and knowledge-sharing. 

## Why
I am writing this guide, because I felt woefully at a loss for finding resources that explained safe, smart and current technologies and methods for authentication in a simple and concise manner. I decided it was time to put my research hat on and do some leg work. 

## How
My writing style aims to be simple, terse, and to leverage layperson terms over jargon and tech-words. 

**Disclaimer** : This document does not serve as an all-encompassing catalogue of all authentications methods for the web; nor does this document purport to provide the "best" methods of authentication.

**Regarding mistakes, errors, and inconsistencies**: I am bound to make mistakes and miss many things. I AM NOT A SECURITY EXPERT. If you see something that could be improved make a pull request explaining what I missed + what I can improve. Bonus points if you're not a jerk about it.

# Examples cases  

I will use a single example across this document to illustrate a login flow both in terms of what is happening on the "client" (the user in front of their computer) and what is occurring on the "server" (behind the scenes, so to speak).

Our example cases will feature a new imaginary friend: `Beorn`. Beorn likes knitting, and often goes to `http://www.knittingworld.com` to buy quilting supplies. Beorn has a user account on `knittingworld` and we will see him continuously login across the examples in this document. 

# General Best Practices

Before jumping into the technologies that exist for managing authentication, let's consider some best practices / things you should never do.

Some of these items following may not directly pertain to login/authentication/user signups, but are generally useful and good to know. 

- Never store passwords as plain text in a database.
- Never write your own hashing algorithm (unless you're really really smart)
- Do not write your own authentication technology, again unless you're really really smart. 
- Use [HTTPS](https://en.wikipedia.org/wiki/HTTPS). 

**Some quotes**

> We also found that many Web sites would design their own authentication mechanism to provide a better user experience. Unfortunately, designers and implementers often do not have a background in security and, as a result, do not have a good understanding of the tools at their disposal[2]

> the best defense against such attacks is to use SSL with some form of client authentication[2]

# Terminology

There is a quite a bit of jargon in this sub-set of web development. At the time of learning about these things, I found myself increasingly frustrated with the seemingly limitless number of jargon / shop talk, tech terms that I didn't understand ( and my respective increasing laziness finally encouraged me to write this doc and learn what the hell this junk is). Below is a list of terms that you will see again and again when it comes to authentication. Each word will link to a defintion I have found, but the description written will be my attempts to create a concise summary / defintion in lay-person terms.

## Terms 

### [Encryption](https://en.wikipedia.org/wiki/Encryption)

From Wiki:
> Encryption is the process of encoding messages or information in such a way that only authorized parties can access it.

### [HTTP](https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol)

Stands for Hyper Text Transfer Protocol. This is a big one, and I'm not sure I can do it justice to explain simply without being reductive. The web is built around HTTP - it's the technology used to communicate between web servers and users. 

Your browser is considered an HTTP client - because it sends _requests_ to an HTTP server. There are many different kind of _requests_ that your client can make — you may have heard of some of the most popular ones — `GET` `POST` `PUT` and `DELETE`.

HTTP servers send _responses_ to your browser — the client — these responses come in the form of _Resources_. Resources could be (but are not limited to): HTML files, images, text, JSON, and more. You can (kind of) think of `resources` as "files" that come back from a server.

_Other Links on this topic_:

- [HTTP Made Really Easy](http://www.jmarshall.com/easy/http/#whatis)
- [RFC2616 ](https://tools.ietf.org/html/rfc2616) — this is a massive document-spec-thing on HTTP. It is listed as outdated, but also lists the documents that have superseded it.
- [rfc7235](https://tools.ietf.org/html/rfc7235) — http documentation specifically regarding Authentication. Another large, and (in my opinion) scary looking doc. I have not read this, nor the link above.


### [HTTPS](https://en.wikipedia.org/wiki/HTTPS)

HTTPS is HTTP with more security. It goes hand in hand with `SSL / TLS` Originally made popular for payment transaction over the internet, it has become much more popular lately. You may recognize https as `that green text that shows up to the left of my url in the browser`; often accompanied by an icon of a lock, or something of the sort.

HTTPS is built on top of HTTP but adds a layer of encryption using SSL/TLS to protect traffic between browsers and servers.

HTTPS encrypts the information that's sent along with your HTTP requests. This is particular important when we start talking about authentication!

From wikipedia:

> HTTPS creates a secure channel over an insecure network. This ensures reasonable protection from eavesdroppers and man-in-the-middle attacks, provided that adequate cipher suites are used and that the server certificate is verified and trusted.

### [TLS / SSL](https://en.wikipedia.org/wiki/Transport_Layer_Security)

TLS and SSL are a cryptographic protocol. TLS and SSL encrypt the data you send across a network — it is designed to prevent people from "eavesdropping" or tampering with the data that you are sending.

There are some useful videos on youtube that help to explain some of these complex topics, but unfortunately I can't speak to the veracity of them all. This [video by MIT opencourseware](https://www.youtube.com/watch?v=S2iBR2ZlZf0) looks relatively useful!

### [State](https://en.wikipedia.org/wiki/State_(computer_science))

This document will make reference to the terms `state` `stateful`, `stateless` and `piece of state`. These are broad terms, that vary in their definition. For the sake of this article, a "piece of state" or something that is "stateful" is describing a piece of data that is living in memory...somewhere. 

HTTP requests are commonly described as "stateless". When you visit a website and login you are passing some information along with your HTTP request — something that _identifies you_. Whatever method of authentication you need to use to identity yourself has to be "attached" to HTTP requests in someway or another, _because_ you cannot simply store that state within the HTTP protocol itself — it has to take another form that can "ride along" with your HTTP requests, so to speak (as you will see in the rest of this documentation.). 

### [Cookies](https://en.wikipedia.org/wiki/HTTP_cookie)

Cookies, at the very base level, are small pieces of data that get stored on a _user's browser_. Cookies, in contrast to HTTP are _stateful_ — meaning that HTTP cannot store user information, but cookies can. 

A common example of a web cookie: 

You visit `http://knittingworld.com` to buy some nice patterns and materials for your next knitting project. You log in, and add three items to your cart. As you are about to "check out" in the online store and ship the items to your home, you hear a loud "bang!" and realize that you left a can of tuna in the microwave on high. Not good! You close the browser, instantly forgetting about your new quilt goodies,  and  go clean up the mess. After the smell of canned tuna has finally sank into the walls and carpet of your home, you return to your computer and revisit `https://knittingworld.com` ... only to find... your items are still in your cart. How?! Cookies.

There are different kinds of cookies. Some cookies will stick around in your browser for many days, while others will disappear as soon as you close your browser. 

Cookies have played a big role in authentication in the past. Authentication cookies are commonly used by web servers to know that user is logged in or not; allowing a server to know whether or not the client can have access to otherwise protected routes. 

Sometimes cookies are considered nerfarious or insecure (see [cross site scripting](https://en.wikipedia.org/wiki/Cross-site_scripting) or [cross-site requiest forgery](https://en.wikipedia.org/wiki/Cross-site_request_forgery)). This can be the case, but there are also methods that can be taken to increase the security of cookies. For example, you can set a `secure` flag on a cookie meaning it can only be transmitted over an encrypted connection (HTTPS`).

[Persistent cookies](https://en.wikipedia.org/wiki/HTTP_cookie#Persistent_cookie) sometimes carry a negative connotation - in that they may be used by advertisers to record information about a user's web habits. On the other hand, they are also often used so that a user does not have to constantly reenter their login credentials everytime a user visits a site. 

You can see what cookies are sent with requests by navigating to your (in chrome) developer tools and opening the `network tab`. Refreshing your page will display a list of incoming resources, of which you can select one and view the `headers` for the page. Scroll through the list and see if you find any cookies!

### [Sessions](https://en.wikipedia.org/wiki/Session_(web_analytics)) / [Session Management](https://www.owasp.org/index.php/Session_Management_Cheat_Sheet)

Rather than attempt to begin with my simplistic attempt at describing Sessions, I will quote from the OWASP session management cheat sheet:

> A web session is a sequence of network HTTP request and response transactions associated to the same user. Modern and complex web applications require the retaining of information or status about each user for the duration of multiple requests. Therefore, sessions provide the ability to establish variables – such as access rights and localization settings – which will apply to each and every interaction a user has with the web application for the duration of the session.

You can find a step-by-step example of session-based authentication in the `Methodologies` section below.

More links on sessions:

[How does a web session work](http://machinesaredigging.com/2013/10/29/how-does-a-web-session-work/)

[What are web sessions?](http://stackoverflow.com/questions/3804209/what-are-sessions-how-do-they-work)


# Methodologies

It's time to get into the actual body of text on writing authentication. Following is a list of technologies for establishing an authentication solution. This is not an exhaustic list.


## [HTTP Basic Authentication](https://en.wikipedia.org/wiki/Basic_access_authentication)

HTTP Basic authentication ( or "Basic Auth") has been around for quite some time. It seems that people tend to use it for it's simplicity and it's wide support across browsers. Here's how it would work in the case of our friend Beorn.

- Beorn goes to `http://knittingworld.com` to get some nice yarn.
- Beorn types in his username and password into a login _form_. This information gets `POST`ed to the server using an XML http request (although you might be more familiar with the term `ajax`  or, `fetch` request).
- The POST request from the client contains _headers_ that have the authorization information (username and password) attached. This header might look something like this: 

`Authorization: Basic QWxhZGRpbjpvcGVuIHNlc2FtZQ==`

- The server goes on to validate the authentication header and determine if a user can proceed to a restricted route or not.

Some important notes on HTTP Basic Authentication

- The example authorization header above does not look like a username and password, but that is because it is base64 encoded. IT IS NOT ENCRYPTED.
- USE HTTPS. If you just use HTTP, authentication credentials are sent to a server as PLAIN TEXT. A user's username and password are being sent over the wire merely as base64 encoded text — which is trivial to decode. By using HTTPS / SSL you are ensuring that data sent from the client to the server is encrypted.
- The use of HTTPS / SSL might make Basic Auth a viable method for your authentication solution but there are still some caveats. 
- There needs to be _some_ sort of caching of a username / password, _because_ HTTP requests are stateless; credentials _have_ to be sent to the server on every request!
- Where are you going to cache a user's password + username? Will it be visible in a cookie? Is that secure? You could perhaps run an encryption algorithm on credentials and store it as a cookie, but it's still public visible in your browser. Continuing on...
- When the password arrives at the server, it will be hashed by your encryption algorithm (which you absolutely should have ) to see if it matches the password in the database. Some people argue that the processing power to encrypt passwords / check on every request is not efficient. I have no say in this matter because I don't know. But consider that idea as you look into other authentication methods.


Links

Some of these links are opinionated. I did not find many articles discussing Basic Auth, although it seems to still be a viable option for companies today. 

[Basic Authentication on OWASP](https://www.owasp.org/index.php/Basic_Authentication)

[Why does stripe use HTTP basic auth with a token instead of a header?](https://www.quora.com/Why-does-Stripe-use-HTTP-Basic-Auth-with-a-token-instead-of-a-header?share=1)

[Why the hell does your api still use basic auth?](http://swaggadocio.com/post/48223179207/why-the-hell-does-your-api-still-use-http-basic)

## [Session based Authentication](https://en.wikipedia.org/wiki/Session_(web_analytics))

Session authentication has been around for a while and seems to be largely advocated. People seem to be moving away from sessions as other options (token based authentication) seem to leverage more modern web architectures (RESTful apis, state-lessness, authentication across more than one API, microservices etc). The key component to Session based authentication, is that a user's login is associated with a _piece of state_ either _in memory_ on the server, or in a key value store (like Redis).

Let's look at an example of our friend Beorn using session based authentication. 

- Beorn goes to `http://knittingworld.com` to get some nice yarn.
- When Beorn logs in, he is sending his credentials to a server.
- When the credentials reach the server, the server, in one way or another, needs to check if Beorn is a user in their database. At this point, Beorn is not yet logged in.
- The server looks in the database to see if Beorn's username and password match a record in the database. It does! Excellent, things are looking good!
- Note: This is where things get a bit tricky. HTTP is _stateless_ ... that means that there is no persistent data that sticks around in HTTP requests — so right now, although Beorn is "in the middle of logging in" — there's nothing (yet) to identify him as "logged in" on future requests. He needs something to _identify_ him on future requests to the server—especially if he's trying to buy things that require him to be logged in. This is where the idea of authentication sessions come in:
- Now that the server knows who Beorn is, and has identified him as a user proper in the database, the server will send him (or "return" ) a cookie — that can identify Beorn as someone who is "logged in" on future requests. 
- Now that Beorn is authenticated and has a session cookie on his browser, he can go check out the yarn that is on sale for logged in members only. When Beorn goes to the page `http://knittingworld.com/great_deals.html` he is making yet another HTTP request - but this time, his session cookie will ride along with the HTTP request to the server, which this time, the server will authenticated based on the cookie matching the in-memory session information (saving the need to run to the database, check passwords, etc — like in Basic Auth)
- When Beorn logs out from `http://knittingworld.com`, his session id will expire and so will his session cookie.
 

## [OAuth](https://en.wikipedia.org/wiki/OAuth)

OAuth is an authentication protocol that allows users to perform an authentication against a server without a password. OAuth has seen various iterations — OAuth 1.0, OAuth 1.0a, and OAuth 2.0.

If you have ever logged into a service by using your Twitter, Google, or Facebook account, then you have used OAuth.

OAuth Providers (Facebook, Google, etc), operate through private, unique, access tokens that provide the means of authentication for your service (the "OAuth client") to allow logins. 

In this process, you will need to register your server as a OAuth Client. This will usually set you up with a `client id`, and `client secret`. Users that login to your service will be reloacted to the OAuth Provider where the user can confirm that they do indeed want to "login" (aka, allow the server they are logging in to, to have access to any required information from the OAuth Provider. )

In the case of our friend Beorn...

- Beorn goes to `http://knittingworld.com` to get some nice yarn.
- Beorn decides to login using his Google account.
- Beorn is prompted to enter his google account credentials (in the case that he already isn't logged in)
- After entering his credentials Google (or whatever OAuth provider he uses) will prompt him to check that he wants to sign into `http://knittinggworld.com` with his Google account.
- After accepting, Beorn is redirected to `http://knittingworld.com`.
- If `knittingworld` needs access to resource regarding Beorn's information it can make requests to a `resource server` (via the OAuth provider) to access then, provided it's access token is valid.


From the [OWASP Authentication Cheat Sheet](https://www.owasp.org/index.php/Authentication_Cheat_Sheet#OAuth)
>The recommendation is to use and implement OAuth 1.0a or OAuth 2.0, since the very first version (OAuth1.0) has been found to be vulnerable to session fixation.

> OAuth 2.0 relies on HTTPS for security and is currently used and implemented by API's from companies such as Facebook, Google, Twitter and Microsoft. OAuth1.0a is more difficult to use because it requires the use of cryptographic libraries for digital signatures. However, since OAuth1.0a does not rely on HTTPS for security it can be more suited for higher risk transactions.

Links

[A Fun explanation of OAuth involving Donuts](http://stackoverflow.com/a/32534239)

## [OpenId](https://en.wikipedia.org/wiki/OpenID)

OpenId is another authentication protocol that (like OAuth) does not require a password. The OpenId [website](http://openid.net/get-an-openid/what-is-openid/) has a very succinct and clear description, in my opinion:

> OpenID allows you to use an existing account to sign in to multiple websites, without needing to create new passwords.

> You may choose to associate information with your OpenID that can be shared with the websites you visit, such as a name or email address. With OpenID, you control how much of that information is shared with the websites you visit.

> With OpenID, your password is only given to your identity provider, and that provider then confirms your identity to the websites you visit.  Other than your provider, no website ever sees your password, so you don’t need to worry about an unscrupulous or insecure website compromising your identity.

Although started in 2005, More recently (2014 ish), OpenId published `OpenId Connect` which is an "interoperable authentication protocol based on the OAuth 2.0 family of specifications" ([Quote](http://openid.net/connect/faq/))

**What's the Difference between OpenId and OAuth?**

OpenId is similar to OAuth, but has some differences. In similarity, OpenId relies on an Identity, `Provider` that interacts with third party `relying parties` (the site you are logging into) to provide authentication credentials. 

Dissimilarly, you might use OAuth to allow the site you are logging into, to have access to your data from the Provider. That might sound scary and confusing but here's a simple example:

- Beorn signs up for twitter. He is going to tweet pictures of the sweet hat he's knitting.
- Beorn doens't know whom to follow, and nobody is following him. Beorn is sad and feels unimportant.
- Twitter prompts Beorn to use OAuth to connect his Google account so that he can import his contacts that also have Twitter.
- Beorn does this, and now he is tweeting non-stop.

**Links**

[What is OpenId?](http://openid.net/get-an-openid/what-is-openid/)
[OpenId Connect FAQ](http://openid.net/connect/faq/)
[OpenId Connect Video](https://www.youtube.com/watch?v=Kb56GzQ2pSk)
[What's the differene between OpenId vs OAuth?](http://stackoverflow.com/questions/3376141/openid-vs-oauth?rq=1)
[What's the differene between OpenId vs OAuth? (different thread)](http://stackoverflow.com/questions/1087031/whats-the-difference-between-openid-and-oauth)
[OpenId according to Dave -- I like this one, albeit dated](https://www.youtube.com/watch?v=xcmY8Pk-qEk)

***

Here's an image of Stack Overflow's login page, that offers many different authentication methods:

![](images/SO_login.png)

## Token based Authentication

Token based authentication has become more popular lately, with the rise of RESTful apis, single-page-apps and micro-services. 

**What is a token?**

A token is just a small piece of data. 

An authentication system that leverages token-based-authentication means that requests a user makes to a server carries a token along with it, to perform authentication logic on. When HTTP requests are made, the token is the piece of data that verifies a user's eligibility to access a resource.

**How is this different from cookie based authentication?**

Token authentication is stateless, whereas session based authentication means that somewhere in your server you have piece of state that is keeping track of users. 

Auth0's blog post [Cookies vs Tokens: The Definitive Guide ](https://auth0.com/blog/cookies-vs-tokens-definitive-guide/) draws out a great step-by-step comparison of the difference in authentication flows between cookies and tokens (so, that means Beorn shall be missing from this example section):

**Session based authentication flow**:

```
1. User enters their login credentials
2. Server verifies the credentials are correct and creates a session which is then stored in a database
3. A cookie with the session ID is placed in the users browser
4. On subsequent requests, the session ID is verified against the database and if valid the request processed
5. Once a user logs out of the app, the session is destroyed both client and server side
```

**Token based authentication flow**:

```
1. User enters their login credentials
2. Server verifies the credentials are correct and returns a signed token
3. This token is stored client-side, most commonly in local storage - but can be stored in session storage or a cookie as well
4. Subsequent requests to the server include this token as an additional Authorization header or through one of the other methods mentioned above
5. The server decodes the JWT and if the token is valid processes the request
6. Once a user logs out, the token is destroyed client-side, no interaction with the server is necessary
```

A key take away is that tokens are stateless. A back-end server doesn't need to keep a record of tokens, or of currently active sessions.

**Wow token's sound cool. Are they better than session based auth?**

You're asking the wrong person bub. I'm just writing this to help my brain figure things out. Like most things on the internet there are lots of people with strong opinions one way or another. I'm doing my best to make it the case that you won't find those here. (Although many of the links I've posted from my research are full of strong opinions or are financially biased)

For more disclaimers visit the `disclaimer` section above. 

**Links**

[Token Based Authentication - Implemenation Demonstration - W3](https://www.w3.org/2001/sw/Europe/events/foaf-galway/papers/fp/token_based_authentication/)
[What is token based Authentication - SO](http://stackoverflow.com/questions/1592534/what-is-token-based-authentication)
[Token Based Authentication Made Easy](https://auth0.com/learn/token-based-authentication-made-easy/#!)
[The Ins and Outs of Token Based Authentication](https://scotch.io/tutorials/the-ins-and-outs-of-token-based-authentication)
[Cookies vs Tokens: The Definitive Guide (opinionated)](https://auth0.com/blog/cookies-vs-tokens-definitive-guide/)


## JWT


# Resources and Footnotes

[1 - Dos and Don’ts of Client Authentication on the Web](https://pdos.csail.mit.edu/papers/webauth:sec10.pdf). Page 2.

[2 - Dos and Don’ts of Client Authentication on the Web](https://pdos.csail.mit.edu/papers/webauth:sec10.pdf). Page 1.

http://stackoverflow.com/questions/549/the-definitive-guide-to-form-based-website-authentication

https://blog.codinghorror.com/youre-probably-storing-passwords-incorrectly/

https://pdos.csail.mit.edu/papers/webauth:sec10.pdf

[OWASP Authentication Cheat Sheet
](https://www.owasp.org/index.php/Authentication_Cheat_Sheet)
