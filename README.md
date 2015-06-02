# Bootstrap practices

:construction: This document lists down the things I've learned when working on Bootstrap projects.

<br>

## Including Bootstrap

* Avoid the Less version of Bootstrap. Use the officially-supported Sass version. Be aware that the next version will move to Sass [eventually](https://twitter.com/mdo/status/591364406816079873?lang=en).
  <a name='use-css' href='#use-css'>#</a>

  ```rb
  gem 'bootstrap-sass'            # Rails
  bower install bootstrap-sass    # elsewhere
  ```
  
* Don't use the [Bootstrap CDN]. Any site you build to last will eventually need to grow up to extend Bootstrap. The only legitimate use of this is for quick demos and jsfiddle. 
  <a name='cdn' href='#cdn'>#</a>

<br>

## Components

* Your objective is to eventually graduate from stock components into new ones.
  <a name='graduate' href='#graduate'>#</a>

  ```scss
  // ✗ Avoid (again, try not to restyle stock components)
  .jumbotron {
    background: url('....');
  }
  
  // ✓ OK
  .new-jumbotron {
    background: url('....');
  }
  ```
  
* Think twice restyling the stock components, especially complicated ones (like navbars and tables).  When in doubt, don't. The stock components account for a lot of corner cases, and working around them is more effort than its worth.
  <a name='restyling-stock-components' href='#restyling-stock-components'>#</a>

  ```scss
  // ✗ Avoid. You don't want to do this.
  .nav-bar {
    background: $gray;
    ...
  }
  ```

<br>

## CSS

* Avoid restyling bare elements like h2, h3, and so on. If you need control on the styles to display documents (like Markdown descriptions), create a prefixed component. This will prevent creeping of your own rules into components that rely on these elements to have default styling.
  <a name='restyling-bare-elements' href='#restyling-bare-elements'>#</a>


  ```scss
  // ✗ Avoid
  h2 { margin-top: 3em; border-bottom: solid 1px #ddd; }
  
  // ✓ Better
  .formatted-content {
    h2 { margin-top: 3em; border-bottom: solid 1px #ddd; }
  }
  ```
  
* You can restyle the following tags: `pre`, `code`, `blockquote`, `kbd`, `mark`.

* <a name='naming-conventions' href='#naming-conventions'>#</a>
  When creating new components, there's no reason to stick to Bootstrap's naming convention. Consider [rscss] or [BEM] instead.

  ```scss
  // ✗ No need to follow bootstrap conventions like .panel-body (elements) or .btn-primary (modifiers)
  .eventlist { ... }
  .eventlist-expanded { ... }
  .eventlist-heading { ... }
  .eventlist-item { ... }
  
  // ✓ Better: rscss conventions are cleaner
  .event-list {
    &.-expanded { ... }
    .heading { ... }
    .item { ...}
  }
  ```
  
* <a name='be-selective' href='#be-selective'>#</a>
  Try not to use everything in Bootstrap. Take [bootstrap.scss](https://github.com/twbs/bootstrap-sass/blob/master/assets/stylesheets/_bootstrap.scss) and start with the bare minimum your project will need. Comment out everything else.

  ```scss
  // Core variables and mixins
  @import "bootstrap/variables";
  @import "bootstrap/mixins";
  
  // Reset and dependencies
  @import "bootstrap/normalize";
  @import "bootstrap/print";
  // @import "bootstrap/glyphicons";
  
  // Core CSS
  @import "bootstrap/scaffolding";
  @import "bootstrap/type";
  @import "bootstrap/code";
  @import "bootstrap/grid";
  @import "bootstrap/tables";
  @import "bootstrap/forms";
  @import "bootstrap/buttons";
  
  // (...snip...)
  
  // Utility classes
  @import "bootstrap/utilities";
  @import "bootstrap/responsive-utilities";
  ```
  
* Use Bootstrap variables for media queries. This will make your breakpoints consistent with where the Bootstrap columns will break.

  ```css
  @media (min-width: $screen-md-min) {
  }
  ```
    
* Always override the following variables. Refer to Bootstrap's [variables.scss](https://github.com/twbs/bootstrap-sass/blob/master/assets/stylesheets/bootstrap/_variables.scss) for things you can (should reconfigure). (Reference: [colors](http://getbootstrap.com/css/#less-variables-colors), [typography](http://getbootstrap.com/css/#less-variables-typography))

  ```scss
  // fonts
  $font-family-sans-serif:  "Helvetica Neue", Helvetica, Arial, sans-serif !default;
  $font-size-base:          14px;
  $line-height-base:        1.42857;
  $headings-font-weight:    500;
  
  // colors
  $brand-primary:         #337ab7; // your main color
  $brand-success:         #5cb85c; // green
  $brand-info:            #5bc0de; // blue
  $brand-warning:         #f0ad4e; // yellow
  $brand-danger:          #d9534f; // red
  
  $gray-light:            #777; // muted text
  $gray-lighter:          #ddd; // lines
  ```

*  Avoid using the following classnames when making new components. They are defined as top-level classes in Bootstrap and may produce conflicts. This is a partial list of the most common ones, but [here is a full list.](Symbols.md)

   > .alert [*](http://getbootstrap.com/components/#alerts),
   > .breadcrumb [*](http://getbootstrap.com/components/#breadcrumbs),
   > .close [*](http://getbootstrap.com/css/#helper-classes-close),
   > .label [*](http://getbootstrap.com/components/#labels),
   > .mark,
   > .open,
   > .small,
   > .badge [*](http://getbootstrap.com/components/#badges)
   
* Use [Autoprefixer] instead of Bootstrap's CSS3 mixins. Autoprefixer has been endorsed officially as of Bootstrap 3.3. ([Reference](http://getbootstrap.com/css/#less-mixins-vendor))

  ```scss
  // ✗ Avoid
  .my-component {
    @include box-shadow(0 1px 4px black);
  }
  
  // ✓ OK
  .my-component {
    box-shadow: 0 1px 4px black;
  }
  ```
  

<br>

## Markup

* Avoid using helper classes like `.pull-left` or `.text-right`. Consider the use of these as code smell anti-patterns: anytime they appear in your code, it's a wise time to evaluate if they should be refactored into new components instead.

  ```html
  <!-- ✗ Bad: too much helper classes -->
  <div class='profile-card clearfix'>
    <div class='info pull-left'>
      <h2 class='name text-center'>John Smith</h2>
      <p class='occupation text-center text-uppercase small'>The doctor</p>
    </div>
    <div class='avatar pull-right'>
      <img src='avatar.jpg' alt='Picture'>
    </div>
  </div>
  ```
  
  ```scss
  /* ✓ Better */
  .profile-card {
    .& { @include clearfix; }
    .info { float: left; }
    .avatar { float: right; }
    .name { text-align: center; }
    .occupation { text-align: center; font-size: 0.8em; }
  }
  ```
  
<br>

## Grids

* Use column mixins when necessary. This will free your markup from unsemantic grid classes. ([Reference](http://getbootstrap.com/css/#grid-less))

  ```scss
  .dashboard-layout {
    & {
      @include make-row();
    }
    > .content {
      @include make-lg-column(8);
    }
    > .sidebar {
      @include make-lg-column(3);
      @include make-lg-column-offset(1);
    }
  }
  ```

* Grid classes are okay to use in your markup. However, if they appear too often, consider making them a CSS component instead. ([Reference](http://getbootstrap.com/css/#grid-less))

  > Grid CSS classes include:
  > .container
  > .container-fluid
  > .row
  > .col-sm-6

  ```scss
  .field-row {
    & {
      @include make-row();
    }
    > .label {
      @include make-lg-column(4);
    }
    > .control {
      @include make-lg-column(8);
    }
  }
  ```
  
<br>

## Icons

* Don't use Glyphicons. They're too bland of a default, so chances are you designer wouldn't wanna use them anyway. If they do, fire them.

  ```scss
  // @import "bootstrap/glyphicons";
  ```
  
* If you do end up using Glyphicons, be aware that you are required to give attribution. ([Reference](http://getbootstrap.com/components/#glyphicons-glyphs))

  ```html
  Icons by <a href='http://glyphicons.com'>Glyphicons</a>.
  ```
  
<br>

## Forms

* TBD
  
[rscss]: https://github.com/rstacruz/rscss
[BEM]: http://bem.info/
[Autoprefixer]: https://github.com/postcss/autoprefixer
[Bootstrap CDN]: http://www.bootstrapcdn.com/