---
layout: doc
title: WebDriver - Codeception - Documentation
---



<div class="btn-group" role="group" style="float: right" aria-label="..."><a class="btn btn-default" href="https://github.com/Codeception/Codeception/blob/2.2/src/Codeception/Module/WebDriver.php">source</a><a class="btn btn-default" href="https://github.com/Codeception/Codeception/blob/master/docs/modules/WebDriver.md">master</a><a class="btn btn-default" href="https://github.com/Codeception/Codeception/blob/2.2/docs/modules/WebDriver.md"><strong>2.2</strong></a><a class="btn btn-default" href="https://github.com/Codeception/Codeception/blob/2.1/docs/modules/WebDriver.md">2.1</a><a class="btn btn-default" href="https://github.com/Codeception/Codeception/blob/2.0/docs/modules/WebDriver.md">2.0</a><a class="btn btn-default" href="https://github.com/Codeception/Codeception/blob/1.8/docs/modules/WebDriver.md">1.8</a></div>

# WebDriver


New generation Selenium WebDriver module.

### Local Testing

#### Selenium

1. Download [Selenium Server](http://docs.seleniumhq.org/download/)
2. Launch the daemon: `java -jar selenium-server-standalone-2.xx.xxx.jar`
3. Configure this module (in acceptance.suite.yml) by setting url and browser:

{% highlight yaml %}

    modules:
       enabled:
          - WebDriver:
             url: 'http://localhost/'
             browser: firefox

{% endhighlight %}

#### PhantomJS

PhantomJS is a headless alternative to Selenium Server that implements
[the WebDriver protocol](https://code.google.com/p/selenium/wiki/JsonWireProtocol).
It allows you to run Selenium tests on a server without a GUI installed.

1. Download [PhantomJS](http://phantomjs.org/download.html)
2. Run PhantomJS in WebDriver mode: `phantomjs --webdriver=4444`
3. Configure this module (in acceptance.suite.yml) by setting url and `phantomjs` as browser:

{% highlight yaml %}

    modules:
       enabled:
          - WebDriver:
             url: 'http://localhost/'
             browser: phantomjs

{% endhighlight %}

### Cloud Testing

Cloud Testing services can run your WebDriver tests in the cloud.
In case you want to test a local site or site behind a firewall
you should use a tunnel application provided by a service.

#### SauceLabs

1. Create an account at [SauceLabs.com](http://SauceLabs.com) to get your username and access key
2. In the module configuration use the format `username`:`access_key`@ondemand.saucelabs.com' for `host`
3. Configure `platform` under `capabilities` to define the [Operating System](https://docs.saucelabs.com/reference/platforms-configurator/#/)
4. run a tunnel app if your site can't be accessed from Internet

{% highlight yaml %}

    modules:
       enabled:
          - WebDriver:
             url: http://mysite.com
             host: '<username>:<access key>@ondemand.saucelabs.com'
             port: 80
             browser: chrome
             capabilities:
                 platform: 'Windows 10'

{% endhighlight %}

#### BrowserStack

1. Create an account at [BrowserStack](https://www.browserstack.com/) to get your username and access key
2. In the module configuration use the format `username`:`access_key`@hub.browserstack.com' for `host`
3. Configure `os` and `os_version` under `capabilities` to define the operating System
4. If your site is available only locally or via VPN you should use a tunnel app. In this case add `browserstack.local` capability and set it to true.

{% highlight yaml %}

    modules:
       enabled:
          - WebDriver:
             url: http://mysite.com
             host: '<username>:<access key>@hub.browserstack.com'
             port: 80
             browser: chrome
             capabilities:
                 os: Windows
                 os_version: 10
                 browserstack.local: true # for local testing

{% endhighlight %}
#### TestingBot

1. Create an account at [TestingBot](https://testingbot.com/) to get your key and secret
2. In the module configuration use the format `key`:`secret`@hub.testingbot.com' for `host`
3. Configure `platform` under `capabilities` to define the [Operating System](https://testingbot.com/support/getting-started/browsers.html)
4. Run [TestingBot Tunnel](https://testingbot.com/support/other/tunnel) if your site can't be accessed from Internet

{% highlight yaml %}

    modules:
       enabled:
          - WebDriver:
             url: http://mysite.com
             host: '<key>:<secret>@hub.testingbot.com'
             port: 80
             browser: chrome
             capabilities:
                 platform: Windows 10

{% endhighlight %}

### Configuration

* `url` *required* - Starting URL for your app.
* `browser` *required* - Browser to launch.
* `host` - Selenium server host (127.0.0.1 by default).
* `port` - Selenium server port (4444 by default).
* `restart` - Set to false (default) to share browser session between tests, or set to true to create a separate session for each test.
* `window_size` - Initial window size. Set to `maximize` or a dimension in the format `640x480`.
* `clear_cookies` - Set to false to keep cookies, or set to true (default) to delete all cookies between tests.
* `wait` - Implicit wait (default 0 seconds).
* `capabilities` - Sets Selenium2 [desired capabilities](https://github.com/SeleniumHQ/selenium/wiki/DesiredCapabilities). Should be a key-value array.
* `connection_timeout` - timeout for opening a connection to remote selenium server (30 seconds by default).
* `request_timeout` - timeout for a request to return something from remote selenium server (30 seconds by default).
* `http_proxy` - sets http proxy server url for testing a remote server.
* `http_proxy_port` - sets http proxy server port
* `debug_log_entries` - how many selenium entries to print with `debugWebDriverLogs` or on fail (15 by default).

Example (`acceptance.suite.yml`)

{% highlight yaml %}

    modules:
       enabled:
          - WebDriver:
             url: 'http://localhost/'
             browser: firefox
             window_size: 1024x768
             capabilities:
                 unexpectedAlertBehaviour: 'accept'
                 firefox_profile: '~/firefox-profiles/codeception-profile.zip.b64'

{% endhighlight %}

#### Status

Stability: **stable**
Based on [facebook php-webdriver](https://github.com/facebook/php-webdriver)

### Usage

#### Locating Elements

Most methods in this module that operate on a DOM element (e.g. `click`) accept a locator as the first argument,
which can be either a string or an array.

If the locator is an array, it should have a single element,
with the key signifying the locator type (`id`, `name`, `css`, `xpath`, `link`, or `class`)
and the value being the locator itself.
This is called a "strict" locator.
Examples:

* `['id' => 'foo']` matches `<div id="foo">`
* `['name' => 'foo']` matches `<div name="foo">`
* `['css' => 'input[type=input][value=foo]']` matches `<input type="input" value="foo">`
* `['xpath' => "//input[@type='submit'][contains(@value, 'foo')]"]` matches `<input type="submit" value="foobar">`
* `['link' => 'Click here']` matches `<a href="google.com">Click here</a>`
* `['class' => 'foo']` matches `<div class="foo">`

Writing good locators can be tricky.
The Mozilla team has written an excellent guide titled [Writing reliable locators for Selenium and WebDriver tests](https://blog.mozilla.org/webqa/2013/09/26/writing-reliable-locators-for-selenium-and-webdriver-tests/).

If you prefer, you may also pass a string for the locator. This is called a "fuzzy" locator.
In this case, Codeception uses a a variety of heuristics (depending on the exact method called) to determine what element you're referring to.
For example, here's the heuristic used for the `submitForm` method:

1. Does the locator look like an ID selector (e.g. "#foo")? If so, try to find a form matching that ID.
2. If nothing found, check if locator looks like a CSS selector. If so, run it.
3. If nothing found, check if locator looks like an XPath expression. If so, run it.
4. Throw an `ElementNotFound` exception.

Be warned that fuzzy locators can be significantly slower than strict locators.
Especially if you use Selenium WebDriver with `wait` (aka implicit wait) option.
In the example above if you set `wait` to 5 seconds and use XPath string as fuzzy locator,
`submitForm` method will wait for 5 seconds at each step.
That means 5 seconds finding the form by ID, another 5 seconds finding by CSS
until it finally tries to find the form by XPath).
If speed is a concern, it's recommended you stick with explicitly specifying the locator type via the array syntax.

### Public Properties

* `webDriver` - instance of `\Facebook\WebDriver\Remote\RemoteWebDriver`. Can be accessed from Helper classes for complex WebDriver interactions.

{% highlight php %}

// inside Helper class
$this->getModule('WebDriver')->webDriver->getKeyboard()->sendKeys('hello, webdriver');

{% endhighlight %}



### Actions

#### _findElements

*hidden API method, expected to be used from Helper classes*
 
Locates element using available Codeception locator types:

* XPath
* CSS
* Strict Locator

Use it in Helpers or GroupObject or Extension classes:

{% highlight php %}

<?php
$els = $this->getModule('WebDriver')->_findElements('.items');
$els = $this->getModule('WebDriver')->_findElements(['name' => 'username']);

$editLinks = $this->getModule('WebDriver')->_findElements(['link' => 'Edit']);
// now you can iterate over $editLinks and check that all them have valid hrefs

{% endhighlight %}

WebDriver module returns `Facebook\WebDriver\Remote\RemoteWebElement` instances
PhpBrowser and Framework modules return `Symfony\Component\DomCrawler\Crawler` instances

 * `param` $locator
 * `return` array of interactive elements


#### _getCurrentUri

*hidden API method, expected to be used from Helper classes*
 
Uri of currently opened page.
 * `return` string
 * `throws`  ModuleException


#### _getUrl

*hidden API method, expected to be used from Helper classes*
 
Returns URL of a host.
 * `throws`  ModuleConfigException


#### _savePageSource

*hidden API method, expected to be used from Helper classes*
 
Saves HTML source of a page to a file
 * `param` $filename


#### _saveScreenshot

*hidden API method, expected to be used from Helper classes*
 
Saves screenshot of current page to a file

{% highlight php %}

$this->getModule('WebDriver')->_saveScreenshot(codecept_output_dir().'screenshot_1.png');

{% endhighlight %}
 * `param` $filename


#### acceptPopup
 
Accepts the active JavaScript native popup window, as created by `window.alert`|`window.confirm`|`window.prompt`.
Don't confuse popups with modal windows,
as created by [various libraries](http://jster.net/category/windows-modals-popups).


#### amOnPage
 
Opens the page for the given relative URI.

{% highlight php %}

<?php
// opens front page
$I->amOnPage('/');
// opens /register page
$I->amOnPage('/register');

{% endhighlight %}

 * `param` $page


#### amOnSubdomain
 
Changes the subdomain for the 'url' configuration parameter.
Does not open a page; use `amOnPage` for that.

{% highlight php %}

<?php
// If config is: 'http://mysite.com'
// or config is: 'http://www.mysite.com'
// or config is: 'http://company.mysite.com'

$I->amOnSubdomain('user');
$I->amOnPage('/');
// moves to http://user.mysite.com/
?>

{% endhighlight %}

 * `param` $subdomain



#### amOnUrl
 
Open web page at the given absolute URL and sets its hostname as the base host.

{% highlight php %}

<?php
$I->amOnUrl('http://codeception.com');
$I->amOnPage('/quickstart'); // moves to http://codeception.com/quickstart
?>

{% endhighlight %}


#### appendField
 
Append the given text to the given element.
Can also add a selection to a select box.

{% highlight php %}

<?php
$I->appendField('#mySelectbox', 'SelectValue');
$I->appendField('#myTextField', 'appended');
?>

{% endhighlight %}

 * `param string` $field
 * `param string` $value
 * `throws`  \Codeception\Exception\ElementNotFound


#### attachFile
 
Attaches a file relative to the Codeception data directory to the given file upload field.

{% highlight php %}

<?php
// file is stored in 'tests/_data/prices.xls'
$I->attachFile('input[ * `type="file"]',`  'prices.xls');
?>

{% endhighlight %}

 * `param` $field
 * `param` $filename


#### cancelPopup
 
Dismisses the active JavaScript popup, as created by `window.alert`|`window.confirm`|`window.prompt`.


#### checkOption
 
Ticks a checkbox. For radio buttons, use the `selectOption` method instead.

{% highlight php %}

<?php
$I->checkOption('#agree');
?>

{% endhighlight %}

 * `param` $option


#### click
 
Perform a click on a link or a button, given by a locator.
If a fuzzy locator is given, the page will be searched for a button, link, or image matching the locator string.
For buttons, the "value" attribute, "name" attribute, and inner text are searched.
For links, the link text is searched.
For images, the "alt" attribute and inner text of any parent links are searched.

The second parameter is a context (CSS or XPath locator) to narrow the search.

Note that if the locator matches a button of type `submit`, the form will be submitted.

{% highlight php %}

<?php
// simple link
$I->click('Logout');
// button of form
$I->click('Submit');
// CSS button
$I->click('#form input[type=submit]');
// XPath
$I->click('//form/*[ * `type=submit]');` 
// link in context
$I->click('Logout', '#nav');
// using strict locator
$I->click(['link' => 'Login']);
?>

{% endhighlight %}

 * `param` $link
 * `param` $context


#### clickWithRightButton
 
Performs contextual click with the right mouse button on an element.

 * `param` $cssOrXPath
 * `throws`  \Codeception\Exception\ElementNotFound


#### debugWebDriverLogs
 
Print out latest Selenium Logs in debug mode


#### dontSee
 
Checks that the current page doesn't contain the text specified (case insensitive).
Give a locator as the second parameter to match a specific region.

{% highlight php %}

<?php
$I->dontSee('Login');                    // I can suppose user is already logged in
$I->dontSee('Sign Up','h1');             // I can suppose it's not a signup page
$I->dontSee('Sign Up','//body/h1');      // with XPath

{% endhighlight %}

Note that the search is done after stripping all HTML tags from the body,
so `$I->dontSee('strong')` will fail on strings like:

  - `<p>I am Stronger than thou</p>`
  - `<script>document.createElement('strong');</script>`

But will ignore strings like:

  - `<strong>Home</strong>`
  - `<div class="strong">Home</strong>`
  - `<!-- strong -->`

For checking the raw source code, use `seeInSource()`.

 * `param`      $text
 * `param null` $selector


#### dontSeeCheckboxIsChecked
 
Check that the specified checkbox is unchecked.

{% highlight php %}

<?php
$I->dontSeeCheckboxIsChecked('#agree'); // I suppose user didn't agree to terms
$I->seeCheckboxIsChecked('#signup_form input[type=checkbox]'); // I suppose user didn't check the first checkbox in form.
?>

{% endhighlight %}

 * `param` $checkbox


#### dontSeeCookie
 
Checks that there isn't a cookie with the given name.
You can set additional cookie params like `domain`, `path` as array passed in last argument.

 * `param` $cookie

 * `param array` $params


#### dontSeeCurrentUrlEquals
 
Checks that the current URL doesn't equal the given string.
Unlike `dontSeeInCurrentUrl`, this only matches the full URL.

{% highlight php %}

<?php
// current url is not root
$I->dontSeeCurrentUrlEquals('/');
?>

{% endhighlight %}

 * `param` $uri


#### dontSeeCurrentUrlMatches
 
Checks that current url doesn't match the given regular expression.

{% highlight php %}

<?php
// to match root url
$I->dontSeeCurrentUrlMatches('~$/users/(\d+)~');
?>

{% endhighlight %}

 * `param` $uri


#### dontSeeElement
 
Checks that the given element is invisible or not present on the page.
You can also specify expected attributes of this element.

{% highlight php %}

<?php
$I->dontSeeElement('.error');
$I->dontSeeElement('//form/input[1]');
$I->dontSeeElement('input', ['name' => 'login']);
$I->dontSeeElement('input', ['value' => '123456']);
?>

{% endhighlight %}

 * `param` $selector
 * `param array` $attributes


#### dontSeeElementInDOM
 
Opposite of `seeElementInDOM`.

 * `param` $selector


#### dontSeeInCurrentUrl
 
Checks that the current URI doesn't contain the given string.

{% highlight php %}

<?php
$I->dontSeeInCurrentUrl('/users/');
?>

{% endhighlight %}

 * `param` $uri


#### dontSeeInField
 
Checks that an input field or textarea doesn't contain the given value.
For fuzzy locators, the field is matched by label text, CSS and XPath.

{% highlight php %}

<?php
$I->dontSeeInField('Body','Type your comment here');
$I->dontSeeInField('form textarea[name=body]','Type your comment here');
$I->dontSeeInField('form input[type=hidden]','hidden_value');
$I->dontSeeInField('#searchform input','Search');
$I->dontSeeInField('//form/*[ * `name=search]','Search');` 
$I->dontSeeInField(['name' => 'search'], 'Search');
?>

{% endhighlight %}

 * `param` $field
 * `param` $value


#### dontSeeInFormFields
 
Checks if the array of form parameters (name => value) are not set on the form matched with
the passed selector.

{% highlight php %}

<?php
$I->dontSeeInFormFields('form[name=myform]', [
     'input1' => 'non-existent value',
     'input2' => 'other non-existent value',
]);
?>

{% endhighlight %}

To check that an element hasn't been assigned any one of many values, an array can be passed
as the value:

{% highlight php %}

<?php
$I->dontSeeInFormFields('.form-class', [
     'fieldName' => [
         'This value shouldn\'t be set',
         'And this value shouldn\'t be set',
     ],
]);
?>

{% endhighlight %}

Additionally, checkbox values can be checked with a boolean.

{% highlight php %}

<?php
$I->dontSeeInFormFields('#form-id', [
     'checkbox1' => true,        // fails if checked
     'checkbox2' => false,       // fails if unchecked
]);
?>

{% endhighlight %}

 * `param` $formSelector
 * `param` $params


#### dontSeeInPageSource
 
Checks that the page source doesn't contain the given string.

 * `param` $text


#### dontSeeInSource
 
Checks that the current page contains the given string in its
raw source code.

{% highlight php %}

<?php
$I->dontSeeInSource('<h1>Green eggs &amp; ham</h1>');

{% endhighlight %}

 * `param`      $raw


#### dontSeeInTitle
 
Checks that the page title does not contain the given string.

 * `param` $title



#### dontSeeLink
 
Checks that the page doesn't contain a link with the given string.
If the second parameter is given, only links with a matching "href" attribute will be checked.

{% highlight php %}

<?php
$I->dontSeeLink('Logout'); // I suppose user is not logged in
$I->dontSeeLink('Checkout now', '/store/cart.php');
?>

{% endhighlight %}

 * `param` $text
 * `param null` $url


#### dontSeeOptionIsSelected
 
Checks that the given option is not selected.

{% highlight php %}

<?php
$I->dontSeeOptionIsSelected('#form input[name=payment]', 'Visa');
?>

{% endhighlight %}

 * `param` $selector
 * `param` $optionText



#### doubleClick
 
Performs a double-click on an element matched by CSS or XPath.

 * `param` $cssOrXPath
 * `throws`  \Codeception\Exception\ElementNotFound


#### dragAndDrop
 
Performs a simple mouse drag-and-drop operation.

{% highlight php %}

<?php
$I->dragAndDrop('#drag', '#drop');
?>

{% endhighlight %}

 * `param string` $source (CSS ID or XPath)
 * `param string` $target (CSS ID or XPath)


#### executeInSelenium
 
Low-level API method.
If Codeception commands are not enough, this allows you to use Selenium WebDriver methods directly:

{% highlight php %}

$I->executeInSelenium(function(\Facebook\WebDriver\Remote\RemoteWebDriver $webdriver) {
  $webdriver->get('http://google.com');
});

{% endhighlight %}

This runs in the context of the
[RemoteWebDriver class](https://github.com/facebook/php-webdriver/blob/master/lib/remote/RemoteWebDriver.php).
Try not to use this command on a regular basis.
If Codeception lacks a feature you need, please implement it and submit a patch.

 * `param callable` $function


#### executeJS
 
Executes custom JavaScript.

This example uses jQuery to get a value and assigns that value to a PHP variable:

{% highlight php %}

<?php
$myVar = $I->executeJS('return $("#myField").val()');
?>

{% endhighlight %}

 * `param` $script


#### fillField
 
Fills a text field or textarea with the given string.

{% highlight php %}

<?php
$I->fillField("//input[ * `type='text']",`  "Hello World!");
$I->fillField(['name' => 'email'], 'jon * `mail.com');` 
?>

{% endhighlight %}

 * `param` $field
 * `param` $value


#### getVisibleText
 
Grabs all visible text from the current page.

 * `return` string


#### grabAttributeFrom
 
Grabs the value of the given attribute value from the given element.
Fails if element is not found.

{% highlight php %}

<?php
$I->grabAttributeFrom('#tooltip', 'title');
?>

{% endhighlight %}


 * `param` $cssOrXpath
 * `param` $attribute



#### grabCookie
 
Grabs a cookie value.
You can set additional cookie params like `domain`, `path` in array passed as last argument.

 * `param` $cookie

 * `param array` $params


#### grabFromCurrentUrl
 
Executes the given regular expression against the current URI and returns the first match.
If no parameters are provided, the full URI is returned.

{% highlight php %}

<?php
$user_id = $I->grabFromCurrentUrl('~$/user/(\d+)/~');
$uri = $I->grabFromCurrentUrl();
?>

{% endhighlight %}

 * `param null` $uri



#### grabMultiple
 
Grabs either the text content, or attribute values, of nodes
matched by $cssOrXpath and returns them as an array.

{% highlight html %}

<a href="#first">First</a>
<a href="#second">Second</a>
<a href="#third">Third</a>

{% endhighlight %}

{% highlight php %}

<?php
// would return ['First', 'Second', 'Third']
$aLinkText = $I->grabMultiple('a');

// would return ['#first', '#second', '#third']
$aLinks = $I->grabMultiple('a', 'href');
?>

{% endhighlight %}

 * `param` $cssOrXpath
 * `param` $attribute
 * `return` string[]


#### grabTextFrom
 
Finds and returns the text contents of the given element.
If a fuzzy locator is used, the element is found using CSS, XPath,
and by matching the full page source by regular expression.

{% highlight php %}

<?php
$heading = $I->grabTextFrom('h1');
$heading = $I->grabTextFrom('descendant-or-self::h1');
$value = $I->grabTextFrom('~<input value=(.*?)]~sgi'); // match with a regex
?>

{% endhighlight %}

 * `param` $cssOrXPathOrRegex



#### grabValueFrom
 
Finds the value for the given form field.
If a fuzzy locator is used, the field is found by field name, CSS, and XPath.

{% highlight php %}

<?php
$name = $I->grabValueFrom('Name');
$name = $I->grabValueFrom('input[name=username]');
$name = $I->grabValueFrom('descendant-or-self::form/descendant::input[ * `name`  = 'username']');
$name = $I->grabValueFrom(['name' => 'username']);
?>

{% endhighlight %}

 * `param` $field



#### loadSessionSnapshot
 
 * `param string` $name
 * `return` bool


#### makeScreenshot
 
Takes a screenshot of the current window and saves it to `tests/_output/debug`.

{% highlight php %}

<?php
$I->amOnPage('/user/edit');
$I->makeScreenshot('edit_page');
// saved to: tests/_output/debug/edit_page.png
?>

{% endhighlight %}

 * `param` $name


#### maximizeWindow
 
Maximizes the current window.


#### moveBack
 
Moves back in history.


#### moveForward
 
Moves forward in history.


#### moveMouseOver
 
Move mouse over the first element matched by the given locator.
If the second and third parameters are given,
then the mouse is moved to an offset of the element's top-left corner.
Otherwise, the mouse is moved to the center of the element.

{% highlight php %}

<?php
$I->moveMouseOver(['css' => '.checkout'], 20, 50);
?>

{% endhighlight %}

 * `param string` $cssOrXPath css or xpath of the web element
 * `param int` $offsetX
 * `param int` $offsetY

 * `throws`  \Codeception\Exception\ElementNotFound


#### pauseExecution
 
Pauses test execution in debug mode.
To proceed test press "ENTER" in console.

This method is useful while writing tests,
since it allows you to inspect the current page in the middle of a test case.


#### pressKey
 
Presses the given key on the given element.
To specify a character and modifier (e.g. ctrl, alt, shift, meta), pass an array for $char with
the modifier as the first element and the character as the second.
For special keys use key constants from WebDriverKeys class.

{% highlight php %}

<?php
// <input id="page" value="old" />
$I->pressKey('#page','a'); // => olda
$I->pressKey('#page',array('ctrl','a'),'new'); //=> new
$I->pressKey('#page',array('shift','111'),'1','x'); //=> old!!!1x
$I->pressKey('descendant-or-self::*[ * `id='page']','u');`  //=> oldu
$I->pressKey('#name', array('ctrl', 'a'), \Facebook\WebDriver\WebDriverKeys::DELETE); //=>''
?>

{% endhighlight %}

 * `param` $element
 * `param` $char Can be char or array with modifier. You can provide several chars.
 * `throws`  \Codeception\Exception\ElementNotFound


#### reloadPage
 
Reloads the current page.


#### resetCookie
 
Unsets cookie with the given name.
You can set additional cookie params like `domain`, `path` in array passed as last argument.

 * `param` $cookie

 * `param array` $params


#### resizeWindow
 
Resize the current window.

{% highlight php %}

<?php
$I->resizeWindow(800, 600);


{% endhighlight %}

 * `param int` $width
 * `param int` $height


#### saveSessionSnapshot
 
 * `param string` $name


#### scrollTo
 
Move to the middle of the given element matched by the given locator.
Extra shift, calculated from the top-left corner of the element,
can be set by passing $offsetX and $offsetY parameters.

{% highlight php %}

<?php
$I->scrollTo(['css' => '.checkout'], 20, 50);
?>

{% endhighlight %}

 * `param` $selector
 * `param int` $offsetX
 * `param int` $offsetY


#### see
 
Checks that the current page contains the given string (case insensitive).

You can specify a specific HTML element (via CSS or XPath) as the second
parameter to only search within that element.

{% highlight php %}

<?php
$I->see('Logout');                 // I can suppose user is logged in
$I->see('Sign Up', 'h1');          // I can suppose it's a signup page
$I->see('Sign Up', '//body/h1');   // with XPath

{% endhighlight %}

Note that the search is done after stripping all HTML tags from the body,
so `$I->see('strong')` will return true for strings like:

  - `<p>I am Stronger than thou</p>`
  - `<script>document.createElement('strong');</script>`

But will *not* be true for strings like:

  - `<strong>Home</strong>`
  - `<div class="strong">Home</strong>`
  - `<!-- strong -->`

For checking the raw source code, use `seeInSource()`.

 * `param`      $text
 * `param null` $selector


#### seeCheckboxIsChecked
 
Checks that the specified checkbox is checked.

{% highlight php %}

<?php
$I->seeCheckboxIsChecked('#agree'); // I suppose user agreed to terms
$I->seeCheckboxIsChecked('#signup_form input[type=checkbox]'); // I suppose user agreed to terms, If there is only one checkbox in form.
$I->seeCheckboxIsChecked('//form/input[ * `type=checkbox`  and  * `name=agree]');` 
?>

{% endhighlight %}

 * `param` $checkbox


#### seeCookie
 
Checks that a cookie with the given name is set.
You can set additional cookie params like `domain`, `path` as array passed in last argument.

{% highlight php %}

<?php
$I->seeCookie('PHPSESSID');
?>

{% endhighlight %}

 * `param` $cookie
 * `param array` $params


#### seeCurrentUrlEquals
 
Checks that the current URL is equal to the given string.
Unlike `seeInCurrentUrl`, this only matches the full URL.

{% highlight php %}

<?php
// to match root url
$I->seeCurrentUrlEquals('/');
?>

{% endhighlight %}

 * `param` $uri


#### seeCurrentUrlMatches
 
Checks that the current URL matches the given regular expression.

{% highlight php %}

<?php
// to match root url
$I->seeCurrentUrlMatches('~$/users/(\d+)~');
?>

{% endhighlight %}

 * `param` $uri


#### seeElement
 
Checks that the given element exists on the page and is visible.
You can also specify expected attributes of this element.

{% highlight php %}

<?php
$I->seeElement('.error');
$I->seeElement('//form/input[1]');
$I->seeElement('input', ['name' => 'login']);
$I->seeElement('input', ['value' => '123456']);

// strict locator in first arg, attributes in second
$I->seeElement(['css' => 'form input'], ['name' => 'login']);
?>

{% endhighlight %}

 * `param` $selector
 * `param array` $attributes
 * `return` 


#### seeElementInDOM
 
Checks that the given element exists on the page, even it is invisible.

{% highlight php %}

<?php
$I->seeElementInDOM('//form/input[type=hidden]');
?>

{% endhighlight %}

 * `param` $selector


#### seeInCurrentUrl
 
Checks that current URI contains the given string.

{% highlight php %}

<?php
// to match: /home/dashboard
$I->seeInCurrentUrl('home');
// to match: /users/1
$I->seeInCurrentUrl('/users/');
?>

{% endhighlight %}

 * `param` $uri


#### seeInField
 
Checks that the given input field or textarea contains the given value.
For fuzzy locators, fields are matched by label text, the "name" attribute, CSS, and XPath.

{% highlight php %}

<?php
$I->seeInField('Body','Type your comment here');
$I->seeInField('form textarea[name=body]','Type your comment here');
$I->seeInField('form input[type=hidden]','hidden_value');
$I->seeInField('#searchform input','Search');
$I->seeInField('//form/*[ * `name=search]','Search');` 
$I->seeInField(['name' => 'search'], 'Search');
?>

{% endhighlight %}

 * `param` $field
 * `param` $value


#### seeInFormFields
 
Checks if the array of form parameters (name => value) are set on the form matched with the
passed selector.

{% highlight php %}

<?php
$I->seeInFormFields('form[name=myform]', [
     'input1' => 'value',
     'input2' => 'other value',
]);
?>

{% endhighlight %}

For multi-select elements, or to check values of multiple elements with the same name, an
array may be passed:

{% highlight php %}

<?php
$I->seeInFormFields('.form-class', [
     'multiselect' => [
         'value1',
         'value2',
     ],
     'checkbox[]' => [
         'a checked value',
         'another checked value',
     ],
]);
?>

{% endhighlight %}

Additionally, checkbox values can be checked with a boolean.

{% highlight php %}

<?php
$I->seeInFormFields('#form-id', [
     'checkbox1' => true,        // passes if checked
     'checkbox2' => false,       // passes if unchecked
]);
?>

{% endhighlight %}

Pair this with submitForm for quick testing magic.

{% highlight php %}

<?php
$form = [
     'field1' => 'value',
     'field2' => 'another value',
     'checkbox1' => true,
     // ...
];
$I->submitForm('//form[ * `id=my-form]',`  $form, 'submitButton');
// $I->amOnPage('/path/to/form-page') may be needed
$I->seeInFormFields('//form[ * `id=my-form]',`  $form);
?>

{% endhighlight %}

 * `param` $formSelector
 * `param` $params


#### seeInPageSource
 
Checks that the page source contains the given string.

{% highlight php %}

<?php
$I->seeInPageSource('<link rel="apple-touch-icon"');

{% endhighlight %}

 * `param` $text


#### seeInPopup
 
Checks that the active JavaScript popup,
as created by `window.alert`|`window.confirm`|`window.prompt`, contains the given string.

 * `param` $text


#### seeInSource
 
Checks that the current page contains the given string in its
raw source code.

{% highlight php %}

<?php
$I->seeInSource('<h1>Green eggs &amp; ham</h1>');

{% endhighlight %}

 * `param`      $raw


#### seeInTitle
 
Checks that the page title contains the given string.

{% highlight php %}

<?php
$I->seeInTitle('Blog - Post #1');
?>

{% endhighlight %}

 * `param` $title



#### seeLink
 
Checks that there's a link with the specified text.
Give a full URL as the second parameter to match links with that exact URL.

{% highlight php %}

<?php
$I->seeLink('Logout'); // matches <a href="#">Logout</a>
$I->seeLink('Logout','/logout'); // matches <a href="/logout">Logout</a>
?>

{% endhighlight %}

 * `param`      $text
 * `param null` $url


#### seeNumberOfElements
 
Checks that there are a certain number of elements matched by the given locator on the page.

{% highlight php %}

<?php
$I->seeNumberOfElements('tr', 10);
$I->seeNumberOfElements('tr', [0,10]); //between 0 and 10 elements
?>

{% endhighlight %}
 * `param` $selector
 * `param mixed` $expected :
- string: strict number
- array: range of numbers [0,10]


#### seeNumberOfElementsInDOM
__not documented__


#### seeOptionIsSelected
 
Checks that the given option is selected.

{% highlight php %}

<?php
$I->seeOptionIsSelected('#form input[name=payment]', 'Visa');
?>

{% endhighlight %}

 * `param` $selector
 * `param` $optionText



#### selectOption
 
Selects an option in a select tag or in radio button group.

{% highlight php %}

<?php
$I->selectOption('form select[name=account]', 'Premium');
$I->selectOption('form input[name=payment]', 'Monthly');
$I->selectOption('//form/select[ * `name=account]',`  'Monthly');
?>

{% endhighlight %}

Provide an array for the second argument to select multiple options:

{% highlight php %}

<?php
$I->selectOption('Which OS do you use?', array('Windows','Linux'));
?>

{% endhighlight %}

Or provide an associative array for the second argument to specifically define which selection method should be used:

{% highlight php %}

<?php
$I->selectOption('Which OS do you use?', array('text' => 'Windows')); // Only search by text 'Windows'
$I->selectOption('Which OS do you use?', array('value' => 'windows')); // Only search by value 'windows'
?>

{% endhighlight %}

 * `param` $select
 * `param` $option


#### setCookie
 
Sets a cookie with the given name and value.
You can set additional cookie params like `domain`, `path`, `expires`, `secure` in array passed as last argument.

{% highlight php %}

<?php
$I->setCookie('PHPSESSID', 'el4ukv0kqbvoirg7nkp4dncpk3');
?>

{% endhighlight %}

 * `param` $name
 * `param` $val
 * `param array` $params



#### submitForm
 
Submits the given form on the page, optionally with the given form
values.  Give the form fields values as an array. Note that hidden fields
can't be accessed.

Skipped fields will be filled by their values from the page.
You don't need to click the 'Submit' button afterwards.
This command itself triggers the request to form's action.

You can optionally specify what button's value to include
in the request with the last parameter as an alternative to
explicitly setting its value in the second parameter, as
button values are not otherwise included in the request.

Examples:

{% highlight php %}

<?php
$I->submitForm('#login', [
    'login' => 'davert',
    'password' => '123456'
]);
// or
$I->submitForm('#login', [
    'login' => 'davert',
    'password' => '123456'
], 'submitButtonName');


{% endhighlight %}

For example, given this sample "Sign Up" form:

{% highlight html %}

<form action="/sign_up">
    Login:
    <input type="text" name="user[login]" /><br/>
    Password:
    <input type="password" name="user[password]" /><br/>
    Do you agree to our terms?
    <input type="checkbox" name="user[agree]" /><br/>
    Select pricing plan:
    <select name="plan">
        <option value="1">Free</option>
        <option value="2" selected="selected">Paid</option>
    </select>
    <input type="submit" name="submitButton" value="Submit" />
</form>

{% endhighlight %}

You could write the following to submit it:

{% highlight php %}

<?php
$I->submitForm(
    '#userForm',
    [
        'user[login]' => 'Davert',
        'user[password]' => '123456',
        'user[agree]' => true
    ],
    'submitButton'
);

{% endhighlight %}
Note that "2" will be the submitted value for the "plan" field, as it is
the selected option.

Also note that this differs from PhpBrowser, in that
{% highlight yaml %}
'user' => [ 'login' => 'Davert' ]
{% endhighlight %} is not supported at the moment.
Named array keys *must* be included in the name as above.

Pair this with seeInFormFields for quick testing magic.

{% highlight php %}

<?php
$form = [
     'field1' => 'value',
     'field2' => 'another value',
     'checkbox1' => true,
     // ...
];
$I->submitForm('//form[ * `id=my-form]',`  $form, 'submitButton');
// $I->amOnPage('/path/to/form-page') may be needed
$I->seeInFormFields('//form[ * `id=my-form]',`  $form);
?>

{% endhighlight %}

Parameter values must be set to arrays for multiple input fields
of the same name, or multi-select combo boxes.  For checkboxes,
either the string value can be used, or boolean values which will
be replaced by the checkbox's value in the DOM.

{% highlight php %}

<?php
$I->submitForm('#my-form', [
     'field1' => 'value',
     'checkbox' => [
         'value of first checkbox',
         'value of second checkbox,
     ],
     'otherCheckboxes' => [
         true,
         false,
         false
     ],
     'multiselect' => [
         'first option value',
         'second option value'
     ]
]);
?>

{% endhighlight %}

Mixing string and boolean values for a checkbox's value is not supported
and may produce unexpected results.

Field names ending in "[]" must be passed without the trailing square
bracket characters, and must contain an array for its value.  This allows
submitting multiple values with the same name, consider:

{% highlight php %}

$I->submitForm('#my-form', [
    'field[]' => 'value',
    'field[]' => 'another value', // 'field[]' is already a defined key
]);

{% endhighlight %}

The solution is to pass an array value:

{% highlight php %}

// this way both values are submitted
$I->submitForm('#my-form', [
    'field' => [
        'value',
        'another value',
    ]
]);

{% endhighlight %}
 * `param` $selector
 * `param` $params
 * `param` $button


#### switchToIFrame
 
Switch to another frame on the page.

Example:
{% highlight html %}

<iframe name="another_frame" src="http://example.com">


{% endhighlight %}

{% highlight php %}

<?php
# switch to iframe
$I->switchToIFrame("another_frame");
# switch to parent page
$I->switchToIFrame();


{% endhighlight %}

 * `param string|null` $name


#### switchToWindow
 
Switch to another window identified by name.

The window can only be identified by name. If the $name parameter is blank, the parent window will be used.

Example:
{% highlight html %}

<input type="button" value="Open window" onclick="window.open('http://example.com', 'another_window')">

{% endhighlight %}

{% highlight php %}

<?php
$I->click("Open window");
# switch to another window
$I->switchToWindow("another_window");
# switch to parent window
$I->switchToWindow();
?>

{% endhighlight %}

If the window has no name, the only way to access it is via the `executeInSelenium()` method, like so:

{% highlight php %}

<?php
$I->executeInSelenium(function (\Facebook\WebDriver\Remote\RemoteWebDriver $webdriver) {
     $handles=$webdriver->getWindowHandles();
     $last_window = end($handles);
     $webdriver->switchTo()->window($last_window);
});
?>

{% endhighlight %}

 * `param string|null` $name


#### typeInPopup
 
Enters text into a native JavaScript prompt popup, as created by `window.prompt`.

 * `param` $keys


#### uncheckOption
 
Unticks a checkbox.

{% highlight php %}

<?php
$I->uncheckOption('#notify');
?>

{% endhighlight %}

 * `param` $option


#### unselectOption
__not documented__


#### wait
 
Wait for $timeout seconds.

 * `param int` $timeout secs
 * `throws`  \Codeception\Exception\TestRuntimeException


#### waitForElement
 
Waits up to $timeout seconds for an element to appear on the page.
If the element doesn't appear, a timeout exception is thrown.

{% highlight php %}

<?php
$I->waitForElement('#agree_button', 30); // secs
$I->click('#agree_button');
?>

{% endhighlight %}

 * `param` $element
 * `param int` $timeout seconds
 * `throws`  \Exception


#### waitForElementChange
 
Waits up to $timeout seconds for the given element to change.
Element "change" is determined by a callback function which is called repeatedly
until the return value evaluates to true.

{% highlight php %}

<?php
use \Facebook\WebDriver\WebDriverElement
$I->waitForElementChange('#menu', function(WebDriverElement $el) {
    return $el->isDisplayed();
}, 100);
?>

{% endhighlight %}

 * `param` $element
 * `param \Closure` $callback
 * `param int` $timeout seconds
 * `throws`  \Codeception\Exception\ElementNotFound


#### waitForElementNotVisible
 
Waits up to $timeout seconds for the given element to become invisible.
If element stays visible, a timeout exception is thrown.

{% highlight php %}

<?php
$I->waitForElementNotVisible('#agree_button', 30); // secs
?>

{% endhighlight %}

 * `param` $element
 * `param int` $timeout seconds
 * `throws`  \Exception


#### waitForElementVisible
 
Waits up to $timeout seconds for the given element to be visible on the page.
If element doesn't appear, a timeout exception is thrown.

{% highlight php %}

<?php
$I->waitForElementVisible('#agree_button', 30); // secs
$I->click('#agree_button');
?>

{% endhighlight %}

 * `param` $element
 * `param int` $timeout seconds
 * `throws`  \Exception


#### waitForJS
 
Executes JavaScript and waits up to $timeout seconds for it to return true.

In this example we will wait up to 60 seconds for all jQuery AJAX requests to finish.

{% highlight php %}

<?php
$I->waitForJS("return $.active == 0;", 60);
?>

{% endhighlight %}

 * `param string` $script
 * `param int` $timeout seconds


#### waitForText
 
Waits up to $timeout seconds for the given string to appear on the page.
Can also be passed a selector to search in.
If the given text doesn't appear, a timeout exception is thrown.

{% highlight php %}

<?php
$I->waitForText('foo', 30); // secs
$I->waitForText('foo', 30, '.title'); // secs
?>

{% endhighlight %}

 * `param string` $text
 * `param int` $timeout seconds
 * `param null` $selector
 * `throws`  \Exception

<p>&nbsp;</p><div class="alert alert-warning">Module reference is taken from the source code. <a href="https://github.com/Codeception/Codeception/tree/2.2/src/Codeception/Module/WebDriver.php">Help us to improve documentation. Edit module reference</a></div>
