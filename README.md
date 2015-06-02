# Bootstrap practices

:construction: This document lists down the things I've learned when working on Bootstrap projects.

## CSS

* Avoid the Less version of bootstrap. Use the Sass version since the next version will migrate to Sass anyway.

* Try not to use everything in Bootstrap. Take [bootstrap.scss](https://github.com/twbs/bootstrap-sass/blob/master/assets/stylesheets/_bootstrap.scss) and start with the bare minimum your project will need. Comment out everything else.

  ```css
  // Core variables and mixins
  @import "bootstrap/variables";
  @import "bootstrap/mixins";
  
  // Reset and dependencies
  @import "bootstrap/normalize";
  @import "bootstrap/print";
  //@import "bootstrap/glyphicons";
  
  // Core CSS
  @import "bootstrap/scaffolding";
  @import "bootstrap/type";
  @import "bootstrap/code";
  @import "bootstrap/grid";
  @import "bootstrap/tables";
  @import "bootstrap/forms";
  @import "bootstrap/buttons";
  
  // Utility classes
  @import "bootstrap/utilities";
  @import "bootstrap/responsive-utilities";
  ```
  
* Don't use glyphicons. They're ugly. Chances are you designer wouldn't wanna use them anyway. If they do, fire them.

  ```css
  // @import "bootstrap/glyphicons";
  ```
  
* Do not restyle the stock components. The stock components account for a lot of corner cases, and working around them is more effort than its worth.

  ```css
  // ✗ Avoid. You don't want to do this.
  .nav-bar {
    background: $gray;
    ...
  }
  ```

* If you're using stock componetns, your objective is to eventually graduate from stock components into new ones.

  ```css
  // ✗ Avoid (again, don't restyle stock components)
  .jumbotron {
    background: url('....');
  }
  
  // ✓ OK
  .new-jumbotron {
  }
  ```
* Avoid restyling un-classed tags like h2, h3, and so on. If you need control on the styles to display documents (like Markdown descriptions), create a prefixed component.

  ```css
  // ✗ Avoid
  h2 { margin-top: 3em; border-bottom: solid 1px #ddd; }
  
  // ✓ Better
  .formatted-content {
    h2 { margin-top: 3em; border-bottom: solid 1px #ddd; }
  }
  ```
  
* You can restyle the following tags: `pre`, `code`, `blockquote`, `abbr`.

* Use Bootstrap variables for media queries. This will make your breakpoints consistent with where the Bootstrap columns will break.

  ```css
  @media (min-width: $screen-md-min) {
  }
  ```
  
* Use column mixins when necessary. ([Reference](http://getbootstrap.com/css/#mixins))

  ```css
  .make-sm-column(4);
  ```
  
* Always override the following variables. Refer to Bootstrap's [variables.scss](https://github.com/twbs/bootstrap-sass/blob/master/assets/stylesheets/bootstrap/_variables.scss) for things you can (should reconfigure).

  ```css
  // fonts
  $font-family-sans-serif:  "Helvetica Neue", Helvetica, Arial, sans-serif !default;
  $font-size-base:          14px;
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

*  Avoid using the following classnames when making new components. They have clashes with Bootstrap. [Here is a full list.](Symbols.md)

> .alert, .breadcrumb, .close, .label, .mark, .open, .small

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
  
  ```css
  /* ✓ Better */
  .profile-card {
    .& { @include clearfix; }
    .info { float: left; }
    .avatar { float: right; }
    .name { text-align: center; }
    .occupation { text-align: center; font-size: 0.8em; }
  }
  ```
