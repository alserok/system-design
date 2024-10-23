# Telegram

---
*To begin with, my goal was to make design for a light Telegram version.
Of course, not all the functions are implemented and not all my
decisions are perfect for that use case.*

<img src="images/design.jpg" alt="design">

## Gateway

<img src="images/gateway.png" alt="gateway">

The first step was to make gateway. 
Here, all incoming requests will be checked for the matter of authorization, 
and then will be procedured to required service which instance will be chosen
by load balancer.

## Chats

<img src="images/chats.png" alt="chats">

*Functionality*
* Write a message
* Edit/delete a message
* Send audio/video file

The most important part of Telegram is its chat part. 
Gateway redirects http/websocket requests to one of the instances, 
then a message is being sent to redis pub/sub and stored in DB.

Here, we send a message to redis pub/sub because we have multiple 
instances of 'chats' service. All instances consume the message, and 
if the current user is online, they send him his message, 
if not they don't. After that stage, the message just being stored to DB.

Cache is being used to cache repeating requests to get chat history.
It should be updated after each new message.

<img src="images/chats_db.png">

As DB, I have chosen Mongo because it is suitable for a high load and
storing data that may significantly differ(f.e a message may have attached files, reply etc.) 