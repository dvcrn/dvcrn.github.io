---
layout: post
title:  "Spacemacs for Atom"
date:   2015-11-27 18:24:23
categories:
- clojurescript
---

If you read my last posts you might notice that I really like editors. Something about finding better and better tools is very interesting and fun. Plus using a new tool for a couple of weeks is keeping things fresh even when you work on the same codebase.

The problem is that since I started using [spacemacs](http://dvcrn.github.io/2015/10/13/my_dream_editor.html), I can't really use anything else. It just... doesn't work. The shortcuts are too awkward, get overriden by third party plugins and all the manual config orchestration is just a pain. 

When I went back to Sublime Text for a little, I ended up writing [sublimious](https://github.com/dvcrn/sublimious), a plugin which ports a big chunk of the spacemacs toolset into Sublime Text: 

![sublimious](http://i.imgur.com/MqsB6Pt.gif)

It works __really REALLY good__! But eventually I ran into problems implementing more things. Stuff like relative line numbers is close to impossible to do without a big performance hit or awkward behavior. Or the gutter still only being able to hold 1 element in it. What's up with that? 

Performance wise it is crazy fast but let's face it: Sublime Text is dead even if the community doesn't want to admit it. Waiting until John Skinner to come along and commit a new feature once a year is just taking too long. 

### My first time with Atom? 

Now I am a little bit biased towards atom. I looked at it multiple times but it felt too slow, too in-performant and most of the plugins were just trash-level (sorry if I insulted some of my readers but they are just... not good!).

All the problems I have about plugins overwriting keybindings are here stronger than anywhere else. A plugin can literally change every little aspect of the editor. This is a really great thing, don't get me wrong! But at the same time you need to take double care of what you are installing. 

Github also did a lot of weird design decicions that I can't follow. Here are a few:

##### Emoji as commit messages
I tried to nibble my way through the codebase and recent changes a bit but emojis... man. I mean, it's cool that you have fun with it but for outsiders it's unnecessary confusing. 🎨 is probably style related, I get that, but what the heck is 📝? 🔥?

Plus using emojis is not a substitute for a commit message. It's like me commiting with the word "style."

##### Coffeescript

I used to like coffeescript a lot but reading through the atom sourcecode made me realise why I will probably not touch it again. 

Take this snippet for example:

```coffee
@eventElement.dispatchEvent(new CustomEvent(name, bubbles: true, cancelable: true))
```

What is it doing? Why do some of the arguments have colons? Where does the object start and where does it end? The constructor is getting 3 arguments, right? Right?

I literally had to run this through a coffeescript parser to understand what it's doing:

```javascript
  this.eventElement.dispatchEvent(new CustomEvent(name, {
    bubbles: true,
    cancelable: true
  }));
```

##### confusing objects everywhere

I'm a big functional guy and everywhere I go in the atom sourcecode, I am dealing with objects after objects. I can't count how many `@element` assignments and reads I saw. Some things are getting assigned at the top of an object, used at the bottom and changed 3x until it reached that spot.

##### cson
For some reason someone decided that coffeescript is so great that we should also start writing json in it! Because... commas are evil or something?


I quickly ran away from it 

### Atom, meet clojurescript

Now, since then and now I got a lot better in clojurescript so I decided to give it another go. I created a little [lein template](https://github.com/dvcrn/ajom) for creating atom plugins in clojurescript and hacked a bit here and there. 

__Turns out this works really well!__ Clojurescript and atom are like... emacs and elisp! You have all the power of clojurescript combined with all the customisation options atom gives you. You still have to deal with some quirks like passing objects around but still - this is making the idea of having a central configuration system so much more possible!

Our clojurescript plugin could literally change how every other package reacts: how it is set up, what keybindings it uses, what options it needs and more. We could even change how other plugins look inside the editor by manipulating it's DOM representation. 

Short said, in the last few days I started porting a bit of functionality from sublimious to atom. 

![demo](/images/proton-demo.gif)

For sublimious, I did __a lot of hacking__. I basically collected all the configuration and flushed it into real files that are then getting re-loaded by sublimetext. For atom, I don't need to do that. 

How do I set a editor config? Like this!

```clj
(defn set-config! [selector value]
  (.set config selector value))
```

How do I wipe the existing user config so it doesn't conflict with our new one? One line.

```clj
(map atom-env/unset-config! (atom-env/get-all-settings)))

```

The only hack I had to implement is the ability to install packages from within a package. That's currently not possible. Here's how I did it anyway:

```clj
(defn install-package [package-name]
  (if (is-installed? package-name)
    true
    (try
      (do
        (.execSync child-process (str (get-apm-path) " install " package-name " --no-colors"))
        true)
      (catch js/Error e
        false))))
```

(I'm just calling apm through node!)

### Is it ready?

Not even close to but I am already a lot more happy with it than with sublimious before. It feels more... powerful! Maybe cljs+atom can really be a alternative to emacs at some point. Let me dream 😊

In the meantime, if you want to help, please check it out [here](https://github.com/dvcrn/proton).
