---
layout: post
title: "MarkRight - a markdown editor in om+electron"
date: "2015-10-22 19:43:17 +0900"
---

If you follow clojurescript development you might have already heard about the next iteration of om, [om.next][1]. 

om.next is David Nolens attempt to bring together what big companies like facebook with [relay][3] and netflix with [falcor][3] are doing at the moment. I will not go into detail but instead of REST, it implements a architecture in which  components clearly declare what kind of data they need to operate. A server / router is then gathering that data and passing it back. Instead of multiple REST endpoints, you'll end up with 1 graph endpoint that understands the client query. 

While still in early alpha, I was looking for an excuse to play around with it - or at least a excuse to use it in the most simple case possible. After scratching my head for a bit I came up with a __markdown editor__. But why the hell would I want to write a markdown editor? 

When I have to write markdown, I'm missing a perfect live preview. I know, there are options out there that give you that either webbased or stuff that just let's you preview what you wrote. The most popular probably being [mou][4]. 

But I don't like mou and it looks like they seem to charge for it soon. On top of that, I was eyeballing githubs [electron](https://github.com/atom/electron) (the thing that atom uses) for a while and wanted to give it a try. 

## Meet MarkRight

So I played a bit electron and om, created a pull request to [descjop][7] on the way and eventually came up with __MarkRight__. 

![markright banner][6]

Markright is like the most simple editor you can imagine but it is this simplicity that I love! It is literally powered by 2 (or 3 if you count root) om / react components and all they do is piping your input text in real-time through [marked][8]. It was fun to give [codemirror][9] a spin as well! 

I was really surprised how easy it was to just plug these different components together. The open source community did an amazing job with building and maintaining these cool toys! 

Anyway, here's how it looks:

![screenshot][10]

There's not much to it yet. You can save files, open files and - well - write markdown. There's a lot of things to do but if you like the idea, please feel free to [contribute at github][11] :)


[1]: https://github.com/omcljs/om
[2]: https://github.com/Netflix/falcor
[3]: https://facebook.github.io/relay/
[4]: http://25.io/mou/
[5]: https://github.com/atom/electron
[7]: https://github.com/karad/lein_template_descjop/
[8]: https://github.com/chjj/marked
[9]: https://codemirror.net/
[11]: https://github.com/dvcrn/markright

[6]: https://raw.githubusercontent.com/dvcrn/dmedit/master/resources/markright-banner.png
[10]: /markright.png