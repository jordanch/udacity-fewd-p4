### About this project

This project revolved around web performance optimization. We were give source code for a website that performs very poorly in terms of "time to paint" (rendering), JavaScript efficiency, frame rate or "jank". We had to optimize the code to meet minimum Google PageSpeed requirements, JavaScript function execution time and frame rates.


### How do you run this application?

1) git clone the repository
2) open the folder frontend-nanodegree-mobile-portfolio
3) open index.html in Chrome or similar browser
4) click Cam's Pizzeria link
5) Open Chrome Dev Tools console
6) Change the pizza slider's size and observe the time it takes to resize pizzas
7) Open Chrome Dev Tools FPS meter (https://developer.chrome.com/devtools/docs/rendering-settings)
8) Scroll down and up on the pizza.html page and observe constant 60 FPS
9) serve index.html through Page Speed Insights to check Google PageSpeed score for Mobile and Desktop

### My changes from origin source code

#### index.html:

- changed loading of Google Web Font from CSS stylesheet to JavaScript async external load.
- added the media="print" attribute to the link element for the print.css file. This means that the stylesheet will be downloaed by the browser but only applied when the media query is true.
- changed google analytics javascript to be async
- included styles for smartphone and main (style.css) styles within index.html in the style element. This reduced browser requests and optimized the delivery of CSS.

My last PageSpeed check had the following results:

93/100 Mobile
96/100 Desktop

#### pizza.html:

[[ Optimizations regarding changePizzaSizes: ]]

The original code was as follows:
```
 function changePizzaSizes(size) {
    for (var i = 0; i < document.querySelectorAll(".randomPizzaContainer").length; i++) {
      var dx = determineDx(document.querySelectorAll(".randomPizzaContainer")[i], size);
      var newwidth = (document.querySelectorAll(".randomPizzaContainer")[i].offsetWidth + dx) + 'px';
      document.querySelectorAll(".randomPizzaContainer")[i].style.width = newwidth;
    }
  }

```
After optimizing JavaScript operation, the code is as follows:

```
var randomPizzaContainerNodeList = document.querySelectorAll(".randomPizzaContainer"); // this expression needs only be computed once, instead of every loop iteration
    var dx = determineDx(randomPizzaContainerNodeList[0], size); // since all the pizza elements are the same, the first element in the nodeList can be used to determine the dx
    var newwidth = (randomPizzaContainerNodeList[0].offsetWidth + dx) + 'px'; // ditto as above
    var length = randomPizzaContainerNodeList.length; // this computation need only be done once so the for loop statement has a variable to reference instead of computing the value every iteration
    function changePizzaSizes(size) {
      for (var i = 0; i < length; i++) {
        randomPizzaContainerNodeList[i].style.width = newwidth;
      };
    };

```
The above optimizations revolve around improving read and write JavaScript operation execution order so as to reduce execution time. The operation order significantly improves paint time.

[[ Optimizations regarding movingPizzas: ]]

The original code for this function is:

```
 var items = document.querySelectorAll('.mover');
  for (var i = 0; i < items.length; i++) {
    var phase = Math.sin((document.body.scrollTop / 1250) + (i % 5));
    items[i].style.left = items[i].basicLeft + 100 * phase + 'px';
  }

```
My modified, optimized code is as follows:

```

  var items = document.querySelectorAll('.mover');

  function computationToOccurBeforeNextRePaint() {
    for (var i = 0; i < items.length; i++) {
      var phase = Math.sin((document.body.scrollTop / 1250) + (i % 5));
      items[i].style.left = items[i].basicLeft + 100 * phase + 'px';
    };
    window.requestAnimationFrame(computationToOccurBeforeNextRePaint);
  };

  window.requestAnimationFrame(computationToOccurBeforeNextRePaint);

```
The optimization leverages requestAnimationFrame API and effectively ensures that all JavaScript computation required for the next animation is handled before the next animation frame.


## Website Performance Optimization portfolio project --- Udacity's brief:

Your challenge, if you wish to accept it (and we sure hope you will), is to optimize this online portfolio for speed! In particular, optimize the critical rendering path and make this page render as quickly as possible by applying the techniques you've picked up in the [Critical Rendering Path course](https://www.udacity.com/course/ud884).

To get started, check out the repository, inspect the code,

### Getting started

####Part 1: Optimize PageSpeed Insights score for index.html

Some useful tips to help you get started:

1. Check out the repository
1. To inspect the site on your phone, you can run a local server

  ```bash
  $> cd /path/to/your-project-folder
  $> python -m SimpleHTTPServer 8080
  ```

1. Open a browser and visit localhost:8080
1. Download and install [ngrok](https://ngrok.com/) to make your local server accessible remotely.

  ``` bash
  $> cd /path/to/your-project-folder
  $> ngrok http 8080
  ```

1. Copy the public URL ngrok gives you and try running it through PageSpeed Insights! Optional: [More on integrating ngrok, Grunt and PageSpeed.](http://www.jamescryer.com/2014/06/12/grunt-pagespeed-and-ngrok-locally-testing/)

Profile, optimize, measure... and then lather, rinse, and repeat. Good luck!

####Part 2: Optimize Frames per Second in pizza.html

To optimize views/pizza.html, you will need to modify views/js/main.js until your frames per second rate is 60 fps or higher. You will find instructive comments in main.js.

You might find the FPS Counter/HUD Display useful in Chrome developer tools described here: [Chrome Dev Tools tips-and-tricks](https://developer.chrome.com/devtools/docs/tips-and-tricks).

### Optimization Tips and Tricks
* [Optimizing Performance](https://developers.google.com/web/fundamentals/performance/ "web performance")
* [Analyzing the Critical Rendering Path](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/analyzing-crp.html "analyzing crp")
* [Optimizing the Critical Rendering Path](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/optimizing-critical-rendering-path.html "optimize the crp!")
* [Avoiding Rendering Blocking CSS](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/render-blocking-css.html "render blocking css")
* [Optimizing JavaScript](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/adding-interactivity-with-javascript.html "javascript")
* [Measuring with Navigation Timing](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/measure-crp.html "nav timing api"). We didn't cover the Navigation Timing API in the first two lessons but it's an incredibly useful tool for automated page profiling. I highly recommend reading.
* <a href="https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/eliminate-downloads.html">The fewer the downloads, the better</a>
* <a href="https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/optimize-encoding-and-transfer.html">Reduce the size of text</a>
* <a href="https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/image-optimization.html">Optimize images</a>
* <a href="https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/http-caching.html">HTTP caching</a>

### Customization with Bootstrap
The portfolio was built on Twitter's <a href="http://getbootstrap.com/">Bootstrap</a> framework. All custom styles are in `dist/css/portfolio.css` in the portfolio repo.

* <a href="http://getbootstrap.com/css/">Bootstrap's CSS Classes</a>
* <a href="http://getbootstrap.com/components/">Bootstrap's Components</a>

### Sample Portfolios

Feeling uninspired by the portfolio? Here's a list of cool portfolios I found after a few minutes of Googling.

* <a href="http://www.reddit.com/r/webdev/comments/280qkr/would_anybody_like_to_post_their_portfolio_site/">A great discussion about portfolios on reddit</a>
* <a href="http://ianlunn.co.uk/">http://ianlunn.co.uk/</a>
* <a href="http://www.adhamdannaway.com/portfolio">http://www.adhamdannaway.com/portfolio</a>
* <a href="http://www.timboelaars.nl/">http://www.timboelaars.nl/</a>
* <a href="http://futoryan.prosite.com/">http://futoryan.prosite.com/</a>
* <a href="http://playonpixels.prosite.com/21591/projects">http://playonpixels.prosite.com/21591/projects</a>
* <a href="http://colintrenter.prosite.com/">http://colintrenter.prosite.com/</a>
* <a href="http://calebmorris.prosite.com/">http://calebmorris.prosite.com/</a>
* <a href="http://www.cullywright.com/">http://www.cullywright.com/</a>
* <a href="http://yourjustlucky.com/">http://yourjustlucky.com/</a>
* <a href="http://nicoledominguez.com/portfolio/">http://nicoledominguez.com/portfolio/</a>
* <a href="http://www.roxannecook.com/">http://www.roxannecook.com/</a>
* <a href="http://www.84colors.com/portfolio.html">http://www.84colors.com/portfolio.html</a>
