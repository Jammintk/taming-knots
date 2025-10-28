+++
date = "2025-11-01"
draft = true
title = "The Home Server"

+++

Long time no see!

I've heard enough interest in the fabled home server that I decided I'd like to push a little update to the blog about it, especially since I am *finally* at a stable spot with it where I feel good about all of the services and features I have implimented. I don't forsee myself rolling out any new services or tools in the near future and I have figured out solutions to some of the gaps I had in my setup so now I'm fully ready to share.

## Why the Home Server?

My reasoning for doing a lot of this work myself comes down to a few key points. One huge one is the feeling that I have, over the years, given up a ton of ground to various corporate interests in my digital life. All my personal photos were in Google's hands, my music tied up in subscriptions, video transiently floating between streaming services, and all of my documents and notes were in proprietary formats tying me down to specific software or web services. While it felt like the data was *safe* in many of these locations, it also felt like it could be very easily leveraged for tracking, advertising, and that my data wasn't really mine. How useful is a pen if you can only use it on the paper sold by the pen company? Another reason I went down this road is that I'm a cheapskate. I don't like spending $15/mo for Amazon Prime, $18/mo for Netflix, $23/mo for YouTube Premium (which includes YouTube Music) and $100/yr for Google One cloud storage, among other things. and even after all that have to deal with the inconviniences that come with the DRM involved, managing numbers of devices on the streaming services, having content go in and out of availability, not being able to mix local media libraries with the streamed ones, and having limits to my file storage. The final and biggest reason to make a home server for me personally, is that I'm jsut plain interested in it. I like doing the management and tinkering to get everything working just right.

So then, my plan was to have a server with several terabytes of redundant storage, that can serve video and music over the internet, can be a repository for all of our family's photos, can act as a general-use file server, and that is backed up offsite so that I can follow data security best practices. I'm happy to say that I've achieved that goal and it even has a few tricks up its sleeve.

## The Hardware

While just about any computer can be a home server, from a raspberry pi with an external hard drive to a monstrous JBOD (Just a Bunch of Disks) intertwined with a compute server with multiple CPUs and 128GB of RAM, my goal is simple enough that I was able to take common home PC parts and assemble a server on a budget.

* CPU: Ryzen 5 1600
* RAM: 16GB DDR4, 2400MT/s
* SSD: 128GB PNY SATA
* HDD: 3x 4TB Seagate BarraCuda 7200RPM
* GPU: The cheapest possible GPU I could find to let the CPU boot because the Ryzen 1600 doesn't have an onboard one.

For the case, I went with the cheapest I could find on Newegg, a $20 mid tower that is a nightmare to work in, but is cheap and holds the parts. None of this hardware was top of the line when I got it and none of it needs replacement quite yet. It's all connected to an ITX motherboard, which is only a problem because it only has 2 RAM slots, so if I want to upgrade I can't just buy more of the same modules. I have to get whole new ones.
