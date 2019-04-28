---
title: 'PHP: writing a dal the DAO way part 1'
author: Christiaan Baes (chrissie1)
type: post
date: 2008-06-14T07:43:10+00:00
ID: 59
url: /index.php/webdev/serverprogramming/php-wrtting-a-dal-the-dao-way-part-1/
views:
  - 4310
categories:
  - PHP
  - Server Programming

---
Since php supports OO I think we should follow the OO rules. SO I wrote a [DAO layer like sun recommends it for it&#8217;s J2EE framework][1].

So lets start with the Factory. lets put it in a folder dal.

```php
&lt;?php
include_once( 'constants.php' );
require_once( 'factory/DatabaseFactory.php' );

class Factory
{
   function getInstance ()
   {
        static $instance;
        if (!isset($instance)) 
        {
            $c = __CLASS__;
            $instance = new $c;
        } 
        return $instance;
   }
    
   function getFactory()
   {
	return dataBaseFactory::getInstance();
   }
}
?&gt;```
this factory is a singleton and can be called as such.

Something like this will do.

```php
require_once('dal/Factory.php');
$factory =  Factory::getInstance();```
the above file also uses a file called constants.php.

which looks like this.

```php
&lt;?php
define('DATABASE', 'mysql');
?&gt;```
We will discuss what it is used for later.

the rest will follow later.

 [1]: http://java.sun.com/blueprints/corej2eepatterns/Patterns/DataAccessObject.html