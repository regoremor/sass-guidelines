
## Architecture

Architecting a CSS project is probably one of the most difficult things you will have to do in a project’s life. Keeping the architecture consistent and meaningful is even harder.

Fortunately, one of the main benefits of using a CSS preprocessor is having the ability to split the codebase over several files without impacting performance (like the `@import` CSS directive would do). Thanks to Sass’s overload of the `@import` directive, it is perfectly safe (and actually recommended) to use as many files as necessary in development, all compiled into a single stylesheet when going to production.

On top of that, I cannot stress enough the need for folders, even on small scale projects. At home, you don’t drop every sheet of paper into the same box. You use folders; one for the house/flat, one for the bank, one for bills, and so on. There is no reason to do otherwise when structuring a CSS project. Split the codebase into meaningful separated folders so it is easy to find stuff later when you have to come back to the code.

There are [a lot of popular architectures](https://www.sitepoint.com/look-different-sass-architectures/) for CSS projects: [OOCSS](https://www.smashingmagazine.com/2011/12/12/an-introduction-to-object-oriented-css-oocss/), [Atomic Design](https://bradfrost.com/blog/post/atomic-web-design/), [Bootstrap](https://getbootstrap.com/)-like, [Foundation](https://get.foundation/)-like… They all have their merits, pros and cons.

I, myself, use an approach that happens to be quite similar to [SMACSS](http://smacss.com/) from [Jonathan Snook](https://snook.ca/), which focuses on keeping things simple and obvious.

<div class="note">
  <p>I have learnt that architecture is most of the time very specific to the project. Feel free to discard completely or adapt the proposed solution so that you deal with a system that suits your needs.</p>
</div>

### Components

There is a major difference between making it *work*, and making it *good*. Again, CSS is quite a messy language <sup>[citation needed]</sup>. The less CSS we have, the merrier. We don’t want to deal with megabytes of CSS code. To keep stylesheets short and efficient&mdash;and this will not be any surprise to you&mdash;it is usually a good idea to think of an interface as a collection of components.

Components can be anything, as long as they:

* do one thing and one thing only;
* are re-usable and re-used across the project;
* are independent.

For instance, a search form should be treated as a component. It should be reusable, at different positions, on different pages, in various situations. It should not depend on its position in the DOM (footer, sidebar, main content…).

Most of any interface can be thought of as little components and I highly recommend you stick to this paradigm. This will not only shorten the amount of CSS needed for the whole project, but also happens to be much easier to maintain than a chaotic mess where everything is flustered.

### Component Structure

Ideally, components should exist in their own Sass partial (within the `components/` folder, as is described in the [7-1 pattern](#the-7-1-pattern)), such as `components/_button.scss`. The styles described in each component file should only be concerned with:

* the style of the component itself;
* the style of the component’s variants, modifiers, and/or states;
* the styles of the component’s descendents (i.e. children), if necessary.

If you want your components to be able to be themed externally (e.g. from a theme inside the `themes/` folder), limit the declarations to only structural styles, such as dimensions (width/height), padding, margins, alignment, etc. Exclude styles such as colors, shadows, font rules, background rules, etc.

A component partial can include component-specific variables, placeholders, and even mixins and functions. Keep in mind, though, that you should avoid referencing (i.e. `@import`-ing) component files from other component files, as this can make your project’s dependency graph an unmaintainable tangled mess.

Here is an example of a button component partial:

{% include snippets/architecture/06/index.html %}

<div class="note">
  <p>Thanks to <a href="https://twitter.com/davidkpiano">David Khourshid</a> for his help and expertise on this section.</p>
</div>

### The 7-1 pattern

Back to architecture, shall we? I usually go with what I call the *7-1 pattern*: 7 folders, 1 file. Basically, you have all your partials stuffed into 7 different folders, and a single file at the root level (usually named `main.scss`) which imports them all to be compiled into a CSS stylesheet.

* `base/`
* `components/`
* `layout/`
* `pages/`
* `themes/`
* `abstracts/`
* `vendors/`

And of course:

* `main.scss`

<div class="note">
  <p>If you are looking to use the 7-1 pattern, there is a <a href="https://github.com/KittyGiraudel/sass-boilerplate">boilerplate</a> ready on GitHub. It should contain everything you need to get started with this architecture.</p>
</div>

{% include images/wallpaper.html %}

Ideally, we can come up with something like this:

{% include snippets/architecture/01/index.html %}

<div class="note">
  <p>Files follow the same naming conventions described above: they are hyphen-delimited.</p>
</div>

#### Base folder

The `base/` folder holds what we might call the boilerplate code for the project. In there, you might find the reset file, some typographic rules, and probably a stylesheet defining some standard styles for commonly used HTML elements (that I like to call `_base.scss`).

* `_base.scss`
* `_reset.scss`
* `_typography.scss`

<div class="note">
  <p>If your project uses <em>a lot</em> of CSS animations, you might consider adding an <code>\_animations.scss</code> file in there containing the <code>@keyframes</code> definitions of all your animations. If you only use them sporadically, let them live along the selectors that use them.</p>
</div>

#### Layout folder

The `layout/` folder contains everything that takes part in laying out the site or application. This folder could have stylesheets for the main parts of the site (header, footer, navigation, sidebar…), the grid system or even CSS styles for all the forms.

* `_grid.scss`
* `_header.scss`
* `_footer.scss`
* `_sidebar.scss`
* `_forms.scss`
* `_navigation.scss`

<div class="note">
  <p>The <code>layout/</code> folder might also be called <code>partials/</code>, depending on what you prefer.</p>
</div>

#### Components folder

For smaller components, there is the `components/` folder. While `layout/` is *macro* (defining the global wireframe), `components/` is more focused on widgets. It contains all kind of specific modules like a slider, a loader, a widget, and basically anything along those lines. There are usually a lot of files in `components/` since the whole site/application should be mostly composed of tiny modules.

* `_media.scss`
* `_carousel.scss`
* `_thumbnails.scss`

<div class="note">
  <p>The <code>components/</code> folder might also be called <code>modules/</code>, depending on what you prefer.</p>
</div>

#### Pages folder

If you have page-specific styles, it is better to put them in a `pages/` folder, in a file named after the page. For instance, it’s not uncommon to have very specific styles for the home page hence the need for a `_home.scss` file in `pages/`.

* `_home.scss`
* `_contact.scss`

<div class="note">
  <p>Depending on your deployment process, these files could be called on their own to avoid merging them with the others in the resulting stylesheet. It is really up to you.</p>
</div>

#### Themes folder

On large sites and applications, it is not unusual to have different themes. There are certainly different ways of dealing with themes but I personally like having them all in a `themes/` folder.

* `_theme.scss`
* `_admin.scss`

<div class="note">
  <p>This is very project-specific and is likely to be non-existent on many projects.</p>
</div>

#### Abstracts folder

The `abstracts/` folder gathers all Sass tools and helpers used across the project. Every global variable, function, mixin and placeholder should be put in here.

The rule of thumb for this folder is that it should not output a single line of CSS when compiled on its own. These are nothing but Sass helpers.

* `_variables.scss`
* `_mixins.scss`
* `_functions.scss`
* `_placeholders.scss`

When working on a very large project with a lot of abstract utilities, it might be interesting to group them by topic rather than type, for instance typography (`_typography.scss`), theming (`_theming.scss`), etc. Each file contains all the related helpers: variables, functions, mixins and placeholders. Doing so can make the code easier to browse and maintain, especially when files are getting very long.

<div class="note">
  <p>The <code>abstracts/</code> folder might also be called <code>utilities/</code> or <code>helpers/</code>, depending on what you prefer.</p>
</div>

#### Vendors folder

And last but not least, most projects will have a `vendors/` folder containing all the CSS files from external libraries and frameworks – Normalize, Bootstrap, jQueryUI, FancyCarouselSliderjQueryPowered, and so on. Putting those aside in the same folder is a good way to say “Hey, this is not from me, not my code, not my responsibility”.

* `_normalize.scss`
* `_bootstrap.scss`
* `_jquery-ui.scss`
* `_select2.scss`

If you have to override a section of any vendor, I recommend you have an 8th folder called `vendors-extensions/` in which you may have files named exactly after the vendors they overwrite.

For instance, `vendors-extensions/_bootstrap.scss` is a file containing all CSS rules intended to re-declare some of Bootstrap’s default CSS. This is to avoid editing the vendor files themselves, which is generally not a good idea.

#### Main file

The main file (usually labelled `main.scss`) should be the only Sass file from the whole code base not to begin with an underscore. This file should not contain anything but `@import` and comments.

Files should be imported according to the folder they live in, one after the other in the following order:

1. `abstracts/`
1. `vendors/`
1. `base/`
1. `layout/`
1. `components/`
1. `pages/`
1. `themes/`

In order to preserve readability, the main file should respect these guidelines:

* one file per `@import`;
* one `@import` per line;
* no new line between two imports from the same folder;
* a new line after the last import from a folder;
* file extensions and leading underscores omitted.

{% include snippets/architecture/02/index.html %}

There is another way of importing partials that I deem valid as well. On the bright side, it makes the file more readable. On the other hand, it makes updating it slightly more painful. Anyway, I’ll let you decide which is best, it does not matter much. For this way of doing, the main file should respect these guidelines:

* one `@import` per folder;
* a linebreak after `@import`;
* each file on its own line;
* a new line after the last import from a folder;
* file extensions and leading underscores omitted.

{% include snippets/architecture/03/index.html %}

### About globbing

In computer programming, glob patterns specify sets of filenames with wildcard characters, such as `*.scss`. To a general extent, globbing means matching a set of files based on an expression instead of a list of filenames. When applied to Sass, it means importing partials into the [main file](#main-file) with a glob pattern rather than by listing them individually. This would lead to a main file looking like this:

{% include snippets/architecture/05/index.html %}

Sass does not support file globbing out of the box because it can be a dangerous feature as CSS is known to be order-dependant. When dynamically importing files (which usually goes in alphabetical order), one does not control the source order anymore, which can lead to hard to debug side-effects.

That being said, in a strict component-based architecture with extra care not to leak any style from one partial to the other, the order should not really matter anymore, which would allow for glob imports. This would make it easier to add or remove partials as carefully updating the main file would no longer be required.

When using Ruby Sass, there is a Ruby gem called [sass-globbing](https://github.com/chriseppstein/sass-globbing) that enables exactly that behavior. If running on node-sass, one can rely either on Node.js, or whatever build tool they use to handle the compilation (Gulp, Grunt, etc.).

### Shame file

There is an interesting concept that has been made popular by [Harry Roberts](https://csswizardry.com), [Dave Rupert](https://daverupert.com) and [Chris Coyier](https://css-tricks.com) that consists of putting all the CSS declarations, hacks and things we are not proud of in a [shame file](https://csswizardry.com/2013/04/shame-css-full-net-interview/). This file, dramatically titled `_shame.scss`, would be imported after any other file, at the very end of the stylesheet.

{% include snippets/architecture/04/index.html %}
