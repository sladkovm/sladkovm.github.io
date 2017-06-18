---
layout: post
title:  "3Epic road in Watts"
date:   2017-06-18 12:00:00 +0100
categories: cycling
comments: true
---

When living in the Netherlands I did spend plenty of time with my cycling buddy Roberto. What is plenty? 8-10 hours per week of training... because we are serious and competitive cyclists of course, or, as my wife would put it: "you spend more time with Roberto than with your family".

Now we live in different parts of Europe - I'm in Belgium, he is in Italy, and I will not even try to rise a question who has got a better deal.

Another day, for example, Roberto just went into his back yard and rode the fabulous Granfondo up the slopes of mystical *Tre Cime di Lavaredo*.

The event is called [*3Epic road*](http://road.3epic.it/) and I've decided to take part in the action the best way I know - through the data wrangling.

<iframe width="640" height="360" src="https://www.youtube.com/embed/pqd9hzKL3ZM" frameborder="0" allowfullscreen></iframe>


### Timing the event Italian way

The event was timed by the company [MySDAM](https://www.mysdam.net/home.do). Rather then timing the full course from the start to the finish, only the slopes of the climbs were recorded and the total was summed up.

To make matters even more complicated the final results were presented in the *pdf* format, which is less text parser friendly than the normal *XML/HTML* tree.

Fortunately, my computer and data processing skills are way better than my athletic performances and, after bringing the mastery of *regular expressions* to the new heights, I had a tidy table with 804 records containing names, ranking, categories and timings from the event.


### Fill in the gaps with data from Strava

The next step was to [turn to *Strava* and find all the records from this event](https://sladkovm.github.io/cycling/2017/05/28/UCI-Schleck-Gran-Fondo-in-Watts.html). I've managed to aggregate 92 records and 23 of those were with powermeter data. It is not much and I whisper to activities tracking services and equipment manufacturers to take a notice of these numbers - there is still a huge market out there.

With *Strava* data at hand I've reconstructed the actual elapsed time at the course for all 804 riders. For this I've used linear regression between official ranking time on the slopes and the strava recorded elapsed time for 92 riders.

### Power to people

The riders had to conquer 6 categorized climbs over the course of 108 km with total elevation gain well in excess of 3000 meters.

According to my projected timings the average speed of *20 kph* on such course would be sufficient to finish in top-200. The truth to be told, the projected timing can not take into account the riders who decided to take a break in between climbs and this leads to some inaccuracy in the calculated data. The true average speed of Roberto, for example, is *20.7 kph* instead of calculated *19.5 kph*.

What is more interesting of course, is how much power is needed to be competitive in such events.

To make predictions based on 23 data points is always a risky task, therefore next to the estimated power trend I've added the bands within which this prediction can be deemed reasonably reliable.

In short, to ride the top-100 one would need a normalized power between 250 and 350 Watts. It is *molte* Watts in the absolute sense, but it is also a very large range for any practical use.

It is very tempting to blame the small data pool for such poor prediction, but the truth to be told, we are talking about the high mountains here and the absolute power is not of much use for any adequate analysis. I can only wonder how the prediction would change if I had access to the weight of the riders.

The maximum power quite expectedly is not mind blowing and only confirms that we are talking about the mountains stage here.

![3Epic Road in Watts]({{site_url}}/assets/2017-06-18-3Epic-Road-in-Watts/Lavaredo_power.png)
