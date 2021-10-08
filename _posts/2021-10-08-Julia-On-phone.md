---
layout: post
title: Running Julia on an Android Device
---

A few days ago I was bored. The only good cure I know of boredom is smashing things together to see if they can work together. This incidentally is also why I love Julia, composability is a stated primary goal. [I've smashed julia packages together before](https://github.com/JuliaMath/Interpolations.jl/pull/414), and heard the satisfying click first hand. But that is a post for a later date. That day my boredom lead me to [making julia work on my android phone](https://www.linkedin.com/feed/update/urn:li:activity:6850505288408145920/).

I often forget that I currently carry a more powerful machine in my pocket (OnePlus 8T, 8gb ram with an octa-core processor) than my first desktop (a blazing fast Pentium 4 with 512mb ram). But that desktop was used to create and experiment while my phone is only being used to consume. Its not like other people don't use their phones to create, people are making entire careers on tiktok. The difference seems to be lack of tooling. I saw [this post](https://www.linkedin.com/posts/matthijscox_someone-asked-how-to-easily-compute-integer-activity-6840208526598582272-Tc9K) by my colleague running Julia on his phone using replit. While replit is an awesome tool, it only offers Julia 1.4 which is [plagued by latency issues](https://discourse.julialang.org/t/will-julia-ever-fix-its-using-latency-problems/50659). After working with Julia 1.6, which [solved a lot of these issues](https://lwn.net/Articles/856819/), I can't go back to that. Time to take matters into my own hands.

## Step 1: Terminal emulation on Android
Termux provides terminal emulation and basic linux packages on android. The recommended way to install is using [F-Droid](https://f-droid.org/en/). (There is a Google Play Store app but it has stopped receiving updates for [technical reasons](https://wiki.termux.com/wiki/Termux_Google_Play)). You can either search for termux in F-Droid, or directly download the apk from [this page](https://f-droid.org/en/packages/com.termux/) (I'm not sure if you can update it then. Once installed, opening the app should give you a familiar linux bash shell.

![_config.yml]({{ site.baseurl }}/images/1633549280164.jpeg)

Update all the base packages by running this in the terminal.

```console
pkg update && pkg upgrade
```

This may take some time depending upon how fast your phone is.

## Step 2: Ubuntu on Android
While android uses a linux kernel, what we colloquially refer to as "Linux OS" also includes several standard libraries, additional software and most often a package manager. To get one of those distributions running we will use proot, which emulates a root file system from any directory. To make it even easier, I found [proot-distro](https://github.com/termux/proot-distro) which can install several distributions. (I chose ubuntu because that is what I'm most familiar with. But there are other options available).

Install proot-distro by entering

```console
pkg install proot-distro
```

And then install ubuntu by running.

```console
proot-distro install ubuntu
```

Again this can take several minutes depending upon how fast your phone is. Once everything is installed, run the following command to log into ubuntu.

```console
proot-distro login ubuntu
```

Now you should see the familiar ubuntu bash prompt like

```console 
root@localhost:~#
```

At this point you can do most things you would want to do in linux. I personally like to upgrade all my ubuntu packages first.

```console
apt update && apt upgrade -y
```

## Step 3: Installing Julia
While you can install julia using the apt package manager, it would not give you the latest version. To get the latest version is easy enough. Download the julia compiled binary using wget and extract it.

```console 
curl -O https://julialang-s3.julialang.org/bin/linux/aarch64/1.6/julia-1.6.3-linux-aarch64.tar.gz

tar xvfa julia-1.6.3-linux-aarch64.tar.gz
```
I got the link for the gz file from julia downloads page. Note we are using the Arm64 version because most phones use arm processor. (I wasted so much time because downloading x86 binaries is a habit).

**And we are done!!** You can now run julia by running

```console 
~/julia-1.6.3/bin/julia
```

It is usually recommended to add the julia binary folder to your path or create an alias command in your ~/.bashrc file. I tried symlinking the binary to some folders in my path, but that does not seem to work. No idea why.

Step 4: Reactive notebooks using Pluto
While having julia REPL is nice, reactive notebooks using pluto is nicer. It doesn't require any fancy steps, since julia has package management built in.

```julia
julia> using Pkg; Pkg.add("Pluto")
julia> using Pluto; Pluto.run()
```
Pluto should give you a link which you can copy paste into your phone browser and then code away. Here is a gif I made of the experience.

Pluto running through android browser

![_config.yml]({{ site.baseurl }}/images/1633549987033.gif)
