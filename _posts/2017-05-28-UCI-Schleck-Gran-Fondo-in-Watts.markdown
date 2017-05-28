---
layout: post
title:  "UCI Schleck Gran Fondo in Watts"
date:   2017-05-28 20:00:00 +0100
categories: cycling
---

### Meaningful metric

In what? In *Watts* - the units of power and the only cycling metric to objectively report the level of suffering during the bicycle race.

In [my previous post](https://sladkovm.github.io/cycling/2017/05/23/what-it-takes-to-qualify-for-uci-gf-wc.html) I did a quick and dirty job at analyzing average speed and finishing time distribution of [*Schleck Gran Fondo*](http://schleckgranfondo.com/) finishers. That exercise might tell me that I was *20 min* or *2.4 kph* short of the [*UCI Granfondo World Championships*](http://www.uci.ch/cyclingforall/uci-gran-fondo-world-series/) qualifying performance, but what it means in terms of real efforts is a wild guess.

Fortunately many of us [do train with power meters](https://sladkovm.github.io/cycling/2017/05/21/Garmin-Vector-vs-Stages-Power.html) and I've bravely embarked on a quest to find the power numbers of my competitors.

### Marrying ChronoRace with Strava

In order to get the power numbers of participants of the *Schleck Granfondo* I first needed to find all *Schleck Granfondo* activities on *Strava*. For that I've used the [*Strava activity search*](https://www.strava.com/activities/search) engine.

I've requested a list of all activities from the starting village *Mondorf-les-Bains* and specified that duration of activities must be between 4 and 8 hours, the distance between 150 and 170 km and the elevation gain between 1800 and 2400 m.

The *Activity Search* is not a *RESTfull* API and I had to do a careful parsing of the returned pages in order to extract IDs of activities that match my search criteria. In total I've collected about 2000 activity IDs.

The next step was to use the collected IDs to request the activities summary information. This step fortunately relies on the *RESTfull* [Strava API](https://strava.github.io/api/).

With activities information at hand I've extracted only records from the date of the *Granfondo* event. I've also focused only on those activities that had power data. In total I've ended up with about 100 activities - 10% of the *Granfondo* participants pool.

With Power numbers from *Strava* and finishing ranking and time from [*ChronoRace*](https://sladkovm.github.io/cycling/2017/05/23/what-it-takes-to-qualify-for-uci-gf-wc.html) I was ready to go. With help of *python* and *pandas* I did merge 2/3 of results using names of the riders. For those records, where *Strava* name did not match the *ChronoRace* name, I've inferred finishing ranking from the average speed using the linear regression fit on the *ChronoRace* dataset.

### Boxplot analysis of Normalized and Maximum power

For power analysis I've focused on the [Normalized Power](https://help.trainingpeaks.com/hc/en-us/articles/204071804-Normalized-Power) and Maximum Power.

The first metric reports kind of an averaged Power, but with high intensity efforts contributing more to the final results than the low intensity efforts. Essentially it values, when the rider is in red, and underrates, when the rider is slacking in the endurance zone. The Maximum Power does not require explanation.

Given I have the very limited set of data (only 10% of the total pool) I've grouped power data according to the finishing ranking. The lower x-axis shows groups, e.g. top-100, 100-200 and so on. The upper x-axis shows the finishing time-frame for each group.

The actual power data are plotted as scatters and overlaid with boxplot statistics per group.  For those unfamiliar with boxplot - the middle blue line is a median, the upper/lower edges of the box are the 25% and 75% quartiles respectively, the whiskers are the statistically relevant extrema. Basically it is a clever way to show not only the mean values, but also the spread in underlying data.

![GF Schleck Watts]({{site_url}}/assets/2017-05-28-GF-Schleck-Watts/gfschleck_power.png)

### I do suck way more than I tend to believe

Now comes the hard truth. In order to finish in top-100 one needs to be able to sustain 280-300 Watts of Normalized Power for more than 4 hours. The sprinting power must also be there and well in excess of 1000 Watts.

Normalized power numbers do drop, more down the ranking we go, and flatten at around top-400 at 230 Watts. My placement at the finish was 476 with 228 Watts and, with a bit of luck, I might have made it to the top-400, but no more than that.

It is interesting to note that the high max efforts are only present in the top-100 group. When you are not in that category of the riders it probably makes no sense to sprint to the line anymore.

Another useful metric to analyze would be a *power per weight* ratio, but here I'm out of luck. In order to protect the riders privacy, *Strava* returns only very brief summary of the requested activities. Therefore, without explicit authorization from the user, the third party applications (me in this case) can not request weight, ftp and other sensitive information.  
