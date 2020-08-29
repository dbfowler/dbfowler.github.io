---
layout: post
title: 'Mapping Music: Visualizing America''s Venues'
author: Brady Fowler
summary: In this post I explore the geographic layout and density of music venues throughout the US.
github: https://github.com/dbfowler/
image: '/images/post_3/cover.png'
date: 2015-11-04 19:21:13.000000000 -05:00
tags: [machine learning, album moods, 930 CLUB, ARCGIS, BLACK CAT, brady, BRADY FOWLER, DC MUSIC, DC9, decibels and decimals, fowler, ILLUSTRATOR, MUSIC, music data, MUSIC VENUES, python, r, SEATGEEK, SOUND ORDINANCE, SVG, TICKET PRICES, WASHINGTON DC MUSIC]
---

<p>A major benefit of life in DC is the vibrant music scene.&nbsp;Generally speaking,&nbsp;ticket prices are reasonable, and the wide offering of venue sizes makes the city an attractive addition to the tour schedules of both big-name artists and those yet to be found. Two summers ago I convinced a friend to head to DC9, one of the District's smaller alternative venues to check out Royal Blood. A year later we barely managed to snag tickets for their sold-out 9:30 Club performance.&nbsp;This experience inspired me to ask some questions about music venues' size, location and pricing which I will explore over the next few posts. I'll go over the findings and graphs first and the coding/procedure at the bottom!</p>

<p>&nbsp;The first and most simple questions any frequent concert goer would ask:</p>
<ol>
<li>Where do we listen to music?</li>
<li>How much does it cost?</li>
</ol>
<p>To answer these questions I turned to common online ticket retailers in order to procure some concert data. Unfortunately TicketMaster and TicketFly and other primary market ticket sellers make it difficult to learn about event offerings; my web scrapers for those sites were frequently 404'ed. Instead, I turned to secondary ticket market SeatGeek who provide an <a target="_blank" href="http://platform.seatgeek.com/">awesome API and detailed documentation</a> on how to pull down ticket data. Using secondary marketplace data for concert tickets does come with some limitations <a target="_blank" href="http://www.npr.org/sections/therecord/2012/06/04/154299904/theres-no-such-thing-as-a-sold-out-concert-even-for-justin-bieber">since scalped tickets are often multiple times more expensive than box office prices</a>. Regardless, the SeatGeek dataset can answer some questions for us:</p>
<!-- end img gallery -->
<div class="w3-content w3-display-container">
<object class="mySlides"  type="image/svg+xml" data="/images/post_3/dc_map.svg" width="100%"></object>
<object class="mySlides" type="image/svg+xml" data="/images/post_3/ATLANTA.svg" width="100%"></object>
<object class="mySlides"  type="image/svg+xml" data="/images/post_3/la_map.svg" width="100%"></object>
<object class="mySlides"  type="image/svg+xml" data="/images/post_3/man_map.svg" width="100%"></object>
<object class="mySlides"  type="image/svg+xml" data="/images/post_3/nash_map.svg" width="100%"></object>
<object class="mySlides"  type="image/svg+xml" data="/images/post_3/vegas_map.svg" width="100%"></object>
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

<p><strong>1. </strong><strong><em>Music venues are geographically clustered.</em></strong> 60% of music venues in the United States are located within 5 miles of another venue. Analysis shows that these "neighborhood clusters"&nbsp;often have a consistent mix of small/medium/large locales,&nbsp;which presumably cater to artists of differing popularity.&nbsp;This clustering trend is apparent in the city-level graphs shown above; certain avenues and neighborhoods contain nearly all of a city's venues. It is likely that venues congregate due to specific <a target="_blank" href="https://en.wikipedia.org/wiki/Noise_regulation">sound ordinances</a> in cities, which also has the positive outcome of proximity to bars and clubs.</p>
<p>Even with significant clustering, the effect of sound ordinance on venue location is still a hot topic.&nbsp;Officials in DC are currently considering the extension of stricter residential area sound laws into business/commercial areas. <a target="_blank" href="http://dailycaller.com/2015/10/26/these-new-regulations-would-kill-the-live-music-scene-in-dc/">According to the owner of a DC rock club,&nbsp;Black Cat,&nbsp;the move "could effectively shut down D.C.’s live music venues.”</a>&nbsp;A better understanding of the relatively tight clustering of venues could help DC officials recognize the value of conserving more relaxed sound-level laws for "rock-n-roll-avenue".</p>
<div align="center">
<img src="/images/post_3/bar_chart.png" width="75%">
</div>
<p><strong>2.</strong> <strong><em>More people = More Music.</em></strong> This might seem like a no-brainer but it's interesting to see the effect of population density on the availability of music venues. To explore this,&nbsp;I mapped each venue in the SeatGeek data set to its respective FIPS code (think ZIP code but better) and pulled in 2010 US Census Bureau population counts.&nbsp;</p>
<p><strong>More populated areas are more likely to have more venues</strong>, a trend clearly seen on the All United States map at the start of the post - larger cities are easily accessible to musicians via air and highway and have the greatest concentration of music fans. (The Pearson correlation between population and number of venues within a FIPS area is .82 with a p-value of 0.000.)&nbsp;</p>
<p><strong>Population heavy areas (cities) don't cost more per ticket than less populated areas ('burbs/country).</strong>&nbsp;There is no statistical relationship between population size and average ticket price. Concert cost is relatively consistent across venues nationally, likely due to nation wide tours maintaining prices between cities.&nbsp;This observation's extension into the real world is, however, fairly limited by the data set which contains mostly city-based venues. Perhaps with a larger/historic data set we could see just the opposite trend with time series analysis.&nbsp;</p>
<p><strong>Where are the most expensive concerts in the nation?&nbsp;</strong>This question is only partially answered by the choropleth map below (think heat map by geographic area). The large grey swatches show that we don't have data for every FIPs county in the US. Even with the missing data, we can see that for our dataset of SeatGeek second hand market tickets, the tip of Florida, New York City and southern California have some of the most expensive tickets. The scale, however, is pretty tight and our above correlation findings showed that these more-populated-areas do not have a super strong relationship.</p>
<object type="image/svg+xml" data="/images/post_3/choropleth.svg" width="100%"></object>
<p>Without a more robust data set of primary market ticket prices we only have a partial glimpse of the nation's ticket prices, but it does allow us to visualize the geographic distribution.</p>
<p>So how did I make these graphs? I used a mix of Python, R (Get maps and SQLDF libraries primarily), SVG manipulation, Excel and coffee. Back in college I used ARCGIS a bit, but on a tight time frame and 0 budget I went the freeware route. I did try some freeware alternatives to ARCGIS but they were slow and clunky.</p>
<p>Ultimately the process was as follows. I am more than happy to share code and data if requested!</p>
<ol>
<li>Use Python to pull all events from the SeatGeek API - push to R</li>
<li>Import/Stack/Munge events data. Summarize individual events to venue level. Explore correlations and plot points for specific cities and US in total. Use "noncensus" library, to crosswalk Zip codes to FIPs codes and pull in 2010 census population values. Summarize average ticket price by FIPs code and export to txt. Other packages used:&nbsp;ggmap, ggplot2, sqldf, rcmdr, hmisc.</li>
<li>Use <a target="_blank" href="http://flowingdata.com/2009/11/12/how-to-make-a-us-county-thematic-map-using-free-tools/">Nathan Yau's method from Flowing Data</a>&nbsp;and Python to edit a FIPS code SVG (scalable vector graphic) document from Wikipedia. Import SVG to Adobe Illustrator and spruce it up a bit.</li>
<li>Listened to lots of Mo Lowda and the Humble during the post creation. This band is like a jazzy Kings of Leon from Temple University and they absolutely rock out.</li>
</ol>
<div align="center">
<iframe src="https://open.spotify.com/embed/user/hoyablues/playlist/4xhw6XTxBKsgyCMtc0t8HK" width="300" height="80" frameborder="0" allowtransparency="true"></iframe>
</div>
{% include disqus.html %} 
