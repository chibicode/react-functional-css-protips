# ProTips for Writing Functional CSS + CSS Modules in React.js

![](https://cloud.githubusercontent.com/assets/992008/17532105/d52e78fa-5e33-11e6-94d2-4b77b801c671.png)

## :sunglasses: Who this is for :sunglasses:

- If you're already sold on:

  1. Writing CSS in a **functional** (or atomic/immutable/utility) way and enjoy using toolkits like [Basscss](http://basscss.com) or [Tachyons](http://tachyons.io/), and
  2. Writing front-end in **[React.js](https://facebook.github.io/react/)**

  Then skip to the **"Problems with Directly using Functional CSS"** section.

- If you're not sold, or not sure what functional CSS is, read the below section called "What is functional CSS and why would I want to use it?".

- If you don't know React, you should learn it! I've written a tutorial called [React.js Introduction For People Who Know Just Enough jQuery To Get By](http://reactfordesigners.com/labs/reactjs-introduction-for-people-who-know-just-enough-jquery-to-get-by/), which has received [0.3 million pageviews](https://cloud.githubusercontent.com/assets/992008/17539561/f2842d0c-5e63-11e6-9617-0068cbb5de28.png) in 1 year.

## :sunglasses: What is functional CSS and why would I want to use it? :sunglasses:

This example is [taken](http://tachyons.io/components/collections/product-card/index.html) from [Tachyons](http://tachyons.io/), one of the popular functional CSS toolkits. Consider this <s>cat</s> card:

<img width="270" alt="screenshot 2016-08-09 at 2 34 55 pm" src="https://cloud.githubusercontent.com/assets/992008/17534567/9ca18760-5e3e-11e6-8609-70ac771a618e.png">

In functional CSS (using [Tachyons](http://tachyons.io/)), you'd write HTML like this:

```html
<article class="br2 ba dark-gray b--black-10 mv4 w-100 w-50-m w-25-l mw5 center">
  <img src="http://placekitten.com/g/600/300" class="db w-100 br2 br--top" alt="kitty">
  <div class="pa2 ph3-ns pb3-ns">
    <div class="dt w-100 mt1">
      <div class="dtc">
        <h1 class="f5 f4-ns mv0">Cat</h1>
      </div>
      <div class="dtc tr">
        <h2 class="f5 mv0">$1,000</h2>
      </div>
    </div>
    <p class="f6 lh-copy measure mt2 mid-gray">...</p>
  </div>
</article>
```

And these are the CSS classes that are built into Tachyons:

```css
.ba { border-style: solid; border-width: 1px; }
.b--black-10 { border-color: rgba(0, 0, 0, .1); }
.br2 { border-radius: .25rem; }
.br--top { border-bottom-left-radius: 0; border-bottom-right-radius: 0; }
.db { display: block; }
.dt { display: table; }
.dtc { display: table-cell; }
.lh-copy { line-height: 1.6; }
.mw5 { max-width: 16rem; }
.w-100 { width: 100%; }
.dark-gray { color: #333; }
.mid-gray { color: #555; }
.pa2 { padding: .5rem; }
.mt1 { margin-top: .25rem; }
.mt2 { margin-top: .5rem; }
.mv0 { margin-top: 0; margin-bottom: 0; }
.mv4 { margin-top: 2rem; margin-bottom: 2rem; }
.tr { text-align: right; }
.f5 { font-size: 1rem; }
.f6 { font-size: .875rem; }
.measure { max-width: 30em; }
.center { margin-right: auto; margin-left: auto; }

@media screen and (min-width: 30em) {
  .pb3-ns { padding-bottom: 1rem; }
  .ph3-ns { padding-left: 1rem; padding-right: 1rem; }
  .f4-ns { font-size: 1.25rem; } }
@media screen and (min-width: 30em) and (max-width: 60em) {
  .w-50-m { width: 50%; } }
@media screen and (min-width: 60em) {
  .w-25-l { width: 25%; } }
```

#### Did you think: "This looks like a *terrible* idea"?

I did too, until I actually started using them on my company's production code, and the *maintenability of our frontend 10x'ed*. So why is this not a bad idea? The answer is simple:

- If you use functional CSS, **when you add something new to a page, you'll rarely write any new CSS.** You can build most of what you want to add by composing these small CSS classes.
- This is a sharp contrast to using traditional "semantic CSS", where you'll *add new CSS classes* like `shopping-cart__item--selected` *every time* you add something new to a page.
- Because you'll write CSS less, overall CSS size will be small. That will be better for users (faster load time), but also *better for developers*. Why? Developers need to look at existing CSS and ask themselves *"is there any class I can reuse?"* - but this gets harder as the size of CSS grows.

#### On *not* writing new CSS

The last point is important. Look at the CSS of a large app you're writing, right now. How often do you end up with CSS like this?

```css
/* utils.css */
/* --------- */
.left {
  float: left!important;
}
/* 50 lines later ... */
.left-max-scr1,
.left-max-scr2,
.left-max-scr3,
.left-only-scr1 {
  float: left;
}

/* nav.css */
/* ------- */
.header-nav-container .header-nav-list {
  float: left;
}
/* 50 lines later... */
.CA .header-nav-list.second {
  float: left;
}
/* 50 lines later... */
#nav.challenger-a .submenu-3col li,
#nav.challenger-a .submenu-3col li {
  float: left;
}

/* ie.css */
/* ------ */
.ie6 #footer-content .flex-control-nav li a,
.ie7 #footer-content .flex-control-nav li a,
.ie8 #footer-content .flex-control-nav li a {
  float: left;
}
```

Or take a look at [GitHub's CSS](https://gist.github.com/mrmrs/786241f0a5fade0324e2), which contains 300+ CSS rules that say `display: none`.

These are actually examples from an excellent post called **[CSS and Scalability](http://mrmrs.io/writing/2016/03/24/scalable-css/)**, written by Adam Morse, the author of Tachyons. He claims:

> If you’re going to build a new component, or change a piece of UI in your app - what do you do? I don’t know anyone that reads all the available css in the app to see if there is something they can reuse. **Who has time to read thousands of lines of code before they start working!?** Even if people do have the time, I have not found that this is their first instinct on how to get going. I don’t blame them.

> Even if you did read all of the available css and stumbled upon some code that you think might be reusable - what if someone edits it later? If you are starting from the assumption that your css isn't reusable, your first instinct is to write new css. **But most likely you aren't creating a new classification of visual styles.** In my experience it is likely that you are replicating visual styles that already exist.

> ... Outside of some brand specific background-images, gradients, and colors etc., **the overwhelming majority of css you would need for your site has already been written.**.
>
> ... **The real way to scale css, is to stop writing css.** Abstract out the things you use most and move to a multi-class pattern where you compose visual patterns in your html. You might be amazed at how quickly your team starts to move.

If you write front-end code for your work, I would strongly encourage you to [read Adam's post](http://mrmrs.io/writing/2016/03/24/scalable-css/).

#### Okay, so you won't need to write more CSS, but isn't writing HTML harder now?


## Problems with directly using functional CSS


## Author

**Shu Uesugi** ([Twitter: @chibicode](http://twitter.com/chibicode) / [shu@chibicode.com](mailto:shu@chibicode.com))
