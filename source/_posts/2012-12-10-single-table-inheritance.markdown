---
layout: post
title: "Single Table Inheritance"
date: 2012-12-10 09:49
comments: true
published: true
categories: 
---

I'm working on a side project to formalize the way poker players take action from one another. The process is a very simple one of initiating a request for action and waiting for a confirmation or denial from the other party. 

So far I have three types of recuests, swaps, pieces, and props. The recuest class looks like this:

{% img [class]  /images/RecuestClass.png 600 [title text [alt text]] %}
***Note: The class is named Recuest to avoid conflict with the rails request class***

Since every type of recuest shared all of the behaviour of this class, inheritance seemed like a natural choice to share this code. Single table inheritance made storing the data and sharing the code a breeze. 


{% img [class]  /images/swap.png 600 [title text [alt text]] %}

One key thing to notice here is that the swap class doesn't inherit from ```ActiveRecord::Base``` and doesn't have it's own table in the database. It inherits from Recuest and instances of Swap are stored in the recuests table. Recuest has a "type" column and when instances of the different types of recuests are instantiated, the type column is filled in with a string representation of the class. 

This allows for some really convenient behaviour. When I want to display all the recuests by a user, I can simply call ```Recuest.find_all_by_user_id(user)```. I also get these methods for each subclass so ```Swap.find_all_by_user_id(user)``` works just like you would expect.

But what about attributes that aren't shared?

{% img [class]  /images/Propclass.png 600 [title text [alt text]] %}

```Description``` and ```time_frame``` are attributes of a prop but not a swap or a piece. They're still stored in the Recuests table but their values default to nil for the other types. Mass assignment is only available in the class you specify ```attr_accessible``` for.

 As of now I have three classes inheriting from Recuest and single table inheritance gives me a convenient way to store this information and share behaviour. Poker players are always coming up with creative new ways to gamble and structuring my code this way allows me to easily add additional classes that inherit this same basic behaviour that all bets require. 