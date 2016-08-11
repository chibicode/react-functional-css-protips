# Functional CSS - The Good, The Bad, and Some Protips for React.js Users

![](https://cloud.githubusercontent.com/assets/992008/17532105/d52e78fa-5e33-11e6-94d2-4b77b801c671.png)

### Table of Contents

This repo is a Three-Act essay.

- [**Prologue:** Who this document is for](#sunglasses-prologue-who-this-document-is-for-sunglasses)
- [**Act I:** What is functional CSS and why would I want to use it?](#sunglasses-act-i-what-is-functional-css-and-why-would-i-want-to-use-it-sunglasses)
- [**Act II**: Some downsides of using functional CSS](#sunglasses-act-ii-some-downsides-of-using-functional-css-sunglasses)
- [**Act III**: Some ProTips on Writing Functional CSS in React](#sunglasses-act-iii-some-protips-on-writing-functional-css-in-react-sunglasses)
  - [**ProTip 1**: Use "virtual classes" and document them](#v-protip-1-use-virtual-classes-and-document-them-v)
  - [**ProTip 2**: Use CSS Modules and call them from `cn`](#v-protip-2-use-css-modules-and-call-them-from-cn-v)
  - [**ProTip 3**: Make `cn` more powerful by integrating classnames library](#v-protip-3-make-cn-more-powerful-by-integrating-classnames-library-v)
- [**Epilogue**](#sunglasses-epilogue-sunglasses)
  - [**Appendix**](#appendix)

## :sunglasses: Prologue: Who this document is for :sunglasses:

I've originally written this to teach functional CSS to my coworkers, but maybe you'll find it helpful too.

- If you're already sold on:

  1. Writing CSS in a **functional** (or atomic/immutable/utility-class) way and enjoy using toolkits like [Basscss](http://basscss.com) or [Tachyons](http://tachyons.io/), and
  2. Writing front-end in **[React.js](https://facebook.github.io/react/)**,

  Then skip to [**Act II**: Some downsides of using functional CSS](#sunglasses-act-ii-some-downsides-of-using-functional-css-sunglasses).

- If you don't know what functional CSS is, or if you know it but aren't sold, continue reading the next section.

- If you don't know React, you should learn it! I've written a [tutorial](http://reactfordesigners.com/labs/reactjs-introduction-for-people-who-know-just-enough-jquery-to-get-by/), which has received [0.3 million pageviews](https://cloud.githubusercontent.com/assets/992008/17539561/f2842d0c-5e63-11e6-9617-0068cbb5de28.png) in 1 year.

---

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

Above HTML uses the following CSS classes that are built into Tachyons.

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

I did too, until I actually started using functional CSS on my company's production code, and the *maintainability of our frontend went up by 10x* (rough estimate). Why? The answer is simple:

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

Instead, you should be using some sort of **HTML templates**. In Rails, you can use partials. In React, you can create a component:

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

In other words, **template/component system (for HTML markup) is what makes functional CSS great.**

### I'm sold.

Great! Here are some more articles on functional CSS:

- [Building and shipping functional CSS](https://blog.colepeters.com/building-and-shipping-functional-css/) by Cole Peters
- [Rationalizing Functional CSS](https://marcelosomers.com/writing/rationalizing-functional-css/) by Marcelo Somers
- [Functional Programming, CSS, and your sanity](http://www.jon.gold/2015/07/functional-css/) by Jon Gold

And here are some real-world styleguides that use functional CSS:

- [Solid, Buzzfeed's Functional CSS Style Guide](http://solid.buzzfeed.com/) ([Blog Post](https://medium.com/buzzfeed-design/introducing-solid-1c16b1bf4868))
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

---

## :sunglasses: Act II: Some downsides of using functional CSS :sunglasses:

Functional CSS isn't perfect, however. Consider a design where, buttons, tabs, and checkboxes all have text styles that look like this:

![](https://cloud.githubusercontent.com/assets/992008/17548431/c116cba2-5ea0-11e6-90fa-f4b517bef37f.png)

(1) They're in uppercase, (2) use San Francisco as `font-family`, and (3) are with some `letter-spacing`. In functional CSS, they'd look like this:

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

// Heading.js - black, large font
const Heading = (...) => <h2 className='black font-h1 uppercase font-san-francisco letter-spacing-1 ...'>
  ...
</h2>
```

Now, suppose that a designer comes in and says that (1) `font-family` should now be Futura, (2) there should be no more uppercase letters and (3) `letter-spacing`.

![](https://cloud.githubusercontent.com/assets/992008/17548450/d222ac40-5ea0-11e6-872e-fac447088760.png)

This will require you to do replace `uppercase font-san-francisco letter-spacing-1` with `font-futura` in each of Button.js, Tab.js, and Heading.js.

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

Fixing this can be painful, because now you'll need to modify different classes on each file. On Button.js, you need to change `font-san-francisco` to `font-futura`. On Tab.js, you need to remove `uppercase`. On Heading.js, you need to remove `letter-spacing-1`.

That's not too bad with if you can visually examine each component and figure out what's wrong. But there's no easy way to point that out by just looking at the code, and the complexity can explode quickly.

Moreover, with functional CSS, **you'll likely end up in a state like above when you're writing new components.** If ideas like "semi-important texts (1) are in uppercase, (2) use San Francisco as font-family, and (3) have some `letter-spacing`." are *not represented/documented anywhere*, and *a dev has to make a decision without a designer* (which happens **often** in practice), you'll end up in a state like above. And once you do, it's hard to get out.

Using components/templates for markup will help, but sometimes similar styles are used across many components/templates.

### You'd still have to write CSS from time to time

If some component *must* have a height of 178px and be absolutely positioned from the bottom at 12px, then you probably **don't** want to make a new class called `height-178` and `bottom-12`. They probably aren't reusable, and probably won't be documented, which are against the philosophies of functional CSS. You should just use write regular CSS.

But again, how should we write regular CSS? Should we just use [BEM again](https://css-tricks.com/bem-101/)? Or write [CSS in JS (inline styles)](https://speakerdeck.com/vjeux/react-css-in-js)? What about a new technique called [CSS Modules](https://github.com/css-modules/css-modules)? I think part of a problem is that React offers so many different ways to write regular CSS, and you probably don't want all those different methods mixed on your codebase.

---

## :sunglasses: Act III: Some ProTips on Writing Functional CSS in React :sunglasses:

I spent some time thinking about how these problems can be solved. Here are my initial thoughts - they are pretty experimental.

Also note: **these protips are React.js specific.**

### :v: ProTip 1: Use "virtual classes" and document them :v:

A **virtual class** is a class which gets converted into a set of functional CSS classes. The name "virtual" is inspired from React's virtual DOM.

Let's recall our previous example, where some texts (1) are in uppercase, (2) use San Francisco as font-family, and (3) have some letter-spacing.

![](https://cloud.githubusercontent.com/assets/992008/17548431/c116cba2-5ea0-11e6-90fa-f4b517bef37f.png)

Instead of writing `uppercase font-san-francisco letter-spacing-1`:

```js
const Button = (...) => <button className='blue uppercase font-san-francisco letter-spacing-1 ...'>
  ...
</button>

const Tab = (...) => <a className='gray uppercase font-san-francisco letter-spacing-1 ...'>
  ...
</a>

const Heading = (...) => <h2 className='black font-h1 uppercase font-san-francisco letter-spacing-1 ...'>
  ...
</h2>
```

you'd write a virtual class called `-text-style-emphasis`. A hyphen is added in the beginning to indicate that it's a virtual class. Then, **use `cn`** (short for "class name") helper function on `className`, which converts `-text-style-emphasis` to `uppercase font-san-francisco letter-spacing-1`.

```js
import cn from '...'

const Button = (...) => <button className={cn('blue -text-style-emphasis ...')}>
  ...
</button>

const Tab = (...) => <a className={cn('gray -text-style-emphasis ...')}>
  ...
</a>

const Heading = (...) => <h2 className={cn('black font-h1 -text-style-emphasis ...')}>
  ...
</h2>
```

#### The `cn` function and virtual classes

The `cn` function, which you can implement quickly, will convert those virtual classes to functional classes.

```js
// cn.js
const virtualToFunctional = {
  '-text-style-emphasis': 'uppercase font-san-francisco letter-spacing-1'
  // More virtual class -> functional class mapping here
}

function convertVirtualClassnames (classNames) {
  return classNames.split(' ').map((className) => (
    // If mapping exists, use that. Else leave it as is
    virtualToFunctional[className] || className
  )).join(' ')
}

export default convertVirtualClassnames
```

You can implement a version which supports **recursive** converting. Also instead of replacing virtual classes with functional classes, it might be useful to **keep** the virtual classes and **add** functional classes, for easier debugging from your browser. This is up to you.

```js
// cn.js
const virtualToFunctional = {
  '-virtual-class-a': '...',
  // One virtual class can reference another virtual class
  '-virtual-class-b': '... -virtual-class-a'
}

function convertVirtualClassnames (classNames) {
  return classNames.split(' ').map((className) => (
    // Recursively convert.
    // Also return both functional and virtual classes
    virtualToFunctional[className]
    ? `${convertVirtualClassnames(virtualToFunctional[className])} ${className}`
    : className
  )).join(' ')
}

export default convertVirtualClassnames
```

By making hyphen (`-`)  the first character and leaving virtual classes when converting them on `cn`, you can easily tell which classes are virtual and which classes are functional from your Chrome/etc DevTools.

#### What problem do virtual classes solve?

**Virtual classes solve the "Find-and-Replace" problem** which I illustrated earlier.

![](https://cloud.githubusercontent.com/assets/992008/17548450/d222ac40-5ea0-11e6-872e-fac447088760.png)

Instead of finding and replacing a set of utility classes, you can just modify the virtual to functional mapping, or change virtual classes on HTML.

If virtual classes are well documented (which I'll cover next), they can force devs to write consistent styles and prevent a mess like this:

![](https://cloud.githubusercontent.com/assets/992008/17549227/20113d2e-5ea4-11e6-851a-2522f01bf80b.png)

Virtual classes also make HTML slightly easier to read without adding any new CSS code.

One downside of virtual classes is that you'd have to call `cn` every time for `className`, but this is actually not too bad - I'll explain why on the next ProTip.

#### Write a living style guide for virtual classes

**Try to write a living style guide for every virtual class** - if you can't do that, then don't write it. Documentation is crucial, because that's how those virtual classes are going to be reused.

Let's say that you write virtual classes called `-row-with-gutter-1` and `-col-with-gutter-1`, which can be used for columns with gutters. And they get converted to Basscss classes. These might not be the best use of virtual classes, but let's ignore that for now.

```js
const virtualToFunctional = {
  '-row-with-gutter-1': 'clearfix mxn1'
  '-col-1-2-3-4': 'sm-col sm-col-6 md-col md-col-4 lg-col lg-col-3'
}
```

Then, you'd want to generate a living styleguide like this. I'll show you the code next.

![screenshot 2016-08-10 at 11 49 15 am](https://cloud.githubusercontent.com/assets/992008/17566510/8a04a544-5ef0-11e6-946e-e6056e0afda4.png)

#### The Styleguide component: usage

The above style guide is generated from this `Styleguide` component:

```js
<Styleguide
  title='Grid with Gutters'
  classnames={['-row-with-gutter-1', '-col-with-gutter-1']}
  SampleCode={({ cnProxy, Placeholder }) => (
    <div className={cnProxy('-row-with-gutter-1')}>
      <div className={cnProxy('-col-with-gutter-1 col col-6')}>
        <Placeholder>Column with Gutter 1</Placeholder>
      </div>
      <div className={cnProxy('-col-with-gutter-1 col col-6')}>
        <Placeholder>Column with Gutter 1</Placeholder>
      </div>
    </div>
  )} />
```

`Styleguide` takes three parameters:

- `title` is the section title.
- `classnames` is the list of virtual classes.
- `SampleCode` is a React component definition, which takes `cnProxy` and `Placeholder` as props, and renders code that illustrates a use of these virtual classes. Use `cnProxy` instead of `cn`, and `Placeholder` for placeholder code.

#### The Styleguide component: implementation (first half)

Here's the first half of the code for `Styleguide`:

```js
import cn from '...'

const Styleguide = ({ title, classnames, SampleCode }) =>
  <div>
    <h2>{title}</h2>
    <div className='mb2'>
      <ul>
        {/* For each virtual class name in classnames,
            show what functional classes they're converted to */}
        {
          classnames.map((classname) => (
            <li key={classname}>
              <code>{classname}</code> → <code>{cn(classname)}</code>
            </li>
          ))
        }
      </ul>
    </div>
    <div className='border mb2'>
      {/* Render SampleCode by using cn for cnProxy and
          a simple component to display placeholders */}
      <SampleCode
        cnProxy={cn}
        Placeholder={
          ({ children }) =>
            <div style={{ background: '#eee', borderColor: '#ccc' }} className='p2 border'>
              {children}
            </div>
        } />
    </div>
    ...
  </div>
```

The above code renders this:

![screenshot 2016-08-10 at 12 04 33 pm](https://cloud.githubusercontent.com/assets/992008/17567069/b2a21ed0-5ef2-11e6-8a23-a618beb92ece.png)

#### The Styleguide component: implementation (second half)

The second half of `Styleguide` code looks like this:

```js
import ...
import { renderToStaticMarkup } from 'react-dom/server'
// You need to install this using `npm install`
import { html } from 'js-beautify'

const Styleguide = ({ title, classnames, SampleCode }) =>
  <div>
    ...
    <pre>
      {(() => {
        // First, create a SampleCode component instance.
        // - Instead of using `cn` for `cnProxy`,
        //   make a simple function which appends __cn__ to class names.
        // - Placeholder component just returns an empty div.
        const sampleCode =
          <SampleCode
            cnProxy={x => `__cn__${x}`}
            Placeholder={() => <div />} />

        // Render the component.
        const renderedHtml = renderToStaticMarkup(sampleCode)

        // Indent the rendered result.
        const renderedHtmlIndented = html(renderedHtml, { indent_size: 2 })

        // Then, replace
        //   class="__cn__..."
        // in the rendered string with:
        //   className={cn('...')}
        // and replace
        //   <div></div>
        // in the rendered string with:
        //   ...
        const renderedHtmlCleaned =
          renderedHtmlIndented
            .replace(/class="__cn__([^"]+)"/g, 'className={cn(\'$1\')}')
            .replace(/<div><\/div>/g, '...')

        // Return the rendered string.
        return renderedHtmlCleaned
      })()}
    </pre>
  </div>
```

The above code renders this:

![screenshot 2016-08-10 at 12 26 57 pm](https://cloud.githubusercontent.com/assets/992008/17567814/c7eeb9d0-5ef5-11e6-98d2-2a770c53f0d4.png)

Note: if you want to enable syntax highlighting, you can try using [Prism](https://github.com/tomchentw/react-prism), which supports JSX well.

#### ProTip 1 Conclusion

- Use virtual classes, which gets converted to functional class names on render.
- Write a mapping from virtual classes to functional classes, and a `cn` helper which does the translation. Then call `cn` for `className`.
- They solve the "Find-and-Replace" problem which can happen often when writing functional CSS.
- Create a component for generating a style guide for virtual classes.

### :v: ProTip 2: Use CSS Modules and call them from `cn` :v:

You'd still have to write CSS if it doesn't make sense to use functional styles. Example I wrote earlier:

> If some component *must* have a height of 178px and be absolutely positioned from the bottom at 12px, then you probably **don't** want to make a new class called `height-178` and `bottom-12`.

React offers many ways to write CSS. After trying all of them, **I decided that [CSS modules](https://github.com/css-modules/css-modules) is the way to go**.

#### What are CSS modules?

If you don't know what CSS modules are, here's an excellent introductory post: [What are CSS Modules and why do we need them?](https://css-tricks.com/css-modules-part-1-need/)

CSS modules solve one problem: **prevents global CSS styles from colliding.** You can write CSS like this:

```css
/* header.css */
.headerImage {
  background-image: url(...);
  ...
}
```

Then, **load that CSS file in JS into a variable on a React component definition**

```js
// Header.js
import styles from './header.css'

const Header = () => {
  ...
  <div className={styles.headerImage}>
    ...
  </div>
  ...
}
```

And it generates a unique class name and a corresponding style declaration when React is rendered:

```html
<style>
  ._styles__headerImage_309571057 {
    background-image: url(...);
    ...
  }
</style>

...

<div class='styles__headerImage_309571057'>
  ...
</div>
```

By using CSS classes, you can name CSS classes whatever you want and don't need to worry about them colliding. Also, because JS files load CSS files, if those JS files are not required, then those CSS won't be loaded, cutting down file size. This is much better than plain-old BEM in my opinion.

Webpack's CSS loader supports CSS modules ([documentation](https://github.com/webpack/css-loader)). CSS modules also work on server-side rendering: use `css-loader/locals` instead of `style-loader!css-loader` on server-side webpack config, and [extract-text-webpack-plugin](https://github.com/webpack/extract-text-webpack-plugin) on client-side webpack config for production.

#### Side note: Alternatives

I spent a good amount of time trying out Khan Academy's [aphrodite](https://github.com/Khan/aphrodite), an inline styles library. It looked great and worked well with server-side rendering, but as of version 0.5.0, there were some issues ([#10](https://github.com/Khan/aphrodite/issues/10) and [#30](https://github.com/Khan/aphrodite/issues/30)) which prevented me from using it.

#### Using CSS Modules with `cn`

Here's how CSS modules are often used in React:

```css
/* header.css */
.headerImage { ... }
```

```js
import styles from './header.css'
...
<div className={styles.headerImage}>
```

This syntax doesn't seem to play nice with virtual CSS function `cn`, where you'd write:

```js
<div className={cn('...')} >
```

You can use string interpolation, but we can do better. Here's an idea: **you can make `cn` function to support CSS modules.**

#### New API for `cn`:

Here's the new API for `cn`. Instead of just importing it:

```js
// Old way
import cn from '...'
```

You first import `makeCn`, and then invoke it to get `cn`:

```js
// New way
import makeCn from '...'
const cn = makeCn(...)
```

The argument for `makeCn` is an object, where **the values are CSS module object and the keys are "prefixes" to use that CSS module object**.

For instance, let's say you have a CSS module like this:

```css
/* header.css */
.headerImage { ... }
```

Then you'd write `makeCn` like this:

```js
import styles from './header.css'
import makeCn from '...'

// Assign "_" as a prefix to use styles from header.css
const cn = makeCn({ _: styles })
```

Then, every class that has an underscore as a prefix will use the equivalent style (without the underscore) from `header.css`:

```js
{/* By using _headerImage inside cn,
    it applies CSS rules from .headerImage {} */}
<div className={cn('_headerImage -some-virtual-class some-functional-class')} />
```

You can use multiple CSS modules too:

```css
/* header.css */
.headerImage { ... }

/* footer.css */
.footerImage { ... }
```

```js
import headerStyles from './header.css'
import footerStyles from './footer.css'
import makeCn from '...'

// Assign "h_" as prefixes for styles from header.css,
// and "f_" as prefixes for styles from footer.css
const cn = makeCn({
  h_: headerStyles,
  f_: footerStyles
})

...

{/* Header Component */}
<div className={cn('h_headerImage ...')} />

{/* Footer Component */}
<div className={cn('f_headerImage ...')} />
```

So you can use `cn` for both virtual classes and CSS modules. Awesome! What's the implementation like for `makeCn`, then?

#### Implementing `makeCn`

Again, here's the API we'd like to implement:

```js
const cn = makeCn({
  h_: headerStyles,
  f_: footerStyles
})

cn('h_headerImage other-classes')

// is equivalent to...

cn(`${headerStyles['headerImage']} other-classes`)
```

First, write a function called `convertCssModuleClassnames`, which takes `prefixToCssmodules`, which is like:

```js
{
  h_: headerStyles,
  f_: footerStyles
}
```

and return a function which takes the argument for `cn` (which is a string containing class names) and returns new class names.

```js
const convertCssModuleClassnames = (prefixToCssmodules) => (classnames) => (
  // Only proceed if prefixToCssmodules is not empty.
  // Otherwise, just return classnames.
  prefixToCssmodules
  ? // For each class name...
  classnames.split(' ').map((classname) => {
    let convertedClassname

    // For each prefixes (like "h_" or "f_")...
    Object.keys(prefixToCssmodules).forEach((prefix) => {
      // Test to see if prefix matches the class name
      const regexp = new RegExp(`^${prefix}`)
      if (regexp.test(classname)) {
        // If it matches, get the CSS module object
        // by calling prefixToCssmodules[prefix],
        // then look up the classname (without the prefix)
        convertedClassname = prefixToCssmodules[prefix][classname.replace(regexp, '')]
      }
    })

    // If successfully converted, return both the CSS module version
    // and the pre-conversion version (for in-browser debugging).
    return `${convertedClassname} ${classname}` || classname
  }).join(' ')
  : classnames
)
```

Then, you can combine it with `convertVirtualClassnames` before you export:

```js
const makeCn = (prefixToCssmodules) => (classnames) => (
  // First run convertVirtualClassnames, then pass the result to convertCssModuleClassnames
  convertCssModuleClassnames(prefixToCssmodules)(convertVirtualClassnames(classnames))
)

export default makeCn
```

#### Alternatives

There's an excellent library called [`react-css-modules`](https://github.com/gajus/react-css-modules/) which solves the same problem. I tried using it, but as of version 3.7.10, it doesn't work well with loops and components that use `children` prop. Also, I wasn't a fan of extending React's API (adding `styleNames` attribute) and decorating every single component just for this purpose. I think `makeCn` is a much lighter-weight solution, but you should still check it out.

#### ProTip 2 Conclusion

- When functional classes don't cut it, write CSS using CSS modules.
- CSS modules solve the global namespace collision problem.
- You can rewrite `cn` to play nice with CSS modules.

### :v: ProTip 3: Make `cn` more powerful by integrating `classnames` library :v:

There's one more thing: Jed Watson wrote a popular library called [`classnames`](https://github.com/JedWatson/classnames), a utility for joining class names together. Here's an example:

```js
// Install using `npm install`
import classNames from 'classnames'

classNames('foo', 'bar'); // => 'foo bar'
classNames('foo', { bar: true }); // => 'foo bar'
classNames({ 'foo-bar': true }); // => 'foo-bar'
classNames({ 'foo-bar': false }); // => ''
classNames({ foo: true }, { bar: true }); // => 'foo bar'
classNames({ foo: true, bar: true }); // => 'foo bar'
classNames('foo', { bar: true, duck: false }, 'baz', { quux: true }); // => 'foo bar baz quux'
```

This is very powerful when combined with functional CSS. **If you're using `cn` to use virtual classes and CSS modules, I suggest integrating `classnames` to it**.

#### Easy Integration

Just modify `makeCn` like this:

```js
import classNames from 'classnames'

const makeCn = (prefixToCssmodules) => (...args) => (
  // First run classNames, then run convertVirtualClassnames,
  // then pass the result to convertCssModuleClassnames
  convertCssModuleClassnames(prefixToCssmodules)(convertVirtualClassnames(classNames(...args)))
)
```

Then you can do something like this:

```js
import style from './header.css'
import makeCn from '...'
const cn = makeCn({_: style})

...

{/* cn now acts like classNames,
    but it now supports virtual classes (-dark-header-text)
    and CSS module classes (_beta-header-image) */}
<div className={cn('bold p2', {
  '-dark-header-text': isDark,
  '_beta-header-image': isBeta
})}>
```

This is powerful - you get the best of everything.

---

## :sunglasses: Epilogue :sunglasses:

Functional CSS is powerful, but with React, you can make it even more so. Try using virtual classes and CSS modules, encapsulated under the simple `cn` function. Your mileage may vary, but I'm enjoying it so far.

Opening issues and pull requests are highly appreciated!

### Author

**Shu Uesugi** (Twitter: [@chibicode](http://twitter.com/chibicode) / Email: [shu@chibicode.com](mailto:shu@chibicode.com))

Like / Retweet appreciated!

[![screenshot 2016-08-10 at 4 06 54 pm](https://cloud.githubusercontent.com/assets/992008/17574250/7bd3c9fe-5f14-11e6-8c45-580f2c1b7734.png)
](https://twitter.com/chibicode/status/763504990736388096)

### Appendix

Some miscellaneous links I found helpful:

- [How is tachyons different from inline styles?](https://github.com/tachyons-css/tachyons/issues/12)
- [immutable-css](https://github.com/johnotander/immutable-css) by John Otander
