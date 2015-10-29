---
layout: post
title:  "Going native with om.next"
date:   2015-10-27 12:47:46
categories: 
- clojurescript
- react
---

I mentioned already earlier that I believe [om.next][1] will be the future. Not particular om itself but the idea it implements: demand driven applications. The component declares what data it needs and the server is just there for delivering it. Components don't want to coordinate 20 ajax requests just to gather all the data they need to operate. Components also don't need all of the data your API provides in most cases, so why even the need to download all of it? 

These concepts make sense as the next iteration of server / client architectures but you know where they fit even better? __Mobile Apps!__

Especially when load time is critical and data volume limited, downloading big chunks of REST json data can get very annoying. We can't just pump all the jsons our website uses into an app and expect it to be as performant as the web counterpart. 

So, wouldn't it be amazing if our app just says _"I need the list of available books, but only the title and the image url and nothing else"_ and it will get exactly that and nothing more? 

With [next][1] around the corner I decided to give it a try with react native. [cljsrn][2] is already bundling a good amount of resources around the topic clojurescript and react native and [natal][3] and [ambly][4] already work great for legacy om, so let's make it work for om.next!

### A stoney path

After poking a bit around with next and natal, I quickly ran into a deadend because of it's dependency to `gdom` and `ReactDOM`. Instead of calling `React.render`, om.next relies on React 0.14 which splits the rendering logic from the component. Here we're calling `ReactDOM.render` instead, which, well, relies on a DOM. 

Luckily after a short chat with [@dnolen][5] he promptly stripped out `ReactDOM` (and `gdom` as well) and added functionality that allows us to pass a `:root-render` and `:root-unmount` function into the reconciler. Whatever we specify here will get used to render and unmount components. 

Reading through the [react native source][6] made it clear that 0.14 has not been adapted yet and the rendering and unmounting logic is still inside `React`. To plug this into om.next, all we have to do is

```cljs
:root-render #(.render js/React %1 %2)
:root-unmount #(.unmountComponentAtNode js/React %)})
```

_...where js/React is the required react-native library._

Another thing that becomes clear is that React Native doesn't render components into DOM references. Instead, it [uses a number][7] that references back to a component. With the first one being `1`, we know exactly what where we have to add our root to:

```cljs
(om/add-root! reconciler my-component 1)
```

### Wrapping up

![showcase][9]

The full code looks something like this:

```cljs
;; Need to set js/React first so that Om can load
(set! js/React (js/require "react-native/Libraries/react-native/react-native.js"))

(ns future-next.core
  (:require [om.next :as om :refer-macros [defui]]))

;; Reset js/React back as the form above loads in an different React
(set! js/React (js/require "react-native/Libraries/react-native/react-native.js"))

;; Setup some methods to help create React Native elements
(defn view [opts & children]
  (apply js/React.createElement js/React.View (clj->js opts) children))

(defn text [opts & children]
  (apply js/React.createElement js/React.Text (clj->js opts) children))


;; Set up our Om UI
(defonce app-state (atom {:app/msg "Hello om.next!"}))

(defui WidgetComponent
  static om/IQuery
  (query [this]
         '[:app/msg])
  Object
  (render [this]
          (let [{:keys [app/msg]} (om/props this)]
            (view {:style {:flexDirection "column" :margin 40}}
                  (text nil msg))
            )))
 
 (defn read
   [{:keys [state] :as env} key params]
   (let [st @state]
     (if-let [[_ v] (find st key)]
       {:value v}
       {:value :not-found})))
 
 (def reconciler
   (om/reconciler
    {:state app-state
     :parser (om/parser {:read read})
     :root-render #(.render js/React %1 %2)
     :root-unmount #(.unmountComponentAtNode js/React %)})
   )
 
(om/add-root! reconciler WidgetComponent 1)

(defn ^:export init []
  ((fn render []
     (.requestAnimationFrame js/window render))))
```

[I submitted a PR][8] to natal and once that's merged in, all you have to do is `natal init` :)

Can you believe that all of this happened in just 1.5 days?

[1]: https://github.com/omcljs/om
[2]: http://cljsrn.org/
[3]: https://github.com/dmotz/natal
[4]: http://cljsrn.org/ambly.html
[5]: https://github.com/swannodette
[6]: https://github.com/facebook/react-native/tree/master/Libraries/ReactNative
[7]: https://github.com/facebook/react-native/blob/master/Libraries/ReactNative/ReactNative.js#L74-L80
[8]: https://github.com/dmotz/natal/pull/18
[9]: /images/om.next-native.png
