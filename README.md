# Laravel Decorator


[![Build Status](https://travis-ci.org/imanghafoori1/laravel-decorator.svg?branch=master)](https://travis-ci.org/imanghafoori1/laravel-decorator)
<a href="https://scrutinizer-ci.com/g/imanghafoori1/laravel-decorator"><img src="https://img.shields.io/scrutinizer/g/imanghafoori1/laravel-decorator.svg?style=round-square" alt="Quality Score"></img></a>
[![StyleCI](https://github.styleci.io/repos/164699371/shield?branch=master)](https://github.styleci.io/repos/164699371)
[![Software License](https://img.shields.io/badge/license-MIT-brightgreen.svg?style=round-square)](LICENSE.md)
[![Latest Stable Version](https://poser.pugx.org/imanghafoori/laravel-decorator/v/stable)](https://packagist.org/packages/imanghafoori/laravel-decorator)
[![Code Coverage](https://scrutinizer-ci.com/g/imanghafoori1/laravel-decorator/badges/coverage.png?b=master)](https://scrutinizer-ci.com/g/imanghafoori1/laravel-decorator/?branch=master)



**Made with :heart: for smart clean coders**


## A try to port "decorator" feature from python language to laravel framework.



![python-and-prey](https://user-images.githubusercontent.com/6961695/51078481-a2ad9300-16ca-11e9-8bf2-1d4ed214e030.jpg)

#### Why this header is chosen for a laravel package ?! You will shortly see why...

### What is a "Decorator" ?

A decorator wraps around the original function, effectively take over it's behaviour and returns result on it's behalf..

Like a python snake swallowing a deer and wraps around it's body ...

Decorators take in a callable, wrap around them and return a newly born callable.

Technically, A `"Decorator"` :

1 - Is a "callable" (python is an animal)

2 - It takes a "callable" as it's only argument (a python swallow an other animal)

3 - It returns a new "callable" (and turns into a bloated animal surrounding it.)

### :truck: Installation :

```
composer require imanghafoori/laravel-decorator
```


### A Use Case: Cache Like a Pro:

Imagine that you have a `MadController`which calls a `MadRepository` to get some `$mad` items.

Then after a while you decide to put a cache layer between does two for obvious reasons, but they both are so mad, that they do not allow you to touch a single line of their code.

You can not, (or maybe you CAN but shouldn't) put the cache logic in your controller or your repository.

You want to add a new feature (caching in this case) without modifing the existing code.

It smells like `Open-closed Principle` Yeah ?! 👃 

And you want to keep the responsibilities seperate. In this case `caching` should not be in a repository or controller but in it's own class. 

It smells like `Single Responsibility Principle` yeah ?! 👃 

**(Or maybe both `MadRepository` and `MadController` are imprisoned in the `vendor` folder and are part of a laravel package, so you can not touch them)**

```php

class MadController extends Controller
{
    public function index () {
    
        // we don't want to put any cache logic here...
        
        $mads = MadRepositoryFacade::getAllMads();
        ...
    }
}

```

So, what now ?!

With the help of laravel-decorator built-in cache decorator, you can go to `AppServiceProvider.php` (without any mad person realizing it.) 😁 

```php
<?php

use Imanghafoori\Decorator\Decorators\CacheResults;

class AppServiceProvider extends ServiceProvider {

    public function boot( ) {

        MadRepositoryFacade::decorateAll('getAllMads', CacheResults::cache('myKey', 10));
    }
}

```
Just that.

You will get cached results from your Facade calls, in your entire app without changing a single line of code !!




#### What is a callable ?

Long story short, A callable in laravel is anything that can be called with `\App::call();`

#### Look at the below picture :

Here you can see the bare anatomy of a decorator.

![image](https://user-images.githubusercontent.com/6961695/50966481-4855dc00-14ea-11e9-884f-5e6b762b6e35.png)

**Why it is a decorator ? well because :**

1 - A public method is a "callable" in php.

2 - The method has taken a "callable" as it's argument.

3 - The whole thing surrounded by the black line is a closure, which is "returned" from the method.


Here we return a callable that calls the original callable and casts it's result into string.


**So Let's apply this to decorate on the baz method on the Bar class :".**

![image](https://user-images.githubusercontent.com/6961695/50967860-a389cd80-14ee-11e9-85a5-e3cf346942a3.png)

Alternatively, you can use the `\Decorator` facade to do so



**It's time to enjoy having a decorated call :".**

![image](https://user-images.githubusercontent.com/6961695/50968397-2bbca280-14f0-11e9-85c9-4112e14da056.png)

**Note:** that finally a closure is returned (not the actual value)

### Sample decorators:

For good working examples please take a look at the tests folder.


### How to apply a decorator on a method ?

```php

// You may set multiple decorators on a single method...

\Decorator::decorate('class@method, 'someClass@someOtherDecorator');

```


### How to call a method with it's decorator ?

![image](https://user-images.githubusercontent.com/6961695/51078628-970f9b80-16cd-11e9-8b23-267b2d1564e7.png)


## Decorate Facades :

### Decorating Facade Methods

First, you should extend the `Imanghafoori\Decorator\DecoratableFacade` class (instead of the laravel base Facade).

![image](https://user-images.githubusercontent.com/6961695/51075625-484d0c00-16a3-11e9-9551-73b199a9c5e9.png)


#### Now You Can Apply Decorators in your ServiceProvider's boot method:

![image](https://user-images.githubusercontent.com/6961695/51078788-6715c780-16d0-11e9-91af-710fc9cd51b7.png)


then if you call your facade as normal you get decorated results.


![image](https://user-images.githubusercontent.com/6961695/51075751-3d937680-16a5-11e9-855b-2b8b61bdb876.png)


### :warning: Warning :

With great power, comes great responsibilities.

Remember not to violate the `Liskoves Substitution Principle` when you decorate something.

For example a method call which returns `int|null` should not unexpectedly return a `string` after being decorated.

`$result = app('decorate')->call(...`

Since the users of the method should be ready for type of value they get back.

But if you return only `int` and your decorator causes the `null` value to be filtered out. that's ok.
