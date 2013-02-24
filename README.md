CIUnit 1.0 Beta
---------------
CIUnit is simple and light-weight PHPUnit/JUnit like unit testing framework for CodeIgniter. The framework runns on top
of CodeIgniter and provides a web interface for test execution. CIUnit is a good alternative
for small projects where the full potential of PHPUnit is not used and a good start for unit testing newbies.

We are still in Beta version, so if you notice any bugs please report them [here][bug-list].


Table of Contents
-----------------
* [Getting Started][getting-started]
* [Writing Tests for CIUnit][writing-tests-for-ciunit]
 * [Testing Exceptions][testing-exceptions]
* [Fixtures][fixtures]
* [Organizing Tests][organizing-tests]
* [CIUnit API][ciunit-api]
 * [AssertArrayHasKey][assertarrayhaskey]
 * [AssertCount][assertcount]
 * [AssertEmpty][assertempty]
 * [AssertFalse][assertfalse]
 * [AssertinstanceOf][assertinstanceof]
 * [AssertinternalType][assertinternaltype]
 * [AssertNull][assertnull]
 * [AssertSameSize][assertsamesize]
 * [AssertTrue][asserttrue]
* [Issues][issues]
* [License][license]

Getting Started
---------------
### Requirements
CIUnit requires CodeIgniter >= 2.0

### How to install

* Download CIUnit Framework from [here][download-master-zip]
* Add the ```resources``` folder in the root of your application
* Place the ```controllers/ciunit_controller.php``` to your ```controllers``` folder
* Add the ```third_party/ciunit``` folder to your ```third_party``` folder
* Create folder ```tests``` inside your ```application``` folder
* Add the following routes to your ```routes.php```

```php
$route['ciunit'] = "ciunit_controller/index";
$route['ciunit/(:any)'] = "ciunit_controller/index/$1";
```
* CIUnit makes use of the ```base_url()``` feature in CodeIgniter. Please, make sure it is properly configured inside your ```config/config.php```

```php
$config['base_url'] = 'http://example.com/';
```
* Now you must be good to go.
 - http://example.com/index.php/ciunit or
 - http://example.com/ciunit

PS. If you have any issues installing the CIUnit, please feel free to contact me.


#### Customization

CIUnit allows you to configure the location of resources and tests. The CIUnit config file is located under ```ciunit/config/config.php```
Default configuration is as follows
```php
$config['tests_path'] = APPPATH .'tests/';
$config['resources_path'] = 'resources/';
```
### Sreenshots
#### Failure
 ![Screenshot of CIUnit, displaying a failure in results.](https://lh5.googleusercontent.com/-PPF0wZLcCok/USkkZO0qRuI/AAAAAAAAFoc/6X5wucMEscQ/s797/failure.png "CIUnit Test Results")
 
#### Success
![Screenshot of CIUnit, displaying a success in results.](https://lh6.googleusercontent.com/-koR9udOOSXU/USkkZO2VLLI/AAAAAAAAFoY/0oY7BRYUijg/s799/success.png "CIUnit Test Results")


Writing Tests for CIUnit
------------------------

The example introduces the basic conventions and steps for writing tests with CIUnit:

```php
<?php

class StackTest extends CIUnit_Framework_TestCase
{   

    public function testPushAndPop()
    {
        $stack = array();
        $this->assertEquals(0, count($stack));
 
        array_push($stack, 'foo');
        $this->assertEquals('foo', $stack[count($stack)-1]);
        $this->assertEquals(1, count($stack));
 
        $this->assertEquals('foo', array_pop($stack));
        $this->assertEquals(0, count($stack));
    }
}

```

### Testing Exceptions

The example introduces the basic steps for testing expected exceptions
```php
<?php

class ExceptionTest extends CIUnit_Framework_TestCase
{
    public function testException()
    {
        $this->setExpectedException('RunTimeException'); 
    }
}
```

Alternative approach to testing exceptions

```php
<?php

class ExceptionTest extends CIUnit_Framework_TestCase
{
    public function testException()
    {
        try {
            // Throw exception code here...
        }
        catch(Exception $e) {
            return;
        }
        
        $this->fail('No exception has been raised');
    }
}
```

Fixtures
--------
I assume we all know what fixtures are and how they work.

The ```setUp()``` and ```tearDown()``` template methods are run once for each test method of the test case class.
```php
<?php

class FixtureTest extends CIUnit_Framework_TestCase
{
    protected function setUp()
    {
        print __METHOD__ . "\n";
    }
    
    public function testOne()
    {
        print __METHOD__ . "\n";
        $this->assertTrue(TRUE);
    }
    
    public function testTwo()
    {
        print __METHOD__ . "\n";
        $this->assertTrue(TRUE);
    }
    
    protected function tearDown()
    {
        print __METHOD__ . "\n";
    }
}
```
The result from running the code will be
```
FixtureTest::setUp
FixtureTest::testOne 
FixtureTest::tearDown 
FixtureTest::setUp 
FixtureTest::testTwo 
FixtureTest::tearDown
```

Organizing Tests
----------------
The CIUnit framework allows us to organize tests into a hierarchy of test suite objects using the ```suite()``` method. 
Here is a basic examplе how to organize your test suites into one test suite.
```php
<?php

require_once 'MyTestOne.php';
require_once 'MyTestTwo.php';

class ProjectSuite extends CIUnit_Framework_TestCase
{
    public static function suite()
    {
        $suite = new CIUnit_Framework_TestSuite('ProjectSuite');
        $suite->addTestSuite('MyTestOne');
        $suite->addTestSuite('MyTestTwo');
        
        return $suite;
    }
}
```
Output
```
MyTestOne::setUp
MyTestOne::testOne 
MyTestOne::tearDown 
MyTestTwo::setUp 
MyTestTwo::testTwo 
MyTestTwo::tearDown
```


CIUnit API
--------------
### assertArrayHasKey
Method asserts that array has a specified key

```assertArrayHasKey(mixed $needle, array $haystack, string $message = '')``` <br/>
Reports an error identified by ```$message``` if ```$array``` does not contain the specified ```$key```. <br/>
```assertArrayNotHasKey()``` is the inverse of this assertion and takes the same arguments.
```php
<?php
class ArrayHasKeyTest extends CIUnit_Framework_TestCase
{ 
    public function testFailure()
    {
        $this->assertArrayHasKey('foo', array('bar' => 'bar'));
    }
}
```
Failure description
```
Failed asserting that an array has the key "bar". 
```

### assertCount
Method asserts the number of elements of an array, Countable or Iterator

```assertCount($expectedCount, array $haystack, string $messge = '')```<br/>
Reports an error identified by ```$message``` if the number of elements in ```$haystack``` is not equal to ```$expectedCount```.<br/>
```assertNotCount()``` is the inverse of this assertion and takes the same arguments.

```php
<?php
class CountTest extends CIUnit_Framework_TestCase
{
    public function testFailure()
    {
        $this->assertCount(0, array('foo'));
    }
}
```
Failure description
```
Failed asserting that actual size 1 matches expected size 0.  
```

### assertEmpty
Method asserts that a variable is empty

```assertEmpty(mixed $actual, string $message = '')```<br/>
Reports an error identified by ```$message``` if ```$actual``` is not empty.<br/>
```assertNotEmpty()``` is the inverse of this assertion and takes the same arguments.

```php
<?php
class EmptyTest extends CIUnit_Framework_TestCase
{
    public function testFailure()
    {
        $this->assertEmpty(array('foo'));
    }
}
```
Failure description
```
Failed asserting that an array is empty. 
```

### assertEquals
Method asserts that two variables are equal

```assertEquals(mixed $expected, mixed $actual, string $message = '')```<br/>
Reports an error identified by ```$message``` if ```$expected``` is not equal to ```$actual```.<br/>
```assertNotEquals()``` is the inverse of this assertion and takes the same arguments.

```php
<?php
class EqualsTest extends CIUnit_Framework_TestCase
{
    public function testFailure()
    {
        $this->assertEquals(12, 13);
    }
    
    public function testFailure2()
    {
        $this->assertEquals('Integer', 'String');
    }
}
```
Failure description
```
Failed asserting that 12 matches expected 13. 

Failed asserting that two strings are equal.

- Expected 
+ Actual 

-'Integer'
+'String'
```

```assertEquals(float $expected, float $actual, float $delta = 0, string $message = '')``` 

Reports an error identified by ```$message``` if the two floats ```$expected``` and ```$actual``` are not within ```$delta``` of each other.
```php
<?php
class EqualsTest extends CIUnit_Framework_TestCase
{
    public function testSuccess()
    {
        $this->assertEquals(12.0, 12.1, 0.2, '');
    }
    
    public function testFailure()
    {
        $this->assertEquals(12.0, 12.1);
    }
}
```
Failure description
```
Failed asserting that 12.1 matches expected 12.0. 
```
```assertEquals(array $expected, array $actual, string $message = '') ```
```php
<?php
class EqualsTest extends CIUnit_Framework_TestCase
{
    public function testFailure()
    {
        $actual = array('a', 'b', 'c');
        
        $expected = array('a', 'b', 'd');
        
        $this->assertEquals($actual, $expected);
    }
}
```
Failure description
```
Failed asserting that two arrays are equal.

- Expected 
+ Actual 

 Array (
    0 => 'a'
    1 => 'b'
-   2 => 'c'
+   2 => 'd'
 )  
```

```assertEquals(object $expected, object $actual, string $message = '') ```
```php
<?php
class EqualsTest extends CIUnit_Framework_TestCase
{
    public function testFailure()
    {
        $expected = new stdClass();
        $expected->name = "John";
        $expected->age = 11;
        
        $actual = new stdClass();
        $actual->name = "John";
        $actual->age = 19; 
        
        $this->assertEquals($actual, $expected);
    }
}
```
Failure description
```
Failed asserting that two objects are equal.

- Expected 
+ Actual 

 stdClass Object (
    'name' => 'John'
-   'age' => 11
+   'age' => '19'
 )  
```

### assertFalse
Method asserts that a condition is false

```assertFalse(bool $condition, string $message = '')```<br/>
Reports an error identified by ```$message``` if ```$condition``` is ```TRUE```. 

```php
<?php
class FalseTest extends CIUnit_Framework_TestCase
{
    public function testFailure()
    {
        $this->assertFalse(TRUE);
    }
}
```
Failure description
```
Failed asserting that true is false.  
```

### assertInstanceOf
Method asserts that a variable is of a given type

```assertInstanceOf(object $expected, object $actual, string $message = '')```<br/>
Reports an error identified by ```$message``` if ```$expected``` is not an instance of ```$actual```. <br/>
```assertNotInstanceOf()``` is the inverse of this assertion and takes the same arguments.
```php
<?php
class InstanceOfTest extends CIUnit_Framework_TestCase
{
    public function testFailure()
    {
        $this->assertInstanceOf('RunTimeException', new Exception());
    }
}
```
Failure description
```
Failed asserting that Exception Object (...) is an instance of RunTimeException. 
```

### assertInternalType
Method assert that variable is of a given type

```assertInternalType(object $expected, object $actual, string $message = '')```<br/>
Reports an error identified by ```$message``` if ```$expected``` is not of type ```$actual```. <br/>
```assertNotInternalType()``` is the inverse of this assertion and takes the same arguments.
```php
<?php
class InternalTypefTest extends CIUnit_Framework_TestCase
{
    public function testFailure()
    {
        $this->assertInternalType('string', 1);
    }
}
```
Failure description
```
Failed asserting that 1 is of type "string".
```

### assertNull
Method asserts that a variable is ```NULL```

```assertNull(mixed $var, string $message = '')```<br/>
Reports an error identified by ```$message``` if ```$var``` is not ```NULL```. <br/>
```assertNotNull()``` is the inverse of this assertion and takes the same arguments.
```php
<?php
class NullTest extends CIUnit_Framework_TestCase
{
    public function testFailure()
    {
        $this->assertNull('string');
    }
}
```
Failure description
```
Failed asserting that 'string' is null. 
```

### assertSameSize
Method assert that the size of two arrays (or `Countable` or `Iterator` objects) is the same

```assertSameSize(mixed $expected, mixed $actual string $message = '')```<br/>
Reports an error identified by ```$message``` if the number of elements in ```$expected``` is not the same as in ```$actual```. <br/>
```assertNotSameSize()``` is the inverse of this assertion and takes the same arguments.
```php
<?php
class SameSizeTest extends CIUnit_Framework_TestCase
{
    public function testFailure()
    {
        $this->assertSameSize(array(1, 2), array());
    }
}
```
Failure description
```
Failed asserting that actual size 0 matches expected size 2. 
```

### assertTrue 
Method asserts that a condition is true

```assertTrue(bool $condition, string $message = '')```<br/>
Reports an error identified by ```$message``` if ```$condition``` is ```FALSE```. 

```php
<?php
class TrueTest extends CIUnit_Framework_TestCase
{
    public function testFailure()
    {
        $this->assertTrue(FALSE);
    }
}
```
Failure description
```
Failed asserting that false is true.  
```

Issues
------

Have a bug? Please create an issue here on GitHub!

https://github.com/agop/ciunit-framework/issues

License
-------

Copyright &copy; 2013

Licensed under the The BSD 3-Clause License

<!-- deep links -->
[getting-started]: #getting-started
[features]: #features 
[writing-tests-for-ciunit]: #writing-tests-for-ciunit
[testing-exceptions]: #testing-exceptions
[testing-php-errors]: #testing-php-errors
[fixtures]: #fixtures
[organizing-tests]: #organizing-tests
[ciunit-api]: #ciunit-api
[assertarrayhaskey]: #assertarrayhaskey
[assertcount]: #assertcount
[assertempty]: #assertempty
[assertfalse]: #assertfalse
[assertinstanceof]: #assertinstanceof
[assertinternaltype]: #assertinternaltype
[assertnull]: #assertnull
[assertsamesize]: #assertsamesize
[asserttrue]: #asserttrue
[issues]: #issues
[license]: #license

[bug-list]: https://github.com/agop/ciunit-framework/issues
[download-master-zip]: https://github.com/agop/ciunit-framework/archive/master.zip


