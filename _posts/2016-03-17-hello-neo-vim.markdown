---
layout: post
title:  "Hello (Neo)Vim, good to see you again!"
date:   2016-01-22 10:30:23
categories:
- elixir
- clojure
- clojurescript
---

It’s weird that I am writing about editors so much but trying a new editor every few months kind of
became a tradition.  It spices things up and allows me to learn something new that I maybe didn’t know yet before. Or just discover a new tool that improves my workflow.

When I wrote about spacemacs, I thought spacemacs would be the thing that I would use for everything. Then I ported it to atom with [proton](https://github.com/dvcrn/proton) and thought that would be the tool I would be using for everything since I could just implement features on the spot that it didn’t have yet. So what happened that I am writing about editors again?

As a VIM fan, I like micro optimizations. One thing in my workflow that I couldn’t stand was hammering `j` `j` `j` `j` to go down instead of `4j`. I knew this keybinding was a lot faster, I just couldn’t get to teach myself to actually use it. After a bit of research I couldn’t find anything that helps me inside atom or emacs - but I found [hardmode for vim](https://github.com/wikitopian/hardmode). It was a bit overkill since it disables all `hjkl` keys but a good start. Then I found [hardtime](https://github.com/takac/vim-hardtime) and it was like my prayers have been answered 🙏. It allows me to use `hjkl` exactly x times - and I can specify how much x is! Only problem - I need to use raw VIM.

## Hello NeoVim (again)
Instead of vim though I grabbed NeoVim as my weapon of choice. Stuffed in my old config file just… to realize that things are not working the way I want them to. Plugins were outdated, keybindings were ‘meh’ and my [old vimrc](https://github.com/dvcrn/dotfiles/blob/master/vim/.vimrc) a complete mess. How were I ever able to work with this??

It had to go and something new had to be in it’s place, so I opened up a new vim pane and started hacking on my new vimrc with the goal that everything I use should be heavily optimized, consistent and use NeoVims async features if possible. No more [syntastic](https://github.com/scrooloose/syntastic) lags on linting - [neomake](https://github.com/benekastah/neomake) runs completely async!

I replaced vundle with the async [vim-plug](https://github.com/junegunn/vim-plug) and re-added crucial packages that I needed - but something was still missing. I can’t just assign random leader keybindings after having such a blast with the spacemacs mnemonic. After changing the leader to `<SPC>` and re-arranging the keybindings a bit I was quite surprised to see what I ended up doing - [re-creating mnemonic keybindings in vim](https://github.com/dvcrn/dotfiles/blob/master/vim/nvim/keybindings.vim#L70-L112)! I also went the extra steps and moved everything related into a package of some sorts. A bit of sourcing here and there and suddenly [I had “layers”](https://github.com/dvcrn/dotfiles/blob/master/vim/nvim/.nvimrc#L79-L104)! Just like in proton or spacemacs!

## Quality plugins ahead

What eventually got me really into vim were probably the amazing plugins of [junegunn](https://github.com/junegunn/), the guy that wrote vim-plug. I am absolutely impressed about the quality of these and didn’t have this much fun with vim for a long time. Stuff like [fzf](https://github.com/junegunn/fzf) makes almost every command feel consistent.

He even wrote a color scheme using the official colors of probably my favorite city on the planet - [Seoul](https://github.com/junegunn/seoul256.vim). Just look how pretty it is:

![screenshot](/images/neovim-1.png)

In all seriousness, his plugin collection is amazing and I can highly recommend you to check it out if you are using vim. He might be the next tpope!

## tl;dr

hardtime for vim made me go back into vim territory and NeoVim made me stay.

If you are a gvim guy like me, check out [neovim-qt](https://github.com/equalsraf/neovim-qt). It is a lot faster performance wise and more stable than [Neovim.app](https://github.com/rogual/neovim-dot-app).

Also my [nvim dotfiles](https://github.com/dvcrn/dotfiles/tree/master/vim/nvim) are a great starting point to get into vim. Everything is written very decoupled so you could just use whatever modules / packages you want and not source the rest.

Do I now use vim for everyting? Nah, probably not. proton is still my main weapon. These days I'm on a VIM streak with the goal to master the more advanced text navigation commands but most of time time proton (if the atom-shell wouldn't suck so much battery....).

Let me know your thoughts [on twitter](https://twitter.com/davicorn).
