# Functional CSS - The Good, The Bad, and Some Protips for React.js Users

![](https://cloud.githubusercontent.com/assets/992008/17532105/d52e78fa-5e33-11e6-94d2-4b77b801c671.png)

### Table of Contents

This repo is a Three-Act <s>rant</s> essay.

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

  1. Writing CSS in a **functional** (or [atomic](http://acss.io/)/immutable/utility-class) way and enjoy using toolkits like [Basscss](http://basscss.com) or [Tachyons](http://tachyons.io/), and
  2. Writing front-end in **[React.js](https://facebook.github.io/react/)**,

  Then skip to [**Act II**: Some downsides of using functional CSS](#sunglasses-act-ii-some-downsides-of-using-functional-css-sunglasses).

- If you don't know what functional CSS is, or if you know it but aren't sold, continue reading the next section.

- If you don't know React, you should learn it! I've written a [tutorial](http://reactfordesigners.com/labs/reactjs-introduction-for-people-who-know-just-enough-jquery-to-get-by/), which has received [0.3 million pageviews](https://cloud.githubusercontent.com/assets/992008/17539561/f2842d0c-5e63-11e6-9617-0068cbb5de28.png) in 1 year.

---

## :sunglasses: Act I: What is functional CSS and why would I want to use it? :sunglasses:

This example is [taken](http://tachyons.io/components/collections/product-card/index.html) from [Tachyons](http://tachyons.io/), one of the popular functional CSS toolkits. Consider this <s>cat</s> card:

<img width="270" alt="" src="https://cloud.githubusercontent.com/assets/992008/17534567/9ca18760-5e3e-11e6-8609-70ac771a618e.png">

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

- If you use functional CSS, **when you add something new to a page, you'll rarely write any new CSS.** You can build most of what you want by composing these small CSS classes.
- This is a sharp contrast to using traditional "semantic CSS", where you'll add new classes like `shopping-cart__item--selected` **every time** you add something new.
- Because you'll write CSS less, your overall CSS size will be small. That's a win for your users (faster load time), but also a win for developers.
  - Why? Because developers need to **look at existing CSS** and ask themselves: *"Is there any class I can reuse?"* - but this gets harder as the size of CSS grows.

### On *not* writing new CSS

The last point is important. Look at the CSS of a large app you're writing, right now. Does it resemble something like below?

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

Or take a look at [GitHub's CSS](https://gist.github.com/mrmrs/786241f0a5fade0324e2), which contains 300+ CSS rules that just say `display: none`.

For GitHub's credit, their code is likely generated by SCSS, so they didn't *actually* write 300+ `display: none`'s. But as a long time SCSS user, I'd say that there's not much preprocessors can do to prevent us from writing more CSS.

By the way, the code above is actually from an excellent article **[CSS and Scalability](http://mrmrs.io/writing/2016/03/24/scalable-css/)**, written by Adam Morse, the author of Tachyons. He claims (emphasis added):

> If you’re going to build a new component, or change a piece of UI in your app - what do you do? I don’t know anyone that reads all the available css in the app to see if there is something they can reuse. **Who has time to read thousands of lines of code before they start working!?** Even if people do have the time, I have not found that this is their first instinct on how to get going. I don’t blame them.

> **Even if you did read all of the available css and stumbled upon some code that you think might be reusable - what if someone edits it later? If you are starting from the assumption that your css isn't reusable, your first instinct is to write new css. But most likely you aren't creating a new classification of visual styles.** In my experience it is likely that you are replicating visual styles that already exist.

> ... Outside of some brand specific background-images, gradients, and colors etc., **the overwhelming majority of css you would need for your site has already been written.**
>
> ... **The real way to scale css, is to stop writing css.** Abstract out the things you use most and move to a multi-class pattern where you compose visual patterns in your html. You might be amazed at how quickly your team starts to move.

I strongly encourage you to [read all of Adam's words](http://mrmrs.io/writing/2016/03/24/scalable-css/). But I know you probably won't. Fine. I'll copy-paste some more:

> There are a number of suggested 'best practices' around how sass/less/stylus etc. will help you build maintainable css. How mixins can make your code more DRY. How extends will keep your markup clean and pretty to look at. **How BEM will make your code so perfect you want to cry.**

> **But what is reality?** What actually gets shipped to production? What do all of these tips and tricks do to our production css? How does it affect the whole team? More important, how are your end users affected?

> **When it comes to designing and shipping products I like to think about reality.** Which can be challenging sometimes... But, if you don't accurately assess where you currently are and what your reality is, it can be pretty tough to figure out what your problems are.

Ok, I'm satisfied. Let's move on.

### So you'll write less CSS, but isn't writing HTML harder now?

When you use these small classes, it'll be harder to write HTML code **IF** you don't know which classes to use. But here's the key part: popular functional CSS toolkits like [Basscss](http://basscss.com/) and [Tachyons](http://tachyons.io/) are **extremely well documented**. Just take a look at their documentation:

- [Basscss Docs](http://www.basscss.com/)
- [Tachyons Docs](http://tachyons.io/docs/)

And both Basscss and Tachyons have illustrative examples of building larger components by combining small classes. Here's how you build a panel using Basscss classes, according to their official docs.

![](https://cloud.githubusercontent.com/assets/992008/17543262/2e5cd03e-5e84-11e6-86bb-7e65f56b0d2c.png)

Once you familiarize yourself with ~50% of the feature set, you'll notice yourself shipping things **really fast**.

As for documentation, the flip side is also true. If you roll your own functional CSS library (or add tons of new classes to extend Basscss/Tachyons), but **don't document them**, that's a recipe for a nightmare. In other words, **documentation (of small CSS classes) is what makes functional CSS work.**

### What about code reuse for HTML?

Let's return to the Tachyons cat example:

<img width="270" alt="" src="https://cloud.githubusercontent.com/assets/992008/17534567/9ca18760-5e3e-11e6-8609-70ac771a618e.png">

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

Suppose that somewhere else on your app, you'll need to have a card similar to above, but the card features a dog instead. You'll need to:

- Copy-paste above code
- Replace the photo, name, price, and description
- Keep all of the classes.

That sounds brittle and not very DRY.

Instead, you should be using some sort of **HTML templates**. Rails has partials and Django has templates for this purpose. In React or Angular, you can create a component. Here's a React example:

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

And **you'd reuse the component instead of reusing HTML directly**:

```js
<ItemCard name='Cat' price='$1,000' image='...' description='...' alt='...' />
<ItemCard name='Dog' price='$2,000' image='...' description='...' alt='...' />
```

In other words, **template/component system (for HTML markup) is what makes functional CSS great.**

### But those CSS classes still look too cryptic to me.

Then try [Basscss](http://www.basscss.com/). Its classes are a bit more readable than Tachyons's.

```html
<div class="inline">inline</div>
<div class="inline-block">inline-block</div>
<a href="#" class="block">block</a>
<div class="table">
  <div class="table-cell">table-cell</div>
  <div class="table-cell">table-cell</div>
</div>
<div class="clearfix">
  <div class="left">float left</div>
  <div class="right">float right</div>
</div>
```

### Ok fine, I'm sold.

Great! Here are some more articles on functional CSS:

- [Building and shipping functional CSS](https://blog.colepeters.com/building-and-shipping-functional-css/) by Cole Peters
- [Rationalizing Functional CSS](https://marcelosomers.com/writing/rationalizing-functional-css/) by Marcelo Somers
- [Functional Programming, CSS, and your sanity](http://www.jon.gold/2015/07/functional-css/) by Jon Gold

And here are some real-world styleguides that use functional CSS:

- [Solid, Buzzfeed's Functional CSS Style Guide](http://solid.buzzfeed.com/) ([Blog Post](https://medium.com/buzzfeed-design/introducing-solid-1c16b1bf4868))
- [Marvel's Functional CSS Style Guide](https://marvelapp.com/styleguide/) ([Blog Post](https://blog.marvelapp.com/the-marvel-styleguide/))

### Do you use [Basscss](http://basscss.com/) or [Tachyons](http://tachyons.io/)? How do I customize the built-in styles?

I use both. I encourage you to learn [Basscss](http://basscss.com/) first because its class names tend to be easier to understand. But I like how [Tachyons](http://tachyons.io/) have first-class responsive design support for all the built-in classes, [like this](http://tachyons.io/docs/typography/text-align/):

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

So if you want to center align something on a mobile screen, but use left align on bigger screens, you'd write:

```html
<span class='tc tl-ns'>...</span>
```

As of this writing, [responsive utilities are available on Basscss as addons](https://github.com/basscss/addons).

#### Notes on Customizing

The new CSS variables used in both Basscss and Tachyons (like `var(--name)` or `@custom-media`) can be customized by using [PostCSS](https://github.com/postcss/postcss) with [postcss-cssnext](https://github.com/MoOx/postcss-cssnext) plugin. If you're using [Webpack](https://webpack.github.io/) to load your React code, use [postcss-loader](https://github.com/postcss/postcss-loader) with [postcss-import](https://github.com/postcss/postcss-loader#integration-with-postcss-import).

You can learn more about the new CSS features [here](http://cssnext.io/features/) and PostCSS [here](http://cssnext.io/postcss/).

Also, sometimes Basscss and Tachyons don't allow customizations via CSS variables:

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

Finally, I **don't** recommend writing a functional CSS library of your own **unless** you're willing to document most of the classes extensively.

Similarly, if you're going to **extend** (not customize) Basscss/Tachyons by adding more small classes, document the ones you add, because otherwise you'll end up adding multiple functional classes that do the same thing.

---

## :sunglasses: Act II: Some downsides of using functional CSS :sunglasses:

Functional CSS isn't perfect, however. Consider a design where, buttons, tabs, and headings all have text styles that look like this:

![](https://cloud.githubusercontent.com/assets/992008/17548431/c116cba2-5ea0-11e6-90fa-f4b517bef37f.png)

(1) They're in `uppercase`, (2) use San Francisco font as `font-family`, and (3) have some `letter-spacing`. In functional CSS, you'd write:

```html
<span class='uppercase font-san-francisco letter-spacing-1 ...'>...</span>
```

And buttons, tabs, and headings are each likely to be written as a component, like so:

```js
// Button.js - button tag, blue font
const Button = (...) => <button className='blue uppercase font-san-francisco letter-spacing-1 ...'>
  ...
</button>

// Tab.js - a tag, gray font
const Tab = (...) => <a className='gray uppercase font-san-francisco letter-spacing-1 ...'>
  ...
</a>

// Heading.js - h2 tag, black, large font
const Heading = (...) => <h2 className='black font-h1 uppercase font-san-francisco letter-spacing-1 ...'>
  ...
</h2>
```

Now, suppose that a designer comes and says that (1) `font-family` should now be Futura, (2) the text should be in regular case and (3) have no `letter-spacing`.

![](https://cloud.githubusercontent.com/assets/992008/17548450/d222ac40-5ea0-11e6-872e-fac447088760.png)

This will require you to replace `uppercase font-san-francisco letter-spacing-1` with `font-futura` in each of Button.js, Tab.js, and Heading.js.

### The "Find-and-Replace" Problem

The above **"Find-and-Replace"** operation would be easy **IF** each file uses the same ordering of classes:

```js
// Must be in this exact order for Button.js, Tab.js and Heading.js
className='... uppercase font-san-francisco letter-spacing-1'
```
But that leaves a lot of room for error. And this is only changing the same 3 classes in 3 different files. What if the codebase is much bigger? What if we have to do more complex Find-and-Replace?

To me, this **"Find-and-Replace"** problem, or batch-modifying similar styles across **multiple** components (templates) is one of the challenging things when using functional CSS **in practice**.

[Marcelo Somers writes](https://marcelosomers.com/writing/rationalizing-functional-css/): (emphasis added):

> It's wonderful the first time you create a component, but updating styles in an existing system can be a complicated mess of find and replace and other keyboard shortcuts.
>
> ... **[Using find and replace] presumes you created multiple of the same "components" by putting the utility classes in the same order, especially when doing find and replace across multiple pages.** Otherwise, there is no easy way to search across your entire application to find where similar components were implemented.
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

But again, you might forget to add this fake class, and then you'd be screwed.

### Styles might diverge, and fixing them can be challenging

If Find-and-Replace fails, your page might end up in a state like this:

![](https://cloud.githubusercontent.com/assets/992008/17549227/20113d2e-5ea4-11e6-851a-2522f01bf80b.png)

Fixing this can be painful, because now you'll need to modify different classes on each file. On Button.js, you need to change `font-san-francisco` to `font-futura`. On Tab.js, you need to remove `uppercase`. On Heading.js, you need to remove `letter-spacing-1`.

That's not too bad with if you can visually examine each component and figure out what's wrong. But there's no easy way to point that out by just looking at the code. And remember: we're dealing with just 3 classes across 3 files.

Moreover, with functional CSS, **you'll likely end up in a state like above when you're writing new components.**

If ideas like "semi-important texts (1) are in `uppercase`, (2) use San Francisco as `font-family`, and (3) have some `letter-spacing`." are not represented/documented anywhere, and **a dev has to make a decision** without a designer (which happens **often** in practice), you'll end up in a state like earlier.

Using components/templates to reuse markup will help, but you can't componentize everything. Sometimes different components need to use similar styles.

### You'd still have to write CSS from time to time

If some component *must* have a height of 178px and be absolutely positioned from the bottom at 12px, then you **don't** want to make new classes called `height-178` and `bottom-12`. They aren't reusable and probably won't be documented. This goes against the philosophies of functional CSS.

In this case, you should just use write regular CSS classes which **augment** functional classes:

```js
// some-box is NOT a functional class - it'll look like this:
//
// .some-box {
//   height: 178px;
//   bottom: 12px;
// }
<div class='p2 absolute some-box'>...</div>
```

But again, how should we write regular CSS? Should we just use [BEM](https://css-tricks.com/bem-101/) again? Or write [CSS in JS (inline styles)](https://speakerdeck.com/vjeux/react-css-in-js)?

One of the downsides of using inline styles is that the support for pseudo selectors (e.g. `:hover`) and `@media` queries is poor. Libraries like [Radium](https://github.com/FormidableLabs/radium) are designed to solve this, but Radium had [some critical bugs](https://github.com/FormidableLabs/radium/issues/367) that don't seem to have a good solution.

Or should we try the new kid on the block, [CSS Modules](https://github.com/css-modules/css-modules)?

<a href="https://twitter.com/mxstbr/status/756769255111192576"><img width="482" alt="" src="https://cloud.githubusercontent.com/assets/992008/17598955/bf5f3620-5fb1-11e6-8c5d-eb752a891b20.png"></a>

I think part of a problem is that React offers many different ways to write regular CSS. You should probably pick one and run with it. But which one should you pick?

#### Coming Up Next...

In summary, the main problems I encountered with functional CSS are:

- The "Find-and-Replace" problem, and
- Still having to write regular CSS (which isn't too bad).

In the next act, I'll present my take on solving these problems.

---

## :sunglasses: Act III: Some ProTips on Writing Functional CSS in React :sunglasses:

I spent some time thinking about how these problems can be solved. Here are my initial thoughts. They are pretty experimental and **not yet** battle tested. I'll update this document after I try these experiments on production for several months.

Also note: **these protips are React.js specific.**

### :v: ProTip 1: Use "virtual classes" and document them :v:

**A virtual class is a class which gets converted into a set of functional CSS classes at runtime.** The name is inspired by React's virtual DOM.

Let's recall our previous example, where some texts (1) are in `uppercase`, (2) use San Francisco font as `font-family`, and (3) have some `letter-spacing`.

![](https://cloud.githubusercontent.com/assets/992008/17548431/c116cba2-5ea0-11e6-90fa-f4b517bef37f.png)

Instead of writing `uppercase font-san-francisco letter-spacing-1` like we did before:

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

You'd write a virtual class called `-text-style-emphasis`:

```js
'blue -text-style-emphasis'
'gray -text-style-emphasis'
'black font-h1 -text-style-emphasis'
```

**Virtual classes are prefixed with a hyphen by convention, so it's easier to spot them.**

Then, **use `cn`** (short for "class name") helper function on `className`, which converts `-text-style-emphasis` to `uppercase font-san-francisco letter-spacing-1` at runtime:

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

The `cn` function will convert pre-defined virtual classes to functional classes. Here's a simple implementation:

```js
// cn.js

// A mapping from virtual class -> functional class
const virtualToFunctional = {
  '-text-style-emphasis': 'uppercase font-san-francisco letter-spacing-1'
  // ...
}

function convertVirtualClassnames (classNames) {
  return classNames.split(' ').map((className) => (
    // If mapping exists, use that. Else leave it as is
    virtualToFunctional[className] || className
  )).join(' ')
}

// This becomes "cn"
export default convertVirtualClassnames
```

You can implement a version which supports **recursive** converting, like the one below.

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

Note that the version above leaves original virtual classes untouched rather than removing them, which might be better for debugging from your browser. Because virtual classes are prefixed with a hyphen by convention, you can easily tell which classes are virtual vs functional from your Chrome/etc DevTools.

#### What problem do virtual classes solve?

**Virtual classes solve the "Find-and-Replace" problem** which I illustrated earlier.

![](https://cloud.githubusercontent.com/assets/992008/17548450/d222ac40-5ea0-11e6-872e-fac447088760.png)

Instead of finding and replacing multiple utility classes, you can just modify the virtual to functional mapping, or change virtual classes used on markup.

If virtual classes are well documented (which I'll cover next), they can force developers to build components with consistent styles and avoid a mess like this:

![](https://cloud.githubusercontent.com/assets/992008/17549227/20113d2e-5ea4-11e6-851a-2522f01bf80b.png)

Virtual classes also make HTML slightly easier to read without adding any new CSS code.

One downside of virtual classes is that you'd have to call `cn` every time for `className`, but this is actually not too bad - I'll explain why on the next ProTip.

#### Build a living style guide for virtual classes

**Try to build a living style guide for every virtual class** - if you can't do that, then don't write a virtual class. Documentation is **crucial**, because that's how those virtual classes get reused.

Let's say that you write virtual classes called `-row-with-gutter-1` and `-col-with-gutter-1`, which can be used to build a grid system with gutters. And they get converted to Basscss classes. These might not be the best use of virtual classes, but let's ignore that for now.

```js
const virtualToFunctional = {
  '-row-with-gutter-1': 'clearfix mxn1',
  '-col-with-gutter-1': 'px1'
}
```

Next, you'd want to generate a living styleguide like this. I'll show you the code next.

![](https://cloud.githubusercontent.com/assets/992008/17566510/8a04a544-5ef0-11e6-946e-e6056e0afda4.png)

#### The Styleguide component: Usage

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
- `SampleCode` is a React component definition, which takes `cnProxy` and `Placeholder` as props, and renders some HTML that illustrates a use of these virtual classes. Use `cnProxy` instead of `cn`, and `Placeholder` as placeholders in HTML.

#### The Styleguide component: Implementation (first half)

Here's the first half of the code for `Styleguide`, with comments. This is the simple part.

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

![](https://cloud.githubusercontent.com/assets/992008/17567069/b2a21ed0-5ef2-11e6-8a23-a618beb92ece.png)

#### The Styleguide component: Implementation (second half)

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

![](https://cloud.githubusercontent.com/assets/992008/17567814/c7eeb9d0-5ef5-11e6-98d2-2a770c53f0d4.png)

Note: if you want to enable syntax highlighting like I did, you can use [Prism](https://github.com/tomchentw/react-prism), which supports JSX well.

#### ProTip 1 Conclusion

- Use virtual classes, which gets converted to functional class names on render.
- Write a mapping from virtual classes to functional classes, and implement a `cn` helper which does the translation. Then call `cn` on `className`.
- Virtual classes solve the "Find-and-Replace" problem which can happen often when writing functional CSS.
- Make it easy to generate a living style guide for virtual classes.

### :v: ProTip 2: Use CSS Modules and call them from `cn` :v:

You'd still have to write regular CSS (or inline styles) when functional classes don't cut it. Example I wrote earlier:

> If some component *must* have a height of 178px and be absolutely positioned from the bottom at 12px, then you **don't** want to make new classes called `height-178` and `bottom-12`.

React offers many ways to write regular CSS. After trying many of them, **I concluded that [CSS modules](https://github.com/css-modules/css-modules) are the way to go**.

#### What are CSS modules?

If you don't know what CSS modules are, here's an excellent introductory post: [What are CSS Modules and why do we need them?](https://css-tricks.com/css-modules-part-1-need/)

CSS modules solve one problem: **global namespace collision.** By using CSS Modules, you can write CSS like this:

```css
/* header.css */
.headerImage {
  background-image: url(...);
  ...
}
```

Then, **load that CSS file from JS as an object, whose keys are the class names**:

```js
// Header.js

// Load CSS into JS as a variable - yes, that's possible with CSS modules!
import styles from './header.css'

const Header = () => {
  ...
  {/* Then use class name "headerImage" as a key */}
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

By using CSS modules, you can name CSS classes whatever you want and don't need to worry about them colliding with some other classes that are written in the past or will be written in the future.

Also, because you load CSS files from JS files, if those JS files are not loaded, then the corresponding CSS won't be loaded. This can cut down the CSS size when you use [Webpack's code splitting feature](https://webpack.github.io/docs/code-splitting.html).

These are the reasons why I think CSS modules are much better than plain-old BEM.

**How to use**: Webpack's CSS loader supports CSS modules, so [read the documentation](https://github.com/webpack/css-loader). CSS modules also work on server-side rendering: use `css-loader/locals` instead of `style-loader!css-loader` on server-side webpack config, and [extract-text-webpack-plugin](https://github.com/webpack/extract-text-webpack-plugin) on client-side webpack config for production.

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

You can use string interpolation to mix the two, but we can do better. Here's an idea: **you can make `cn` function support CSS modules.**

#### New API for `cn`:

Here's the new API for `cn`. Instead of just importing it:

```js
// Old way
import cn from '...'
```

You'd first import `makeCn`, and then invoke it once to get `cn` back:

```js
// New way
import makeCn from '...'
const cn = makeCn(...)
```

The argument for `makeCn` is an object, where **the values are CSS module object and the keys are "prefixes" for that CSS module object**.

It'll be clear when you see an example. For instance, let's say you have a CSS module like this:

```css
/* header.css */
.headerImage { ... }
```

Then you'd write `makeCn` like this:

```js
// Import CSS module
import styles from './header.css'
import makeCn from '...'

// Assign "_" as a prefix to use styles from header.css
const cn = makeCn({ _: styles })
```

Then, every class that has an underscore as a prefix will use the equivalent class (without the underscore) written on `header.css`:

```js
{/* By using _headerImage inside cn(...),
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

// Assign "h_" as a prefix for styles from header.css,
//    and "f_" as a prefix for styles from footer.css
const cn = makeCn({
  h_: headerStyles,
  f_: footerStyles
})

...

{/* Header Component
    - use h_XYZ to use XYZ class from header.css */}
<div className={cn('h_headerImage ...')} />

{/* Footer Component
    - use f_XYZ to use XYZ class from footer.css */}
<div className={cn('f_headerImage ...')} />
```

So `cn` can be used to reference virtual classes and CSS modules classes **at the same time**. Note that if you don't have any CSS modules to import, you can just call `makeCn()` without any argument.

What's the implementation like for `makeCn`, then?

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
// prefixToCssmodules argument
{
  h_: headerStyles,
  f_: footerStyles
}
```

and return a function which takes an argument for `cn` (which is a string containing class names) and returns new class names.

```js
const convertCssModuleClassnames = (prefixToCssmodules) => (classNames) => (
  // Only proceed if prefixToCssmodules is not empty/undefined.
  // Otherwise, just return classNames (else case)
  prefixToCssmodules
  ? classNames.split(' ').map((className) => { // For each class name...
    let convertedClassname

    // For each prefix (like "h_" or "f_")...
    Object.keys(prefixToCssmodules).forEach((prefix) => {
      // Test to see if the prefix matches the class name
      const regexp = new RegExp(`^${prefix}`)
      if (regexp.test(className)) {
        // If it matches, get the CSS module object
        // by calling prefixToCssmodules[prefix],
        // then look up the className (without the prefix)
        convertedClassname = prefixToCssmodules[prefix][className.replace(regexp, '')]
      }
    })

    // If successfully converted, return both the CSS module version
    // and the pre-conversion version (for in-browser debugging).
    return `${convertedClassname} ${className}` || className
  }).join(' ')
  : classNames
)
```

Then, you can combine it with `convertVirtualClassnames` before you export:

```js
const makeCn = (prefixToCssmodules) => (classNames) => (
  // First run convertVirtualClassnames, then pass the result to convertCssModuleClassnames
  convertCssModuleClassnames(prefixToCssmodules)(convertVirtualClassnames(classNames))
)

export default makeCn
```

#### Alternatives

There's an excellent library called [`react-css-modules`](https://github.com/gajus/react-css-modules/) which solves the same problem. I tried using it, but as of version 3.7.10, it doesn't work well inside loops and components that use `children` prop. Also, I wasn't a fan of extending React's API (adding `styleNames` attribute). Decorating every single component just for this purpose seemed overkill. I think `makeCn` is a much lighter-weight solution.

#### ProTip 2 Conclusion

- When functional classes don't cut it, write regular CSS using CSS modules.
- CSS modules solve the global namespace collision problem.
- You can rewrite `cn` to play nice with CSS modules.

### :v: ProTip 3: Make `cn` more powerful by integrating the [`classnames`](https://github.com/JedWatson/classnames) package :v:

There's one more thing: Jed Watson wrote a popular library called [`classnames`](https://github.com/JedWatson/classnames), a utility for joining class names together. Here's an example:

```js
// Install using `npm install`
import classnames from 'classnames'

classnames('foo', 'bar'); // => 'foo bar'
classnames('foo', { bar: true }); // => 'foo bar'
classnames({ 'foo-bar': true }); // => 'foo-bar'
classnames({ 'foo-bar': false }); // => ''
classnames({ foo: true }, { bar: true }); // => 'foo bar'
classnames({ foo: true, bar: true }); // => 'foo bar'
classnames('foo', { bar: true, duck: false }, 'baz', { quux: true }); // => 'foo bar baz quux'
```

This is very powerful when combined with functional CSS. **If you're using `cn` to use virtual classes and CSS modules, I suggest integrating `classnames` to it**. Here's how.

#### Easy Integration

Just modify `makeCn` like this:

```js
// Add an underscore so it doesn't collide w/ local variable names
import _classnames from 'classnames'

...

const makeCn = (prefixToCssmodules) => (...args) => (
  // First run classnames, then run convertVirtualClassnames,
  // then pass the result to convertCssModuleClassnames
  convertCssModuleClassnames(prefixToCssmodules)(convertVirtualClassnames(_classnames(...args)))
)
```

Then you can do:

```js
import style from './header.css'
import makeCn from '...'
const cn = makeCn({_: style})

...

{/* cn now acts like Jed Watson's classnames,
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

Functional CSS is awesome, but with React, you can make it even more so. Try using virtual classes and CSS modules, encapsulated under the simple `cn` function. Your mileage may vary, but I'm enjoying the setup so far.

Issues and pull requests are highly appreciated!

### Author

**Shu Uesugi** (Twitter: [@chibicode](http://twitter.com/chibicode) / Email: [shu@chibicode.com](mailto:shu@chibicode.com))

<a href="https://twitter.com/chibicode/status/763504990736388096"><img width="480" alt="" src="https://cloud.githubusercontent.com/assets/992008/17598666/6b295028-5fb0-11e6-80dd-9b9432392b97.png"></a>

### Appendix

Some miscellaneous links I found helpful:

- [How is tachyons different from inline styles?](https://github.com/tachyons-css/tachyons/issues/12)
- [immutable-css](https://github.com/johnotander/immutable-css) by John Otander
- [Atomic CSS Modules](https://medium.com/yplan-eng/atomic-css-modules-cb44d5993b27) by Michele Bertoli
- [Atomic CSS](http://acss.io/) and [atomizer](https://github.com/acss-io/atomizer)
