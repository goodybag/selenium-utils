# Selenium Utils

A place to keep our Selenium Webdriver utility functions. We extend the [WebDriver](http://selenium.googlecode.com/git/docs/api/javascript/module_selenium-webdriver.html) prototype to add functionality to an instance of WebDriver.

## Usage

__Install__

```
npm install selenium-utils
```

__Example__

```javascript
var assert    = require('assert');
var test      = require('selenium-webdriver/testing');
var webdriver = require('selenium-webdriver');

// Extends WebDriver
require('selenium-utils'):

test.describe( 'Some suite', function(){
  it( 'should do something', function( done ){
    var driver = new webdriver.Builder().withCapabilities(
      webdriver.Capabilities.chrome()
    ).build();

    test.after( function(){
      driver.quit();
    });

    // Wait until the page is ready
    driver.waitUntilSelector( '.my-selector', function( error, el ){
      assert.equal( !!error, false );

      done();
    });
  });
});
```

## Docs

So far, all methods are instance methods on the prototype of the WebDriver class. This is just the initial scaffolding of the module and will likely change.

### WebDriver.prototype

#### driver.find( selector, callback )

Finds the elements from the CSS selector using jquery. Callback is passed error and an instance of [WebElement](http://selenium.googlecode.com/git/docs/api/javascript/class_webdriver_WebElement.html) matching the elements.

```
@param  {String}   selector The CSS selector to test
@param  {Function} ( error, element )
```

#### driver.waitUntil( check, options, callback )

Waits until the check function passes. Assumes asynchrony so return your result as the first argument to the callback supplied to `check`

```
@param  {Function} check    Truthiness test
@param  {Object}   options  Options object
@param  {Function} callback callback( error, result ) when complete
```

__Options:__


```
{
  tickInterval:  100   // How often to check
, timeout:       5000  // How long until an error is thrown
}
```

__Example:__


```javascript
// Wait until jquery is ready
driver.waitUntil( function( callback ){
  driver.executeAsyncScript( function( callback ){
   return callback( typeof window.$ === 'function' );
 }).then( callback );
}, function(){
  console.log('Complete!');
});
```

#### driver.waitUntilSelector( selector, options, callback )

Waits until the selector is found. Returns with the element

```
@param  {String}   selector CSS Selector
@param  {Object}   options  Options object (see waitUntil)
@param  {Function} callback callback( error, element )
```

__Options:__


```
{
  tickInterval:  100   // How often to check
, timeout:       5000  // How long until an error is thrown
}
```

__Example:__


```javascript
// Wait the modal comes in
driver.waitUntilSelector( '.my-modal.in', function( error, modal ){
  /* ... */
});
```

## A Note on Promises

The WebDriver/Selenium module extensively uses promises for its async interface. I totally dig promises and I wish everyone used them, but the majority of JavaScript I write has to interop with existing code that's node-style callbacks. This library will often just convert promises to node-style callbacks to make the interface more composable with existing systems.