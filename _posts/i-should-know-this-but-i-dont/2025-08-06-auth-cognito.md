---
title: "Authentication & Authorization (ft. Amazon Cognito)"
layout: post
date: 2025-08-06"
categories: [I-Should-Know-This-But-I-Don't]
---

# TL;DR

- **Authentication** is the process of proving **who you are** to the system you want to access.
  - An **Identity Provider (IdP)** is a trusted service that authenticates users and shares the verified identity information with applications that rely on it for login. For example, when I log into my LeetCode account using GitHub, GitHub acts as the IdP.
- **Authorization** is the process of verifying **what you can do** within the system to which you’re authenticated to access. Typically, authentication comes before authorization.
  - A **Federated Identity** in a system (e.g., a mobile application) is a digital identity for which the system itself doesn’t perform the user’s authentication; instead, it relies on an authentication token issued by another, separate identity provider

<div style="text-align: center;">
    <img src="/assets/i-should-know-this-but-i-dont/authentication-authorization/authentication-authorization.jpeg" alt="Basic Auth" style="width: 80%; max-width: 800px;">
</div>

# Intro

I don’t blame anyone who clicks out of my blog posts after reading my TL;DR. Honestly, I do the same – I either read the TL;DR (if there is one) or scroll down to the conclusion.

You see, my goal with these blog posts is **not** to get people to read the entire thing. It’s actually the exact opposite. I’d say a successful post is one where the reader clicks out after reading my TL;DR. So if you’re still here reading this, I’ll do a better job next time :)

A post in this series has at least one of these two intentions.

1. For _me_ to understand something to the point that I can bring it up and answer questions about at work without having to pretend I know what I’m talking about.
2. For _you_ to understand something as quickly as possible.

Now, this post started with Intention #1. I was talking about a user authentication and authorization flow with Amazon Cognito, and found myself unable to articulate what a “federated identity” was. I had simply used the term because those were the exact words of the AWS docs I referred to when I read about auth solutions in AWS, and giving logged-in users access to AWS services. That conversation made me realize I’d been repeating terminology I hadn’t truly internalized—so I set out to fix that.

Whenever something like this happens – and I’ll tell you, this wasn’t the first time – I feel so, so, so painfully dumb. But I try to look at the bright side, persuading myself that I’m at least 0.01% closer to the engineer I hope to be if I just take a few hours to nail down the concept.

So, here’s to another addition to I-Should-Know-This-But-I-Don’t – hope it helps!

# I wish I could get into one someday

> Going through TSA → Authentication

> Getting into an airport lounge → Authorization

I’ve been traveling a lot more than ever this year, and I can’t think of a better analogy for this post’s topic than the process of going through TSA and maybe a nice lounge afterward.

Authentication is like going through security. You give your Real ID (which I hope you have by now if you want to travel domestically), and the airport staff makes sure you’re really you, before you can rummage through your backpack and get your laptop out. At least we don’t have to take our shoes off every time – this was a nightmare when all I used to wear were Converse high tops.

Once you’re in, if you have a fancy credit card, you might be able to go into a nice airport lounge. That’s authorization. Even if I’ve proved that I’m me and successfully made it into the sterile area, I can’t go into any of the lounges because I don’t have permission – i.e., whatever type of credit card I need, I don’t have it.

# What exactly was I pretending to know?

So after the meeting where I couldn’t articulate what a federated identity was, I went back to my desk and started typing into Perplexity. Except, the definition it gave me didn’t make any sense because A. I didn’t know the definition of the words it used to explain federated identities, and B. I still didn’t understand how it tied to how Amazon Cognito Identity Pools worked.

Here’s what is written in the AWS Docs, word for word (as of August 2025):

> An Amazon Cognito identity pool is a directory of federated identities that you can exchange for AWS credentials

All I knew at the time of the meeting was that you need to set up a Cognito Identity Pool for users in your system’s Cognito User Pool to access AWS’s resources or use its services (e.g., upload files to an S3 bucket). So, when I was explaining my solution to my project team, I used the term “federated identities” and “cognito identities” interchangeably, until I got struck with a question that – I don’t remember word for word – required me to articulate what a federated identity was.

But…

- Why were Cognito Identity Pools originally called Federated Identities?
- What do I call a Cognito Identity in general terms – i.e., in a way that people with no AWS experience, but with a background in tech, understand?
- How do I explain the difference between a Cognito User and a Cognito Identity in a way that I can post it in the “Explain Like I’m 5” Subreddit?

So I’ll be spending the next few sections answering my own questions, probably sprinkling in some definitions I wrote in the [TL;DR](#tldr) here and there.

# Cognito what?

Let me take a step back for a second and tell you what Amazon Cognito is, since some of you may not be familiar with AWS services, let alone Cognito. I think it’ll also help, since I’ll be using it to describe terms like “Identity Provider” or “Federated Identity.”

According to the official docs, Amazon Cognito is an AWS service that “handles user authentication and authorization for your web and mobile apps.”

Simply said, it helps you set up a system for:

1. Verifying who your users are
2. Defining what your users can do

Currently, it has two main services: Cognito User Pools and Cognito Identity Pools. Cognito User Pools handle authentication—verifying who your users are—while Identity Pools enable your users to access AWS services.

Section done. K bye.

Well… I wouldn’t have pulled on my hair all my precious weekend if that did it for me, because obviously it didn’t.

The fact that Cognito Identity Pools were previously called “Federated Identities” deeply bothered me, especially because that was my weak spot – the question I couldn’t answer in that meeting. I used the terms “Cognito Identity” and “Federated Identity” interchangeably, assuming that a “Cognito Identity” was the AWS version of the more generic “Federated Identity.”

# So what’s a Federated Identity?

Some of the most common search results told me that a federated identity is a solution that allows users to access multiple applications and/or services using a single set of credentials managed by a trusted identity provider (IdP).

In my context, that made absolutely no sense.

First of all, I only had a single application, not multiple.
Second, I barely remembered what an identity provider even was, ever since I took my last CS elective class in Cloud System Administration.

I had to take another step back and figure out what an identity provider even was.

## Identity Provider (IdP)

An Identity Provider (IdP) is a trusted service that checks in your users, makes sure they’re really who they claim they are, and shares the verified identity information with applications that rely on it for login. Many of us have probably seen the “Sign in with Google” or “Sign in with Facebook” options when trying to log into a new website or application you’re trying to use. Here, Google and Facebook would be your identity providers – services that the application/website trusts to verify its users.

Let’s try this federated identity thing again
With my understanding of an IdP, I revisited the definition of a federated identity.

> a solution that allows users to access multiple applications and/or services using a single set of credentials managed by a trusted identity provider (IdP)

The next thing that bothered me was how this “identity” was defined as a “solution” (system in some other definitions). I decided I would need to come up with a definition of my own and asked ChatGPT to explain what a federated identity was like I was dumb. It actually did serve as a gateway to my current understanding, so I thought it’d be worth sharing.

## Summer Libraries

I don’t know about you, but when I was in elementary school, my local libraries always had a summer reading challenge, where you would complete a reading challenge, and as a reward, get a free scoop coupon to the infamous [Fenton’s Creamery](https://www.fentonscreamery.com/about) (iykyk, [the ice cream shop that comes up at the end of the movie Up](https://www.reddit.com/r/MovieDetails/comments/kvuksn/in_up_2009_carl_and_russell_go_to_fentons_this_is/)). I used to have the tie-dye-designed library cards, which would get me into all the public libraries in Berkeley.

One card, multiple libraries – that’s the point of focus.

My library card was issued at the central branch, the largest one, where the children’s section was on the fourth floor. However, I didn’t live a walking distance away from the central branch, so the branch I visited the most was a smaller library that was a 10-minute walk away from where I lived.

Just because I was going to a physically different library location didn’t mean that I had to get a separate library card issued – I could simply use the one from the central branch because my local branch trusted the central library that the cards they’d issue were legit.

The card is analogous to a federated identity from the local branch's POV because the proving-of-who-I-am (i.e., authentication) was done elsewhere, outside the local branch. So, to put it clearly, a digital identity is federated if an application or a service accepts an authentication token issued by another, separate identity provider, rather than performing user authentication natively.

So, to put it together, it’d be:

- Central branch: IdP
- Local branch: Mobile application that trusts IdP
- That single library card: The federated identity

<div style="text-align: center;">
    <img src="/assets/i-should-know-this-but-i-dont/authentication-authorization/library-analogy.jpeg" alt="Library Analogy" style="width: 80%; max-width: 800px;">
</div>

# Back to Cognito

Let’s now return to Amazon Cognito and link all this back together.

In Cognito, there are two separate services:

- **User Pools** that handle proving who you are (i.e., authentication)
- **Identity Pools**, which help authenticated users get access to AWS resources (e.g., once you log in, you can start uploading images that will get stored in the AWS Cloud – S3 buckets)

Now the part that tripped me up: **Cognito Identity Pools were previously called “Federated Identities.”**

At first, I thought, “I’m not using Google or Facebook to log into my app. It’s just Cognito itself. Where does the 'access multiple applications and/or services using a single set of credentials managed by a trusted identity provider’ come in?

And then it hit me: even though the user is logging in through Cognito User Pools, once they’re authenticated, **the Identity Pool still treats that identity as federated** -- because from the Identity Pool’s perspective, **it didn’t do the authentication itself**. Instead, it accepted a token that came from a separate system: the User Pool.

So yes, even though both services are part of “Cognito,” they operate independently. And because the Identity Pool is just receiving a token from somewhere else (even if that “somewhere else” is within AWS, within Cognito), that identity is federated from the Identity Pool’s point of view. It’s **external** from the Cognito Identity Pool’s POV.

That’s why it was originally called “Federated Identities.” It’s not necessarily about logging in with Google or Facebook. It’s about where the authentication happened—and whether the system that’s granting you access did the authentication itself or accepted it from somewhere else.
So that cleared up some confusion as to why Amazon would call something a “federated identity” when all my work involved signing users up with a basic Cognito User Pool login.

 <div style="text-align: center;">
    <img src="/assets/i-should-know-this-but-i-dont/authentication-authorization/cognito-pools.jpeg" alt="Cognito Relationships" style="width: 80%; max-width: 800px;">
</div>

# Closing

Now, if you’re still here, through all my drawings, TSA metaphors, and library stories—thank you nonetheless.

As always, I didn’t expect this post to get this long. But this is what happens when I get caught up in things to the point where I have to write seven pages worth of a post according to Google Docs.

For those of you who scrolled down all the way to see if I had any other conclusions, you’re in the wrong section – all my TL;DRs are, and will be, upfront, and I don’t plan to change that anytime soon.

Thanks to everyone who stopped by, and let me know if you have any questions or comments about anything I write!

Until my next confusion,<br>Ael
