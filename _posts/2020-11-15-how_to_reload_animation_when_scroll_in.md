---
title: 'How to reload animation when scroll in?'
layout:   post
category: tutorial-tips
tags:     [js, javascript, animation, css]
feature:  /assets/img/multiple-db-pic1.png
---
For this tutorial we’ll be using some JavaScript. 
The idea is to check when the page is scrolled, for any special elements we want to animate. If any of these special elements are visible, we can give them a special class and use CSS to animate or transition them into view.

To do this we’ll need two things. We’ll need the JavaScript to detect and add a class when one of these elements is visible, and we’ll need to set up before and after styles on the elements.
<!--more-->

# What will you do?
1. create animation css
2. create javascript

# Start Installing
## create animation by css
This is ease animation for any page you can see, we will using it for this tutorials
```css
.active.fade-in {
  animation: fade-in 2s;
}

@keyframes fade-in {
  from { opacity: 0; }
  to   { opacity: 1; }
}
```

## Write js to reload animation 
To catch event scroll, we use code below:
```js
window.addEventListener('scroll', function() {
  console.log("Hello I'm using scroll");
});
```

To set up our requestAnimationFrame method we’ll apply it to a variable. This way we are able to have a fallback function for browsers that don’t yet support it.
```js
var scroll = window.requestAnimationFrame ||
            function(callback){ window.setTimeout(callback, 1000/60)};
```

Next we’ll grab the elements on the page we want to look out for.
```js
var elementsToShow = document.querySelectorAll('.animation');
```

This will look for all elements with class animation and return them as an array we can loop through.
Let’s set up that looping function.

```js
function loop() {

  elementsToShow.forEach(function (element) {
    if (isElementInViewport(element)) {
      element.classList.add('active');
    } else {
      element.classList.remove('active');
    }
  });

  scroll(loop);
}
```

then we will call loop function like below: 
```js
loop();
```

Checking if element in the view port 
```js
function isElementInViewport(el) {
  // special bonus for those using jQuery
  if (typeof jQuery === "function" && el instanceof jQuery) {
    el = el[0];
  }
  var rect = el.getBoundingClientRect();
  return (
    (rect.top <= 0
      && rect.bottom >= 0)
    ||
    (rect.bottom >= (window.innerHeight || document.documentElement.clientHeight) &&
      rect.top <= (window.innerHeight || document.documentElement.clientHeight))
    ||
    (rect.top >= 0 &&
      rect.bottom <= (window.innerHeight || document.documentElement.clientHeight))
  );
}
```

Alternate approach: Intersection Observer
```js
const callback = function(entries) {
  entries.forEach(entry => {
    entry.target.classList.toggle("active");
  });
};

const observer = new IntersectionObserver(callback);

const targets = document.querySelectorAll(".animation");
targets.forEach(function(target) {
  observer.observe(target);
});
```

=> we completed file js like below:
```js
  window.addEventListener('scroll', function() {
    console.log("Hello I'm using scroll");
  });
  var scroll = window.requestAnimationFrame ||
    function(callback){ window.setTimeout(callback, 1000/60)};
  var elementsToShow = document.querySelectorAll('.animation');
  function loop() {

    elementsToShow.forEach(function (element) {
      if (isElementInViewport(element)) {
        element.classList.add('active');
      } else {
        element.classList.remove('active');
      }
    });
  
    scroll(loop);
  }
  loop();
  function isElementInViewport(el) {
    // special bonus for those using jQuery
    if (typeof jQuery === "function" && el instanceof jQuery) {
      el = el[0];
    }
    var rect = el.getBoundingClientRect();
    return (
      (rect.top <= 0
        && rect.bottom >= 0)
      ||
      (rect.bottom >= (window.innerHeight || document.documentElement.clientHeight) &&
        rect.top <= (window.innerHeight || document.documentElement.clientHeight))
      ||
      (rect.top >= 0 &&
        rect.bottom <= (window.innerHeight || document.documentElement.clientHeight))
    );
  }
  const callback = function(entries) {
    entries.forEach(entry => {
      entry.target.classList.toggle("active");
    });
  };
  
  const observer = new IntersectionObserver(callback);
  
  const targets = document.querySelectorAll(".animation");
  targets.forEach(function(target) {
    observer.observe(target);
  });
```

## add animation to an element and watch it
```html
<img src="images/pizza.jpg" class="inline-photo animation fade-in">
```
