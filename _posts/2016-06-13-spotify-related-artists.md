---
layout: post
title: 'Who Needs Genres When There Is Data?'
author: Brady Fowler
summary: "Using network graphs to identify musician communities. 6/19 - 9:03 AM :: Apparently the interactive chart already hit the 500 views allowed with my free Plotly account! I am going to upgrade, but if it doesn't work for a bit come back later!"
github: https://github.com/dbfowler/related_artists
image: '/images/post_5/cover.png'
date: 2016-06-13
tags: [music recommendation, spotify, genres, atomic man, beyonce, data, genre, gephi, google, igraph, ILLUSTRATOR, MUSIC, music data, network graph, page rank, pagerank, python, spotify]
---

<p>6/19 - 9:03 AM :: Apparently the interactive chart already hit the 500 views allowed with my free Plotly account! I am going to upgrade, but if it doesn't work for a bit come back later!</p>

<p style="text-align: center">
  <a href="#intro">Intro</a>&nbsp;|&nbsp;
  <a href="#data">Data</a>&nbsp;|&nbsp;
  <a href="#static">Static Viz</a>&nbsp;|&nbsp;
  <a href="#interactive">Interactive Viz</a>&nbsp;|&nbsp;<br/>
  <a href="#road">Exploring the Network w/Playlists</a>&nbsp;|&nbsp;
  <a href="#tech">Technical Stuff</a>
</p>


<h1 id="intro">Exploring the connection between artists:</h1>
<p>One of my favorite ways to find music is by surfing related artists; I pick a band I've been hooked on then click through the list of similar artists,&nbsp;find one I like then click on <em>their</em>&nbsp;similar artists etc. etc. Definitely a nerdy way to try and source new bands, but I've had some great success (this week I happened across <a target="_blank" href="https://open.spotify.com/track/5yKDZ5yyBcP3o4v7R4SU5c">Gillbanks </a>- they have a weird <a target="_blank" href="https://open.spotify.com/track/4m0Vgr48VFaMYw0Sp1ozJu">Radiohead</a>-ish feel at times and a <a target="_blank" href="https://open.spotify.com/track/6jrMVRReY24qzCfe1BRrww">Two-Door Cinema Club</a> feel at others... weird but it works).</p>
<p>All of this artist surfing got me thinking about what makes one artist "related" to another - touring together, similar influences, same record label, sharing bandmates - the list could go on. In my opinion,&nbsp;Spotify has tackled this question of relatedness in a really great and fundamental way:</p>
<p>&nbsp;<br />
<figure>
<blockquote>
    <span>&#8220;</span>Spotify’s related artists and radio are determined by algorithms which look at what people listen to alongside your music.  So if I put your music in a playlist alongside artist X &amp; artist Y then artists X &amp; Y are more likely to be shown as related to you or played on radio.<span>&#8221;</span>
  </blockquote>
<figcaption class="source">&mdash; http://www.spotifyartists.com/faq/#how-are-the-artists-on-the-radio-and-related-artists-populated</figcaption>
</figure>
<p>&nbsp;
<p>In other words: My sister Kendal says "Yo, you saw St. Paul and the Broken Bones? I love that band - you should check out Nathaniel Ratliff and the Night Sweats they have a similar vibe."&nbsp;Not only are the bands similar in their exotic names, but they can be considered to be related because Kendal often listens to them together. This example is a loose interpretation, Spotify's metrics seem to be more based on playlist proximity and probably some population measures of artist overlap by listening-session.</p>
<p>Imagine "related artists" to be sort of like neighbors; the two artists seem to get along, so they live in similar areas so they can hang out. Those areas (aka communities) can be roughly understood as "genres."</p>
<p>Now, I don't particularly like the concept of genres; in most musical contexts, they are a clunky and pre-judicious way of bucketing artists. ["I wouldn't like Chris Stapleton I HATE country!!" or "Banks? Weird name. Plus I don't like electronica, just Adele.."]. I appreciate the attempt to let a listener know what they are getting into,&nbsp;but genres are heavy-handed in their labeling and don't encourage exploration. <em><strong>What we will see in the following graphs is how user-level listening data can indicate how two artists fit together, both in the meta sense of genres and on a more useful granular level.</strong></em></p>
<p><a href="#anchor-link-example"><em>Click here to skip the technical parts</em></a></p>
<h1 id="data">Data time:</h1>
<p>This is the fun part. With the idea of "communities" and neighborhoods in mind I knew I wanted to create a network graph that plotted artists and their relations (more on this in a few - I added a more technical section at the end for those interested). What I needed was data, so I build a program in Python to ping Spotify's API and pull back a list of artist relationships. The simplified work flow is as follows:</p>
<ol>
<li>Define a list of 10 artists - we will call this the To_Parse list. I used: Kings of Leon, Kendrick Lamar, The War on Drugs, Zac Brown Band, The Rolling Stones, Daft Punk, Spoon, Kanye West, Rage Against the Machine, Atomic Man.
<ol>
<li>I chose these artists to get a diverse picture of each "mega-genre". You'll notice that in a moment of complete vanity, I added my own band <a target="_blank" href="https://open.spotify.com/artist/3ASuWb9N2dwCl18ZX9ljyc">Atomic Man</a> to the mix. This had an unexpected and interesting outcome that we'll discuss later!</li>
</ol>
</li>
<li>For each artist in this list, pull back their top 20 related artists and store them like: [Kings of Leon, The Strokes].</li>
<li>If any of these 10 "related artists" has more than 100,000 followers on Spotify, add it to the To_Parse list.</li>
<li>Repeat until at least 10,000 artists have been searched for.</li>
<li>For every artist in the data set, reach out to the EchoNest API for genre information and then programmatically group these sub-genres into more general labels.</li>
</ol>
<p>This program allowed me to collect information on exactly 7,609 artists' top 20 similar artists.</p>
<h1><strong>Visualization Time:</strong></h1>
<p>Network graphs are often used in data science to visualize many relationships between members of a group. <a target="_blank" href="https://kieranhealy.org/blog/archives/2013/06/09/using-metadata-to-find-paul-revere/">Recently a Duke researcher used graph theory to explain how meta-data could have implicated Paul Revere as a potential terrorist in the eyes of the British government</a> - it's a super cool topic, but I won't delve into the more technical aspects till the end.</p>
<p>For now, it is only important to imagine each artist as a dot and the "related status" to be a line between those dots. Many algorithms have been developed to determine where each dot should be placed in order to properly visualize how some dots are closer neighbors than others and highlight the overall trends of a network.&nbsp;</p>
<p id="static">I used a program called <a href="https://gephi.org/">Gephi</a> to import my data and apply 7 different placement algorithms. These algorithms weight relationships differently and allow us to see how artists are naturally clumped by "close relation." Although the dots are colored according to a genre variable I calculated, their placement does <b>NOT</b> take genre into account, but rather shows how two artists are frequently listened in conjunction with one another. Take a look at the graphs here:</p>

<!-- end img gallery -->
<div class="w3-content w3-display-container" style="width:75%">
<img class="mySlides" src="/images/post_5/G1.png" style="width:100%">
<img class="mySlides" src="/images/post_5/G2.png" style="width:100%">
<img class="mySlides" src="/images/post_5/G3.png" style="width:100%">
<img class="mySlides" src="/images/post_5/G4.png" style="width:100%">
<img class="mySlides" src="/images/post_5/G5.png" style="width:100%">
<img class="mySlides" src="/images/post_5/G6.png" style="width:100%">
<img class="mySlides" src="/images/post_5/G7.png" style="width:100%">
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

<div class="row">
     <div class="col-md-8">
            <p>What is most interesting to me is how the algorithms were able to place artists into clusters that accurately reflected "closeness." The fact that most clusters are homogeneously colored indicates that Spotify's use of related artist status is a good substitute for "genre";&nbsp;it captures large trends in music similarity but also allows for more nuanced relationships and groupings.</p>
            <p>These 7 different graph layouts above allow us to look at the network from different perspectives, but none of the underlying relationships are changed. On average, this sample of related artists had ~8 degrees of separation (sorry Kevin Bacon...), but this number would likely drop if I were to include artists with fewer followers or utilize a larger data set of artists. I chose these cut offs to keep the amount of small artist noise down and work with a manageable amount of data for the interactive chart below.</p>
     </div>
     <div class="col-md-3 pull-right">
        <img src="/images/post_5/lookup.jpeg" alt="Colors indicate EchoNest genre which was determined using some manual bucketing of sub genres. All the network graphs have the same color scheme but point placement doesn't rely on the genre variable." class="pull-right img-responsive">
        <em><small>Colors indicate EchoNest genre which was determined using some manual bucketing of sub genres. All the network graphs have the same color scheme but point placement doesn't rely on the genre variable.</small></em>
     </div>
   </div>


<h1 id="interactive">Interactive Visualization</h1>
<p>Once I found a graph layout that showed some good community clustering I realized it would be helpful to interactively zoom and label the points. I used a Python plotting package called iGraph to import Gephi's GML file and hosted the resulting interactive visualization on Plotly after spending <em>way </em>too long figuring out how to format it :). You can hover over the chart to view artist name, number of followers, meta-genre and top three sub-genres. Use the crosshair pointer to select a section to zoom (to un-zoom click the "autoscale" button that looks like [X] in the top right corner). Notice anything cool? Let me know in the comments!</p>
<p>(PS: This graph doesn't work as well on mobile so you might want to check it out on a desktop/laptop)</p>
<p>

<div align="center">
<iframe width="900" height="800" frameborder="0" scrolling="no" src="https://plot.ly/~fowler.brady/41.embed?link=false?modebar=true"></iframe></div>

<br />
<h1 id="explore">Exploring the Graph</h1>
<p>So who is the most important artist in this dataset? Who is the least important?</p>
<p>These are really useful and interesting questions that I chose to answer using the PageRank algorithm developed by Google founders Larry Page and Sergey Brin. This algorithm was used to allow the earliest versions of Google decide which webpages were most useful and reputable and rank results accordingly. The algorithm has been subsequently abandoned for much more complex ones, but it is still really useful for characterizing network connectivity (have you ever gone past page 1 on Google in the past 5 years?).&nbsp;</p>
<p>Essentially PageRank gives each dot a value based on how many incoming and outgoing links it has (these values are further weighted by the importance of surrounding nodes). I rank the PageRank algorithm on the data sample in order to determine which are the top 15 most important and 5 least important bands:</p>

<div align="center"><img src="/images/post_5/band_rank.png" width="70%"></div>
<p>
<p>You'll notice that the majority of the "important" artists as determined by PageRank are mainstream pop acts; I think this is a result of the non-random sample I collected that does include slightly more pop artists than other types, combined with the fact that these sort of artists will probably have more listeners and relationships due to the Billboard effect.&nbsp;</p>
<p>The least important artists is where <strong>MY</strong>&nbsp; college band Atomic Man gets to shine! Since we are as-of-yet undiscovered and were placed into the dataset manually we are a bit of an anomaly. All of our related artists had fewer than 100,000 followers so they were not added to the To_Parse list, therefore our neighborhood exploration died a sudden and painful death. (Please feel free to listen to our music extensively and help us build better network value!)</p>
<h1 id="road">The Long and Winding Road</h1>

<div class="row">
     <div class="col-md-7">
            <p>Another metric I looked into was "betweenness" of dots - basically who are the two furthest apart artists in this entire graph? I leveraged an algorithm to track the walk between these points which involves 27 different artists; it starts with "Herbie Hancock" and ends with Latin sensation "El Indio." I placed the top song by each artist on the path in a playlist to the right. Listening through in order seems like a natural progression from artist to artist, further indicating the power of related-powered play listing.</p>
            <p>While analyzing this data, I came across dozens of bizarre band names and sub-genres I had never heard of before. My favorite of the lot was "Viking Metal" - I don't really understand what that means but its provocative and super intense. I used the same algorithm described above to figure out the shortest path necessary to take us from the sensational Viking Metal band "Amon Amarth" to our one and only true Queen: Beyonce.</p>
            <p>Surprisingly, this path only involves 10 artists and actually seems pretty natural (that is if you can stomach the Viking Metal at the beginning..."Twilight of the Thunder God" is my new ring tone...)</p>
            <p>Follow these playlists and let me know what you think of the paths in the comments - I've been really enjoying their diversity this week!</p>
     </div>
     <div class="col-md-2">
        <iframe src="https://open.spotify.com/embed/user/hoyablues/playlist/0kUyD5YM8aw1jYtiAlewQh" width="300" height="380" frameborder="0" allowtransparency="true"></iframe>
        <iframe src="https://open.spotify.com/embed/user/hoyablues/playlist/1u83fPKNBmKg1HjZTqtMYi" width="300" height="380" frameborder="0" allowtransparency="true"></iframe>
     </div>
   </div>

<h1 id="tech">Technical Appendix</h1>
<p>I have been working on this post for several months - it took a while to figure out the best way to ping the Spotify API and pull back the data and then finagle it into an interactive. I plan on posting my code once I get a public GitHub set up, but for now I'll put my answers to anyone's technical questions here - just drop a note in the comments!&nbsp;More to follow..</p>
<p><strong>Download Editable PDFs</strong></p>
<p><a target="_blank" href="/images/post_5/g1.pdf">File1</a>, <a target="_blank" href="images/post_5/g2.pdf">File2</a>, <a target="_blank" href="images/post_5/g3.pdf">File3</a>, <a target="_blank" href="images/post_5/g4.pdf">File4</a>, <a target="_blank" href="images/post_5/g5.pdf">File5</a>, <a target="_blank" href="images/post_5/g6.pdf">File6</a>, <a target="_blank" href="images/post_5/g7.pdf">File7</a></p>
<p>As always, special thanks to: Katie for putting up with me, my parents for listening to me ramble about related artists for nearly 6 months and Brian/Stephen for proof reading.</p>

{% include disqus.html %} 
