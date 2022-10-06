---
layout: post
date: 06/10/2022
title: How to logout with JWT
tags: jwt session
---
A few years ago, when I went to the SE interview, I got an interesting question: "How do you logout using JWT? Can you expired a JWT Token?". At that time, I had lack of experience and I thought JWT libruary will have some kind of API functions to expired the token, then I said yes. In this article, I will dive into this question to find the complete answer.

## JSON Web Tokens
JWT (JSON Web Tokens) provides a way to handle user authentication in a stateless way. What does that mean? Alright, It helps to manage authentication in any storage without storing the authentication state, whether it be a session or a database. Therefore you do not need to access the session or perform a database query while verifying the user’s authentication status. Instead, you create a token based on the user payload of your choice and use it to identify the user on the server in client-side requests.

So, basically, once a token is created, it can be used permanently, or until it is expired. After specified time, JWT generator can get an option to invalidate the token. You cannot manually expire it. 
So what should you do if you wish to invalidate an existing token? What should you do when the user decides to sign out or let’s say change password?
 
## 1. Simply remove the token from the client
As you know, the client side store the token somewhere (in local storage or cookies) while using JWT authentication, and attaches it to any request that needs authentication. So the first thing we need to do is delete the token we saved on the client side. This does nothing for for server side security. If the attacker had stolen the token before we deleted, they would still be able to make requests on the user’s behalf.

## 2. Create a token blacklist
To verify if token is valid from the server side, we need save all the tokens that are invalid and have not expired yet into storage. It usually called "blacklist". You can use a DB that has TTL option on documents which would be set to the amount of time left until the token is expired. **Redis** is a good option for this, that will allow fast in memory access to the list. Then, in a middleware of some kind that runs on every authorized request, you should check if provided token is in The Blacklist. If it is you should throw an unauthorized error. And if it is not, let it go and the JWT verification will handle it and identify if it is expired or still active.

This solutions's drawback is it make the authentication verification slow. We have to query the blacklist on every API call. Therefore, we will move to a new way to handle this situation.

## 3. Use a refresh token
In this solution, when the user logs in, the server issues a both short-lived JWT (the access token), and a long-lived refresh token. Refresh token will be store on the database to manage  When a user is granted access, both of these tokens are sent to the client. We also create a JWT refresh API endpoint that the client can call before the expiry of the JWT.

When the user attempts to access a resource, they send the JWT access token along with every request. When the JWT expires, the client then uses the refresh token to request a new JWT and a new refresh token. This process is known as refresh token rotation.

The benefits of this approach is that if you want to revoke access, then all you need to do is invalidate the refresh token on the server side. Then, when the refresh endpoint is called, the server looks up the refresh token, sees that it has expired, and logs the user out.

Note that this doesn’t solve the problem of still-valid JWTs existing on the client. But, because you can make those JWTs much more short-lived (even just a few mins), you should find that it’s not too big a problem.

