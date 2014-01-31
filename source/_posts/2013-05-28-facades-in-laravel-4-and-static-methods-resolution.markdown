---
layout: post
title: Facades in Laravel 4 and Static methods resolution
date: '2013-5-28'
comments: true
categories: life
link: false
---
Several months ago in a blog post I saw people complaining about Laravel3's Static methods stating Static methods are very difficult to Unit Test with and Laravel is a pile of Static API's rather than a framework. I dont remember the Link. It said Laravel 3 is not architecturally “pure”, but it has super clean syntax. 

But Laravel 4 combines both of those things. It’s extremely architecturally pure, yet maintains the same terse, expressive syntax. 

Last weekend I was checking a video on Laravel 4 which was discussing about facades in Laravel 4 and I was truly amazed to see how the fancy looking static API's are mapped into their original Classes. Laravel 4 takes advantage of Facade Design Pattern[ <a href="http://en.wikipedia.org/wiki/Facade_pattern">http://en.wikipedia.org/wiki/Facade_pattern</a> ] which allows it to provide expressive syntax through static API's but still keep it testable under the hood.

So, back to the main point of the post, how a static method like 

``` php Facade Examples
File:get(),View::make, Redirect::to, Cache::put 
```
is resolved under the hood. Let's take File:get() and see how that maps to it's original class. Seeing File:: I first though there must be a File.php some where where the static method will be available. Searched for that and found one at /vendor/laravel/framework/src/Illuminate/Support/Facades/File.php surprisingly it barely has any content in it. 
<a href="https://github.com/illuminate/support/blob/master/Facades/File.php">https://github.com/illuminate/support/blob/master/Facades/File.php</a>
``` php File Facade
<?php namespace Illuminate\Support\Facades;

class File extends Facade {

	/**
	 * Get the registered name of the component.
	 *
	 * @return string
	 */
	protected static function getFacadeAccessor() { return 'files'; }

}
```

As I didn't find a static get method there my second target was to search in the Facade Base class as File class was extending it. But there wasn't any static get method either. But I did find a __callStatic magic method and became sure thats handling the call dynamically. You can check the contents of Facade.php here at <a href="https://github.com/illuminate/support/blob/master/Facades/Facade.php#L167">https://github.com/illuminate/support/blob/master/Facades/Facade.php#L167</a>

The first line in __callStatic method is

``` php Resolving Facade
$instance = static::resolveFacadeInstance(static::getFacadeAccessor());
```
Where's its calling the static resolveFacadeInstance() method and passing child class's static method's static::getFacadeAccessor() return value as a argument. Which is in above you can see returns the string 'files'. Now lets look at the definition of resolveFacadeInstance()
``` php resolveFacadeInstance function
protected static function resolveFacadeInstance($name)
{
	if (is_object($name)) return $name;

	if (isset(static::$resolvedInstance[$name]))
	{
	    return static::$resolvedInstance[$name];
	}

	return static::$resolvedInstance[$name] = static::$app[$name];
}
```

First two conditions wont work for us. So it will return static::$app[$name]. What does $app['file'] have in it. Must be an instance of a class. To check this I was studying about the bootstrapping of Laravel 4 and found that at some point in booting up, Laravel 4 registers the service providers passed into it via /app/config/app.php file. And checking that I found a serviceproviders as follows
``` php Service Provider
'Illuminate\Filesystem\FilesystemServiceProvider'
```

Then I checked the file Illuminate\Filesystem\FilesystemServiceProvider and found out how $app['files'] is being assigned. Here's it's contents

``` php Service Provider
class FilesystemServiceProvider extends ServiceProvider {

 /**
 * Register the service provider.
 *
 * @return void
 */
  public function register()
   {
      $this-&gt;app['files'] = $this-&gt;app-&gt;share(function() { return new Filesystem; });
   }
}
```
So $app['files'] is actually being assigned to a instance of FileSystem class.
Laravel uses that register method to add that service in the $app container.
So File::domeSomething() is actually somewhat equivalent to

``` php Original Filesystem Class
$fileSystem = new Filesystem;
$fileSystem->doSomething();
```

And I did find a public get() method in the FileSystem Class. You can check here at
https://github.com/illuminate/filesystem/blob/master/Filesystem.php#L27

So, a simple call like File::get() which just works, have a lot more going on under the hood. 

The word File is actually an alias to the Facade at 'Illuminate\Support\Facades\File' which you can find at /app/config/app.php in alias block.

Thats true for most if not all(well I havent checked all, but lets hope so) static expressive API's Laravel 4 Provide. 

A quick way to check which Class is being mapped for a Facade by using a special route in the route file as follows

``` php Getting Facade Accessor
Route::get('/',function(){
  $root = get_class(File::getFacadeRoot());
  // using other Facade in place of 'File' works too
  var_dump($root);
});
```

It will print the original Class that's being used for the Facade.

I dont know why but finding the whole thing was a great joy for me. It always feels great to understand the inner working principle of a thing I am using. This is the most awesome thing I have learnt/known in a while.

Hope you enjoyed it. :D
