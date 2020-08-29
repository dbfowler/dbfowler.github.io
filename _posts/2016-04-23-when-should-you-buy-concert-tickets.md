---
layout: post
title: "When Should You Buy Concert Tickets?"
author: Brady Fowler
summary: Using proprietary data shared by SeatGeek to analyse trends in ticket pricing for big-box acts. Ever wondered when is the best time to buy concert tickets? This analysis shows that taking on the risk and buying at the last second could pay off!
github: https://github.com/dbfowler
image: '/images/post_4/cover.png'
date: 2016-04-23
tags: [ticketing, music economy, BRADY FOWLER, r, SEATGEEK, ticket, TICKET PRICES]
---

<p>It's 10 AM on a Tuesday and I'm anxiously refreshing the 9:30 Club ticketing page, trying to buy tickets for the St. Paul and the Broken Bones' New Years Eve show. A few minutes after my first attempt, the show was sold out and I am out of luck. After shopping around on secondhand ticket sites, I find two GA tickets priced just above the original official page - wanting to hear "Call Me" as the ball dropped, I anxiously pulled the trigger.&nbsp;</p>
<p>Those who buy tickets are familiar with the question: should I buy now or wait to see if the prices drop later? There are a host of factors in play that can alter ticket prices: total venue capacity, event exclusivity (DMB tours every summer, The Stones won't be touring for much longer..), tour scale (nationwide vs. regional tours have differing production costs) and of course the demand for tickets.&nbsp;</p>
<p>In my last blog post, I scraped data from the secondhand ticket aggregation site <a target="_blank" href="https://seatgeek.com/">SeatGeek </a>in order to map music venues across the United States. This time, I sought to explore whether or not there is a "sweet spot" of when to buy concert tickets from resellers.&nbsp;</p>
<hr />
<h1>THE ANALYSIS</h1>
<p>For this post, I worked with Chris Leydon from SeatGeek to pull some sales information on the big acts of last summer. Although transaction level data were unavailable for privacy reasons, Chris sent over the median ticket price by day for each concert held by the summer's biggest acts, namely:&nbsp;Taylor Swift, Ed Sheeran, U2, The Rolling Stones, Kenny Chesney, Billy Joel, Rush, Grateful Dead, Foo Fighters, and One Direction.</p>
<p>      <img src="/images/post_4/all_acts.png" width="100%"></p>
<p>First I plotted the daily median price of every event in the data set (see above). I colored each event line by artist, which highlights the first takeaway: daily prices are quite volatile. This, coupled with the privacy reasons mentioned before, is why Chris recommended using the median price.&nbsp;</p>
<p>The <strong>upside</strong> to using median-price by day is that we are able to visualize general fluctuations in ticket prices over time without worrying much about outlier postings. The <strong>downside </strong>is that, without more detailed information on sales volume (i.e.:&nbsp;the quantity of tickets sold by section and price point) its hard to determine what may have driven price fluctuations.&nbsp;</p>
<p>Nevertheless, at a high level the question of: "When is the best time to buy tickets?" appears to have different answers depending on your artist of choice, but not broad differences across an artist's concerts:</p>

<!-- end img gallery -->
<div class="w3-content w3-display-container" style="width:100%">
<img class="mySlides" src="/images/post_4/Trend+for+Billy+Joel.png
" style="width:100%">
<img class="mySlides" src="/images/post_4/Trend+for+Ed+Sheeran.png" style="width:100%">
<img class="mySlides" src="/images/post_4/Trend+for+Foo+Fighters.png" style="width:100%">
<img class="mySlides" src="/images/post_4/Trend+for+Grateful+Dead.png" style="width:100%">
<img class="mySlides" src="/images/post_4/Trend+for+Kenny+Chesney.png" style="width:100%">
<img class="mySlides" src="/images/post_4/Trend+for+One+Direction.png" style="width:100%">
<img class="mySlides" src="/images/post_4/Trend+for+Rush.png" style="width:100%">
<img class="mySlides" src="/images/post_4/Trend+for+Taylor+Swift.png" style="width:100%">
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


<p>These graphs show the rather extreme trends by artist; some thoughts on their creation/interpretation:</p>
<ul style="margin-left:40px;">
<li>Note that each graph has a different Y-axis tailored to an artist's individual range of median ticket prices.&nbsp;&nbsp;</li>
<li>I have pooled all events for an artist together, and using the STATSMOOTH R function to generate a LOESS curve for the artist
<ul style="margin-left:40px;">
<li>A LOESS curve is a method for locally weighted polynomial regression;</li>
<li>The surrounding shadow area denotes the 95% confidence interval (aka how often observed values will fall within the range);</li>
<li>Essentially the goal is to fit a line to a scatter plot (typically with noisy data) that may not have a clear linear line of best fit;</li>
<li>For each data point (n)&nbsp;in a plot a regression value is computed - we then weight whichever regression values are closest to the real values in a given subset/window of the data. Finally these weighted values are used to estimate a line of best fit;</li>
<li>The above is a simplification of the process but essentially its a quick and dirty way to pull a trend out of scattered and temporal data which is just what we have here;&nbsp;</li>
</ul>
</li>
</ul>
<h1>FURTHER Q'S</h1>
<p>At the conclusion of these initial peeks I have more questions than answers about the secondary ticketing market! Clearly there are some noticeable (probably exploitable) fluctuations in ticket prices as we get closer to the D-Day of a concert. Back in January, New York Attorney General Eric Schneiderman said “Ticketing is a fixed game..”&nbsp;and his office release a report on the ticket markets which opened:&nbsp;</p>
<p style="margin-left:80px;"><em>The New York Attorney General (“NYAG”) regularly receives complaints from New Yorkers frustrated by their inability to purchase tickets to concerts and other events that appear to sell out within moments of the tickets’ release. These consumers wonder how the same tickets can then appear moments later on StubHub or another ticket resale site, available for resale at substantial markups. In response to these complaints, NYAG has been investigating the entire industry and the process by which event tickets are distributed – from the moment a venue is booked through the sale of tickets to the public. This Report outlines the findings of our investigation</em><em>. [http://www.ag.ny.gov/pdfs/Ticket_Sales_Report.pdf, January 2016]</em></p>
<p>The available data suggest that as we get closer to an event, ticket prices are more volatile (likely due to increased supply and demand as concert goers finalize their schedules), so it is possible that one could exploit a sizable price drop. I have found that, outside of "must-see-shows" the best approach is to buy the afternoon of a show and, if things don't go as planned hit up the box office as soon as the openers hit the stage.&nbsp;SeatGeek has developed behind the scenes analytics to create a <a target="_blank" href="http://chairnerd.seatgeek.com/the-math-behind-ticket-bargains/">"Deal Score"</a> for a current ticket offering on a 0-to-100 scale which takes into account the pricing for similar events and seating charts. There is certainly more to learn about the secondary pricing market, but this is a great first start!</p>


{% include disqus.html %} 

