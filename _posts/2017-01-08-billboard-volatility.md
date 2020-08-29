---
layout: post
title: 'Billboard Volatility : How Have The Charts Changed?'
author: Brady Fowler
summary: Visualizing Billboard Hot-100 trends over time! In this post I explore the changing trends of the Billboard Hot 100 chart over the past 60 years. Increased "Chart Stickiness" reflects underlying changes in the ways we consume music as well as the rule-changes of the Billboard songs. Further audio analysis reveals that the popular songs of today are more energetic and danceable than ever before!
github: https://github.com/dbfowler/billboard_volatility
image: '/images/post_6/allyears_cover.gif'
date: 2017-01-08 12:00:00.000000000 -05:00
tags: [music trends, billboard, billboard, echo nest, hot 100, logistic regression, next big sound, prediction, python, r, random forest, song popularity, spotify]
---

<p style="text-align: center">
    <a href="#datatime">The Data</a>&nbsp;|&nbsp;
    <a href="#lifespan">The Viz (Gif)</a>&nbsp;|&nbsp;
    <a href="#INTERACTIVE">The Interactive Viz</a>&nbsp;|&nbsp;
    <a href="#audiotrends">Audio Trends</a><br/>
    <a href="#PREDICTING">Predicting Success</a>&nbsp;|&nbsp;
    <a href="#appendixvol">Appendix</a>
</p>

<p>Since the early days of rock and roll, the success of popular music has been tracked and compared through charts. Collective rankings of tracks are useful for tastemakers and listeners alike; they give insight to which artists and musical themes define a time period. Over time, the data behind these charts has changed quite a bit (<a target="_blank" href="https://en.wikipedia.org/wiki/Billboard_Hot_100">Wikipedia has a great page of the history</a>&nbsp;so I won't go into it too much here).</p>
<p>Essentially, the earliest iterations of charts followed song popularity through categories such as "Best Sellers in Stores", "Most Played by Jockeys", and "Most Played in Jukeboxes" and created a weighted "Top 100" songs that showed each track's performance.&nbsp;</p>
<p>As music consumption changed over the following decades, the folks at Nielsen and Billboard updated the data inputs to add CD and singles sales and also added a few new rules to better capture track popularity.&nbsp;For example, the weighting of sales vs. airplay has shifted substantially as the electronic marketplace and subsequent streaming revolution picked up.</p>
<p>One really interesting rule was the 1991 decision to filter out tracks that have been on the charts for at least 20 weeks and fell below position 50. When they announced the latest update to this rule in 2015, <a target="_blank" href="http://www.billboard.com/articles/columns/chart-beat/6770427/adele-tops-hot-100-fourth-week-justin-bieber-alessia-cara">Billboard wrote</a> "Also, note that effective this week, a new 'recurrent rule'&nbsp;goes into effect on the Hot 100: descending songs are now removed from the chart if ranking below No. 25 after 52 weeks, as well as if ranking below No. 50 after 20 weeks. Previously only the latter measure was applied."&nbsp;</p>
<p><img src="/images/post_6/hot100.png" alt="Screencap of this week's Hot 100 chart header." width="100%"> <em><small>Screencap of this week's Hot 100 chart header.</small></em></p>
<p>Other charts have emerged in recent years to better capture streaming success; the Next Big Sound 25 chart, for example, uses a proprietary algorithm to project which artists will achieve future success based on social media and social listening traction. Conversely, the Spotify fueled "Viral 50", "Velocity," and "Rewind" lists capture a mix of shares and listens within the Spotify ecosystem.&nbsp;</p>
<p>I was drawn to the idea of exploring Billboard data when Adele's latest album overtook the charts and spurred dozens of articles about her groundbreaking ride at #1 for so many weeks. I knew from a Rock History course in college that bands like the Stones and the Beatles had similar chart success, but I couldn't find a good summary of how track "lifetimes" have changed over the years. Essentially, what I am interested in here is the ride a given track has on the charts: How long does it take to hit the top of the charts? Has that changed over the years? Can you spot the influence of rule changes?&nbsp;</p>
<p>The structure of the rest of this post is as follows:&nbsp;</p>
<ol>
<li>Collecting the data.</li>
<li>Selected Statistics</li>
<li>Interactive Line Chart Viz: Find your favourite artists and watch their careers grow (or fail...)</li>
<li>Noise Trends: Pulling in Spotify audio fingerprinting data to look at how song styles have changed</li>
<li>Predictive Modeling: Can we use raw audio information and the "state of the chart" to predict if a track will hit the top ten when it first hits the charts?&nbsp;</li>
</ol>
<h1 id="datatime">THE DATA</h1>
<p>In the production of this post, I played with a bunch of different data sets containing the Billboard Hot 100 charts. Each dataset had its own faults (more on this in the appendix) but ultimately, I chose to scrape the Billboard Hot 100 directly, then use the Spotify ID in its metadata to ping the Spotify API and grab audio measures like "danceability" and "energy". The brief process is as follows:</p>
<ol>
<li>Make a list of every day between the present and 9/8/1958 (which is the first chart ever).</li>
<li>Drink coffee.</li>
<li>Split this list into 8 groups and pull the data in parallel from the Billboard Hot 100 site using Gu's Python package. I chose this approach because its fastest and parallelization is pretty pretty pretty pretty cool.&nbsp;</li>
<li>Get a unique list of the Spotify ID's from the #2 data: chunk that and pull back the audio features into a relational table.&nbsp;</li>
<li>Pull the data into R. There are some downright errors in the Spotify IDs provided by Billboard as well as inconsistent spelling of tracks/artist names. These errors make isolating songs with a unique identifier a bit difficult (more on this in the appendix).
<ol>
<li>Some other blog posts using this same data did not appear to have accounted for these problems but since my goal is to look at each track individually, I needed to clean it up.</li>
<li>Those interested can find the exact process in my code, but essentially, I did some fuzzy string distance comparisons and a couple rounds of filtering/matching to try and weed out incorrectly tagged songs and consolidate incorrect spellings.</li>
<li>I am sure some errors slipped through the cracks, but this approach seemed to clean up the data enough to be usable.&nbsp;</li>
</ol>
</li>
<li>Separate out "re-entries": the Billboard rules allow songs to fall off the charts and re-enter. I decided that any re-entry that occurred after 60 days should be split out into a new record; mostly this impacted seasonal tracks and theme songs but was an important step to remove misleading trends.</li>
</ol>
<h1 id="lifespan">
  LIFESPAN OVER TIME<br />
</h1>
<p><img src='/images/post_6/allyears_cover.gif' width="100%"></p>
<p>The GIF above shows the life span of each individual track bucketed by the year it "entered" the charts. The x-axis shows the # of weeks that a track was on the charts and the y-axis shows it's current ranking for that week. Overall you can see the parabolic trajectory of each track over time. Some lead takeaways:</p>
<ol>
<li>Generally, tracks enter at a low ranking and then rise to their peak before tapering off. What is immediately evident here is how the parabola of each song's lifespan widens over time. This reflects the increased lifespan of tracks over time which is visualized below.</li>
<li>Secondly, you can see how the 1991 rule mentioned above really did change the volatility of the charts overall. There is significantly more chart turnover in the bottom 80 tracks in the last few decades and those tracks that <em>do</em> hit the top 10 tend to stay there longer. I have coined this phenomenon "chart stickiness" since in later years, popular tracks stick around longer.&nbsp;</li>
<li>There is no universally accepted standard for "success" on the charts - some artists like Adele have tracks that stay at #1 for long periods of time, while others like Radioactive by Imagine Dragons maintain enough popularity to stay on the charts for a LONG time (like 87 weeks).&nbsp;
<ol>
<li>While reading up on chart-smashing successes, I noticed that most <a target="_blank" href="http://www.billboard.com/articles/columns/chart-beat/7604339/adele-top-billboard-artist-2016-justin-bieber">Billboard stories</a> talk about the post-Nielsen era in recording hits. It is clear that the trend seen in the GIF above is that the underlying data sources and methodologies have as much of an impact on track lifespans as the overall changes in music consumption habits.</li>
</ol>
</li>
</ol>
<p>While you can explore the trends on your own in the interactive below, I compiled the powerhouse artists in terms of the number of weeks their tracks have spent at #1. Takeaway: Damn Mariah Carey.&nbsp;</p>
<p><img src="/images/post_6/powerhouse.png" alt="Powerhouse artists all time vs. past decade: # Tracks that hit top 10 and # Weeks spent at top 10." width="100%"><em><small>Powerhouse artists all time vs. past decade: # Tracks that hit top 10 and # Weeks spent at top 10.</small></em></p>
<p>Below, I segmented tracks by decade and used a GAM smoothing curve to capture the average trend for all tracks. I noticed that in the last 30 years, songs have a slightly lower acceleration in their early lifespan. Furthermore, the tracks that do stick around for the long term almost entirely hit the top 10 (I removed the standard errors here for a better visual but they were relatively tight). Keep in mind that these curves depend on the number of tracks present at a given week/rank combination; the pink 2010 curve's shape at 80+ weeks is set entirely by "Radioactive" by Imagine Dragons.</p>
<p>The other graph shows how the lifespan of tracks has increased over the years with tracks that <em>do</em>&nbsp;hit the top ten sticking around even longer than those that didn't. This "chart stickiness" reflects fundamental changes in the way we consume music and the lengthened cycle of turnover.</p>
<p>So what's the final conclusion here? Both changing habits in music consumption and the rule adaptations intended to accommodate these changes have changed the way songs rise in the charts. Artists could certainly exploit these parabolic trends to perfectly time their track releases and optimize their chances of reaching the top of the charts (see the prediction section at the end for more on this).</p>

<!-- end img gallery -->
<div class="w3-content w3-display-container">
  <img class="mySlides" src="/images/post_6/LOESS_Smoothed.png" style="width:100%">
  <img class="mySlides" src="/images/post_6/Avg_Lifespan_by_Year.png" style="width:100%">
  <button class="w3-button w3-black w3-display-left" onclick="plusDivs(-1)">&#10094;</button>
  <button class="w3-button w3-black w3-display-right" onclick="plusDivs(1)">&#10095;</button>
</div>

<script>
    var slideIndex = 1;
    showDivs(slideIndex);

    function plusDivs(n) {
      showDivs(slideIndex += n);
    }

    function showDivs(n) {
      var i;
      var x = document.getElementsByClassName("mySlides");
      if (n > x.length) {slideIndex = 1}    
      if (n < 1) {slideIndex = x.length}
      for (i = 0; i < x.length; i++) {
         x[i].style.display = "none";  
      }
      x[slideIndex-1].style.display = "block";  
    }
</script>
<!-- end img gallery -->


<h1 id="INTERACTIVE">
  INTERACTIVE<br />
</h1>
<p>Below is a dashboard that shows each of the 27,652 tracks (originals + recurrents). You can play each track through the Spotify plug-in below by clicking on a particular line. Because there is so much data (~300k rows) in this dashboard, the filters take a few seconds to load so its best to choose your time periods of interest first to speed up the processing! If the plug-in below freezes, check it out directly on the <a target="_blank" href="https://public.tableau.com/views/BB3_0/Dashboard1?:embed=y&amp;:display_count=yes">
Tableau website here.</a>&nbsp;If you are logged into Spotify in your browser you'll be able to play full tracks. Otherwise, it should just play a 30 second excerpt for tracks that were matched with a Spotify ID.</p>
<div style="text-align: center;">
<iframe src="https://public.tableau.com/views/BB3_0/Dashboard1?:embed=y&:display_count=yes:showVizHome=no&:embed=true" width="100%" height="800px" align="middle"></iframe>
  </div>
<p>What interesting trends do you folks see? Add your findings in the comment section and if you find a data error feel free to let me know in <a target="_blank" href="https://docs.google.com/forms/d/e/1FAIpQLScY8kgcLREk2MwPGBLHsQjGb8_cPoVQJJ_wi12n1PTtreXP3Q/viewform">this Form.</a></p>
<h1 id="audiotrends">
  AUDIO TRENDS<br />
</h1>
<p>As seen above, the lifespan and trajectories of tracks on the Billboard have changed significantly over the past few decades. Similarly, the specific audio themes of these tracks have changed. Using the Spotify ID pulled from the Billboard site, I pinged their API to extract the "audio fingerprint" of each track. Those features are graphed below:</p>
<p><img src="/images/post_6/Audio_Traits_by_Year.png" alt="Click to Enlarge. My theory about the 2010-ish drop in danceability for non-hit songs reflects the short-lived dubstep fad." width="100%"><em><small>My theory about the 2010-ish drop in danceability for non-hit songs reflects the short-lived dubstep fad.</small></em></p>
<p>Generally speaking, songs have become less acoustic, longer, louder, and more energetic and danceable.&nbsp;<a target="_blank" href="http://static.echonest.com/SortYourMusic/">Spotify defined</a>&nbsp;each of these features as such:</p>
<ol>
<li><strong>Beats Per Minute (BPM)</strong>&nbsp;- The tempo of the song.</li>
<li><strong>Energy</strong>&nbsp;- The energy of a song - the higher the value, the more energetic the song</li>
<li><strong>Danceability</strong>&nbsp;- The higher the value, the easier it is to dance to this song.</li>
<li><strong>Loudness</strong>&nbsp;- The higher the value, the louder the song.</li>
<li><strong>Valence</strong>&nbsp;- The higher the value, the more positive mood for the song.</li>
<li><strong>Length</strong>&nbsp;- The duration of the song.</li>
<li><strong>Acoustic</strong>&nbsp;- The higher the value the more acoustic the song is.</li>
</ol>
<p>You can really see the pickup of the <a target="_blank" href="https://en.wikipedia.org/wiki/Loudness_war">"loudness wars"</a>. The loudness wars is a time period in which engineers boosted track volume during mixing/mastering so that their songs would stand out more on the radio and on CD mixes (the side effect being higher compression and a worse audio experience overall since all the sounds get mushed together).&nbsp;</p>
<p>I also think that the dip then spike in the speechiness "hit top ten" line in the mid 90's and mid 00's respectively shows the rise of rap into mainstream acceptance (likely including the speechiness increase introduced by rapper features). If you have any theories post them in the comments!</a></p>
<h1 id="PREDICTING">
  PREDICTING SUCCESS<br />
</h1>
<p>With all of these trends in mind it is natural to ask the question: given chart conditions (i.e. the average age of songs on the chart,&nbsp;the number of prior hits an artist had and the number of weeks those songs lasted on the charts)&nbsp;and a chart's specific audio features, can we predict whether a song will rise to the top 10 based on their first week?</p>
<p>A couple of classmates and I tackled this question for a final project in our MS Data Science program at UVA this last semester. We used logistic regression models (selected both manually and hybrid stepwise) as well as a random forest model. The models were trained on 1999-2014 data and tested on 2015-2016 tracks to assess our prediction abilities. Our best model achieved a 74% classification accuracy on the hold-out data (however, since this is an imbalanced class situation the AUC value of 69% is certainly more useful).&nbsp;</p>
<p>I won't go into significant detail here, but if you're interested <a target="_blank" href="https://github.com/dbfowler/predicting_billboard_hits/blob/master/Output/LinearModelsReport.pdf">you can check out our final report here</a>&nbsp;(shout out to Monica, Gabe and Lander). Our success in this task shows that there are certain measurable traits, trends and timing that could be manipulated to enhance a track's success on the charts. <a target="_blank" href="https://www.nextbigsound.com/charts/predictions">Next Big Sound</a> (recently acquired by Pandora) has designed a more comprehensive model that accounts for social media trends, seasonality, and other factors to predict artist success. Their prediction task is a bit more difficult than the one we designed since it "aims to predict which artists are most likely to hit the Billboard 200 chart for the first time within a year," but this goes to show how the data trends behind music consumption can be leveraged in a meaningful way.</p>
<h1 id="appendixvol">
  APPENDIX<br />
</h1>
<p><a target="_blank" href="https://github.com/dbfowler/billboard_volatility">The full code for this project can be found on my GitHub here.</a></p>
<p>I mentioned some data problems I tried to account for. The gallery below shows a smattering of these problems that I fixed using some light string matching; the resulting set is certainly not perfect but worked well enough for the exploratory graphics above. If this were used in a more serious application I recommend the <a href="http://chairnerd.seatgeek.com/fuzzywuzzy-fuzzy-string-matching-in-python/">FuzzyWuzzy</a> package in Python since R's <a target="_blank" href="https://cran.r-project.org/web/packages/stringdist/stringdist.pdf">StringDist</a> and <a href="https://journal.r-project.org/archive/2010-2/RJournal_2010-2_Sariyar+Borg.pdf">RecordLinkage</a> packages leave me wanting faster performance and the ability to do partial string matching. Simple <a href="https://en.wikipedia.org/wiki/Levenshtein_distance">Levenshtein distance</a>&nbsp;falls short in this task since names like "Fergie and the Black Eyed Peas" might need to be matched with "The Black Eyed Peas". My quick solution was to compare overlapped string portions (rather than code up the Partial String Match function from FuzzyWuzzy) which worked good enough to clean up some junk.&nbsp;</p>
<div align="center"><img src="/images/post_6/ss1.png" width="75%"></div>
#### Discontinuous Re-entries
Track that rebounded > 60 days after it fell off. This was split into 2 records.
<br/><br/>
<div align="center"><img src="/images/post_6/ss2.png" width="75%"></div>
#### Naming Errors
Artist name misspelling.
<br/><br/>
<div align="center"><img src="/images/post_6/ss3.png" width="75%"></div>
#### ID Issues
Billboard incorrectly applied the same song ID to Beyonce and TaySway songs.
<br/><br/>
<div align="center"><img src="/images/post_6/ss4.png" width="75%"></div>

<p>Finally, I have worked with music data long enough at this point (both the past and future posts on this blog as well as a simple recommender system for my Data Mining final project) to feel outrage at the lack of standardization in track labelling. I think two things would make music data analytics fly in the future:</p>
<ol>
<li>Standardized labelling. "Ft", "feat", "ft.", "featuring", "with", "duet with", "remix", "REPRISE", "part 2", &nbsp;and many more words/symbols are used to willy-nilly string together titles. This makes parsing them out an act of art,&nbsp;prayer and a ton of time to get it right.&nbsp;</li>
<li>Labels and streaming sites should work towards a universally applied song encyclopedia. I have played with Discogs and MusicBrainz a lot which bridge the gap quite nicely but introduce a level of uncertainty that can compound over a large dataset. I understand a lot of these services build their own in-house lookups and don't have a financial incentive to spend the time but that doesn't stop a young data scientist from dreaming!</li>
</ol>
<p>Thanks for reading! Let me know if you have any questions or feedback in the comments. Thanks to Katie, Brian, Bebe, Joe, Mom, Monica, Lander and Gabe for their help, support and feedback!</p>

{% include disqus.html %} 

