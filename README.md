# ember-percy

[![Package Status](https://img.shields.io/npm/v/ember-percy.svg)](https://www.npmjs.com/package/ember-percy)
[![Build Status](https://travis-ci.org/percy/ember-percy.svg?branch=master)](https://travis-ci.org/percy/ember-percy)

Ember addon for visual regression testing with [Percy](https://percy.io).

## Installation

Requires Node >= 0.12.0.
Requires `ember-cli` >= 1.13.13, preferably >= 2.4.0.

1. `ember install ember-percy`
1. Set the `PERCY_TOKEN` environment variables in your CI settings.
1. Set the `PERCY_PROJECT=org/repo-name` environment variables in your CI settings.
1. Register the percy test helpers for acceptance tests.
  * For apps, add `import '../helpers/percy/register-helpers';` to `module-for-acceptance.js`.
  * For addons, add `import 'dummy/tests/helpers/percy/register-helpers';` in `module-for-acceptance.js`.
1. Add `percySnapshot` to `tests/.jshintrc` in the `predef` section to avoid "percySnapshot is not defined" errors.

## Usage

```javascript
percySnapshot(name);
percySnapshot(name, options);
```

`options` is an optional hash that can include:

* `scope`: A CSS selector or element to snapshot. Defaults to the full page.

Examples:

```javascript
percySnapshot('homepage');
```

```javascript
percySnapshot('homepage', {scope: '#header'});
```

With Mocha tests, you can use `this.test.fullTitle()` to autogenerate the name arg, for example:

```javascript
percySnapshot(this.test.fullTitle());
```

Using Mocha's `this.test.fullTitle()` will return a name that includes all nesting of the current
test, for example: `Acceptance: Marketing pages can visit /about`.

### Responsive visual diffs (optional)

In your app's `config/environment.js`:

```javascript
if (environment === 'test') {
  // ...
  ENV.percy = {
    breakpointsConfig: {
      mobile: 375,
      tablet: 768,
      desktop: 1280
    },
    defaultBreakpoints: ['mobile', 'desktop']
  }
}
```

With the above configuration, all snapshots will render at both the `mobile` and `desktop`
breakpoints by default.

You can override this on a per-snapshot basis by passing the `breakpoints` option to `percySnapshot()`.
For example:

```javascript
// Desktop-only snapshot:
percySnapshot('meter bar full', {breakpoints: ['desktop']});
```

### Acceptance test example

Make sure you have completed the [Installation](#installation) steps above.

```javascript
describe('Acceptance: Marketing pages', function() {
  it('can visit /about', function() {
    visit('/about');
    percySnapshot('about page');
  });
});
```

### Component integration test example

```javascript
import { percySnapshot } from 'ember-percy';

describeComponent(
  'meter-bar',
  'Integration: MeterBarComponent',
  {
    integration: true
  },
  function() {
    it('renders', function() {
      this.set('count', 0);
      this.render(hbs`{{meter-bar count=count total=100}}`);
      percySnapshot('meter bar zero');

      this.set('count', 50);
      percySnapshot('meter bar half');

      this.set('count', 100);
      percySnapshot('meter bar full');
    });
  }
);
```

## Troubleshooting

* If you use `ember-cli-mirage`, network requests that are not mocked will raise an error.
  You will likely need to add `this.passthrough('/_percy/**');` to your mirage routes to whitelist
  Percy's internal requests that are made in tests.

## Contributing

1. Fork it ( https://github.com/percy/ember-percy/fork )
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create a new Pull Request

Throw a ★ on it! :)

### Running Tests

* `npm test` (Runs `ember try:testall` to test your addon against multiple Ember versions)
* `ember test`
* `ember test --server`
