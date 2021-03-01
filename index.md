---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: default
title: Get Started
permalink: /
nav_order: 1
---

# {{ page.title }}

Alloverse is an open platform for collaborative workspaces in 3D. It’s Gibson style Cyberspace, but for your day-to-day work and play, with your friends and colleagues. It’s a VR and AR platform for creating spaces, and for running real applications within those spaces, together with other people.

Please see the public website for more information.

In nerd terms, it's a VR/AR/3D "window manager" and collaborative workspace.

Your "place" is where you decorate, run apps, invite people, and hang out. It's like a collaborative X11 server: It runs a world simulation server, a voip gateway, and all the backing data for 3d UIs for the running apps. The reference Elixir implementation is in alloplace.
A "visor" is the GUI application you use to visit places and interact with your apps. allovisor implements such a visor for VR in Unity.
An "appliance" is a process running on your computer, or on a computer on the Internet. Like opening a web page (or launching a remote X11 process), this app can then show its interface and be interacted with inside your place.
The network and "UI protocol" is abstracted into the allonet library, written in C and used by all the above projects.

### Developing an Alloverse App

An Alloverse app is a server-side app that you run on your own server, similarly to how you would host a web app on your own server. The difference is, when a user goes to your app on the web, it loads into that user’s web browser on their computer; but when a user goes to your alloapp in an Alloverse Place, the app loads into the Place for all users in that Place to use, and your code gets collaboration and VR super-powers for free.

This tutorial will take you through creating an Alloverse app using the Lua programming language, since that is the language we’ve developed the most support for so far.

You can build apps on Mac, Linux or Windows; but for the latter, you’ll need a Unix shell, e g mingw, msys2 or WSL.

In contrast to a web framework like Rails or Django, Alloverse doesn’t install any software onto your system; instead, a project is completely self-contained within its project folder, and contains everything it needs to run.

### Creating your project

First, create a project folder. Allo Assist requires Git to fetch all its dependencies, so we’ll init a Git repo in the folder. Then we can initialize the alloapp environment into it, and make sure it works:

{% highlight terminal %}
$ mkdir allo-todo
$ cd allo-todo
$ git init
$ /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/alloverse/alloapp-assist/master/setup.bash)"
$ ./allo/assist help
{% endhighlight %}

(This is also a good time to git commit -a, so the next commit only contains your own changes.)

The curl’d script will have created this project structure for you:

- `lua` contains the Lua source code for your app
  - `lua/main.lua` contains your app! It barely does anything yet. But you can add more code to this file to keep going, and add more files into lua to modularize your code.
- `allo` contains the Alloverse app framework. You should not edit anything in here, as an upgrade of the framework will overwrite your changes.
  - `allo/assist` is a script that helps you with tasks such as starting a server, installing dependencies, etc…
  - `allo/libs` contains compiled C dependencies as dynamic libraries and executables. These are .gitignored to save space; if you check out your code on another computer, you can assist fetch to install them again.
  - `allo/boot.lua` sets up the runtime environment for your app.

### Trying your app out

That’s it! You should now be able to start your project and see it appear in an Alloplace. If you don’t have one already, you can get or rent a Place at [places.alloverse.com](places.alloverse.com/), boot one on your own, or just use our playground [alloplace://nevyn.places.alloverse.com](alloplace://nevyn.places.alloverse.com/).

{% highlight terminal %}
$ ./allo/assist run alloplace://nevyn.places.alloverse.com
{% endhighlight %}

If it all checks out, you should be able to jump into that place on your VR headset and press the button it has created. Download the visor here for your desktop or VR platform of choice, and then click “Connect”, “Nevyn’s place” (or click the alloplace link of the place you’ve rented or booted yourself).

![This is a alt text.](/assets/images/hello-world-1024x318.jpg "This is a sample image.")
Our app running in Nevyn’s place. I’ve clicked our simple app’s red button button three times.

### Making it pop

Let’s open up main.lua and have a look at the code, and start extending it to make a to-do list app.

TODO

### Deploying your app

also TODO
