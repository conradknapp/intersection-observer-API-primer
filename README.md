# Basics of Using the Intersecton Observer API

## Usage

## Browser Support

According to [caniuse.com](https://caniuse.com/#feat=intersectionobserver) statistics, browser support for the Intersection Observer API is ~75% globally (72.94%). This repo includes a polyfill to support the remaining browsers that don't accept it yet.

## Polyfills

One easy to check for the Insection Observer API is with the navigator object in your browser's dev tools:

```js
!!("IntersectionObserver" in window); // true--you have it; false--you don't
```

If the return value is false, there is a [polyfill](https://github.com/w3c/IntersectionObserver/tree/master/polyfill) provided by the W3C, available either as a drop-in script or an npm package:

```
npm install intersection-observer
```

```
<script src="https://polyfill.io/v2/polyfill.min.js?features=IntersectionObserver"></script>
```

To set up the polyfill in our project (as it is set up here), we can use a simple conditional:

## Basic Implementation

Step 1 - Rename src attribute of images to data-src (or a more declarative data attribute; i.e. data-LL-src)

```
<img data-src="./images/nature-4.jpeg" alt="Sun Setting in the Desert">
```

Why? The browser immediately starts downloading and displaying images with the src attribute. We want to make sure the images get deferred (to load the images only when they are in or near the viewport), we rename src to a property we turn back to the src when the observer is fired.

Step 1.5 - Make a small function that will turn the newly created data-src of each image back into the src.

Step 2 - Grab all the image elements using JS (using document.querySelectorAll(img[data-src]), for example) and store it in a variable.

Step 3 - Create a function that generates an Intersection Observer using the Intersection Observer constructor (new IntersectionObserver()) and store it as a variable. Within the same function, iterate over the stored images, call .observe() on the created reference to the IO constructor and pass to it each individual image.

Step 4 - To specify that we want to load images as they enter the viewport, we use the intersectionRatio.

Note: intersectionRatio represents how much percent the image is in the viewport in a range of 1 to 0. It signifies that if the value is above 0, it is entering the viewport.

Additionally, we can specify a value called 'root margin', which tells the observer to wait to fire when the image is that percent of the viewport away. Root margin is useful, because we don't want images to load unevenly if users scroll quickly; we want to load the image a little before it enters the viewport.

## Development

For this project, I have live-server as a dev dependency, which provides a nice live reloading functionality.

To get started:

```
yarn install
```

Then simply run:

```
live-server
```

## An Aside about this Project

This project uses what were (originally) very large .jpeg files from Pexels and were optimized using the compression tool ImageOptim. The images could have been reduced in size even further, however, for this project I wanted somewhat larger files so the process of loading the image was visible when hitting the breakpoint of our Intersection Observer.
