AugurUI: Conventions
====================

Modules
-------
```javascript
  // Module Export Examples:

  // single constant export
  export default const BUY = 'buy';
  // or for a single function
  export default (args) => {
    // do something...
  };
```
Modules with a single export should have that denoted as `default`
If the export is a function, it should be anonymous

Components
----------
```html
<!-- JSX Layout Conventions -->
<!-- Example Component tree (after rendering)-->
<!-- only one main tag in the whole app, currently in app.jsx -->
<main id="app">
<!-- each view has it's own section component -->
  <section id="create-market-view" >
  <!-- each view has reusable components which should be articles
  unless they are specifically a button, list, link, etc.-->
    <article>
      <!-- more reusable components may be inside -->
    </article>
    <!-- ....more reusable components -->
  </section>
</main>
```
```javascript
// Import Conventions

// Import Path Example:
// Good: Using Aliases to import
import ValueDenomination from 'modules/common/components/value-denomination';
import shouldComponentUpdatePure from 'utils/should-component-update-pure';
import { rpc } from 'services/augurjs';

// Bad: Using relative paths to import
import ValueDenomination from '../../common/components/value-denomination';

// Imports Order Example:
// 3rd party modules > services > components/selectors/reducers/actions > constants > utils
// Good:
import React, { Component } from 'react';
import { augur } from 'services/augurjs';
import Input from 'modules/common/components/input';
import { BUY, SELL } from 'modules/trade/constants/types';
import trimString from 'utils/trim-string';

// Bad:
import Input from 'modules/common/components/input';
import React, { Component } from 'react';
import trimString from 'utils/trim-string';
import { augur } from 'services/augurjs';
import { BUY, SELL } from 'modules/trade/constants/types';

// React Conventions:

// PropTypes example:
// Good: Required props come first
static propTypes = {
  url: PropTypes.string.isRequired,
  text: PropTypes.string
};

// Bad: Required props should come first
static propTypes = {
  text: PropTypes.string,
  url: PropTypes.string.isRequired
};

// Null state example / conditional display:
const MyMarkets = p => (
  <article className="my-markets">
    <div className="view-header">
      // some more jsx...
    </div>
    {p.myMarkets && p.myMarkets.length ?
      <div>
        // some more jsx...
      </div> :
      <NullStateMessage message="No Markets Created" />
    }
  </article>
);
```
All components should be semantically tagged, highly reusable, and DRY.

To help ensure this, the following conventions have been employed:

#### General Conventions
- Do leave comments for functionality that may be non-obvious, dependence on other methods, complex mutations/filters, etc.
- Font Awesome characters are used directly in the components -- in order to render these inside your text editors/IDEs, reference the [typography](https://github.com/AugurProject/augur/blob/master/src/modules/app/less/typography.less) stylesheet.

#### JSX layout conventions:
- Only one `main` tag which is currently employed in the [app.jsx](https://github.com/AugurProject/augur/blob/master/src/modules/app/components/app.jsx) component to contain all view content.
- All top-level components (i.e. - views) should be contained within a `section` tag. Though valid, `section` should only be used for view level components. All `section` tags should have an accompanying `id` attribute (excluding unique semantic tags (main, header, footer, etc.)).
- All reusable components should be contained within an `article` tag unless this use would be semantically incorrect; in which case, use whatever tag is appropriate.
- All components should be standard HTML5 elements with their default behaviors intact. (*Note: Due to some implementation constraints, there may be a reason to deviate from this, but it should be dialoged over prior to implementation.*)

#### Import Conventions
- Import paths for `assets`, `modules`, `utils`, and `services` are aliased, so avoid relative paths. <b>Always</b> traverse from the aliases.
- Import requirements in the following Order: 3rd party modules > services > components/selectors/reducers > constants > utils

#### React Conventions
- Props that are being passed to a component should be explicit from both ends
- Prop validations should have required props first, optional props after
- Null component states (ex: 'no markets', 'no tags', etc.) should always come first in the component.
- Conditional display should occur as far down the component tree as possible.


#### Other Component Conventions
Not all conventions are detailed above, but rather just the main points.
For a full review of the breadth of the conventions employed, reference:

- `.eslintrc` - JS linting rules are found [here](https://github.com/AugurProject/augur/blob/master/.eslintrc).
- `.editorconfig` - Editor focused rules to keep coding styles consistent. Can be found [here](https://github.com/AugurProject/augur/blob/master/.editorconfig).
- Optional and primarily for convenience, but the linting enforces many of these conventions

Styles
------
```css
  /* Mixin Component Example (taken from colors.less)*/

  /* constants */
  @color-green: #28e071;
  @color-red: #ff3b34;
  @color-green-light: lighten(@color-green, @amount-light);
  @color-red-muted: lighten(@color-red, @amount-muted);

  /* Mixins */
  .colorize {
    &.positive,
    .positive {
      .value {
        color: @color-green;
      }

      .denomination {
        color: @color-green-light;
      }
    }

    &.negative,
    .negative {
      .value {
        color: @color-red;
      }

      .denomination {
        color: @color-red-muted;
      }
    }
  }

  /* Component Style Conventions: */

  .example-widget-viewer {
    .border(true);
    .font-size-medium();

    align-items: center;
    background-color: @color-white;
    display: flex;
    flex: 1;
    min-width: 22em;
  }
```
All styles should be contextual such that styling rules are only directly applied to the immediately relevant component(s), with the ultimate goal always being consistency and maintainability.

This is seen reflected in the overall structure of the stylesheets -- generally a 1-to-1 between a component and a stylesheet.

To help ensure this, the following conventions have been employed:

#### General Conventions

- The full breadth of Less's functionality is permissible.
- All styles should be contextual and housed within their relevant files (1-to-1 component to stylesheet)
- If you need a less variable to be accessible during runtime, create a rule set for `body` and with identically named custom properties which have their values as the respective less variables.  You can then get these values by calling `getComputedStyle` and `getPropertyValue` on `document.body`.

#### Mixin Conventions

- Use mixins where provided.
- If an identical style is to be applied to multiple elements, that declaration block should probably be abstracted to a mixin. This can be seen in the way (not exhaustive) [typography](https://github.com/AugurProject/augur/blob/master/src/modules/app/less/typography.less) and [borders](https://github.com/AugurProject/augur/blob/master/src/modules/app/less/borders.less) are employed.
 - Some notable mixins include: [animations](https://github.com/AugurProject/augur/blob/master/src/modules/app/less/animations.less), [borders](https://github.com/AugurProject/augur/blob/master/src/modules/app/less/borders.less), [colors](https://github.com/AugurProject/augur/blob/master/src/modules/app/less/colors.less), and [typography](https://github.com/AugurProject/augur/blob/master/src/modules/app/less/typography.less).
- Comments should be employed for mixins to help inform the utilization.

#### Component Style Conventions

- Every component should have the minimum amount of styling required (helps maintain contextualization). This allows for stylesheets of parent components that employ a component to apply any additional 'chrome' required.
- Mixin inclusions should always come first within a declaration block and be alphabetical.
- Classnames should be contextual, non-generic, and should be discrete words delimited by a `-` (dash).
- IDs should be discrete words delimited by an `_` (underscore).
- Flexbox should be utilized for all layout (exceptions would be something like a fixed element, but that should be very rare).
- Avoid the use of `!important` unless absolutely necessary.
- All external borders (parent level components within a view) should be the normal border color. All internal borders should be either muted or light (depending on the application).
- When utilizing `CSSTransitionGroup`, styling of this wrapper should be housed within the rendered child's stylesheet


#### Other Style Conventions
Above are the main points, but additional structural and styling conventions of the stylesheets themselves are enforced through linting.
For a full review of all conventions, reference:

- `.stylelintrc` - Less linting rules can be found [here](https://github.com/AugurProject/augur/blob/master/.stylelintrc).
