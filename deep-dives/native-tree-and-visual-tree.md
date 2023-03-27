---
description: What should  you know about how MauiReactor works
---

# Native tree and Visual tree

{% hint style="info" %}
This article describes some internals of the MauiReactor library; even if it's not strictly required for the developer to know it, it's still useful to have a good understanding of how MauiReactor handles Native and Visual trees and their differences.
{% endhint %}

.NET MAUI app is composed of a tree of visual elements (classes deriving from `Element`). More precisely any page is the root of a controls tree that describes its UI.&#x20;

For example, you could have a `ContentPage` that contains a `ScrollViewer` that in turn contains a `StackPanel` with children and so on. \
This kind of visual tree structure is pretty common to many other frameworks or technologies (think of HTML DOM or WPF visual tree just to name a few).\
It's often called the _native_ visual tree because it represents the lowest layer of abstraction in UI just above pixels on the screen.

Frameworks like MauiReactor (which is freely inspired to React/Flutter libraries) create a second higher visual tree (sometimes called _ghost_ tree).&#x20;

When you create a page with children in MauiReactor, you're actually creating a visual tree of objects deriving from `VisualNode`. Often a visual node in ReactorUI pairs with a corresponding native control usually with the same name.&#x20;

A MauiReactor component is an object that describes its UI in terms of visual nodes. A component is also a VisualNode so you can include components in a Visual tree.&#x20;

A few things happen when a component calls its `Invalidate` method (`SetState` in components with State) \
1\) MauiReactor calls its `Render` method and created a second version of the component _ghost_ tree\
2\) The previous tree is compared to the new one: new nodes are "mounted" and old ones are "unmounted"\
3\) Any mounted visual node usually creates its paired native control and adds it to the native tree in the correct position; any unmounted node is removed from the native tree\
4\) Any new or preserved node _usually_ updates the corresponding properties of the native counterpart

This section of articles or tutorials will essentially show you how to create ghost visual nodes for any control you want to use in MauiReactor. This could appear complex at first, but you'll see that most of the time is mostly a repetitive process that consists in creating a few small classes that for more clarity are usually contained in a single c# file.
