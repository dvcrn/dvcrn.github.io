---
layout: post
title:  "Spacemacs for atom - a few weeks later"
date:   2015-12-10 11:59:23
categories:
- clojurescript
---

I recently [wrote](/clojurescript/2015/11/27/spacemacs-for-atom.html) about my attempt to bring spacemacs style editing to atom. A lot of stuff happened that I wanna tell you about!

![showcase](https://raw.githubusercontent.com/dvcrn/proton/master/plugin/resources/showcase.gif)

### In review
Since the last post, [proton](https://github.com/dvcrn/proton) acquired 72 stars, 13 watches and 4 forks. We now have 3 people that actively contribute changes. 

Proton became a good amount more stable, removed a lot of the ugly hacks it had in the beginning and added some really cool stuff on top. Let me give you a short overview:

- We started creating a better default experience. The goal is that you just install proton, it reads your `~/.proton` and bam! You have a superb editor experience. Since 0.1.2 we ship a better theme, a better font, better default settings and put a lot more power into the dotfile.
- Step by step we are trying to allow users to toggle the entire atom experience from within `~/.proton` or through the `<spc> t` menu. We created the base for pulling stuff like `<spc> t n` for relative line numbers off (which by the way is in a third party package and not part of atoms core) or `<spc> t t` to disable the tab-bar (which __is__ a core package). 
- You can swap-out components more easily. Say you like [nuclide](http://nuclide.io/) and you want to use their quick-open tool instead of the default one. Now all you have to do is swap `proton.core.quickOpenProvider` to `:nuclide` and proton will replace it. Following this, adding different providers like swapping the file-tree with something else in the future will be no problem.
- We have context aware shortcuts and modes [on the way](https://github.com/dvcrn/proton/pull/27)! This means that your `<spc>` menu could change depending on what file you are editing. `<spc> m =` in python could execute yapf while in javascript, it could use js-beautify. This would make proton a truly dynamic experience. 
- We added a handful new [layers](https://github.com/dvcrn/proton/tree/master/src/cljs/proton/layers) with more being added over time.

The crazy thing is that proton already bypassed [sublimious](https://github.com/dvcrn/sublimious) in popularity, functionality and features.


### Live reloading inside Atom
Thanks to the amazing [thheller](https://github.com/thheller) we now even have full support for hot code reloading while developing proton. It goes even this far that we might even be one of the onlys plugin out there that was able to pull this off with full REPL integration! Even the guys at the [atom forums](http://discuss.atom.io) couldn't do that 😁

With this new and even more stable build process, creating plugins in atom has never been this fun and easy! ([ajom](https://github.com/dvcrn/ajom) has been updated with the latest changes.)

### The future
I am very impressed to see how proton is doing. As assumed, clojurescript is making atom such a powerful tool with a lot of unused potential. With [modes-ish](https://github.com/dvcrn/proton/pull/27) stuff on the way, we could even see something similar to emacs's modes in the future. Just imagine that, composing modes inside atom! (Just wondering when the atom API will become limiting...)

Proton is on a steady way to become your central gateway when interacting with atom. Leveraging clojure goodies and now even support for live reloading and REPL development - developing a atom extension has never been this fun. 

Now just to hope that more people adopt this and we might see more high quality and faster developed extensions on the way.

Spacemacs, we are coming for you!