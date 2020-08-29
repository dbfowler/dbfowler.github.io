---
layout: post
title: 'Pitchfork: Reviewing the Reviewers'
author: Brady Fowler
summary: How much bias exists in album reviews? In this post I explore whether reviewer tenure at Pitchfork Media is associated with how harshly albums are scored. 
github: https://github.com/dbfowler
image: '/images/post_1/pitchforkscreenshot.png'
date: 2015-07-19 23:45:00.000000000 -04:00
tags: [album reviews, bias, analysis, brady, data, decibels and decimals, fowler, illustrator graphics, music data, pitchfork, python, r]
---

<p>Since its formal entrance to the music review business in 1999, Pitchfork Media has become one of the most disruptive and influential voices in music criticism. The site is as equally well known for its promotion of “break through” indie bands as its self-indulgent tone and intense distaste for a variety of generally well-regarded musical acts (see&nbsp;<a target="_blank" href="http://www.theonion.com/article/pitchfork-gives-music-68-2278">The Onion roast of Pitchfork</a>). As a casual reader of Pitchfork, I became interested in what I perceived to be a cookie-cutter review format paired with a skewed scoring benchmark, designed to placate intense fans while intriguing casual ones.&nbsp;</p>

<p>I began my exploration of Pitchfork's scoring tendencies by first building a database of every review currently hosted on their site from January 5, 1999 through July 16, 2015. In order to pull down this data, I built a two-stage Python scraping tool: In stage one, I first pulled the URL of every single review listed on the site's 835 "recent reviews" pages. Next, in stage two, using the BeautifulSoup Python library, I visited each of those individual URLs (16,736&nbsp;all together) and parsed out the album, artist, record label, review date, reviewer, and rating. This master list was then exported to a pipe delimited text file.</p>
<p>The munging (cleaning) process was relatively simple and involved consolidating reviewer names and flagging a number of reviews for exclusion in the coming analyses. A reviewer name lookup was generated to correct for spelling errors and alterations (e.g.: “Dr. Beta” was merged with “Andy Beta”). I flagged all collaboration reviews and reviews with well established sarcasm (see:&nbsp;Jet, “Shine On”) for exclusion since I couldn't decide on a good methodology to handle them and they were a small proportion (~.1%) of the set.&nbsp;For analysis and graph generation I relied on a combination of R and Adobe Illustrator. I hadn't used R extensively since college, but found the SQLDF package to be a god send - it allowed me to sidestep much of the clunky R syntax for more concise SQL (my language of choice).</p>
<p>The first graph was intended to set a baseline: how much content does Pitchfork create and, on average how&nbsp;do they score&nbsp;albums?</p>

<object type="image/svg+xml" data="/images/post_1/General Stats.svg" width="100%"></object>


<p>The most noticeable trend is the spike in yearly reviews in 2001, doubling the review output in just a single year. Since that point, Pitchfork reviews around 1,100 albums a year (approximately 4.5 per day excluding weekends). At first, I viewed the flat "average score" line to be a non-story but after some thought, I recognized that this sort of consistency, when reviewing 7,695 artists across 16,243 albums from 8,768 record labels&nbsp;is&nbsp;the story:</p>
<ul>
<li>Conceptually, a 7/10 rating feels lukewarm – a "not so good", "not so bad" rating that will leave hardcore fans relatively nonplussed and newcomers interested enough to read the review and check out the album.</li>
<li>On the other hand, it would be unreasonable to expect a normal distribution of scores averaging out to a 5 – clearly there is some necessary selection bias of the albums (labels won’t consistently put out enough 0 records for Pitchfork to get an even selection and a site has no incentive to review albums no one wants to listen to).</li>
</ul>
<p>Pitchfork is well known for "breaking" new indie artists (e.g.: Arcade Fire, Cold War Kids, Broken Social Scene, <a target="_blank" href="https://en.wikipedia.org/wiki/Pitchfork_Media">see Wikipedia for full list</a>). I determined to estimate how many "new" artists the site introduces to its readers on a monthly basis. I briefly considered consolidating the "artist names" in my data set (Dan Auerbach's "Keep it Hid"&nbsp;would be merged into the Black Keys discography etc.) but&nbsp;with 7,695 artists over 16 years this manual research based review would have taken too long. Instead, in R I proxied "new artists" by determining the minimum review date for each artist - I like to refer to this as an artist's Pitchfork "birthday." I charted the number of artist "birthdays" by month below:</p>
<object type="image/svg+xml" data="/images/post_1/New Artists.svg" width="100%"></object>
<p>Quite impressively, the green line-of-best-fit shows that in a given month Pitchfork likely debuts 35-45 "new" artists. In other words, it is likely that 1 out of the 4.5 average reviews posted per day is a "new" artist, which is a surprising amount of new artist exposure. I did notice that in February/March of each year there is a noticeable drop in the number of new artists - none of my web research suggested a theory-worthy explanation (if you can think of any, drop a line in the comments section, I'd love to hear your thoughts!).</p>
<p>My next thought was to look at the reviewers themselves: who is doing these reviews and how might their biases affect the average 7/10 score? I myself have encountered reviewers who give the occasional album a poor score and harsh review with the simple goal of "creating a name for themselves".</p>
<p>Do young reviewers and weathered veterans score albums differently?

<object type="image/svg+xml" data="/images/post_1/Tenure Stats.svg" width="100%"></object>

<p>The short answer is yes, to a degree. For each&nbsp;reviewer, I used the difference in days between their first and most recent review as a proxy for "tenure". I then calculated the average score given across this tenure and mapped it against the tenure in days. Finally, I colored each reviewer's dot depending on if they were "green" and&nbsp;had produced fewer reviews then the median of 13 and blue if they were above average. The dispersion of green dots along the x-axis at 0-days tenure compared to the more condensed groupings (centering around 7) for reviewers with longer tenure is the story here. The takeaway is that newer reviewers are more likely to have a mixed&nbsp;set of review scores, but over a career this average tends towards 7/10. Some theories on this behaviour:</p>
<ol>
<li>More tenured reviewers develop a taste/preference for certain types of music and consequently review more music that is "up their alley" (I couldn't find any documentation on how reviewers are assigned an album).</li>
<li>Younger reviewers want to make a name for themselves.</li>
<li>As a reviewer's tenure increases so does their number of reviews (if you have only reviewed one album it makes up your entire average score and is not an economically sound sample)</li>
</ol>
<p>To explore the theory in number 3 a bit more, I returned to the data to determine the most likely score given depending on a reviewer's tenure at the time of review (the above chart was done across a reviewer's career, but now we look at their tenure per review). Put into words: my first review is scored as a 5/10 on day 0 of my tenure, my second review 3 weeks later is scored as a 8.6/10 on day 21 of my tenure. The probability densities are shown below:</p>

<object type="image/svg+xml" data="/images/post_1/Density_Plot.svg" width="100%"></object>


<p>I toyed with a lot of methods to show these densities most clearly, but the final product shown above rolls tenure days at review into 24 evenly spaced groups: 0-250 days, 251-500 days... 5,757-6,000 days and so on. If you look closely, you can see that the blue and pink colored curves have a more dense peak than the orange/yellow curves. Furthermore, the average score across all curves levels out to the 7-7.5/10 average we have seen in all previous graphs. These densities show the trend we theorized about earlier: more tenure is associated with a higher probability of receiving an average 7 score.&nbsp;</p>
<p>Overall, this preliminary look into Pitchfork's reviewing tendencies shows an an interesting&nbsp;but understandable pattern of skewed&nbsp;scoring distribution and a discernible effect of reviewer experience on album scores. There is certainly more to look at: I am now turning back to Python to try and parse through the text of each review and estimate the review's tone/attitude (analyses forthcoming).&nbsp;All the analyses shown above are subject to future revision and re-interpretation as new data becomes available.</p>
<p>If you have any questions about the data, graphs or code, reach out in the comments or the contact page!&nbsp;</p>
<p>- db</p>
<p><em>(by the way here are 5 great songs to check out!)</em></p>
<iframe src="https://open.spotify.com/embed/user/hoyablues/playlist/7fs3kYMadT8rhUoQ9v4d3B" width="300" height="80" frameborder="0" allowtransparency="true"></iframe>

<p>Thanks to: Katie, Kendal, Brice (my Python guru), Joe (my R guru), Bebe (draft editor), Robert&nbsp;and&nbsp;Donald for their help and support throughout the creation of my first post!</p>

{% include disqus.html %} 

