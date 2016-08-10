# ProTips for Writing Functional CSS in React.js

![](https://cloud.githubusercontent.com/assets/992008/17532105/d52e78fa-5e33-11e6-94d2-4b77b801c671.png)

## :sunglasses: Prologue: Who this document is for :sunglasses:

I've originally written this to help my coworkers learn functional CSS, but maybe you'll find it helpful too.

- If you're already sold on:

  1. Writing CSS in a **functional** (or atomic/immutable/utility) way and enjoy using toolkits like [Basscss](http://basscss.com) or [Tachyons](http://tachyons.io/), and
  2. Writing front-end in **[React.js](https://facebook.github.io/react/)**

  Then skip to **"ACT II: Some downsides of using functional CSS"** section.

- If you're not sold, or not sure what functional CSS is, read the below section called "What is functional CSS and why would I want to use it?".

- If you don't know React, you should learn it! I've written a tutorial called [React.js Introduction For People Who Know Just Enough jQuery To Get By](http://reactfordesigners.com/labs/reactjs-introduction-for-people-who-know-just-enough-jquery-to-get-by/), which has received [0.3 million pageviews](https://cloud.githubusercontent.com/assets/992008/17539561/f2842d0c-5e63-11e6-9617-0068cbb5de28.png) in 1 year.

## :sunglasses: Act I: What is functional CSS and why would I want to use it? :sunglasses:

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

### Did you think: "This looks like a *terrible* idea"?

I did too, until I actually started using them on my company's production code, and the *maintenability of our frontend 10x'ed*. So why is this not a bad idea? The answer is simple:

- If you use functional CSS, **when you add something new to a page, you'll rarely write any new CSS.** You can build most of what you want to add by composing these small CSS classes.
- This is a sharp contrast to using traditional "semantic CSS", where you'll *add new CSS classes* like `shopping-cart__item--selected` *every time* you add something new to a page.
- Because you'll write CSS less, overall CSS size will be small. That will be better for users (faster load time), but also *better for developers*. Why? Developers need to look at existing CSS and ask themselves *"is there any class I can reuse?"* - but this gets harder as the size of CSS grows.

### On *not* writing new CSS

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

Or take a look at [GitHub's CSS](https://gist.github.com/mrmrs/786241f0a5fade0324e2), which contains 300+ CSS rules that say `display: none`. For GitHub's credit, this code is likely generated by SCSS, so they didn't *actually* write 300+ `display: none`'s. But as a long time SCSS user, I can say that there's not much preprocessors can do to prevent us from writing more CSS.

By the way, the code above is actually from an excellent post called **[CSS and Scalability](http://mrmrs.io/writing/2016/03/24/scalable-css/)**, written by Adam Morse, the author of Tachyons. He claims (emphasis added):

> If you’re going to build a new component, or change a piece of UI in your app - what do you do? I don’t know anyone that reads all the available css in the app to see if there is something they can reuse. **Who has time to read thousands of lines of code before they start working!?** Even if people do have the time, I have not found that this is their first instinct on how to get going. I don’t blame them.

> **Even if you did read all of the available css and stumbled upon some code that you think might be reusable - what if someone edits it later? If you are starting from the assumption that your css isn't reusable, your first instinct is to write new css. But most likely you aren't creating a new classification of visual styles.** In my experience it is likely that you are replicating visual styles that already exist.

> ... Outside of some brand specific background-images, gradients, and colors etc., **the overwhelming majority of css you would need for your site has already been written.**.
>
> ... **The real way to scale css, is to stop writing css.** Abstract out the things you use most and move to a multi-class pattern where you compose visual patterns in your html. You might be amazed at how quickly your team starts to move.

I would strongly encourage you to [read all of Adam's post](http://mrmrs.io/writing/2016/03/24/scalable-css/).

### Okay, so you won't need to write more CSS, but isn't writing HTML harder now?

When you use these small classes, it'll be harder to write HTML code **IF** you don't know which one of those small classes to use. But here's the key part: popular functional CSS toolkits like [Basscss](http://basscss.com/) and [Tachyons](http://tachyons.io/) are **extremely well documented**. Just take a look at them:

- [Basscss Docs](http://www.basscss.com/)
- [Tachyons Docs](http://tachyons.io/docs/)

And both Basscss and Tachyons have illustrative examples of building larger components using small classes:

![](https://cloud.githubusercontent.com/assets/992008/17543262/2e5cd03e-5e84-11e6-86bb-7e65f56b0d2c.png)

Once you memorize ~50% of the feature sets, you'll be prototyping designs really fast.

The flip side is also true - if you roll your own functional CSS library (or add a lot of new classes to extend Basscss/Tachyons), but don't document them, that's a recipe for nightmare. In other words, **documentation (of small CSS classes) is what makes functional CSS work.**

### What about code reuse for HTML?

Let's return to the Tachyons cat example:

<img width="270" alt="screenshot 2016-08-09 at 2 34 55 pm" src="https://cloud.githubusercontent.com/assets/992008/17534567/9ca18760-5e3e-11e6-8609-70ac771a618e.png">

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

Suppose that somewhere else on your app, you'll need to have something similar to above, but for dogs instead of cats. You'll need to copy paste above code, and replace the photo, name, price, and description, and *keep all of the classes.* That sounds brittle and not very DRY.

Instead, you should be using some sort of a **templating mechanism**. In Rails, you can use partials. In React, you can create a component:

```js
const ItemCard = ({ name, price, image, description, alt }) => (
  <article class="br2 ba dark-gray b--black-10 mv4 w-100 w-50-m w-25-l mw5 center">
    <img src={image} class="db w-100 br2 br--top" alt={alt}>
    <div class="pa2 ph3-ns pb3-ns">
      <div class="dt w-100 mt1">
        <div class="dtc">
          <h1 class="f5 f4-ns mv0">{name}</h1>
        </div>
        <div class="dtc tr">
          <h2 class="f5 mv0">{price}</h2>
        </div>
      </div>
      <p class="f6 lh-copy measure mt2 mid-gray">{description}</p>
    </div>
  </article>
)
```

and you reuse the component instead of reusing HTML:

```js
<ItemCard name='Cat' price='$1,000' image='...' description='...' alt='...' />
<ItemCard name='Dog' price='$2,000' image='...' description='...' alt='...' />
```

In other words, **templating/component system (for HTML markup) is what makes functional CSS great.**

### I'm sold.

Great! Here are some more articles on functional CSS:

- [Building and shipping functional CSS](https://blog.colepeters.com/building-and-shipping-functional-css/) by Cole Peters
- [Rationalizing Functional CSS](https://marcelosomers.com/writing/rationalizing-functional-css/) by Marcelo Somers

And here are some real-world styleguides that use functional CSS:

- [Solid, Buzzfeed's Functional CSS Style Guide](http://solid.buzzfeed.com/) ([Blog Post](http://solid.buzzfeed.com/))
- [Marvel's Functional CSS Style Guide](https://marvelapp.com/styleguide/) ([Blog Post](https://blog.marvelapp.com/the-marvel-styleguide/))

### [Basscss](http://basscss.com/) or [Tachyons](http://tachyons.io/)? How to customize each?

I use both. I encourage you to learn [Basscss](http://basscss.com/) first because its class names tend to be easier to understand. But I like how [Tachyons](http://tachyons.io/) have first-class responsive support for all built-in classes, [like this](http://tachyons.io/docs/typography/text-align/):

```css
@custom-media --breakpoint-not-small screen and (min-width: 48em);
@custom-media --breakpoint-medium screen and (min-width: 48em) and (max-width: 64em);
@custom-media --breakpoint-large screen and (min-width: 64em);

.tl  { text-align: left; }
.tr  { text-align: right; }
.tc  { text-align: center; }

@media (--breakpoint-not-small) {
  .tl-ns  { text-align: left; }
  .tr-ns  { text-align: right; }
  .tc-ns  { text-align: center; }
}

@media (--breakpoint-medium) {
  .tl-m  { text-align: left; }
  .tr-m  { text-align: right; }
  .tc-m  { text-align: center; }
}

@media (--breakpoint-large) {
  .tl-l  { text-align: left; }
  .tr-l  { text-align: right; }
  .tc-l  { text-align: center; }
}
```

As of this writing, [similar utilities are available on Basscss as addons](https://github.com/basscss/addons).

The new CSS variables used in both Basscss and Tachyons (like `var(--name)` or `@custom-media`) can be customized by using [PostCSS](https://github.com/postcss/postcss) with [postcss-cssnext](https://github.com/MoOx/postcss-cssnext) plugin. If you're using [Webpack](https://webpack.github.io/) to load your React code, use [postcss-loader](https://github.com/postcss/postcss-loader) with [postcss-import](https://github.com/postcss/postcss-loader#integration-with-postcss-import). You can learn more about the new CSS features [here](http://cssnext.io/features/) and PostCSS [here](http://cssnext.io/postcss/).

Sometimes Basscss/Tachyons don't allow customizations via CSS variables:

```css
/* Tachyons src/_type-scale.css */
.f1 { font-size: 3rem; }
.f2 { font-size: 2.25rem; }
.f3 { font-size: 1.5rem; }
.f4 { font-size: 1.25rem; }
.f5 { font-size: 1rem; }
.f6 { font-size: .875rem; }
```

In that case, **copy the file** you want to customize, **change the values** (but not class names), and **load the new file** instead of the original.

```css
/* new-tachyons-import.css */
/* load everything tachyons.css is importing, but swap the customized ones */
@import ...;
@import 'tachyons/src/_text-transform';
@import 'customized-type-scale';
@import 'tachyons/src/_utilities';
@import ...;
```

Finally, I **don't** recommend writing a functional CSS library of your own **unless** you're willing to document most of the classes extensively. Similarly, if you're going to **extend** (not customize) Basscss/Tachyons by adding more small classes, document most of them.

## :sunglasses: Act II: Some downsides of using functional CSS :sunglasses:

Functional CSS isn't perfect, however. Consider a design where, buttons, tabs, and checkboxes all have text styles that look like this:

![](https://cloud.githubusercontent.com/assets/992008/17548431/c116cba2-5ea0-11e6-90fa-f4b517bef37f.png)

(1) They're in uppercase, (2) uses San Francisco as `font-family`, and (3) with some `letter-spacing`. In functional CSS, they'd look like this:

```html
<span class='uppercase font-san-francisco letter-spacing-1 ...'>...</span>
```

They're most likely written in different components, like so:

```js
// Button.js - blue font
const Button = (...) => <button className='blue uppercase font-san-francisco letter-spacing-1 ...'>
  ...
</button>

// Tab.js - gray font
const Tab = (...) => <a className='gray uppercase font-san-francisco letter-spacing-1 ...'>
  ...
</a>

// Checkbox.js - black, large font
const Checkbox = (...) => <label ...>
  <input ... />
  <span class='black font-h1 uppercase font-san-francisco letter-spacing-1'>...</span>
</label>
```

Now, suppose that a designer comes in and says that (1) `font-family` should now be Futura, (2) there should be no more uppercase letters and (3) `letter-spacing`.

![](https://cloud.githubusercontent.com/assets/992008/17548450/d222ac40-5ea0-11e6-872e-fac447088760.png)

This will require you to do replace `uppercase font-san-francisco letter-spacing-1` with `font-futura` in each of Button.js, Tab.js, and Checkbox.js.

### The "Find-and-Replace" Problem

Above find-and-replace operation would be easy if they all use the same ordering of classes: `uppercase font-san-francisco letter-spacing-1` - but that leaves a lot of room for error. And this is only changing the same 3 classes in 3 different files - what if the codebase is much bigger, or class replacement logic is more complex?

To me, this **find-and-replace** problem, or **batch-modifying similar styles across multiple components (templates)**, can be problematic for functional CSS.

Marcelo Somers writes this on his article "[Rationalizing Functional CSS](https://marcelosomers.com/writing/rationalizing-functional-css/)" (emphasis added):

> It's wonderful the first time you create a component, but updating styles in an existing system can be a complicated mess of find and replace and other keyboard shortcuts.
>
> ... **But [using find and replace] presumes you created multiple of the same "components" by putting the utility classes in the same order, especially when doing find and replace across multiple pages.** Otherwise, there is no easy way to search across your entire application to find where similar components were implemented.
>
> This might be my single biggest concern about implementing functional CSS in a large app. Just imagine a standard "box" component made up of 7-10 utility classes. If you wanted to change the font size inside all the boxes, you'd need find every instance across your app and update each one manually.
>
> **That leaves a lot of room for error.**

He suggests a "possible fix," where you'd add "a fake class" that has no effect but is just a marker for find-and-replace:

```html
<div class="box-component flex p2 bg-blue white h4 bold">
  The .box-component class wouldn't actually style anything, it would merely be a search key for finding instances where the box component gets used.
</div>
```

But again, you might forget to use add that class, and you're screwed.

### Styles might diverge, and fixing them can be challenging

If find-and-replace fails, your page might end up in a state like this:

![](https://cloud.githubusercontent.com/assets/992008/17549227/20113d2e-5ea4-11e6-851a-2522f01bf80b.png)

Fixing this can be painful, because now you'll need to modify different classes on each file. On Button.js, you need to change `font-san-francisco` to `font-futura`. On Tab.js, you need to remove `uppercase`. On Checkbox.js, you need to remove `letter-spacing-1`.

That's not too bad with if you can visually examine each component and figure out what's wrong. But there's no easy way to point that out by just looking at the code, and the complexity can explode quickly.

Moreover, with functional CSS, **you'll likely end up in a state like above when you're writing new components.** If ideas like "semi-important texts (1) are in uppercase, (2) use San Francisco as font-family, and (3) have some `letter-spacing`." are *not represented/documented anywhere*, and *a dev has to make a decision without a designer* (which happens **often** in practice), you'll end up in a state like above. And once you do, it's hard to get out.

Using components/templates for markup will help, but sometimes similar styles are used across many components/templates.

### You'd still have to write CSS from time to time

If some component *must* have a height of 178px and be absolutely positioned from the bottom at 12px, then you probably **don't** want to make a new class called `height-178` and `bottom-12`. They probably aren't reusable, and probably won't be documented, which are against the philosophies of functional CSS. You should just use write regular CSS.

But again, how should we write regular CSS? Should we just use [BEM again](https://css-tricks.com/bem-101/)? Or write [CSS in JS (inline styles)](https://speakerdeck.com/vjeux/react-css-in-js)? What about a new technique called [CSS Modules](https://github.com/css-modules/css-modules)? I think part of a problem is that React offers so many different ways to write regular CSS, and you probably don't want all those different methods mixed on your codebase.

## :sunglasses: Act III: My Recommendation on Writing Functional CSS in React :sunglasses:

Coming soon...

## Author

**Shu Uesugi** ([Twitter: @chibicode](http://twitter.com/chibicode) / [shu@chibicode.com](mailto:shu@chibicode.com))
