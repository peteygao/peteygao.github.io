---
layout: post
title:  "Headache free setup of Elm 0.18 with Phoenix 1.3 using Brunch 2"
date:   2018-07-18 00:43:00 -0800
tags: [Elm, Phoenix, Elixir]
---


This is an end-to-end tutorial for getting setup with the **latest** [Elm](http://elm-lang.org) (0.18.x), [Elixir](https://elixir-lang.org/) (1.6.x), [Phoenix](http://phoenixframework.org/) (1.3.x) and [Brunch](https://brunch.io/) (2.10.x) with _minimum_ headaches.

There are plenty of great tutorials for past versions of Elm, Elixir and Phoenix, of course. Some of those lessons are largely still applicable, but with Phoenix 1.3, the directory structure for things have shifted around a little bit, so here's a fresh introduction to getting all of these moving targets to play nicely together.

Oh, and we don't have to monkey around with Webpack, as we'll be doing everything purely the Phoenix-way: with Brunch! No unwieldy configs and monkeying around with cryptic Webpack config parameters.

Let's jump in!

<br/>
<a name="installing-elixir"></a>
### Installing (or updating) Elixir to 1.6.x

_Skip to the [next step](#phoenix-install) if latest Elixir (1.6.x) is already installed._

Before we get _anything_ off the ground, we need to install Elixir. I highly recommend installing Elixir using the version manager [asdf](://github.com/asdf-vm/asdf), which can manage both the Erlang runtime (necessary to compile and run Elixir) and Elixir platform under one unified tool. More details on installing `asdf` are available on their [Github page](://github.com/asdf-vm/asdf).

From a clean slate with `asdf`, installing both Erlang and Elixir itself should quite simple:
```bash
$ # We may need to install dependencies for Erlang runtime
$ # for our system prior to installing Erlang itself.
$ # See: https://www.tiot.jp/riak-docs/riak/kv/2.2.3/setup/installing/source/erlang/

$ asdf plugin-add erlang
$ asdf plugin-add elixir
$ asdf install erlang 21.0.3 # Latest version as of this writing, but double check newest version with `asdf list-all erlang`!
$ asdf install elixir 1.6.6 # Same notice as above ^. Check `asdf list-all elixir`
```

If we have an older version of Erlang or Elixir, we will want to update them both. Updating Erlang and Elixir versions with `asdf` is trivial and operates exactly the same as above, except we will not have to perform the two `plugin-add` steps:
```bash
$ asdf install erlang 21.0.3 # Latest version as of this writing, but double check newest version with `asdf list-all erlang`!
$ asdf install elixir 1.6.6 # Same notice as above ^. Check `asdf list-all elixir`
```

<br/>
<a name="phoenix-install"></a>
### Installing (or updating) Phoenix to 1.3.x

_Skip to the [next step](#installing-elm) if latest Phoenix (1.3.x) is already installed._

Before installing Phoenix, we should go through the [Phoenix pre-install guide](https://hexdocs.pm/phoenix/installation.html) (**tl;dr** we'll need Hex [an Elixir package manager], [PostgreSQL](https://www.postgresql.org/download/) and Node.js--If we don't have Node.js yet, we can install it with `asdf` as well!).

Once we have the pre-requisites installed, installing Phoenix itself is one command away:
```bash
$ mix archive.install https://github.com/phoenixframework/archives/raw/master/phx_new.ez
```

If we're on an older MINOR release of Phoenix (i.e. 1.2.x or below), we'll have update our local Phoenix package:
```bash
$ mix local.phoenix
```

If we're already on 1.3.x, but not the absolute latest version of 1.3.x, we can use:
```bash
$ mix local.phx
```

**Note:** Phoenix changed its `mix` task name between 1.2 and 1.3 from `phoenix` to `phx`.

<br/>
<a name="installing-elm"></a>
### Installing (or updating) Elm to 0.18.x

_Skip to the [next step](#new-project) if latest Elm (0.18.0) is already installed._

Installing Elm once we have npm installed is a piece of cake.

```bash
$ npm install -g elm
```

Updating is similarily easy, you just have to run:
```bash
$ npm update -g elm
```

Yep, that's all we have to do! Easiest step of this whole process, by far. Thanks, Elm!

<br/>
<a name="new-project"></a>
### Creating a new Phoenix project

With all our dependencies installed, we can create a new Phoenix project like so:
```bash
$ mix phx.new phoenixWithElm
```

This will create a new folder named `phoenixWithElm` at the current working directory, and prompt us to install the default Node.js dependencies for Phoenix (the Brunch build tool, for one).

Unless we have reasons to defer installing the dependencies (which we will need, eventually), we should respond with `[Y]es` now.


<br/>
<a name="configuring-elm-brunch"></a>
### Installing and Configuring "elm-brunch"

We need a third party node package called [`elm-brunch`](https://github.com/madsflensted/elm-brunch), which is a plugin for Brunch and performs the Elm compilation for us whenever we save a `.elm` file in the assets directory.

**From now on, all actions should be performed from within the `assets/` directory of our project. Filepaths and Directories will be given relative to `assets/`.**

To start, add the `"elm-brunch"` line to our `package.json` file:

```javascript
{
  // ...
  // Other sections
  // ...
    "devDependencies": {
        "elm-brunch": "^0.11.1",
          // ...
          // Other dependencies
          // ...
    }
}
```

After we've added [`elm-brunch`](https://github.com/madsflensted/elm-brunch) to our `package.json` file, we need to install it:
```bash
$ npm install
```

If everything has gone well so far, we just need to put the final touches on our elm-brunch configuration and we should be off to the races!

We should still be inside of the **`assets/`** directory. Time to edit `brunch-config.js`. Under `plugins` section, we should see `babel` as the sole default plugin:
```javascript
{
  // ...
  // Other sections
  // ...
  plugins: {
    babel: {
      // Do not use ES6 compiler in vendor code
      ignore: [/vendor/]
    }
  }
}
```

We want to extend this section to include our `elm-brunch` plugin configs:
```javascript
{
  // ...
  // Other sections
  // ...
  plugins: {
    babel: {
      // Do not use ES6 compiler in vendor code
      ignore: [/vendor/]
    },
    elmBrunch: {
      // (required) Set to the elm file(s) containing our "main" function `elm make`
      //            handles all elm dependencies relative to `elmFolder`
      mainModules: ['elm/Main.elm'],

      // (optional) If specified, all mainModules will be compiled to a single file
      //            This is merged with outputFolder.
      outputFile: 'elm.js',

      // (optional) add some parameters that are passed to elm-make
      makeParameters: ['--warn']
    }
  }
}
```

These are the bare minimum configs necessary to compile our Elm app, and more config flags are available and documented in the [`elm-brunch`](https://github.com/madsflensted/elm-brunch) repository.


We want to make an additional change to the `paths` section, which is to add `"elm"` to the list of watched paths:
```javascript
{
  paths: {
    // Dependencies and current project directories to watch
    watched: ["static", "css", "js", "elm", "vendor"],
    // Other configurations
  },
}
```

<br/>
<a name="creating-elm-app"></a>
### Creating our Hello World Elm app!

With all of the config all setup, we just have one last thing to do: Create an `elm/` directory under `assets/` and make an Elm file within it!

```bash
$ mkdir elm
$ cd elm
```

Then using our favourite text editor, create and save `Main.elm` with the bare minimum content:
```haskell
module Main exposing (..)
import Html exposing (..)

main =
    p [] [ text "Hello world" ]
```

Let's boot up the Phoenix server (which will in turn start up a Brunch watch job) and have it compile our `Main.elm` file:
```bash
$ mix phx.server
```

Compiled Elm files can be found in `<project_root>/priv/static/js/elm.js` by default. We can change both the location and the name of the compiled file in the `brunch-config.js` file if we so wished.

One last thing we need to do before we can check out our Hello World elm code, add it to our layout file so that it gets loaded on page load.

Add this line to `lib/property_speculation_web/templates/layout/app.html.eex`:
```erb
<script src="<%= static_path(@conn, "/js/elm.js") %>"></script>
```

It should go right below the existing `<script>` that's pointing to `/js/app.js`.

Everything is in place and we should be able to hit up `localhost:4000` in a browser and see the Phoenix App's greeting screen.

To make sure that our Elm app is actually loaded, open up the inspector and plunk down this line of JS:
```javascript
Elm.Main.embed(document.getElementsByClassName('logo')[0])
```

Immediately we should see the words "Hello world" overlap the Phoenix logo banner. Huzzah! This proves that our Elm app is being loaded onto the page, and can be successfully mounted!

**Troubleshooting tip:** If you're running into issues, make sure that the `elm/` directory is created under `<project_root>/assets/`!

<br/>
<a name="wrapping-up"></a>
### Wrapping Up

Congratulations! At this point the Brunch is setup to compile changes made to our Elm code in real time, so long as the Brunch watcher is running (and it will be autorun whenever the Phoenix server is started). The app template is updated to load our `elm.js` file.

I have noticed that the Brunch watcher self-exits and does not restart when `brunch-config.js` is modified while Phoenix is running, necessitating an entire server reboot. If you know a way around this, I'd love to hear from you! My contact info is in the footer, please reach out!

Let's review our finalized directory structure under `assets/` and `priv/`:
```
.
├── assets
│   ├── css
│   ├── elm # This is where we should put our *.elm source
│   ├── elm-stuff # This is where Elm packages and dependencies go
│   ├── js
│   ├── node_modules
│   ├── static
│   └── vendor
└── priv
    ├── gettext
    ├── repo
    └── static
        ├── css
        ├── images
        └── js # This is where our compiled Elm gets output as "elm.js"
```
* This list focuses on the two main directories and filters out all the other directories.
