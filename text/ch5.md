# The Internet
This chapter is going to give you a general introduction to the internet, internet traffic, and how applications send and receive data. There won't be much code in this chapter, but I believe you should spend some time getting to know, at very least, the basics of how internet traffic works. 

When traffic flows over the internet, it is broken into packets. Those packets get routed by **routers** along the way and the recipient's computer reassembles those packages into a complete message. There are two different protocols for this, one that assembles and loads the message as it is received (UDP) and one that waits for the entire message to be received before loading anything (TCP). Streaming video services generally use UDP because the designers know that we're more frustrated by buffering than reduction in video quality. However, this all occurs below the application level and our FLASK application doesn't deal with unassembled packets. UDP and TCP manage the packets before they're sent to the application. 

Our applications are concerned with the entire message. When we request a page on a website, the message is that entire page. Likewise, when we send data to a website, the message is that entire data object (even if it is a massive photo that must be broken into lots of packets). When we look at an entire message that is sent over the internet, we are talking about the HTTP protocol. This protocol defines how entire messages are sent from one application (a web server, like Flask) to another (a browser, like Firefox). Within this protocols, there are two actions: Requests and Responses. Requests are made by clients, like web browsers, and either request some content from the server or request that some data be accepted by the server. On the other end, servers make responses. They do this when they receive a request from a client and their response depends upon the request. 

### Requests
An HTTP request is send by a client. This could be your web browser or any other application set up to make requests over the internet using the HTTP protocol. For fun, try out the following in your terminal:

```
$ curl --request GET https://dlondonmedina.github.io/tester/
```

Curl is a program that sends HTTP requests similar to a web browers. What the above says is send a GET request to the address https://dlondonmedina.github.io/tester/

That request leaves curl and goes through my network card, gets sent across a bunch of routers to a DNS server which matches the name dlondonmedina.github.io with this IP address 185.199.109.153. The specific message is something like "Get me whatever resource is named 'tester.'" Now curl waits to see if there's a server that can respond to the particular message at the address 185.199.109.153. We'll see that response in just a moment.

Before we get there, you'll notice the word --request GET in the command above. GET is a key word, and refers to a particular method in HTTP. Just as a method is a particular operation of an object and performs particular processes, HTTP methods are the particular operations that a request asks the server to do. GET above means get me whatever data is appropriate for the name 'tester.' The following are the most commonly used HTTP methods, but we'll mainly be concerned with GET and POST.

| Method | Effect |
| -------| ------ |
| GET | This method retrieves data from the resource at the specified URL or IP Address. GET should not be used to send data. |
| POST | This method submits data to the resource at the specified URL or IP Address. It causes a change in state on the server. For instance, when you create an account on a website, the data you enter in their form is sent via a POST request and stored on, or at least processed by, the server. |
| PUT | Like POST, this method submits data to the resource at the specified URL or IP address, but the data is an alteration to a preexisting resource on the server. The new data updates the old data. Think of this as resetting a username that is stored in a database on the server. |
| DELETE | This method submits data to identify a particular resource on a server at the specified URL or IP Address, and it deletes that data. |

There are others, but with Flask, you generally only use GET and POST. A crucial thing to know about using the HTTP methods is that some change the state of an object on the server and others do not. GET, for example, only retrieves data from a server, but doesn't change the state of that data. When you open a blog post on a web page, your browser uses GET to request the contents of the blog post. It doesn't change the coontents on the server or have any other side-effects (like placing an order or creating a user). On the other hand, when you submit a comment to that blog post, the broswer uses the POST method, which adds content to a database on the server, thus changing the state of that database resource. If this is all exciting, I encourage you to read more about all the HTTP methods, and perhaps pursue web development further. 

### Responses

Let's go back to our sample request above. We sent a GET request to the resource located at the URL dlondonmedina.github.io/ requesting the resource 'tester/'. Along with that request, we sent our IP address so that the server knows where to return the response. There are a few things that happen:

* Once our request goes to the DNS, which pairs the URL: dlondonmedina.github.io with the IP address: 185.199.109.153.

* Our request is sent to the IP address (185.199.109.153)

* If there is not a server listening for requests at 185.199.109.153, then an error message will be returned.

* If there is a server listening for requests at 185.199.109.153, then the server will receive the request and look for 'tester/' as a named resource. 

* If the server doesn't know 'tester/' as a named resource (static file or method), then it will respond with an error.

* If the server does know 'tester/', then it will gather whatever resource is identified by that name (sometimes an HTML file).

* Once the resource is gathered, the server sends that data back to the IP address sent by the client along with the address. 

Here is the full request/response that I showed you above:

```
$ curl -v --request GET https://dlondonmedina.github.io/tester/
<!DOCTYPE html>
<html lang="en">
<head>
   <meta charset="UTF-8">
   <meta name="viewport" content="width=device-width, initial-scale=1.0">
   <title>Tester</title>
</head>
<body>
<h1>Welcome</h1>
<p>You've successfully tested the site.</p>
</body>
</html>
```

The first line is the request sent by the curl client. The rest of the lines are the HTML content that the server sent back in response. Of course, it's not showing you the entire HTTP wrapper that goes along with the request.  

The Responses will be sent along with a code that signifies what happend. Status code 200 means that the request was successful. Most other codes identify one sort of error or another. You don't need to know much about the error codes for Flask, but they might be useful to learn for debugging or if you're excited about web technologies. The status codes are part of the HTTP protocol just as the methods listed above are.

So, here again is the entire request/response cycle with the HTTP wrapper displayed (I've cut out the TLS handshake that allows encryption to happen with https). The lines beginning with ```>``` are the request from my curl client. The lines beginning with ```<``` are the response from the server, and everything after ```<!DOCTYPE html>``` until ```</html>``` is the body of the response:

```
$ curl -v https://dlondonmedina.github.io/tester/
> GET /tester/ HTTP/2
> Host: dlondonmedina.github.io
> User-Agent: curl/7.58.0
> Accept: */*
> 
< HTTP/2 200 
< server: GitHub.com
< content-type: text/html; charset=utf-8
< last-modified: Sat, 18 Apr 2020 01:54:09 GMT
< etag: "5e9a5dc1-101"
< access-control-allow-origin: *
< expires: Sat, 18 Apr 2020 02:06:20 GMT
< cache-control: max-age=600
< x-proxy-cache: MISS
< x-github-request-id: 
< accept-ranges: bytes
< date: Sun, 19 Apr 2020 00:52:52 GMT
< via: 1.1 varnish
< age: 14
< x-served-by: cache-yvr1522-YVR
< x-cache: HIT
< x-cache-hits: 1
< x-timer: S1587257573.591449,VS0,VE0
< vary: Accept-Encoding
< x-fastly-request-id: 
< content-length: 257
< 
<!DOCTYPE html>
<html lang="en">
<head>
   <meta charset="UTF-8">
   <meta name="viewport" content="width=device-width, initial-scale=1.0">
   <title>Tester</title>
</head>
<body>
<h1>Welcome</h1>
<p>You've successfully tested the site.</p>
</body>
</html>
```
### An Analogy

It is quite important to understand the basic flow of traffic on the internet to understand how our Flask app will be working. Hopefully the technical information above is useful. In this section, I'm going to describe the flow with a metaphor--specifically a postal metaphor.

Imagine I'm a freelance consultant in the old days where invoices were sent over the mail. I've done $400 worth of work for Tyler in Spokane at 203 W 28th Avenue, Spokane, WA, 99203. I fill out my invoice with all the pertinent data (hours worked, rate, subtotal, taxes, total, etc.). That is like the web form that you fill out online. Then I fold up my invoice and put it in an envelope with Tyler's address and my return address. The envelope is the HTTP layer that's necessary for routing by the postal network. 

I drop my letter in the mail and the carrier picks it up. They read the letter and look to see if the address is a valid address. If not, they try to return it to me. If you've ever miss addressed a piece of mail, you've seen that they return it to your return address with a stamp on it saying what the error was (wrong address?). In this case, the process at the postal hub trying to find if my address is valid is like the DNS server trying to find out if the URL is valid and related to a particular physical location (IP address is the address of a particular server or maybe router).

If the address was valid, then the postal carriers take my invoice to the address that I put on it. It arrives at that address, and someone has to be answering the mail (imagine it is certified mail and needs to be signed for). If nobody picks it up, the postal carrier will return it to sender with an error message. Otherwise, if Tyler is there to sign for it, he takes the letter, opens the envelop (if we're using HTTPS, this is akin to decrypting) and reads the content. Tyler is the server on the other end waiting to respond.

If Tyler is unfamiliar with me or rejects the contents of my request, he'll add a note saying something like "I don't know what you're talking about" or something else, and then he'll send my request back to me. This would be like a server sending back an error code because it doesn't know what to do with the request. On the other hand, he'll write me a check, which changes the state of his bank account, and put it into a return envelope with the invoice ticket. 

Then Tyler puts my address on the return envelope and the same transport process occurs as when I sent my invoice to him. If all that works, I get my money and I'm satisfied. If I get an error code, then I might try again or try a different address. In any case, this process of sending and responding with a bunch of address and content checking along the way is very much like a simplified view of internet traffic. 

