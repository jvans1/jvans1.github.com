---
layout: post
title: "Using Click Events to Fill an Autocomplete Field"
date: 2012-12-22 12:23
comments: true
published: true
categories: 
---

The main form on my side project uses autocomplete but I came up with an interesting idea to make the user experience a little nicer. The page looks like: 

{% img [class]  /images/autocompleteST.png 600 [title text [alt text]] %}
 
I thought it would be pretty cool for a user to click on events in the calendar and have that action add the event to the autocomplete field. To accomplish this, I began breaking down the problem. Since the current events that were listed in that calendar were hardcoded in the javascript, I needed to do two main things to make this work: </br></br> 1.  Use click events to add tokens to the autocomplete field </br> 2.  Dynamically Generate the tournament schedule from database to populate the calendar

I used a library called [tokeninput](http://loopj.com/jquery-tokeninput/) for autocomplete so the first thing I did was check their API to figure out how to add a token. I saw the format for adding events manually was: ```selector.tokenInput("add", {id: x, name: y});```

Hardcoding in the id and name to test this, I wrote the following code.

{% img [class]  /images/hardcodeTI.png 600 [title text [alt text]] %}

Clicking on the events in the calendar now added the hardcoded event's token to the autocomplete field. Goal 1, halfway there.

The next thing I needed to do was make the click event specific to the item clicked so the correct event is added to the input field. I thought it would be much easier to do this once I solved the second problem so I started on that. 

Working backwards from the click event, I figured out the next steps I needed to take.</br>
 1. When the click event triggers, I want to grab html around the click event and pull out the information I need(name and ID). 
</br>
 2. In order to do this I need to first embed the correct information into the html. </br>
 3. Since the events are generated through javascript, I need a method to take all the tournaments in my database and generate the correct javascript.

So what does javascript need to look like? </br>
{% img [class]  /images/javascriptcal.png 300 [title text [alt text]] %}



 Ok so I just need to generate an array of hashes with className, title, and date as keys. The className key will let me add the event ID to the html so I can pull that out later. Then I just need the title and the date.

{% img [class]  /images/selftournaments.png 600 [title text [alt text]] %}

A normal ruby array will throw errors in javascript but converting it to JSON saves the day. In order to call this method within a js file, I can end the file in .erb and surround it with erb tags. The only issue here is that each hash returned is actually a string, not a JS hash. I can fix this issue by using jQuery.parseJSON() on each element of the array and creating a new one. 


{% img [class]  /images/tourneyjs.png 600 [title text [alt text]] %}

Now everything is all set, the graph generates dynamically from my database. The only thing left is to extract the information I embedded in the html during the ```onClick``` event and add it to the token input field.

{% img [class]  /images/htmlparse.png 600 [title text [alt text]] %}

After playing around in console for a bit, I used jQuery travserable methods to access the correct ID and name information that was in the html. The id became a class of a div that had several classes in it. I used ```split()``` to create an array of strings and select the one I was looking for. Using ```class_array[class_array.length - 2]``` works but is not ideal solution since the addition of another class will break it. I plan on refactoring here and using a method that loops through the array and selects the class string with length 1. I think it's pretty safe to assume any other class added will have a length greater than 1. 

