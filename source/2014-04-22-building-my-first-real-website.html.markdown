---
title: Building My First Real Website
date: 2014-04-22 02:54 UTC
tags: Sinatra
---

I haven’t been blogging as much as I would like the past few weeks since I’ve been spending most of my time working on my first website. Middleman is great for blogging but I wanted a site that fit my personality better and I wanted some features that Middleman didn’t have. I envisioned a site where I could have my blog (with commenting) and then also showcase my work with a portfolio page and resume. So, I started building it.

For the past two weeks I’ve pretty much been spending all my free time at gSchool working on it, as well as the time I have on weekends. I decided to use Sinatra to build the site instead of waiting until we learned Rails (which we start tomorrow!) because I wanted to learn how to build my own content management system from scratch, rather then using all that Rails gives you. While I’m sure I would have saved some time, I’ve learned an incredible amount from this project.

I was able to really solidify my knowledge of postgreSQL and especially how migrations work (it seems like every time I write a migration I think of another column I want to add within 10 minutes). I also learned a lot more about Ruby and Sinatra: how to use sessions to create an admin panel, using layouts, incorporating new gems into my project, and more. I also had my first large scale introduction to CSS; I never thought that centering things could be so difficult.

Far and away the biggest lessons I have taken from this project have been related to website architecture. Talking with my mentors (<a href="https://twitter.com/jayzes">Jay Zeschin</a> and <a href="https://twitter.com/hollarab">Brooks Hollar</a>) has been extremely helpful in planning my file structure, how my databases would be set up and how they would interact with each other, and figuring out the overall plan for the site. If there is one thing that I have learned from these couple months of coding it is that change is inevitable in our world. The only thing that one can do is be prepared for change and make sure that your app can adjust to change easily.

My goal is to have the site up by next week as I put some of these final touches on the site:
<ul>
<li class="list_items">adding tags for blog posts</li>
<li class="list_items">adding my portfolio page</li>
<li class="list_items">finishing up the CSS (and trying to add in some jQuery)</li>
</ul>

In a future post I’ll go over more of the technical challenges I faced with this project. Here’s the project so far: <a href="https://github.com/nburt/personal_website">https://github.com/nburt/personal_website</a>, if anyone has time to give feedback, it would be truly appreciated.