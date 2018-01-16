# JavaScript Concurrency

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

# Cryptography

### What is DSA?
### What is RSA?
### How does DSA differ from RSA?
### What is AES?

### What is the Diffie-Hellman exchange?
### How does Diffie-Hellman work?
### What is elliptic-curve cryptography?
### How is elliptic-curve cryptography used?
### Why would elliptic-curve cryptography be used?

### What is SHA?

# Immutable Data Structures
### What are they?
Immutable data structures are any data structure whose value cannot change. In
practice, most "immutable" data structures are actually _persistant_ data
structures. The difference is that while immutable data structures absolutely
cannot change their value, persistant data structures can, though they always
keep the data they had and use clever tricks to add new data.

### How do they work?
### Why are they useful?

# Scalability and Architecture

# Common HTTP Headers

# Data Structures

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

### What are some real world examples of its use?
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
anitize all input to neutralize HTML.

