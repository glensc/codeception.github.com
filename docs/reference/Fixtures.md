---
layout: doc
title: Fixtures - Codeception - Documentation
---


## Codeception\Util\Fixtures



Really basic class to store data in global array and use it in Cests/Tests.

{% highlight php %}

<?php
Fixtures::add('user1', ['name' => 'davert']);
Fixtures::get('user1');

?>

{% endhighlight %}



### add 

*static*

[See source](https://github.com/Codeception/Codeception/blob/2.2/src/Codeception/Util/Fixtures.php#L20)

### cleanup 

*static*

[See source](https://github.com/Codeception/Codeception/blob/2.2/src/Codeception/Util/Fixtures.php#L34)

### get 

*static*

[See source](https://github.com/Codeception/Codeception/blob/2.2/src/Codeception/Util/Fixtures.php#L25)

<p>&nbsp;</p><div class="alert alert-warning">Reference is taken from the source code. <a href="https://github.com/Codeception/Codeception/blob/2.2/src/Codeception/Util/Fixtures.php">Help us to improve documentation. Edit module reference</a></div>
