# Basics of Using the Intersection Observer API

## Browser Support

According to [caniuse.com](https://caniuse.com/#feat=intersectionobserver) statistics, browser support for the Intersection Observer API is ~75% globally (72.94%). This repo includes a polyfill to support the remaining browsers that don't accept it yet.

## Polyfills

One easy to check for the Insection Observer API is with the navigator object in your browser's dev tools:

```js
!!("IntersectionObserver" in window); // true--you have it; false--you don't
```

If the return value is false, there is a [polyfill](https://github.com/w3c/IntersectionObserver/tree/master/polyfill) provided by the W3C, available either as a drop-in script or an npm package:

NPM:

```
npm install intersection-observer
```

Script:

```
<script src="https://polyfill.io/v2/polyfill.min.js?features=IntersectionObserver"></script>
```

To set up the polyfill in our project (as it is set up here), we can use a simple conditional:

## Basic Implementation

Step 1 - Rename src attribute of images to data-src (or a more declarative data attribute; i.e. data-LL-src / data-lazyload-src)

```
<img data-src="./images/nature-4.jpeg" alt="Sun Setting in the Desert">
```

Why is this necessary? The browser immediately starts downloading and displaying images with the src attribute. We want to make sure the images get deferred (to load the images only when they are in or near the viewport), we rename src to a property we turn back to the src when the observer is fired.

Step 1.5 - Make a small function that will turn the newly created data-src of each image back into the src.

```js
function convertDataSrcToSrc(image) {
  image.src = image.getAttribute('data-src');
}
```

Step 2 - Create a function that generates an Intersection Observer using the Intersection Observer constructor (new IntersectionObserver()) and store it as a variable (which we call IO). 

```js
function createObserverAndObserve() {
  const IO = new IntersectionObserver();
}
```

The Intersection Observer takes a callback function, in which we iterate over the items (images, in the case of this site) and check for certain properties on each image. Two of them are .isIntersecting and .intersectionRatio.

Step 3 - Grab all the image elements using JS (using document.querySelectorAll(img[data-src]), for example) and store it in a variable.

```js
const unloadedImages = document.querySelectorAll('img[data-src]');
```

Then, loop over the images, and for each iteration, pass in the given image to the observe method on the Intersection Observer object that we created.

```js
function createObserverAndObserve() {
  const IO = new IntersectionObserver();

  unloadedImages.forEach(el => {
    IO.observe(el);
  });
}
```

Note: A pared-down example of using the Observer (somewhat differing from these instructions) to give you an idea of what is happening is like this:

```js
const IO = new IntersectionObserver(images => {
  for (const image of images) {
    console.log(`${image.target.alt} is in view: ${image.isIntersecting}`);
  }
})

document.querySelectorAll('img')
  .forEach(el => IO.observe(el));
```

Which should give us a result like this (when scrolling down from the top of our page):

```
Placid Lake is in view: true
Placid Lake is in view: false
Beautiful Green Forest is in view: true
Beautiful Green Forest is in view: false
Fog-covered Hill is in view: true
```

Step 4 - In the brief example above, we used the .isIntersecting property on each image to indicate that they were entering and exiting the viewport. For our site, to specify that we want to load images as they enter the viewport, we will use another property that is available to use when observing images-- intersectionRatio.

intersectionRatio represents how much percent the image is in the viewport in a range of 1 to 0. It signifies that if the value is above 0, it is entering the viewport.

Let's create a separate function that will serve as the callback function to the Intersection Observer constructor function:

```js
function checkIfImgVisible(images) {
  images.forEach(image => {
    if (image.intersectionRatio > 0) {
      convertDataSrcToSrc(image.target);
    }
  })
}
```

Then we pass this function into the Intersection Observer function:

```js
function createObserverAndObserve() {
  const IO = new IntersectionObserver(checkIfImgVisible);

  unloadedImages.forEach(el => {
    IO.observe(el);
  });
}
```

Additionally, we can specify a value called 'root margin', which tells the observer to wait to fire when the image is that percent of the viewport away. Root margin is useful, because we don't want images to load unevenly if users scroll quickly; we want to load the image a little before it enters the viewport.

Step 5 - All that remains to be done is to call the function in which we created the Intersection Observer and iterate over the images, calling observe on each, this should it turn reference our callback function, which checks uses the intersectionRatio to determine if the image is visible, and then, when it is, it moves the image link stored in the data-src to the src attribute.

At the bottom of our script, we call:

```js
createObserverAndObserve();
```

And we're done! Now, if you check your network tab, you should see images being loaded as they enter the viewport.

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
