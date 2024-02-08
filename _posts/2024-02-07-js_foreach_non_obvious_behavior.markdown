---
layout: post
title:  "Non-obvious behaviors in Javascript foreach() loop"
date:   2024-02-07
description: I believe that you use foreach loop in Javascript, but do you know about two specific behaviors in it?
---
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/blog-cells@0.7.1/dist/blog-cells.css" />
<script src="https://cdn.jsdelivr.net/npm/blog-cells@0.7.1/dist/blog-cells.js"></script>

<p class="intro"><span class="dropcap">I</span> want to show two inconspicuous-looking behaviors in Javascript foreach() loop that could confuse you while using it.
</p>

#### How to stop foreach loop in Javascript?

At first look pretty simple, inconspicuous-looking question, isn't it? But it is Javascript... so any question could be tricky 😏

Let's look at the sample code below (you can edit and run it)  

<script type="text/notebook-cell">
const someArray = [0, 1, 2, 3, 4, 5];

someArray.forEach((number) => {
  if (number > 2) {
    console.log("Is it stopped? No! The number is:", number);
    return; //You could expect that this is enough to stop the loop, but it's not
  }
  console.log("Number:", number);
});
</script>
It prints every number from array but ```return``` keyword is ineffective here. As is turns out, this is correct behavior according to [documentation](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach){:target="_blank"}:
 > There is no way to stop or break a ```forEach()``` loop other than by throwing an exception.  

So, we have only one way to stop this loop:

<script type="text/notebook-cell">
const someArray = [0, 1, 2, 3, 4, 5];

try {
  someArray.forEach((number) => {
    console.log("Number:", number);
    if (number > 2) {
      throw new Error("Loop stopped.");
    }
  });
} catch (error) {
  console.log("Caught an error:", error.message);
}
</script>

Now the loop is successfully stopped but... isn't it looks like using cannon to kill a fly? So if you have to break loop you should consider to use e.g ```for``` loop instead ```foreach```

#### Async function in foreach loop - will it work?

The second thing in ```foreach``` loop that could be misleading is: 
 > ```foreach()``` expects a synchronous function - it does not wait for promises  

Check this code:

<script type="text/notebook-cell">
const array = [0, 1, 2, 3, 4];
let sum = 0;

const sumFunction = async (a, b) => a + b;

array.forEach(async (number) => {
  sum = await sumFunction(sum, number);
});

console.log("Sum:", sum); // Expected output: 10, actual: 0
</script>

As you can see, async function is not awaited.  


Maybe for someone this two topics are obvious, but for me both of presented behaviors were unexpected (until today 😉).