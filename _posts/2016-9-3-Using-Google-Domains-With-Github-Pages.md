---
layout: post
title: Using Google Domains with Github Pages
categories: [snippets, devops]
tags: [devops]
description: Hooking up a custom domain purchased through google domains with your site hosted through github - no more <username>.github.io sites!
---

Setting up my github page to use a custom domain was difficult for me. I'm not sure if it's because I'm incompetent or the guides
floating arounda are meant for superstars, but it took me a solid hour to do three or so steps. This guide should simplify the 
whole process - something that should easily be done in five minutes.

***
  Register a domain with [Google Domains](https://domains.google/)
  The domain ran $12 a year for me, as of 9/3/2016. Navigate onto the site and start a search for your desired domain name. The page
  should look something like the picture below:

  As soon as you've found the domain you'd like to claim, check out and you're done! Whatever domain you've registered, I'll refer to
  from now on as <yourdomain>.

  Create a **CNAME** file in your Github Pages repository.
  The first line in your CNAME file should contain your domain name. If, for example, your domain name is 'www.yourdomain.com', 
  and you also want 'yourdomain.com' to redirect to 'www.yourdomain.com', add that in on the second line. It should look something
  like this:

  Change the custom domain for your Github repository.
  This is easily done by going to your repository's settings, and then looking for the section labeled 'Github Pages'. Simply fill
  in the 'Custom domain' box with <yourdomain>!

  Configure your Google Domain.
  Navigate to your dashboard, and tap into the DNS options for your domain.
  
  Find the section labeled 'custom resource records' at the bottom - it should ask for type and IPv4 address, among other things.
  
  In this section you will have to put **TWO** things:
  1) An **'@'** record, of type **'A'**, with TTL of **'1h'** that points to Github at **'192.30.252.153'**.
  2) A **'www'** record, of type **'CNAME'**, with TTL of **'1h'** that points to **<yourusername>.github.io**.
  
  When all is said and done, it should look something like the picture below:

5. Done!
  You should be able to navigate to <yourdomain> and the page hosted in your Github repository will show up!
