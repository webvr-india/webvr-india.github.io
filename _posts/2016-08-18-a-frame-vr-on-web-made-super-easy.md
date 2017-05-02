---
layout: post
title: A-Frame - VR on Web made super easy!
categories: tutorial
tags: aframe, a-frame, webvr, webvr india, tushar, tushar arora
---

[A-Frame](https://aframe.io/) is an open source framework by [mozilla VR](https://mozvr.com/) team for creating VR scenes using html like markup on web. You don’t require advanced math skills or knowledge of low level WebGL API to come up with a cool VR scene. Simple declarative syntax is all you need to learn.

It is capable of doing what Three.js can do. It works on desktop, Android, iOS, the Oculus rift and the HTC Vive.

I’ll be coming up with a series of posts to understand three.js, a-frame and sharing my experiences with VR and Web in general.

Let’s get started!

## Introduction

Essentially a-frame brings the entity-component system to the DOM (Document Object Model). If you have used three.js, whatever you add to THREE.Scene() is an entity. We can attach components to entity to add any appearance, behavior and functionality.

```html
<a-entity geometry="primitive: box; depth: 2" material="color: #6173F4;opacity: 0.8"></a-entity>
```
  
Here ```<a-entity>``` is a primitive and geometry,  material are its components. Some components are built in a-frame, others you can create of your own. In addition you can also use components created by open source contributors.

Nested entities follow a parent-child relationship with each other. Child entities are affected by behavior and functionality of their parents (child entities do not inherit parent attributes rather apply some transformations based on them). This  is particularly helpful when we need to apply particular behavior to whole set of entities.

Let’s dive into some code.

### Example – Rubik’s Cube

First we need to [download latest a-frame development build](https://aframe.io/releases/0.5.0/aframe.js). Here is a basic skeleton to get started

```html
<!DOCTYPE html>
<html>

<head>
  <meta charset="utf-8">
  <title>Rubik's Cube Demo</title>
  <meta name="description" content="">
  <script src="../js/aframe.js"></script>
</head>

<body>
  <a-scene></a-scene>
</body>
</html>
```
Let’s add a camera to the ```<a-scene>```
```html
<a-entity position="0 10 30">
  <a-entity camera look-controls wasd-controls></a-entity>
</a-entity>
```
Here camera denotes a component with look-controls and wasd-controls. We can also add camera using ```<a-camera>``` primitive.

Now we’ll add some text to the scene using [aframe-bm-text-component](https://github.com/bryik/aframe-bmfont-text-component). Create a new script tag to include this component and put it in header

```html
<script src="../js/aframe-bmfont-text-component.js"></script>
```
  
Add the text entity to the scene
```html
<a-entity bmfont-text="text: Rubik's Cube Demo Using A-Frame;align:left" position="-22 27 0" scale="15 15 15"></a-entity>
```
Now let’s define multicolored-cube component
```js
<script>
  AFRAME.registerComponent('multicolored-cube', {
    init: function() {
      var mesh = this.el.getObject3D('mesh');
      var colors = this.data.colors.split(',');
      var geom = mesh.geometry;
      for (var i = 0; i < geom.faces.length; i++) {
        var face = geom.faces[i];
        face.color.setHex(colors[i]);
      }
      mesh.material.vertexColors = THREE.FaceColors;
      this.el.setObject3D('mesh', mesh);
    }
  });
</script>
```
```AFRAME.registerComponent()``` as the name suggests is used to register a component. It can be used with multiple entities. We need this component to set colors to the cube’s faces. We can get the data passed to the component using [```this.data.propertyName.getObject3D```](https://aframe.io/docs/0.2.0/core/entity.html#getobject3d-type) is used to access THREE.Object3D properties. You can debug the code to see what other properties are available to this object.

Now we’ll be using Jade templating engine to create multiple cubes for our rubik’s cube. The idea is to create 27 cubes positioned in the right manner to create one entity as a whole.

> Note – This component also supports other templating engines like [Handlebars](https://http//handlebarsjs.com/), [Nunjucks](https://mozilla.github.io/nunjucks/) and [Mustache](https://mustache.github.io/)

To add templating support we’ll use [aframe-template-component](https://github.com/ngokevin/aframe-template-component).

```html
<script src="../js/aframe-template-component.js"></script>
```
  
Add the templating script to the <a-assets> which is an asset-management-system to preload assets required by the scene (Must be inside <a-scene>). Also we need to compile the jade template to html markup before rendering the scene.

```html
<a-assets>
  <script id="faces" type="text/x-jade-template">
    - for (var x = 0; x < 3; x++) {
      - for (var y = 0; y < 3; y++) {
        - for (var z = 0; z < 3; z++) {
          a-entity(geometry="primitive: box; depth: 2.9; height: 2.9; width: 2.9", material="color: #FFFFFF", position="#{x * 3 - 3} #{y*3} #{z * 3 - 3}", multicolored-cube="colors: 0x009e60, 0x009e60, 0x0051ba, 0xffd500, 0xffd500, 0xff5800, 0xff5800, 0xC41E3A, 0xC41E3A, 0xB02020, 0xB02020")
         -}
      - }
    - }
  </script>
</a-assets>
```
Here multiple entities are added as siblings of one another. Note that they are not at all nested. All that above for loops are doing is to create entities with different x, y, z coordinates for position.  Also script tag has its type attribute set to ```text/x-jade-template```. It is necessary to make sure component knows which templating engine to use.

Now let’s add this whole script as a source to an entity

```html
<a-entity position="-2 9 0">
  <a-animation easing="linear" attribute="rotation" fill="forwards" 
  to="360 180 360" dur="3000" repeat="indefinite"></a-animation>
  <a-entity template="src: #faces"></a-entity>
</a-entity>
```
An entity is created with the src attribute set to the script tag id so that it can be parsed and appended to the current entity.

You can use your favorite VR device to view this scene or if you don’t have one I would suggest [Google Cardboard](https://vr.google.com/cardboard/) which is an awesome piece of tech for VR to reach masses. Here is the [source code][https://github.com/tushararora/tushararora.github.io/blob/master/rubiks-cube/index.html]. You can also take a look at the [demo](http://tushararora.github.io/rubiks-cube/).

I’ll be coming back with some awesome a-frame stuff.

Till then,

Happy Coding!

