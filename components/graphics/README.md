---
description: >-
  This page enumerates the options you have to to draw directly on a canvas in
  MauiReactor
---

# Graphics

Sometimes you want to directly just draw lines, text, or shapes in general inside a blank canvas. Drawing directly has some advantages:

* You  can follow UI designs at the pixel level, for example, you can perfectly round corners at the required value or use the same shadow color as specified in a Figma project
* You need to boost your application performance, especially when dealing with a lot of views present at the same time on a page
* You require that a specific control or group of controls have exactly the same appearance among all platforms

Some disadvantages:

* Often dealing with low-level commands like DrawLine or DrawString results in a more complex code to write a text
* Is sometimes difficult to handle correctly all the user interactions as the native control does: take for example the simple Button view that appears and behaves differently under Android and iOS

MauiReactor features a complete set of tools that allows you to write graphics objects and different levels of abstraction.&#x20;

From the higher level to the lower:

* Using the MauiReactor CanvasView allows declaring graphics objects and interacting with them like any MauiReactor visual node
* Using GraphicsView standard MAUI control as described [here](../../deep-dives/working-with-the-graphicsview.md)
* Using SkiaSharp package to directly issue commands to a Skia canvas

