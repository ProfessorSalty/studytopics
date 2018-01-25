# JavaScript Concurrency
[Phillip Roberts: What the heck is the event loop anyway?](https://www.youtube.com/watch?v=8aGhZQkoFbQ)

[Loupe](http://latentflip.com/loupe/?code=JC5vbignYnV0dG9uJywgJ2NsaWNrJywgZnVuY3Rpb24gb25DbGljaygpIHsKICAgIHNldFRpbWVvdXQoZnVuY3Rpb24gdGltZXIoKSB7CiAgICAgICAgY29uc29sZS5sb2coJ1lvdSBjbGlja2VkIHRoZSBidXR0b24hJyk7ICAgIAogICAgfSwgMjAwMCk7Cn0pOwoKY29uc29sZS5sb2coIkhpISIpOwoKc2V0VGltZW91dChmdW5jdGlvbiB0aW1lb3V0KCkgewogICAgY29uc29sZS5sb2coIkNsaWNrIHRoZSBidXR0b24hIik7Cn0sIDUwMDApOwoKY29uc29sZS5sb2coIldlbGNvbWUgdG8gbG91cGUuIik7!!!PGJ1dHRvbj5DbGljayBtZSE8L2J1dHRvbj4%3D)

### Web APIs
The browser provides many APIs that developers use. Functions like `setTimeout`
or `console.log` do not exist in the JavaScript engine, but refer to functions
provided by the platform. Calls to these functions are delegated out fromt the
JavaScript runtime and sent to the browser. Any callbacks are pushed to the
task queue to be handled later.

### Frame
The frame is a structured collection of data important to the function call. It
includes the return address of the function, any arguments passed to it, the
lexical environment of the function (or a reference to it), and a space for
local variables.

### Frame Stack
Each function call adds a _frame_ to the stack. Function calls are nested by
using the stack's First-In-Last-Out ordering. Frames are popped off of the
stack when the function returns.

### Message (or Task) Queue
Functions can be referred to by a message. Callbacks are pushed to the queue as
a message after the asynchronous function has finished execution. The message
merely refers to the function.

### Render Queue
Because we're in a single threaded environment, the runtime must juggle both
script execution and rendering. Rendering has a higher priority, so the render
queue will be checked before the task queue. This means that if rendering takes
a long time, it will block script execution. Similarly, if a function on the
call stack takes too long to finish, it will block rendering.

It may be that there is no physically separate render queue. Both tasks and messages could be in the same priority queue.

### Heap
Objects are allocated in the heap - a large, unstructured section of memory for the application.

### Event Loop
The event loop continually checks the stack and the queues at regular intervals.
When the stack is empty, it dequeues the first message in the queue and adds
its referenced function to the stack to be executed.

### Communication
Web workers and cross-origin `iframe`s have their own stack, heap, and message queues. The two runtimes communicate via the `postMessage` method, which will send a message of arbitrary data to any other runtimes that are listening to the `message` event.

# Public Key Infrastructure
### Goals
The goal of PKI is to provide a way to encrypt data while also providing a way
to verify the sender, receiver, or both.

### How does it work?
Generally speaking, each participant has both a private and a public encryption
key. The private key is kept safe while the public key is made available on a
public repository or sent along with the encoded message. Any data encrypted
with one can only be decrypted with the other. In ths way, person A can encrypt
a message with their private key, then send a message to person B, who can
decrypt it with person A's public key. By virtue of the data being decrypted by
the public key, person A's identity is verified. For an added level of
security, person A could encrypt their message first with their private key,
and then with person B's public key. Person B, being the only person with thier
private key, then is able to assert that the message was both intended for them
and sent by person A.

# HTTPS
### Why do we need it?
HTTPS is meant to accomplish two things: allow the client to verify the
identity of the server, and provide a way to establish a secure connection
without sending sensitive data in the clear.

### How does it work?
SSL certificates are signed and verified using the chain of trust. When a
client wants to establish a secure connection, they initiate the TLS handshake.
Starting with a CLIENT_HELLO message, the handshake involves selecting a cypher
suite, then having the server transmit its SSL certificate. The client decrypts
the certificate using the issuing authority's public key, which reveals the
server's public key. Now the client has both established the server's identity
and a way to encrypt data which can only be decrypted by the server. The client
picks some random prime number to server as the shared secret, then encrypts
that secret with the public key and sends it back to the server with a
CHANGE_CYPHER_SPEC message, indicating that the client is ready to switch to
symmeitrcal encryption. When the server receives the shared secret and the
message, it sends a CHANGE_CYPHER_SPEC message back to the client. At this
point, an encrypted channel of communication has been established.

### What is long polling?
Long polling is an application design pattern that allows the server to "push"
data to the client, which is normally not possible in HTTP. The client
initiates a connection as normal, but the connection is set to stay open for a
long time. When the connection closes, the client opens a new one. The problem
with this method is that both servers and browsers can support only a set
number of active connections, so long polling can easily eat into available
resources.

### What is DTD (Document Type Declaration)?

A DTD defines the structure, legal elements, and attributes of an XML document.

* PCDATA - A Parsed Character Data. XML parsers usually parse all the text in an XML document.

* CDATA - Marks code that could be parsed as XML, but indicates that it should _not_ be. CDATA is like a comment, but with some key differences:

    - CDATA is still part of the document, whereas comments are stripped away.
    - CDATA cannot include the string `]]>` because it ends the block. Comments
      cannot include double dashes `--` because it ends the comment block.

    - CDATA will interpolate [Parameter Entity References](http://www.w3.org/TR/REC-xml/#dt-PERef) while comments will not.

### What is HTTP/2?


### What is WebSockets?
WebSockets is a fully duplex protocol for client-server communication over a single TCP connection.

### What are Server-sent DOM events?

# Cryptography

### DSA
#### What is it?
The Digital Signature Algorithm (DSA) is a standard for processing digital signatures. The algorithm produces a signature consisting of two numbers, _r_ and _s_, which are both 160-bits in length. It is primarily used to create assymetric keys.

#### How does it work?
Person A wants to sign a certificate that person B can validate. Person A will be the party who creates the signature while person B decrypts it.

**Steps to create keys**
1. Select a prime number, _q_, with bit length _l_.
2. Select another prime number _p_ that
    * has bit length _l_
    * _q_ divides into _p_ - 1
3. Select a generator, _𝝰_ of the unique cyclic group of order _q_ in
   ℤ % _q_. This means that the generator must be a number where
   _𝝰_<sup>q</sup> is 1, and no power less than _q_ will be 1.
    * Select some number _g_ in ℤ % _q_ and compute:
        > _𝝰_ = (g <sup> _p_ - 1 / _q_ </sup> % _p_)
    * Repeat until _𝝰_ is not 1
4. Select some random integer _d_ such that 1 ≤ _d_ ≤ _q_ - 1
5. Compute:
    > <strong>B</strong> = (_𝝰_<sup>_d_</sup> % _p_)

The public key is _k_<sub>pub</sub> = (_p_, _q_, _𝝰_, _B_)

The private key is _k_<sub>pr</sub> = _d_

**Steps to use keys to sign data**
1. Pick an **ephemeral key**, K<sub>E</sub>, such that 0 ≤ K<sub>E</sub> ≤ _q_
2. Compute
    > _r_ = (_𝝰_<sup>K<sub>E</sub></sup> % _p_) % _q_
3. Compute
    > _s_ = (SHA(message) + _d_ * _r_) K<sub>E</sub><sup>-1</sup> % q

    _Note the use of an SHA hash function to get a fingerprint of the message_

**Steps to verify a signature**
1. Compute
    > _u_<sub>1</sub> = s<sup>-1</sup> * SHA(message) % _q_
2. Compute
    > _u_<sub>2</sub> = s<sup>-1</sup> * r % _q_
3. Compute
    > _V_ = (_𝝰_<sup>_u_<sub>1</sub></sup>B<sup>_u_<sub>2</sub></sup> % _p_) % _q_
4. If _V_ == _r_ % _q_, then the signature is valid

### RSA

#### What is it?
#### How does it work?

### How does DSA differ from RSA?
[source](https://www.quora.com/What-is-the-difference-between-RSA-and-DSA)
DSA and RSA are very similar in that they are both algorithms that generate asymmetric keys for encryption. DSA is based on disrete logarithms (_𝝰_<sup>_d_</sup> = B) while RSA is based on large-number factorization (P * Q = N). Both methods take a very long time to reverse engineer with modern computers. DSA is a bit faster than RSA when creating a key pair and decrypting. RSA is a bit faster when validating the signature token and encrypting.

### What is AES?

### What is the Diffie-Hellman exchange algorithm?
Diffie-Hellman is a way for two parties to come up with a shared secret that
can be used for symmetical encryption without having to send any compromising
data in the clear. Instead of sharing sensitive data, both parties are creating
a key together. Since nothing is ever transmitted or saved, anyone looking at
the traffic later cannot easily decrypt it.

### How does Diffie-Hellman work?
DFE relies on a straightforward mathmatical property of exponents.
> (M<sup>a</sup>)<sup>b</sup> % p == (M<sup>b</sup>)<sup>a</sup> % p



[source1](https://security.stackexchange.com/a/45971)
[related](https://crypto.stackexchange.com/questions/2867/whats-the-fundamental-difference-between-diffie-hellman-and-rsa)
### What is elliptic-curve cryptography?
Because [elliptic functions](https://en.wikipedia.org/wiki/Elliptic_function)
are periodic, inputs can be mapped to a finite set of numbers within a group,
much like we accomplish with modular arithmetic in Diffie-Hellman. By using
[point multiplication](https://en.wikipedia.org/wiki/Elliptic_curve_point_multiplication),
a shared secret key can be derived for key agreement protocols like
Diffie-Hellman.

### How is elliptic-curve cryptography used?
Elliptic-curve cryptography is useful for key agreement, digital signatures,
and number generators. Its use in encryption is in key agreement for symmetic
encryption.

### Why would elliptic-curve cryptography be used?
We use elliptic-curve cryptography because its very efficient. To reach the
same level of security that we would get from a 3,000+ bit key using modular
arithemtic, we only need 256 bits using elliptic functions.

### What is SHA?

The Secure Hash Algorithm is a one-way hash function that is meant for
signatures rather than cryptographic encryption. It always outputs a 160-bit
number that can be used to verify the integrity of the data.

# Immutable Data Structures
### What are they?
Immutable data structures are any data structure whose value or state cannot change. In
practice, most "immutable" data structures are actually _persistant_ data
structures. The difference is that while immutable data structures absolutely
cannot change their value, persistant data structures can, though they always
keep the data they had and use clever tricks to add new data.

### How do they work?
### Why are they useful?

[source](http://www.yegor256.com/2014/06/09/objects-should-be-immutable.html)
* Thread safety - because the state of any object cannot be modified, threads
  working on the same data can only copy it to their own memory space in stack
  in order to manipulate it. This means it's impossible for any one thread to
  interfere with the operation of another.

* Temporal Coupling - Objects that take configuration to work can be tricky to
  manage since misconfigurations will only be exposed during runtime. This
  makes the code brittle as deleting those calls to the configuration methods
  would silently break the code. If the object's configuration methods were to
  return a new object with the desired setting instead of altering internal
  state, the compiler would be able to watch for code breaking modifications.

* Avoiding side effects - if two different function calls are manipulating the
  same piece of data, such as sorting an array, then the manipulation has a
  side effect of altering the array's state for the other function. This leads
  to bugs and maintainability issues, as well as the extra code required to
  make defensive copies.

* Failure Atomicity - If an object's methods alter its internal state, an error
  in one line of code could leave the object's state broken (one field gets
  updated just before the crash so it no longer accurately represents
  anything). Forcing the object to return a new object each time the method is
  called means that any errors just results in a discarded object.

* Immutable data structures can be shared easily by reference and do not
  require defensive copying.

* Debugging is easier as there is only one value for the structure.

# Scalability and Architecture
## Microservices
### What are they?
The microservice architecture is an application style that structures app's
logic in loosely coupled services which are usually distributed among distinct
systems. For instance, a server app may itself simply handle client requests,
and operations like data fetching or image processing will be handled by
sending a request to yet another machine.

### Why are they useful?
Microservices make development easier to plan and scale because each service
can focus on their own goals and implementations without having to worry about
what any other team is doing. So long as there is a stable API for the services
to communicate with each other, modules may be refactored, updated, and tested
separately where a monolithic app would have to be tested for regressions in
its entirity before each deployment.

Using microservices, it is also much easier to scale an application's backend
to include as many client platforms as needed. Because services are so loosely
coupled, they can be mixed into any app stack without having to build a tightly
itegrated monolith.

## SaaS
### What does it mean?
SaaS (Software as a Service)is a delivery model that turns software into
something that the user pays to access rather than something they pay to
obtain. Such services are typically access via a thin client, most often a web
browser.

## PaaS
### What does it mean?
PaaS (Platform as a Service) takes the same concepts as SaaS but applies them
specifically to developers who want to create apps without having to manage
their own infrastructure. This could come in the form of a cloud service that
the user can access from anywhere, or a private service which is local to their
own intranet or even their own system.

Because it streamlines setup and removes many opportunities for mistakes, PaaS
can accelerate development and reduce friction for new employees.

# HTTP
## What is it?
HTTP (Hypertext transfer protocol)is an application protocol that connects distributed hypermedia information systems. It is defined as structured text which uses logical links betwen nodes (hyperlinks) to exchange or transfer hypertext. It is stateless.

HTTP has been the foundational protocol for the internet.

### Definitions:
<dl>
    <dt>Session</dt>
    <dd>A series of request-response transactions between server and client.
    Begins with a client hello mesage on port 80 (443 for SSl). Server responds
    with requested resource (if any) or an error code.</dd>
    <dt>Authentication scheme</dt>
    <dd>A challenge-response mechanism that verifies the client's identity.
    HTTP provides basic access and digest access schemes but is also
    extensible.</dd>
    <dt>Authentication realms</dt>
    <dd>Realms divide resources within a single URI to allow for more granular access.</dd>
    <dt>Methods</dt>
    <dd>A text indication that categorizes the desired action to be performed
    by the server.</dd>
    <dt>Status code</dt>
    <dd>A 3-digit numeric code that, along with the <emph>reason phrase</emph>,
    describes the response and its outcome.</dd>
    <dt>Persistent connection</dt>
    <dd>HTTP/1.1 introduced a <emph>keep-alive</emph> mechanism. Prior to this
    version, connections were close after a single request-response pair and
    any subsequent requests needed to restart the TCP handshake. Because this
    is slow, persistent connections reduces latency and with other
    optimizations (chunked transfer encoding, pipelining, byte serving),
    connections become noticebly faster.</dd>
    <dt>Session state</dt>
    <dd>Because HTTP is stateless, there is no protocol definition for
    maintaining connection state. Some applications do manage connection state
    by using cookies or hidden tokens in web forms.</dd>
    <dt>Chunked transfer encoding</dt>
    <dd>Allows the server to maintain a persistent connection for dynamicaly
    generated content. Because the length of the content isn't known before it
    has been completed, the Content-Length header cannot be used. Chunked
    encoding allows the server to send the data in discrete chunks that do not
    have to have any knowledge of any other chunk, with the final chunk
    explicitly indicating that the content is finished. Chunking also allows
    the server to send additional header fields after the body which cannot be
    known until the content has been produced. An example of this is digital
    signatures. Without chunking, the sender would have to buffer data until it
    was complete in order to calculate these field values.</dd>
    <dt>HTTP pipelining</dt>
    <dd>Pipelining allows for multiple HTTP requests to be made in a single TCP
    connection without waiting for each response. This technique dramatically
    improves page load times. Each request is queued by the server, so they are
    processed in the order that they are received and while each request can be
    made without waiting for any response, the response to any one request
    could be blocked if there were a problem with the request made before it.
    Idempotent requests can be pipelined, but POST should never be.
    </dd>
    <dt>Byte serving</dt>
    <dd>This allows a client to request only a particular piece of a file.
    Servers advertise their willingness to serve partial requests with the
    <emph>Accept-Ranges</emph> response header, and subsequent requests from
    the client can use the <emph>Range</emph> header to specify the byte range
    of the request. Successful partial requests are returned with a 206 status
    code. If the range is invalid, the response includes a 416 code. Byte
    serving is an important part of media streaming, as well as reading very
    large PDF documents.</dd>
</dl>


## Common Headers

### Request Fields
<dl>
    <dt>Accept</dt>
    <dd>Lists acceptable Content-Type for response</dd>
    <dt>Accept-Encoding</dt>
    <dd>Lists acceptable encodings (ex. gzip, deflate)</dd>
    <dt>Accept-Language</dt>
    <dd>Lists acceptable languages for response</dd>
    <dt>Access-Control-Request-Method,
    Access-Control-Request-Headers</dt>
    <dd>Initiates a request for cross-origin resource sharing</dd>
    <dt>Authorization</dt>
    <dd>Credentials for HTTP authentication</dd>
    <dt>Cache-Control</dt>
    <dd>Used to specify directives that _must_ be obeyed by all caching mechanisms along the request-response chain (the client and any intermediate servers)</dd>
    <dt>Connection</dt>
    <dd>Control options for the current connection and list of hop-by-hop request fields.  Must not be used with HTTP/2.</dd>
    <dt>Cookie</dt>
    <dd>An HTTP cookie previously sent by the server with `Set-Cookie`</dd>
    <dt>Content-Type</dt>
    <dd>The MIME type of the request body (for use with POST and PUT)</dd>
    <dt>Date</dt>
    <dd>The date and time that the message was originated</dd>
    <dt>Forwarded</dt>
    <dd>Disclose original information of a client connecting to a web server through an HTTP proxy</dd>
    <dt>Host</dt>
    <dd>The domain name of the server (virtual or real) and the TCP port number on which the server is listening (omitted if standard). Mandatory in HTTP/1.1, but omitted in HTTP/2</dd>
    <dt>If-Modified-Since</dt>
    <dd>Allows _304 Not Modified_ to be returned if content is unchanged (by date)</dd>
    <dt>If-None-Match</dt>
    <dd>Allows a _304 Not Modified_ to be returned if content is unchanged (ETag)</dd>
    <dt>Origin</dt>
    <dd>Initiates a request for CORS (asks server for Access-Control-* fields)</dd>
    <dt>Referer [_sic_]</dt>
    <dd>Misspelled. The address of the previous web page from which a link the currently requested page was followed.</dd>
    <dt>User-Agent</dt>
    <dd>The user agent string</dd>
    <dt>Upgrade</dt>
    <dd>Ask the server to upgrade to another protocol (ex. HTTPS/1.2, websocket). Cannot upgrade to HTTP/2.</dd>
    <dt>X-Requested-With</dt>
    <dd>Mainly used to identiy AJAX requests. Most JavaScript frameworks send this field with value of XMLHttpRequest.</dd>
    <dt>DNT</dt>
    <dd>Do not track request.</dd>
    <dt>X-Forwarded-For</dt>
    <dd>De facto standard for identifying originating IP address of a client connecting to a web server through an HTTP proxy or load balancer. Superceded by _Forwarded_ header.</dd>
    <dt>X-Forwarded-Host</dt>
    <dd>Like X-Forwarded-For, but identifies the host rather than IP address. Also superceded by _Forwarded_</dd>
    <dt>X-Forwarded-Proto</dt>
    <dd>Like X-Forarded-For, but identifies the protocol rather than the IP address. Also superceded by _Forwarded_</dd>
    <dt>X-Csrf-Token</dt>
    <dd>Transmit token to prevent CSRF attack.</dd>
</dl>

### Response fields
<dl>
    <dt>Access-Control-Allow-Origin,
Access-Control-Allow-Credentials,
Access-Control-Expose-Headers,
Access-Control-Max-Age,
Access-Control-Allow-Methods,
Access-Control-Allow-Headers</dt>
    <dd>Specifies which sites, methods, and headers can participate in CORS.</dd>
    <dt>Accept-Patch</dt>
    <dd>Specifies which patch document formats the server supports.</dd>
    <dt>Age</dt>
    <dd>The age the object has been in proxy cache (seconds).</dd>
    <dt>Allow</dt>
    <dd>Valid methods for a specified resource. Disallowed methods result in _405 Method Not Allowed_</dd>
    <dt>Cache-Control</dt>
    <dd>Instructs all caching mechanisms from server to client whether the object can be cached, and for how long. Measured in seconds.</dd>
    <dt>Connection</dt>
    <dd>Control options for the current connection and list hop-by-hop response fields. Must not use with HTTP/2.</dd>
    <dt>Content-Disposition</dt>
    <dd>Indicates whether the response body content should be displayed **inline** - that is, as markup - or as an attachment to be saved on the client machine.</dd>
    <dt>Content-Encoding</dt>
    <dd>Specifies the encoding type.</dd>
    <dt>Content-Language</dt>
    <dd>The natural language of the content.</dd>
    <dt>Content-Length</dt>
    <dd>The length of the response body in octets (8-bit bytes).</dd>
    <dt>Content-Location</dt>
    <dd>An alternate location for the returned data.</dd>
    <dt>Content-MD5</dt>
    <dd>A Base64-encoded binary MD5 sum of the response content.</dd>
    <dt>Content-Type</dt>
    <dd>MIME type of the content.</dd>
    <dt>Date</dt>
    <dd>The date and time that the message wa sent.</dd>
    <dt>ETag</dt>
    <dd>Identifies a specific version of the resource for comparing against cached copies.</dd>
    <dt>Expires</dt>
    <dd>Gives the date/time after which the response is considered stale.</dd>
    <dt>Last-Modified</dt>
    <dd>The last modified date for the requested object.</dd>
    <dt>Link</dt>
    <dd>Used to express a typed relationship with another resource.</dd>
    <dt>Location</dt>
    <dd>Used in redirection, or when a new resource has been created.</dd>
    <dt>Proxy-Authenticate</dt>
    <dd>Request authentication to access the proxy.</dd>
    <dt>Public-Key-Pins</dt>
    <dd>[HTTP Public Key Pinning](https://en.wikipedia.org/wiki/HTTP_Public_Key_Pinning), announces hash of website's authentic TLS certificate.</dd>
    <dt>Retry-After</dt>
    <dd>If an entity is temporarily unavailable, instructs the client when to try again (seconds or HTTP-date).</dd>
    <dt>Server</dt>
    <dd>Name of the server.</dd>
    <dt>Set-Cookie</dt>
    <dd>Sets an HTTP cookie.</dd>
    <dt>Strict-Transport-Security</dt>
    <dd>Defines HSTS policy for the client, how long to cache the HTTPS only policy and whether it applies to subdomains.</dd>
    <dt>Transfer-Encoding</dt>
    <dd>The encoding used to transfer data to the user. Must not be used with HTTP/2.</dd>
    <dt>Tk</dt>
    <dd>Response to DNT. Possible values:
        > "!" — under construction
        "?" — dynamic
        "G" — gateway to multiple parties
        "N" — not tracking
        "T" — tracking
        "C" — tracking with consent
        "P" — tracking only if consented
        "D" — disregarding DNT
        "U" — updated
    </dd>
    <dt>Upgrade</dt>
    <dd>Ask the client to upgrade to another protocol. Cannot be used with HTTP/2.</dd>
    <dt>WWW-Authenticate</dt>
    <dd>Indicates the authentication scheme that should be used to access the requested content.</dd>
    <dt>X-Frame-Options</dt>
    <dd>Clickjacking protection: deny - no rendering within a frame; sameorigin - no rendering if origin mismatch; allow-from - allow from specified location; allowall - allow from any location (nonstandard).</dd>
    <dt>Upgrade-Insecure-Requests</dt>
    <dd>Tells a server which hosts mixed content that the client would prefer redirection to HTTPS and can handle `Content-Security-Policy: upgrade-insecure-requests`. Must not be used with HTTP/2.</dd>
    <dt>X-Powered-By</dt>
    <dd>Specifies the server technology (related to X-Runtime and X-Version).</dd>
    <dt>X-Request-ID, X-Correlation-ID</dt>
    <dd>Correlates HTTP requests between client and server.</dd>
    <dt>X-UA-Compatible</dt>
    <dd>Recommends the preferred rendering engine.</dd>
    <dt>X-XSS-Protection</dt>
    <dd>Cross-site scripting filter.</dd>
</dl>

## How does ETag work?
Each ETag identifies a specific version of a resource. If the resource ever
changes, so does the ETag. If the resource is downloaded and cached, the ETag
is stored with it. Any subsequent requests for resource is sent with
`If-None-Match` its ETag. The server compares the versions, and if they match,
a 304 response is sent to the client. If they differ, the server sends the
updated resource in a full response, including a new ETag.

## Methods
* GET: Retrieves data from the server with no side effects
* POST: Sends data to the server to create a new entitiy.
* PUT: Sends data to the server to replace an existing entity.
* PATCH: Sends data to the server to update an existing entity.
* DELETE: Remove the specified data from the server.
* TRACE: Tests which machine receives the request.
* OPTIONS: First step in CORS. Requests information about the methods and headers supporting cross origin requests.
* HEAD: Retrieves just the response headers with no message body.
* CONNECT: Establishes a network connection to a resource. Returns a 200 status code and "Connection Established" message.* HEAD: Retrieves just the response headers with no message body.

# Data Structures

## Thunk
### What is it?
A thunk is a pattern that resembles partial completion. A thunk is a function
that returns another function with all parameters completed but one. This allows
for function composition and caching the result of a computation for later
reuse. Very useful when combined with persistent data structures for
asynchronous operations, such as that used in Redux.

## Bloom Filters
### What is it?
Bloom filters are a way to map arbitrary values to indicies in an array. Data
are run through a hash function which outputs the index at which a small value,
usually a bit, is switched. In this way, the data's existence can be
remembered in a way that's fast to look up, though the exact data itself is not
stored. In order to avoid hash collision, most implementations run the data
through more than one function.

### Why do we use it?
Because lookups are so fast, bloom filters are great for checking whether a
given datum exists in a set, such as words in a dictionary or addresses in a
routing table.

## Binary Search Tree
### What is it?
A BST is a tree of data where all nodes have up to two children each - a "left"
and a "right". The left side values are all less than the parent node's value,
and the right side are all greater than it. This facilitates searches by binary
search.

### Why do we use it?
Binary search is very fast. A BST allows for searches in the order of Log<sub>2</sub> of
the number of nodes.

### What does it mean to be balanced?

There is no single definition, but a BST could be considered balanced when the
depth of all branches of the tree differ in depth by no more than 1.

### How do we balance it?

Balancing a binary tree can be done using either a series of tree rotations
(where values are exchanged between parent, left, and right children), or by
placing all of the tree's values into a linked list or array, then creating a
new BST.

## Promises?
### What are they?
Promises are an object that represent the eventual result of some asynchronous
operation. The promise constructor takes an executor function which itself
takes both a success and a failure function reference. The executor function
will perform the asynchronous operation and call the appropriate function with
the result. The promise object's API consists of two methods - then and catch.
A function passed to then becomes the success callback, and a function passed
to catch becomes the failure callback. Regardless of what gets returned in
either callback, the value is always wrapped in another promise. This means
that promises can be chained.

### Why do we use them?
Being single threaded, the model historically used to handle asynchronicity in
JavaScript is the callback function. The problem with callbacks is that it
doesn't take many before the code becomes difficult to read due to indentation.
It can also be complicated to keep track of return values. Promises allow for
composable asynchronous functions that are not only easier to reason about, but
maintain a flat indentation style that is readable.

# Security

## Mixed Content
### What is it?
Mixed content occurs when the initial HTML is loaded over a secure (HTTPS)
connection, but other resources (media, stylesheets, scripts) are loaded over
insecure (HTTP). It is broken up into two general categories:

* Passive - This is content that does not interact with the rest of the page.
  This includes images, video, and audio.
* Active - This is content that does interact with the rest of the page. This
  includes scripts, stylesheets, iframes, flash, and other code that the
  browser can execute. Active Mixed Content has the highest potential for
  damage to the user.

### Why is it a problem?
* Secrecy - HTTPS prevents an attacker from evesdropping on requests. Mixed
  content reveals those requests. This opens up the user to man-in-the-middle
  attack.
* Integrity - HTTPS lets the browser detect if an attacker has changed any of
  the data the browser is receiving. While this is important for media and
  other content, it's critical for scripts that transfer sensitive data.
* Authentication - It's important to know that the server you're requesting
  data from is the same server that you expect and not someone impersonating
  your bank.

### How do we prevent it?
* Always use HTTPS URLS when loading resources.
* Use the Content-Security-Policy-Report-Only header to monitor mixed content errors on your site.
* Use the upgrade-insecure-requests CSP directive to protect visitors.

## Cross Site Request Forgery
### What is it?
A CSRF attack is one that exploits a site that accepts unauthorized actions
from authenticated users. For instance, a banking app that gives authenticated
users a form to transfer funds will send a message to the server via a POST
request. If the user doesn't have the opportunity to confirm or deny each
request, or if the app doesn't somehow authorize each transfer, then it is
simple for a malicious actor to create a POST Request on an unrelated site that
makes a transfer without the user's knowledge or input. Because a browser will
send authentication information in a cookie to the domain, if the user is
logged in then there is nothing to stop the malicious request from going
through.

### What do we do for defense?
The simplest way to mitigate CSRF attack is to have the server generate a token
which is embedded in the form. Any action that the user sends with the form
will include the token.

## Cross Site Scripting
### What is it?
Any site that takes user input and injects it into the markup unsanitized is at
risk for injecting a malicious script into a link or altered web page. The
script could access cookies, send data to another server, run a cryptocurrency
miner, or any number of activities that are unwanted by the user.

### What do we do for defense?
Browser protections vary by vendor, but the most effective protection is to
sanitize all input to neutralize HTML.

## What is the same-origin policy?
The same-origin policy restricts JavaScript's ability to access data in domains
other than the one it was originally downloaded from. An origin is defined as a
combination of URI scheme, host name, and port number. This is a security
measure to prevent malicious scripts from accessing sensitive data on another
domain, such as the user's bank (the login info would be submitted
automatically via cookies) and sending it to yet another (the malicious actor's
own server).

## JSONP
### What is it?
Browser security prevents code execution if it was received via JavaScirpt from
a domain other than what is currently in context. This is a security feature
meant to mitigate XSS attacks, but it can be frustrating to deal with if your
app is meant to get data from one or more different sources. Browsers do allow
script tags to download arbitrary JavaScript code, however, and JSONP is a hack
that gets around browser security by placing the download request in a
generated script element.

### Why do we use it?
JSONP allows us to get around the same-origin security policy by generating a
script tag and setting its source to the desired endpoint with a function name
appended on the end. The endpoint responds with the desired code wrapped in the
function call. Once the browser has downloaded the code, it executes the
function.

## CORS
### What is it?
CORS is a mechanism in HTML5 that manages script access to different domains. It works by intercepting all HTTP requests, first sending an OPTIONS request to the server. The server's response includes all REST methods and origins that apply to CORS.

### Why do we use it?
Browser security policy prohibits JavaScript from fetching data in another
domain without CORS enabled. By establishing permitted origin domains, we can open APIs to applications.

## HSTS
### What is it?

## Clickjacking
### What is it?
### What do we do for defense?

# General

### Tips to reduce load times of a web app

* Optimize images to no longer than screen resolution and save it as a compressed file

* Eliminate all JavaScript files to reduce the amount of transferable data

* Combine and minify all CSS and JS files

* Call all JS files at the bottom of the body tag

* Defer or async JS files

* HTTP compression

* Reduce lookups

* Minimize redirects

* Cache

### What is a MIME type?
The Multipurpose Internet Mail Extensions standard was originally a way to
extend the format of email to support media, extended character sets, multipart
message bodies, and header information with non ASCII characters. HTTP has
adopted the content types for its headers as a way to appropriately select a
media viewer or process data included with the message.

### What does a MIME multipart message look like?
MIME messages begin with a message header which contains information about the
MIME-Version, Content-Type, and boundary. The version will be 1.0, the type
multipart/mixed. The boundary can be an arbitrary string which will appear in
the body prepended with two dashes '--'. Each boundary separates distinct MIME
messages with zero or more content headers and a body.

<pre>
    MIME-Version: 1.0
    Content-Type: multipart/mixed; boundary=wall

    Message with multiple parts.
    --wall
    Content-Type: text/plain

    Here's a text message
    --wall
    Content-Type: application/octet-stream
    Content-Transfer-Encoding: base64

    PGh0bWw+CiAgPGhlYWQ+CiAgPC9oZWFkPgogIDxib2R5PgogICAgPHA+VGhpcyBpcyB0aGUg
Ym9keSBvZiB0aGUgbWVzc2FnZS48L3A+CiAgPC9ib2R5Pgo8L2h0bWw+Cg==
    --wall
</pre>

### Why do we include JavaScript files at the end of the body?
Script downloads block the download of other resources (images, style, content). By leaving the scripts until all of the markup and content has been downloaded, the page can appear to be loaded more quickly.

### What do the async and defer attributes do for script tags?
async indicates that the browser should, if possible, execute the script
asynchronously.

defer indicates that the browser should download the script after the document
has been parsed, but before firing DOMContentLoaded.

### Why do we include CSS files in the head?
With the style loaded before the DOM is rendered, the browser can apply CSS
rules as the elements are loaded.

### What is the difference between cookies and local storage?

|                 | Cookies       | Local Storage  |
| ----------------|:-------------:| -----:|
| Client / Server | Data accessible both at client and server side. Data is sent to the server with each HTTP request | Accessible only at the client. Data must be sent manually. |
| Size            | Storage capacity of cookies is 4095 bytes per cookie      |   Storage capacity of local storage is 5MB per domain |
| Expiration      | Cookies have expiration and cookie data gets deleted after some time |    There is no expiration and has to be removed manually. |

### How is XHTML different from HTML?

XHMTL

* ...requires that all tags should be lowercase

* ...requires that all tags should be closed with closing tag

* ...requires that all attributes are enclosed in double quotes

* ...forbids inline elements from containing block level elements

### What is a RESTful web service?

### What is the difference between stateless and stateful protocols?
A stateful protocol is one in which the responder maintains information about the requesting entity across multiple requests from the same source.

A stateless protocol treats each request as an independent transaction, providing all of the information required for the connection.
