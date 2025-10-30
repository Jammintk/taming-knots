+++
date = "2025-10-30"
draft = false
title = "The Home Server"

+++

Long time no see!

I've heard enough interest in the fabled home server that I decided I'd like to push a little update to the blog about it, especially since I am *finally* at a stable spot with it where I feel good about all of the services and features I have implimented. I don't forsee myself rolling out any new services or tools in the near future and I have figured out solutions to some of the gaps I had in my setup so now I'm fully ready to share.

## Why the Home Server?

My reasoning for doing a lot of this work myself comes down to a few key points. One huge one is the feeling that I have, over the years, given up a ton of ground to various corporate interests in my digital life. All my personal photos were in Google's hands, my music tied up in subscriptions, video transiently floating between streaming services, and all of my documents and notes were in proprietary formats tying me down to specific software or web services. While it felt like the data was *safe* in many of these locations, it also felt like it could be very easily leveraged for tracking, advertising, and that my data wasn't really mine. How useful is a pen if you can only use it on the paper sold by the pen company? Another reason I went down this road is that I'm a cheapskate. I don't like spending $15/mo for Amazon Prime, $18/mo for Netflix, $23/mo for YouTube Premium (which includes YouTube Music) and $100/yr for Google One cloud storage, among other things and even after all that have to deal with the inconviniences that come with the DRM involved, managing numbers of devices on the streaming services, having content go in and out of availability, not being able to mix local media libraries with the streamed ones, and having limits to my file storage. The final and biggest reason to make a home server for me personally, is that I'm just plain interested in it. I like doing the management and tinkering to get everything working just right.

So then, my plan was to have a server with several terabytes of redundant storage, that can serve video and music over the internet, can be a repository for all of our family's photos, can act as a general-use file server, and that is backed up offsite so that I can follow data security best practices. I'm happy to say that I've achieved that goal and it even has a few tricks up its sleeve.

## Layer 0: The Hardware

While just about any computer can be a home server, from a raspberry pi with an external hard drive to a monstrous JBOD (Just a Bunch of Disks) intertwined with a compute server with multiple CPUs and 128GB of RAM, my goal is simple enough that I was able to take common home PC parts and assemble a server on a budget.

* CPU: Ryzen 5 1600
* RAM: 16GB DDR4, 2400MT/s
* SSD: 128GB PNY SATA
* HDD: 3x 4TB Seagate BarraCuda 7200RPM
* GPU: The cheapest possible GPU I could find to let the CPU boot because the Ryzen 1600 doesn't have an onboard one.

For the case, I went with the cheapest I could find on Newegg, a $20 mid tower that is a nightmare to work in, but is cheap and holds the parts. None of this hardware was top of the line when I got it and none of it needs replacement quite yet. It's all connected to an ITX motherboard, which is only a problem because it only has 2 RAM slots, so if I want to upgrade I can't just buy more of the same modules. I have to get whole new ones.

## Layer 1: Operating System

There's a few operating system options for home servers. I started my journey with a Windows Server installation. Quickly, though, I found that Windows was not really suitable for the kind of setup I wanted. I didn't want to have to access the server remotely with Remote Desktop. My next stop was an Ubuntu server. Again, I was stymied by the interface, which required too much management via command line interface than I was ready to accept at that time. My next port of call was Unraid. Finally, a Network Attached Storage-focused opoerating system. I liked Unraid a lot. It ran off of a USB stick, was available on the web aywhere in the network, and had support for Plex, the primary service I wanted the server to provide at the time. In the end, though, Unraid just wasn't really cutting it.

Finally, though, I settled on TrueNAS Scale, the Ubuntu-based NAS operating system by iX Systems. iX has been making NAS software for ages. TrueNAS Scale is an enterprise-level solution that is free for personal use. I've found that it makes having a complex, robust setup fairly easy as long as you are willing to learn some of the more advanced concepts you will need. The operating system handles backup and network file shares natively for me, with docker containers making up the bulk of the utility that the server provides.

## Layer 2: Network Setup

One of the most complicated things to get your head around when working with a home server is how you want the network to be set up. The most basic setup would be everything hosted and exposed over local IP addresses and ports. This ensures network security, but you have to remember that 192.168.1.50:32400 is Plex, 192.168.1.50:8123 is Home Assistant, etc. Some of these applications can make a handshake with an external service to allow you to access them outside your network with no further setup, like Plex, but for the most part, this limits access to within the network only.

The next tier up of network complexisty involves creating a domain for your local services, but not exposing that domain to the wider internet. This can be done with a combination of a reverse proxy, like Nginx, and a DNS server. A DNS server can be a useful application on its own, so I'll cover that more in depth later. The short of it is that the DNS server rewrites specific domain calls to an IP address. Once the traffic is going to the local IP, the reverse proxy will look at the request's intended destination (the domain you typed) and forward it to the port you set for that domain. For example, a request for `http://jellyfin.local` can be intercepted and sent to `192.168.1.50`. The `http://` at the start means the traffic is going to come on port 80. The reverse proxy is listening there, sees traffic intended for `http://jellyfin.local` and knows to forward it from its own port `192.168.1.50:80` to the Jellyfin port, `192.168.1.50:8096`. From there, a connection is made and your browser is sent the Jellyfin homepage form your home server. Neat, right? Additional proxy hosts can be added so that each different subdomain sends traffic to additional services.

The level I settled on is just above this. Externally accessible services. The setup is similar to the last one. A DNS server and reverse proxy push traffic to the various services, but in addition to that, you buy the domain you're going to use and ensure ports 80/443 are forwarded from your router to the home server so that the reverse proxy can pick up outside traffic. The safest way to do this is to use a tunnel from the external DNS provider (like Cloudflare) and set up SSL on the domains. IN addition, make sure you never forward anything that would be bad to have public unless you also protect it in another way. Everything should be at minimum behind a username and password. There's also other authentication options, like forcing the reverse proxy to ask for a login. I have my services split. Anything that is bad to have public, like the admin console for the NAS itself, is only available on the local network.

## Layer 3: Services

Here's the fun part. What the server can actually do. My list of services is pretty tame compared to a lot of home-labbers. I'd like to expand in a couple directions, but that would require more powerful hardware, so this is where I'm at. Here's everything that runs on the server right now.

### Samba Shares

Samba is the sharing protocol primarily used by Windows devices. It's also fairly easy to set up on Linux, thanks to how common it is. My server hosts a few Samba shares, though a couple are now redundant as you will see. First is the Cloud-Data share. This directory is a general-purpose file server. The folder has subfolders for myself and my wife to put documents, notes, and any other stuff we want backed up. Next is the Copypartycfg share. This exposes the config directory for another service on the server, Copy Party. This is explicitly to allow me to configure copyparty easily, since it doesn't have a native TrueNAS app and simply runs as a docker container. Finally, the third share is Media, a folder to store books, music, movies, and TV shows. Being able to add things easily to media libraries as I buy them is important to me, so having a share directly to that folder was very helpful. Finally, there's a fourth share for PC backups. It simply exists as a network endpoint for backing up the desktop PC in the house.

### NAS Backup Service

I use Storj for backup. It has similar uptime to other S3 storage providers like Backblaze or Amazon Web Services, but is significantly cheaper for my specific use case. I'm not currently backing up my largest files, being TV and movies, since it is easy to re-download those, but I may add them in the future. The server backs itself up automatically once a month currently, but I may increase the frequency depending on how it goes and how much they charge for the higher frequency. They charge a little more for pulling data out, but I think it's worth it.

### PC backup service

While it doesn't technically run on the server, the server receives the backup files generated by Vorta, which is a GUI frontend for Borg backup. Vorta runs once a day, backing up all of the important things from my desktop- to the samba share endpoint. It also handles the pruning of old backups and backs up as a delta, basically only backing up the changes since the last backup unless the last full backup is out of date.

### Network Services

These are the basic bones that make everything else work. Cloudflared maintains the Cloudflare Tunnel to my external services, and then I have a reverse proxy to allow external traffic.

### Plex

Plex is likely leaving my setup soon because I hate nearly every change they make to the web or smart TV applications. Plex is a media server, hosting on-demand access to movies, TV shows, and music that are hosted on the server. Plex is free, with an optional subscription or one-time payment for a lifetime license to the paid features. I don't really use the paid features, but I have used and liked the software for more than ten years, so I think it's fair to pay for the license.

### Jellyfin

Jellyfin is the free and open source cousin of Plex. It does largely the same job, but isn't quite as polished thanks to its noncommercial nature. I believe it is starting to get mature enough, though, to take over Plex's job. It warrants some more testing before I commit.

### Calibre Web Automated

Calibre is a piece of free ebook library management software, but it was built for a time when folks would just run it on their computer and hook up their e-reader via USB. Calibre Web is a wrapper around Calibre that allows the default interface to be available via a website. Calibre Web Automated is a significantly improved version with a web-first approach. It can be added as an online library to most e-readers, allowing you to host books on the server and download them as-needed to your devide. It makes having a huge library or a device with very little storage much easier to enjoy.

### Immich

This is the best Google Photos replacement I've ever seen. It handles automatic photo backup from our phones, can generate share links, handles albums and parterner sharing, can import photos from raw files, google photos, or icloud, surfaces all the same metadata Google photos does, replicates Google Photos' motion image features, and will even run facial recognition on the photos so you can make automatic albums for certain people. All for free with no nags or anything. 

### Copyparty

I was on Nextcloud for a time, but Copyparty does a significantly better job. This file server works as my cloud storage. I have it hosting the Cloud-Data and Media directories and have mounted the server as a WebDAV share in my desktop PC and my phone. Because it hosts WebDAV I can also use it as an endpoint for a community web sync plugin for Obsidian (Remotely Save) which gives me a good replacement for Google Keep and other note-taking apps. It even can host a drop folder so that people can send me files for other projects.

### VPN

Finally, a VPN. This lets me get into the network from outside if I need to while also being secure. I don't need it for all the services, but if I need to configure the OS or something, it needs to be there. I use one of the various dynamic VPN services that handles the registration and handshaking of a WireGuard installation without me needing to update everything every time the iP of my home changes.

## To be Continued?

There you have it. The current setup of my home server. If anything major changes, I might come back and write an updated article. For now, as I said, I'm happy with the state of things.