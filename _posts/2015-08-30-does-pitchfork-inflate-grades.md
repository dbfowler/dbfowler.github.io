---
layout: post
title: Does Pitchfork Inflate Grades?
author: Brady Fowler
summary: Using a naive Bayes pre-trained sentiment analyzer I determine whether or not Pitchfork's reviews are graded on a curve!
github: https://github.com/dbfowler
image: '/images/post_2/Histogram.png'
date: 2015-08-30 22:57:46.000000000 -04:00
tags: [machine learning, album reviews, sentiment analysis, python, pitchfork, album reviews, bias]
---

<p><a target="_blank" href="https://twitter.com/intent/tweet?text=Sentiment%20analysis%20of%20Pitchfork%27s%20review%20text%20suggests%20scoring%20inflation.%20Read%20more%20here:%20decibelsanddecimals.com">Sentiment analysis of Pitchfork reviews suggests scoring inflation for some albums. (click to Tweet this).</a></p>
<p><a target="_blank" href="http://www.decibelsanddecimals.com/dbdblog/2015/7/18/pitchfork-r">In the last Decibels and Decimals post </a>we visualized Pitchfork Media's album reviewing tendencies, concluding:<br />1. Reviewer tenure (at the time of review) does appear to be correlated with the score given to an album.<br />2. The average album review received around a 7.5, even while the number of albums reviewed annually exploded (200 to 1,200) and the genre mix changed.<br />3. Pitchfork reviews approximately 40 new artists a month.</p>
<p>For this post, I used <a target="_blank" href="https://en.wikipedia.org/wiki/Machine_learning">machine learning</a> techniques to estimate the sentiment for each Pitchfork review's text content since 1999 (16,792 reviews total after data cleaning). Later on we will get into how sentiment analysis really works (and some code!),&nbsp;but first, the graphs:</p>
<img src="/images/post_2/animation.gif" width="100%">
<p>On the Y axis I've plotted the estimated sentiment of the review against the actual score given to that Pitchfork album.</p>
<p>For example, an album review reading "This album is AMAZING and is definitely the best album of the past decade!!" while receiving a score of 5/10 would be plotted in the upper middle of the graph. On the other hand, a review with text content that accurately mirrors the numeric score would fall most closely to an imaginary 45 degree line beginning at the origin (0,0) and extending to the maximum score (10,10).</p>
<p>The trend line shows that each year, a higher rated album will have more positive sentiment. To generalize, a highly rated album will have a review that says nicer things about it (here is where you get to say "...duh... did we really need to go through all that trouble to show this?").</p>
<p><em>However</em>, what is most interesting about these graphs is the high density of points around the 7.5 mark of review score. To get a better idea of what is going on with the estimated sentiment for reviews awarded the average 7.5 score I ran the following distribution:</p>
<img src='/images/post_2/Histogram.png' width="100%"><em><small>Here we can see that albums scored a ~7.5 by Pitchfork are most frequently accompanied by lukewarm 5.5 or 6.0 rated text.</small></em>
<p>After limiting the data to albums receiving between a 7.4 and 7.6, we can see that the majority of those reviews receive a 5.5 or 6 score in terms of estimated sentiment. This implies that, intentionally or not, reviews with a more-lukewarm editorial text are frequently receiving <em>inflated</em>&nbsp;scores in the upper 7.5's.&nbsp;</p>
<p>A reasonable explanation for this sort of grade skewing is that editors attempt to control for different writing styles and enthusiasms in the review text by separately choosing a numeric score.</p>

<p>So how does this all work?</p>
<p>Sentiment analysis has become a popular and useful tool for businesses and researchers seeking to quickly classify interactions on social media,&nbsp;news content and much more. The classifier I relied on leveraged naive Bayes classification models to project the most likely sentiment-polarity given a chunk of text. These sorts of naive Bayes sentiment models must be trained with test sets in order to learn more specifically which words, phrases and sentence constructions are most associated with neutrality, negativity and positivity.</p>
<p>I used a model "pre-trained" with two data-sets frequently used by researchers that respectively contain Tweets and movie reviews hand tagged with a variety of sentiment metrics. I accessed <a target="_blank" href="http://text-processing.com/demo/sentiment/">this model</a> via their API by writing Python to send an editorial's text to the site and then parsed and stored the returned positivity/negativity/neutrality metrics. You can play with the site to get a better feel how it works:</p>
<img src="/images/post_2/screenshot.png" alt="(The metrics received via API have more significant figures than we see here!)" /> <em><small>(The metrics received via API have more significant figures than we see here!)</small></em>
<p>Since the first post, I have become more comfortable in my Python and R skills. However, while initially pulling sentiment from the API I neglected to include rest stops in my code and was temporarily banned by the text-processing.com admins -&nbsp;sorry for that, I've learned my lesson :). Below is a partial code excerpt - contact me via Twitter if you'd like to discuss the code or grab some data!</p>
<p>Upcoming analyses I'm really excited about:&nbsp;Where do Americans listen to music and how much do they pay for it? Exploring Spotify's related artist network with node analysis!</p>
<pre class="source-code">fileHandle = open("/files/full_review_details_20150817.txt","r")
for line in fileHandle:
    fields = line.split('|')
    editorial = fields[7]
    try:
        data = urllib.urlencode({"text":editorial})
        response = unirest.post("https://japerk-text-processing.p.mashape.com/sentiment/",
            headers={
                "X-Mashape-Key": "BRADYS API KEY",
                "Content-Type": "application/x-www-form-urlencoded",
                "Accept": "application/json"
            },
            params={
                "language": "english",
                "text": editorial
            }
        )
        sent_negative = response.body.get('probability').get('neg')
        sent_neutral = response.body.get('probability').get('neutral')
        sent_positive = response.body.get('probability').get('pos')
        sent_label = response.body.get('label')
        sent_positive_scaled = sent_positive*10</pre>
<p>Thanks to Bebe for proof reading and Katie for making me a margarita while I wrote the post :)</p>

{% include disqus.html %} 