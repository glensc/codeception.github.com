---
layout: doc
title: 04-FunctionalTests - Codeception - Documentation
---

# Functional Tests

Now that we've written some acceptance tests, functional tests are almost the same, with just one major difference: functional tests don't require a web server to run tests.

In simple terms we set `$_REQUEST`, `$_GET` and `$_POST` variables and then we execute application from a test. This may be valuable as functional tests are faster and provide detailed stack traces on failures.

Codeception can connect to different web frameworks which support functional testing: Symfony2, Laravel5, Yii2, Zend Framework and others. You just need to enable desired module in your functional suite config to start.

Modules for all of these frameworks share the same interface, and thus your tests are not bound to any one of them. This is a sample functional test.

{% highlight php %}

<?php
$I = new FunctionalTester($scenario);
$I->amOnPage('/');
$I->click('Login');
$I->fillField('Username', 'Miles');
$I->fillField('Password', 'Davis');
$I->click('Enter');
$I->see('Hello, Miles', 'h1');
// $I->seeEmailIsSent() - special for Symfony2

{% endhighlight %}

As you see you can use same tests for functional and acceptance testing. 

## Pitfalls

Acceptance tests are usually much slower than functional tests. But functional tests are less stable as they run Codeception and application in one environment. If your application was not designed to run in long living process, for instance you use `exit` operator or global variables, probably functional tests are not for you. 

#### Headers, Cookies, Sessions

One of the common issues with functional tests is usage of PHP functions that deal with `headers`, `sessions`, `cookies`.
As you know, `header` function triggers an error if it is executed more than once for the same header. In functional tests we run application multiple times, thus, we will get lots of trash errors in the result.

#### Shared Memory

In functional testing unlike the traditional way, PHP application does not stop after it finished processing a request. 
As all requests run in one memory container they are not isolated.
So **if you see that your tests are mysteriously failing when they shouldn't - try to execute a single test.**
This will check if tests were isolated during run. Because it's really easy to spoil environment as all tests are run in shared memory.
Keep your memory clean, avoid memory leaks and clean global and static variables.

## Enabling Framework Modules

You have a functional testing suite in `tests/functional` dir.
To start you need to include one of the framework modules in suite config file: `tests/functional.suite.yml`. Below we provide simplified instructions for setting up functional tests with the most popular PHP frameworks.

### Symfony

To perform Symfony integrations you don't need to install any bundles or do any configuration changes.
You just need to include the `Symfony` module into your test suite. If you also use Doctrine2, don't forget to include it too.
To make Doctrine2 module connect using `doctrine` service from Symfony DIC you should specify Symfony module as a dependency for Doctrine2.

Example of `functional.suite.yml`

{% highlight yaml %}

class_name: FunctionalTester
modules:
    enabled: 
        - Symfony
        - Doctrine2:
            depends: Symfony # connect to Symfony
        - \Helper\Functional

{% endhighlight %}

By default this module will search for App Kernel in the `app` directory.

The module uses the Symfony Profiler to provide additional information and assertions.

[See the full reference](http://codeception.com/docs/modules/Symfony)

### Laravel5

[Laravel5](http://codeception.com/docs/modules/Laravel5)
module is included and requires no configuration.


{% highlight yaml %}

class_name: FunctionalTester
modules:
    enabled: 
        - Laravel5
        - \Helper\Functional

{% endhighlight %}


### Yii2

Yii2 tests are included in [Basic](https://github.com/yiisoft/yii2-app-basic) and [Advanced](https://github.com/yiisoft/yii2-app-advanced) application templates. Follow Yii2 guides to start.

### Yii

By itself Yii framework does not have an engine for functional testing.
So Codeception is the first and the only functional testing framework for Yii.
To use it with Yii include `Yii1` module into config.

{% highlight yaml %}

class_name: FunctionalTester
modules:
    enabled: 
        - Yii1
        - \Helper\Functional

{% endhighlight %}

To avoid common pitfalls we discussed earlier, Codeception provides basic hooks over Yii engine.
Please set them up following [the installation steps in module reference](http://codeception.com/docs/modules/Yii1).

### Zend Framework 2

Use [ZF2](http://codeception.com/docs/modules/ZF2) module to run functional tests inside Zend Framework 2.

{% highlight yaml %}

class_name: FunctionalTester
modules:
    enabled: 
        - ZF2
        - \Helper\Functional

{% endhighlight %}

### Zend Framework 1.x

The module for Zend Framework is highly inspired by ControllerTestCase class, used for functional testing with PHPUnit. 
It follows similar approaches for bootstrapping and cleaning up. To start using Zend Framework in your functional tests, include the `ZF1` module.

Example of `functional.suite.yml`

{% highlight yaml %}

class_name: FunctionalTester
modules:
    enabled: 
        - ZF1
        - \Helper\Functional 

{% endhighlight %}

[See the full reference](http://codeception.com/docs/modules/ZF1)

### Phalcon

`Phalcon` module requires creating bootstrap file which returns instance of `\Phalcon\Mvc\Application`. To start writing functional tests with Phalcon support you should enable `Phalcon` module and provide path to this bootstrap file:

{% highlight yaml %}

class_name: FunctionalTester
modules:
    enabled:
        - Phalcon:
            bootstrap: 'app/config/bootstrap.php'
             cleanup: true
             savepoints: true
        - \Helper\Functional

{% endhighlight %}

[See the full reference](http://codeception.com/docs/modules/Phalcon)

## Writing Functional Tests

Functional tests are written in the same manner as [Acceptance Tests](http://codeception.com/docs/03-AcceptanceTests) with `PhpBrowser` module enabled. All framework modules and `PhpBrowser` module share the same methods and the same engine.

Therefore we can open a web page with `amOnPage` command.

{% highlight php %}

<?php
$I = new FunctionalTester($scenario);
$I->amOnPage('/login');

{% endhighlight %}

We can click links to open web pages of application.

{% highlight php %}

<?php
$I->click('Logout');
// click link inside .nav element
$I->click('Logout', '.nav');
// click by CSS
$I->click('a.logout');
// click with strict locator
$I->click(['class' => 'logout']);

{% endhighlight %}

We can submit forms as well:

{% highlight php %}

<?php
$I->submitForm('form#login', ['name' => 'john', 'password' => '123456']);
// alternatively
$I->fillField('#login input[name=name]', 'john');
$I->fillField('#login input[name=password]', '123456');
$I->click('Submit', '#login');

{% endhighlight %}

And do assertions:

{% highlight php %}

<?php
$I->see('Welcome, john');
$I->see('Logged in successfully', '.notice');
$I->seeCurrentUrlEquals('/profile/john');

{% endhighlight %}

Framework modules also contain additional methods to access framework internals. For instance, `Laravel5`, `Phalcon`, and `Yii2` modules have `seeRecord` method which uses ActiveRecord layer to check that record exists in database.

Take a look at the complete reference for module you are using. Most of its methods are common for all modules but some of them are unique.

Also you can access framework globals inside a test or access Dependency Injection containers inside `Helper\Functional` class.

{% highlight php %}

<?php
namespace Helper;

class Functional extends \Codeception\Module
{
    function doSomethingWithMyService()
    {
        $service = $this->getModule('Symfony')->grabServiceFromContainer('myservice');
        $service->doSomething();
    }
}

{% endhighlight %}

Check also all available *Public Properties* of used modules to get full access to its data. 

## Error Reporting

By default Codeception uses `E_ALL & ~E_STRICT & ~E_DEPRECATED` error reporting level. 
In functional tests you might want to change this level depending on framework's error policy.
The error reporting level can be set in the suite configuration file:
    
{% highlight yaml %}

class_name: FunctionalTester
modules:
    enabled: 
        - Yii1
        - \Helper\Functional
error_level: "E_ALL & ~E_STRICT & ~E_DEPRECATED"

{% endhighlight %}

`error_level` can be set globally in `codeception.yml` file.


## Conclusion

Functional tests are great if you are using powerful frameworks. By using functional tests you can access and manipulate their internal state. 
This makes your tests shorter and faster. In other cases, if you don't use frameworks there is no practical reason to write functional tests.
If you are using a framework other than the ones listed here, create a module for it and share it with community.




* **Next Chapter: [UnitTests >](/docs/05-UnitTests)**
* **Previous Chapter: [< AcceptanceTests](/docs/03-AcceptanceTests)**