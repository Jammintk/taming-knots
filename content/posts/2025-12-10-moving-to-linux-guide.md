+++
date = "2025-12-10"
draft = false
title = "Ditching Windows, SteamOS, and Linux"
+++

As Windows 11 updates have continued to have increasingly damaging bugs, I have started to see more and more people interested in taking the plunge into Linux and having a fair amount of confusion about what they need. On the one hand, I love to yap and will happily repeat my takes as often as needed as a Linux supporter and enabler, but on the other, it would be nice to have a central place to put those thoughts in case I can't type it all out right away. Then, if someone has questions after, I'd be happy to answer them. So, without further ado, let's get into it.

# The Background

## Bazzite? SteamOS? Mint? Cachy? What even are these?

You just named three **Distributions**. GNU/Linux itself is just the kernel, the very basic chunk at the center of the operating system that performs the most basic functions of the OS that the rest of the software builds on top of. A distribution is the bundle of other software that makes up the operating system. It will include low-level stuff, like video drivers, but also the desktop environment and basic applications, like a file explorer, web browser, or office software. Any piece of software in Linux is called a **Package** and distributions include different packages based on what their goals are as an operating system. An OS aimed at penetration testing (Think hacking but for good) is going to need different tools than an OS aimed at daily use or one aimed at gaming.

## Okay, so what's the difference?

Distributions will generally take one of a few different positions when it comes to package releases. Thankfully, we have a nice variety of common examples up above, so let's go through them one at a time.

### Rolling Release

A **Rolling Release** distribution like EndeavourOS, Arch, or Cachy OS, tends to use the most up-to-date versions of packages. The idea is that an up to date system is going to be more stable, secure, and feature rich than an out of date system. Therefore, Arch and other rolling release distributions will test and ship the latest versions of packages as they come out, with no holding of anything back to be grouped together in a larger periodic release. For these distributions, there is no version number. I run EndeavourOS, not EndeavourOS 25.10 or what have you. The downside to this approach is that a system that isn't being actively used and maintained will go out of date and then break. Updates to packages are meant to be applied one after another. Skipping versions can be damaging to the system and cause the system to stop functioning as expected. If you aren't comfortable with turning on the computer once every couple weeks to make sure you're applying all the latest updates, then skip this release style.

### Periodic Release

Mint, Ubuntu, Debian, Fedora, and many other distributions use a **Periodic Release**. Every six months or so, a new version will come out with major changes to many packages all at once. Packages can be updated between major release versions, but anything that makes major fundamental changes to the system will often be held back to be applied all at once during a version upgrade. These distributions tend to focus on very long term support and ensuring an out-of-date system will still function. Major upgrades can break things, but packages tend to undergo more significant testing to make sure everything will work on the other side of the upgrade. The downside here is that, due to the longer testing period and the general attitude towards updates, packages are more likely to be out of date even after running a package manager upgrade command. 

### Atomic Distribution

SteamOS, Fedora Silverblue, and Bazzite all take the periodic release schedule one step further and are released as **Atomic Distributions** Atomic distributions are built such that everyone who is running a particular OS version is running the exact same set of packages. Not just the packages themselves, but the same *versions* of them. An atomic distro uses layers of access to ensure that the system remains unchangeable to the user. These distributions make great use of **Containerized Applications**, which are a format for applications where all of the applications' dependencies, the packages that application needs to run, are packed into a little sandbox with the application itself. Common formats for this are **AppImage** and **Flatpak**. These systems are very resilient to breakage, but the isolated system environment means the distribution must include drivers by default because the user can't install them. If your hardware isn't supported, then you'll have to hope they add that support in the next release. You can't add the package yourself. They are also very easy to support, though, since every computer running the distro will be running the exact same set of packages.

# The Advice

This advice might be a little bit underwhelming, because unlike Windows and MacOS, there's so much variety to be had. No shoe fits every foot, and no Linux distribution is good for everyone. Differences may be slight, but I feel like I've landed on a pretty good breakdown here.

### I want as console-like of an experience as I can. No tinkering.

No solution will be completely tinker-free, but the closest you can get is an Atomic Distribution. **Bazzite** is my pick here. SteamOS may come along and be better *eventually* but currently, SteamOS's support is very limited to Valve's own hardware, with a few very minor exceptions. If you don't use your gaming PC super regularly, then this is a great option. Updates still come somewhat regularly, but are inclusive of the entire OS, so an update is very unlikely to break your PC. There will be fewer applications available to you, but Flatpak and AppImage are both very well supported, so all the basics are there.

### Okay, I can tinker a little bit, but I still want something easy.

Have no fear. **Linux Mint** is here for you. It's based on Ubuntu (unless you get the Debian Edition) which is based on Debian. It uses a Periodic Release schedule. Since it's a couple steps removed from the original distribution, packages tend to be out of date, but the system is very stable and the community is world-class. It will be very easy to get help with a Mint system if anything goes wrong and even if you can't find help in the Mint community, you have the underlying Ubuntu community to help as well. The support structure is excellent.

### But I hear Cachy is good? How much tinkering is there, really?

You're right. **Cachy OS** is what Wendell at Level 1 Techs is recommending for folks to get into gaming on Linux and one of the options that GamersNexus is using in their Linux benchmarks. Cachy is an Arch system that uses a rolling release schedule. The default applications are sensible for someone who may be new to Linux, but has technical experience. You get a package manager with a UI, my particular favorite desktop environment, and extremely good application availability through the **Arch User Repository**. If you can't find a package through the official repositories, someone has probably made a build file for it and stuck it in the AUR. Because the system is Arch based, the Arch Wiki is an extremely good resource and the community is generally pretty good as long as you don't do things that are against the recommendations set by the wiki and community. Things that can lead to a nonfunctioning system will be called out immediately. You will need to routinely get updates from your package manager, and maintain an up to date backup, but otherwise a stable system can last basically forever.

### Are there any distros you should avoid?

Yes, actually. There are a couple of red flags in a distribution to me. The biggest is how much control the team exerts over including packages in the default repositories for the distribution. If the team insists on testing packages when their distribution is based on another one, then that means things are more likely to get out of date because the bigger upstream distribution has the larger community of contributors and testers to make that work. A downstream distro won't. The other red flag is more conditional. If the team exerts a lot of control and is also small, that's a big no from me. Smaller teams tend to be more opinionated, so if they also exert a lot of control over the packages, you can be sure that things will be out of date and your system is more prone to breakage.

### So what do you use?

None of the above, actually. I use **Endeavour OS**. While it is *technically* its own distribution, it would be more accurate to say that it is Arch Linux with an install script to make getting up and running with a functional system significantly faster and easier. I get package updates pretty quickly and haven't had anything seriously break in an unfixable way yet. Endeavour is *NOT* for everyone though. They do not offer or support Package manager front-ends. The idea is that users of the OS should be comfortable in the terminal and to keep their system up to date, they need to be updating through the terminal. You can install a GUI package manager frontend if you like, but don't expect support.

# Questions?

Feel free to shoot me a message via [the Contact form](https://tk-web.top/pages/contact/) and I will expand this guide with answers (and reply directly, of course.)


