---
title: "Relearning REST APIs"
layout: post 
---

# Intro

So if you read my [about](../../about/) page, you might've expected a first-post on something related to AI/ML. 

Well... The short answer is **no**. 

Why? 

First of all, to be completely honest, I don't have anything I *recently* learned about AI/ML that I can write about. 

Plus I already have some things written that I have archived in my Obsidian and Notion. 

So why not utilize the ones I have already? 

My first post will be on REST APIs, and yes it's basic. You might ask, "You're a software engineer and you just recently learned about REST APIs?"

Well I did learn it in school... 2.5 years ago in a Software Engineering class. But the thing is, I forgot. It was only until last week until I soft-started a new project at work, when I was asking my (awesome) manager about what I should do to prepare myself for the project implementation. At that point, I had a bunch of architecture proposals that included a bunch of APIs, one of them being RESTful APIs. 

*"Do you know about REST APIs?"*

Errrrr.... No...?

So there I was, reading blog posts and conversing with my friend Claude -- and here is what I wrote up.

Hope it helps. 

# Table of Contents
- [RESTful APIs](#restful-apis)
- [REST Constraints](#rest-constraints)
- [Benefits of RESTful APIs](#benefits-of-restful-apis)
- [How RESTful APIs Work](#how-restful-apis-work)
- [What's in the Client Request?](#whats-in-the-client-request)
- [Authentication Methods](#authentication-methods)
- [REST API Server Response Contents](#rest-api-server-response-contents)

# RESTful APIs

There are several ways to design APIs.

APIs designed by following the REST architecture are called REST APIs. 
Web services that implement REST architecture are called RESTful web services.

# REST Constraints

## 1. Uniform Interface (UI)

- You can identify resources on a server using a single URI
- e.g. Access user information using `GET /user/{user-id}` — this URI will be unique for each user because each user has their own unique id

## 2. Client-Server based

- The client who requests resources is separated from the concerns of the server who provides the resources
- The separation allows each service to be developed and maintained independently of each other

## 3. Statelessness

- The server does not store information about the client making the resource request
- e.g. if the client provides their username and password to create a new resource in the server, the server does not store the username and password for future inquires from the client
- Every request is independent of all previous and future request

## 4. Layered System Architecture

- The client can interact with multiple intermediary services, while the request to the server is still routed through the intermediaries
    - A client request goes through a security layer, a load balancer, to one of the many servers that handle the business logic
    - The client doesn’t need to know which server its request is routed to
    - Allows for flexibility, security, and scalability without complicating the client’s interaction

## 5. Resource Caching

- Clients can temporarily store server responses to improve response time
    - RESTful web services control caching by specifying themselves as cacheable or non-cacheable
    - When you load a page, the response to `GET /some-image` specifies whether the image can be cached
    - If possible, the browser stores the image for as long as the specified cacheable time

## 6. Code On Demand

- The server can send executable code to the client, which can extend the functionality of the client without having to pre-program the client for every scenario
- e.g. dynamic form validation

# Benefits of RESTful APIs

## Scalability: Handle Traffic Spikes

- Statelessness removes server loads → no sessions states are stored on the server
- Caching reduces the number of requests
- e.g. Attach a load balancer in front of multiple servers and make requests to a single URL without having to specify the specific server

## Flexibility: Decoupling

- Client interface and the backend server can evolve separately
- e.g. Database migration from relational → NoSQL doesn’t mean the client application needs to change the way it requests information from the database

## Independence: Through Standard Web Protocols

- programming language & framework agnostic
- Any client written in any language can communicate with the server as long as it can send HTTP requests
- e.g. An iOS application written in Swift and an Android application written in Kotlin both use the same set of APIs to get information (e.g. `GET /user/12345`)

# How RESTful APIs Work

1. The client sends a request to the server. The request contains all the information necessary for the request to be handled successfully.
2. The server authenticates the client and confirms the client is authorized to make the request
3. The server receives the request and processes it
4. The server returns a response based on the client’s request, indicating whether the request was successful and any requested information

# What's in the Client Request?

## Unique Resource Identifier

- According to the “uniform interface” constraint, every resource in the server is uniquely identified
- The server identifies resources using URLs (uniform resource locator), which specifies the path to the resource
- The URL specifies the exact information the client wants

## Method

- REST APIs are implemented using HTTP methods, which define what the server needs to do to the resource identified by the URL
- idempotency: Performing multiple idempotent requests produces the same result as a single request, whereas a performing multiple non-idempotent methods changes the state of the server multiple times.

| Method | Operation | Idempotent? |
| --- | --- | --- |
| `GET` | read | Yes |
| `POST`  | create | No |
| `PUT`  | update | Yes |
| `DELETE`  | delete | Yes |

## HTTP Headers

- The client and server exchanges metadata with *request headers*, which contain information like:
    - request format
    - response format
    - request status
- If the client is sending a `POST`  or `PUT` request, they might include extra data
- The client can provide additional information on its request with parameters

# Authentication Methods

The server must authenticate the client request before it can process and send a response back

## HTTP Authentication

- Basic authentication: The client sends its base64-encoded username + password in the request header
- Bearer authentication
    - Bearer authentication is the process of giving access control to whoever has the correct authentication token
    - The client usually gets the token through an authorization/login flow
    - Once the client obtains the token, it uses the token in future requests by including it in the request header

## API Keys

- The server gives a unique key to the client
- The client transmits the key when making requests
- Less-secure approach

## OAuth

- Passwords + tokens
1. Server requests password
2. Server requests additional token

# REST API Server Response Contents

## Status

- `2XX`: Successful request and response
- `4XX`: Client-side error
- `5XX`: Server-side error

## Message Body

- representation of the resource from the server in JSON or XML format

## Headers

- metadata about the response

# Sources
Here are the sources I used to write this up: 
- [What is a RESTful API?](https://aws.amazon.com/what-is/restful-api/)
- [Wikipedia Page on REST](https://en.wikipedia.org/wiki/REST)