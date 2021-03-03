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

(This is also a good time to `git commit -a`, so the next commit only contains your own changes.)

The curl’d script will have created this project structure for you:

- `lua` contains the Lua source code for your app
  - `lua/main.lua` contains your app! It barely does anything yet. But you can add more code to this file to keep going, and add more files into lua to modularize your code.
- `images` contains images you want to use as textures on buttons, etc.
- `allo` contains the Alloverse app framework. You should not edit anything in here, as an upgrade of the framework will overwrite your changes.
  - `allo/assist` is a script that helps you with tasks such as starting a server, installing dependencies, etc…
  - `allo/libs` contains compiled C dependencies as dynamic libraries and executables. These are `.gitignore`d to save space. If you check out your code on another computer, you can `./allo/assist fetch` to install them again, so you don't need to (and shouldn't) commit them.
  - `allo/boot.lua` sets up the runtime environment for your app. This code is generated, so it's not
    recommended to modify this file.

### Trying your app out

That’s it! You should now be able to start your project and see it appear in an Alloplace. If you don’t have one already, you can get or rent a Place at [places.alloverse.com](places.alloverse.com/), boot one on your own (`docker run -e ALLOPLACE_NAME="my place" -p 21337:21337/udp -it alloverse/alloplace`), or just use our playground [alloplace://nevyn.places.alloverse.com](alloplace://nevyn.places.alloverse.com/). I'm gonna do the latter:

{% highlight terminal %}
$ ./allo/assist run alloplace://nevyn.places.alloverse.com
{% endhighlight %}


If it all checks out, you should be able to jump into that place on your VR headset and press the button it has created.

[Download the Visor app for your VR or desktop platform of choice](https://alloverse.com/download/), and then click “Connect”, “Nevyn’s place” (or click the alloplace link of the place you’ve rented or started yourself).

![This is a alt text.](/assets/images/hello-world-1024x318.jpg "This is a sample image.")
Our app running in Nevyn’s place. I’ve clicked our simple app’s red button button three times.

### Anatomy of an app

Let’s open up main.lua and have a look at the code, to understand what's going on.

{% highlight lua %}
local client = Client(
    arg[2], 
    "allo-todo2"
)
local app = App(client)
{% endhighlight %}

A `Client` is used to connect this app to a Place. `arg[2]` is the URL of the place to connect to, which Assist sets up for you in boot.lua. `App` then manages the Client connection for you, and manages the lifetime of your app.

{% highlight lua %}
assets = {
    quit = ui.Asset.File("images/quit.png"),
}
app.assetManager:add(assets)
{% endhighlight %}

Assets are files (images, glb models, videos, sounds, etc...) that you want to use
in your app. They need to be published so that user's headsets can download them
before you can use them. We make `assets` global so you can use it throughout your app.

{% highlight lua %}
local mainView = ui.Surface(ui.Bounds(0, 1.2, -2,   1, 0.5, 0.01))
mainView.grabbable = true
{% endhighlight}

Your application is a represented as a hierarchy of views. If you're a web developer, you can think of it as nested divs in a DOM. If you come from Unity, it's similar to the GameObject hierarchy, with transforms being relative to parent GameObjects.

`mainView` is the main UI for your app. You should set it up with your default user interface view hierarchy before connecting to the Place.

Bounds is the position and size of your object. The first three numbers means this view is centered horizontally; its base is 1.2 meters up from the floor; and -2 means 2 meters into the Place, depth-wise. The second group of three numbers means 1 meter wide, half a meter wide, and one centimeter deep.

We also make the main view grabbable, so the user can move it around the Place.

{% highlight lua %}
local button = ui.Button(ui.Bounds(0.0, 0.05, 0.05,   0.2, 0.2, 0.1))
mainView:addSubview(button)
button.onActivated = function()
    print("Hello!")
end
{% endhighlight %}

Let's create a standard Button component, at 2 decimeters wide and high. Its position of 5 centimeter down and forward is relative to the `mainView`'s position.

The onActivated callback lets us run code when the button is pressed. This'll print "hello" to your local terminal when you run the app on your machine (even if you're running the visor somewhere else on the Internet!)

{% highlight lua %}
app.mainView = mainView
app:connect()
app:run()
{% endhighlight %}

Let's configure the app's main view to our new fresh UI, connect to the Place we've been asked to run in, and then hand over the runtime of the app to the UI framework so it keeps running until the user quits it.

And that's the basic structure that we can now build on.

### Making it pop

Let's make a todo list app! Select everything from `local mainView = ...` down to `app.mainView = mainView` and delete it, and we'll write some new, fun code.

TODO

### Deploying your app

also TODO
