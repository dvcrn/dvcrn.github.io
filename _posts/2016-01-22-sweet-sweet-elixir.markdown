---
layout: post
title:  "Sweet, sweet Elixir - the language of my dreams?"
date:   2016-01-22 10:30:23
categories:
- elixir
- clojure
- clojurescript
---

I like learning new languages, I think that’s no secret. It’s a fun task that teaches you new interesting takes on existing problems. A language can make your programmers life very easy but could also turn it into a living hell. In fact, I am always on the hunt to find the _perfect_ language for me. That thing that I __want__ to use for everything.

### Meet ClojureScript

Lately I’ve been doing a lot of clojure. I am glad that I finally arrived at a point where I don’t have to look into the documentation just to find out how to append something to a vector. Clojure is __extremely well__ designed. I learned a lot just by listening to talks (especially the rich hickey ones!) and reading about the languages' core concepts and the reasons behind them. But above all, Clojure is just fun to use and ClojureScript is even more fun! I love using it and even wrote a [MarkDown editor](https://github.com/dvcrn/markright) in it.  Lately clojurescript ended up becoming the magic that fueles my [entire main editor](https://github.com/dvcrn/proton). 

What’s amazing about ClojureScript is that it can literally go everywhere javascript can go and man can javascript go to places these days. I even started to build freaking [native apps](https://dvcrn.github.io/clojurescript/react/2015/11/19/my-cljs-talk.html) with it! Imagine I would tell that to someone a few years back: 

```
A: Oh that app looks cool. How did you build it?
B: Ah I’m using clojurescript to control the native view properties.
A: cloj… what? 
B: Yeah, it cross compiles to javascript.
A: Javascript? native?
B: Javascript is then using react native to bridge into ObjC
A: 😮
```

I like clojurescript to that degree that I start to feel bothered if someone asks me to write raw javascript these days. I like it that much. 

### Clojure

The only thing I am not 100% happy with right now is… well… clojure! I tried a few times to use it on the serverside but it just never felt completely right to me. I ended up searching all these little building blocks like migrations, database connections, router and templating, and proceeded to stack them together… somehow. 

Then there was the big problem of libraries getting abandoned. Here’s a example: When I wrote my first server it took me ages to find [lobos](https://github.com/budu/lobos) - the migration library everyone told me to use. Shortly after, it turned inactive and communication around it died. Suddenly noone wanted to use lobos. A few questions here and there and people told me to _just_ write SQL files and pipe them with clojure into the db.

Clojure people feel to me like these people that like to build stuff raw and by themselves. To have full control over every aspect and if a building block doesn't exist yet - it will just get written on the spot.
My server eventually worked and that is what counts, right? But I wasn’t happy maintaining it. All these lego bricks felt glued together in a not stable way. 

So I kept going on my way, sometimes using clojure, but most of the times django for my server stuff. I kept adding things to my _you-should-check-that-out-list_ including Go, Rust, and a few frameworks here and there, just waiting for me to have some more free time. 

A few days ago I wanted to start a new side project - an app to be specific. My fingers were itching to do a real world app in clojurescript but needed something for the server. I started mocking things up with django but then took a short look at my list. On first place was something called _elixir_. “Elixir, huh?”, I thought and opened the website

### Hello Elixir

When I first checked out Elixir I didn’t think of much. Looks like ruby and feels like ruby with a bit of clojure sugar sprinkled in. The `|>` operator is obviously taken straight out it. Atoms are no longer state containers but now what I would call… a keyword? In any way, being a functional language inspired by clojure and coming directly _from_ clojure I felt home right away.

I downloaded [phoenix](http://www.phoenixframework.org), figured out [ecto](https://github.com/elixir-lang/ecto), hacked my API server together and went on with my app. That was until I had to implement a image upload and resize service to s3.

My django instinct told me to find the [celery](http://celeryproject.org) of the elixir world but what came next completely changed my way I looked at the language. 

You see - I didn’t read the entire documentation on elixir-lang (which I should have done). I wanted to get a feel for the language first before going into all the little details. When I asked on the elixir slack for the idiomatic way of implementing a worker I got pointed to the [processes](http://elixir-lang.org/getting-started/processes.html), [agents](http://elixir-lang.org/getting-started/mix-otp/agent.html) and [genserver](http://elixir-lang.org/getting-started/mix-otp/genserver.html) guides. 

I knew that elixir is running inside the erlang VM but I never used erlang before and all I knew about it was from hearing about it. 

### Mind = 💥

To summarise it very short: The concurrency handling of elixir is just effing amazing. Without any troubles and at any time you can spin up subprocesses, pass stuff to them, link them together and build your little network. 

Processes are completely isolated and very well managed. I never had the feeling that using processes will now lock me into a big  headache network of synchronisation, orchestration and possible chaos. And even if something goes wrong - elixir and erlang actually _encourage_ you to let things fail! No try/catch blocks and exception handling. You just let the process fail and the supervisor will take care of troubleshooting and restart that thing - just like that!

Here’s an example that really shows how powerful this is: State management! The recommended way of storing state in elixir is… a process! You have a process that holds your piece of state and reacts to defined messages passed to it: e.g. _store_ and _retrieve_. I mean… what?!

If you now connect another node to your mesh network, that node could immediately send messages to the same state management process if it knows it’s identifier (e.g. `:kwstore1`). Just like that you suddenly have state management - but synchronised on multiple machines… I mean… what?!?!

### The joy of concurrency and even more 😮 = 💥

Elixir doesn’t care if you have 1 node or a thousand. It directly works with all of them right away.

Let’s go back to my image upload and resize example: In django I would take the image, push it to s3, push a message into amqp / redis, tell the user “success” and let a worker process the resizing before marking the image as “processed” in a db. 3 components: Web server, queue server, worker.

Now elixir. I take the image, put it on the disk and push the original to s3. I tell the user “success” but at the _same time_ spin up a child process with the reference to image on the disk. The user gets his “success” but the child process lives on! It does it’s resizing stuff, uploads to s3, updates the db and if something goes wrong like a timeout from s3, then so what? It just crashes, the supervisor restarts the process and it tries again. All while the user is already gone! Without a queue! Without a worker! Out of the box!

To give that more context, on heroku with django I would have to scale up another worker process (costs $$), add a amqp server (even more $$) and let everything run at the same time. On heroku with elixir I can literally have all of that inside a free dyno! I could even spin up 50 worker processes and it could still run inside the same free dyno without a cent extra if I am nice with memory.

And you know actually how easy it is to spawn a simple child process?

```
spawn fn ->
	IO.puts “Hello from Child”
end
```

But I’m just scratching the surface of this amazing language. Phoenix for example has built in support for websockets, you know what this means when it comes to elixir? You could connect to server1, I could connect to server98934 which is part of the same mesh and we could __still__ chat with each other without any extra work! 

Elixir made it damn appealing to use concurrency programming. I never fear that I will break something essential because it is just this well done. It is designed that it actually encourages you to use these features more often - because seriously, why wouldn’t you? 

### Scripted Elixir

Amazing syntax, functional programming, REPL driven development, unbeatable concurrency model - how could you make this language even more attractive? I will tell you how:

Elixir has 2 ways of executing your code: Pre-compiled and scripted. Pre-compiled means that once you are ready to run your app you tell elixir (or mix) to compile everything down and execute it. But what could _scripted_ mean? 

```
[i] λ elixir -e ‘IO.puts “Hello World”’
Hello World
```

…that’s what it means! Elixir comes with a [scripted mode](http://elixir-lang.org/getting-started/modules.html#scripted-mode) that compiles your code on the spot in memory before executing it! No slow spinning up java to execute your clojure code or compile it into javascript. Executing elixir in scripted mode takes 1 second at max and doesn’t require you to have anything set up. No project, no mix - nothing. 

### The language of my dreams? 

I know it is too early to judge but I really like Elixir. I didn’t have this much fun programming on the server since I initially discovered django. Pair Elixir on the server with clojurescript on the client and you have the stack of my dreams - powerful and fun programming on the client and server at the same time (let’s call it _dave-stack_). 

I will continue my journey with Elixir. If you didn’t check it out - I highly urge you to do so. It could very easily become the next ruby in a very short time. 