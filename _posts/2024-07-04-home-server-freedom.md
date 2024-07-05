---
title: Home Server Freedom! 
date: 2024-07-04 16:54:00 -0400
categories: [Projects, Home Server]
tags: [projects, home server, casa os, networking, vpn, jellyfin, media server]     # TAG names should always be lowercase
---

![Casabi Homepage View](assets/home-server-freedom/casabiHomepage.png){: width="1200" height="630" }


### Freedom and Independence: Creating Your Own Home Server!

4th of July is about FREEDOM! I found a new kind of freedom in creating my own home server. This is a project I've wanted to do for a while and I'm proud to have finally gotten it configured to a usable and stable state. This is far from the most sophisticated home server, and it is quite novice, but I learned so much and I'm quite happy with the results I've gotten. 

# Why I Chose to Build a Home Server

The decision to build my own home server stemmed from my burning hatred of streaming services like Netflix, Amazon Prime, HBO Max (I guess it's just called Max?). In the beginning of the streaming wars, it was a great time to be a consumer. For just a few dollars a month, we'd have a sea of content to drown ourselves in. Now? You're paying $15/month to be served 3 minutes of ads for every 10 minutes of content. Okay, I might be talking about cable, but we're getting to that level of intrusiveness. I can't stand it! I can legally purchase my own media and host it on my own server, and then stream it to my own devices. It's not that hard. I've seen friends do this, why should I continue to be a chump and pay Netflix to watch Stranger Things? 

And most importantly I want my data to be free! I have been a lifelong user of Google's services, I have my entire life cataloged in my Drive folders. But why should I let my data be at the complete control of some global conglomerate when I can host it myself? I can control it. The services they provide are no longer novel. Anyone can do it, and it's unfortunate it took me this long to realize it. 

# Setting up the Server: Ubuntu Server and Casa OS

To run all of this I went with a mini-PC, the GMKTec N100. Main reasons being if I ended up too far over my head in all this I'm only out $100 :D 

<figure style="float: left; width: 300px; margin-right: 10px;">
  <img src="assets/home-server-freedom/gmktecCasabi.png" alt="GMKTec N100 Mini-PC" style="width: 100%;">
  <figcaption style="text-align: center;">GMKTec N100 Mini-PC</figcaption>
</figure>

But I chose a mini-PC mainly for the power-efficiency. This entire process was more of a trial run to dip my toes into the hobby, and these cute little computers are a great entry point. Think they cost me a few quarters a day to run? Dunno. Have had it running two months now and I haven't seen any noticeable bump in my power bill! Despite its efficiency I haven't seen any hiccups. It has been sufficient for any work I have thrown at it, albeit that's been mainly limited to network routing and media streaming but it can stream 4k video with H.265 at at least 28fps. Which is more than enough for my needs. 

To streamline everything I installed Casa OS over Ubuntu server. It really does help manage all the administrative stuff and common walls that newbies like me run into. I would rather see results and have baby's first home server than no server! 

# Blocking Ads: Pi-Hole 

One thing I hate about the modern internet is the intrusiveness of ads. I haven't used a web browser without uBlock Origin installed in years. I can't do it. But sadly on iOS it's a lot harder to install reliable ad blockers. 

<figure style="float: right; width: 300px; margin-left: 10px;">
  <img src="assets/home-server-freedom/piholeDashboard.png" alt="Another Feature" style="width: 100%;">
  <figcaption style="text-align: center;">Pi-Hole Admin Dashboard</figcaption>
</figure>

To finally deal with this on a network-wide level I finally set up Pi-Hole. Pi-Hole is a 'DNS-based, network-wide ad-blocker' which basically means it takes known traffic affiliated with advertising and blocks it. It's at the Layer 3 level of OSI model so it can't really be defeated by ad-block prevention programs that run on some websites which is what makes it so good. 

Despite having installed Casa OS, and there being a Pi-Hole app within the app store, I chose to install it directly through the command line. This is a key service this server is going to offer so I wanted to install it on the Ubuntu server itself to ensure I had complete control over any configurations and to prevent any possible incompatibilities that might arise from running it in a Docker container. 

Once I got it up and running I was able to manually configure every device on my network to use my server as their DNS server so every device on my network gets access to this ad-block service. 

Finally being able to browse the internet on my phone without having every single mobile page be ruined with adverts has saved the internet for me. It is a night and day difference. 

# Setting Up VPN Access: Overcoming CGNAT with Tailscale

Initially I wanted to install Wireguard on my server to establish a secure VPN connection between my phone/laptops and my home network. This involved creating a permanent DuckDNS address to maintain connectivity, regardless of my ISP's changing dynamic IP. Additionally, I configured port forwarding in my router settings to ensure traffic flow. Every guide showed me the same steps. Easy, right? 

My plans hit a roadblock when I found out that Spectrum employs Carrier-Grade NAT (CGNAT), preventing direct access to my server through VPN protocols like WireGuard. I don't understand the exact technical details, but CGNAT assigns multiple customers a single public IP address, complicating direct communication with devices behind the NAT. Basically it can't tell which device to communicate to behind the NAT. 

To get around this I had to use Tailscale, a mesh VPN designed specifically to get around CGNAT restrictions. It's a mesh VPN compared to Wireguard's direct IP connection. But it was a pretty simple set up. I installed it through the command line again for the same reasons as before. Once I established it as an exit node through the web portal it was as simple as scanning the QR code on the mobile app and voila, my phone was now connected to my home network over 5G! 

# Enabling Media Streaming: Enter Jellyfin

With the VPN operational, I decided to use Jellyfin as my media server. I installed it through the Casa OS app store rather than the command line because I'm still uncertain if I want to use Jellyfin or the much more popular Plex. 

<figure style="float: left; width: 300px; margin-right: 10px;">
  <img src="assets/home-server-freedom/jellyfinProof.png" alt="GMKTec N100 Mini-PC" style="width: 100%;">
  <figcaption style="text-align: center;">Streaming Dance (1965) via Jellyfin</figcaption>
</figure>

But for now, this allowed me to store and stream movies and TV shows locally, which got me out of subscribing to Crunchyroll and HBO. It's giving me the freedom to enjoy my media collection wherever I am regardless of what streaming service it's actually on. I just legally purchase the media and upload it to my media folder on my server and now I can watch it. Jellyfin not only centralizes my media library but also provides a lot of pretty cool features for organizing and streaming content. I can categorize my movies and TV shows, create playlists, and in the future I plan on sharing access with family and friends securely through the VPN. I just have to embrace the responsibility of making sure the content they watch is on there! So far it has been a flawless experience. I have tested it by watching a movie on my laptop while out at my friend's house and it worked perfectly.

# Embracing Freedom

This journey wasn't just about technology; it was about reclaiming digital freedom!!! As we celebrate freedom on this 4th of July, I've found a new kind of liberation! What's more American than that?

