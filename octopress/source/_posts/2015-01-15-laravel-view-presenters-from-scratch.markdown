---
layout: post
title: "Laravel View Presenters from Scratch"
date: 2015-01-15 10:34:14 -0500
comments: true
categories: web laravel
---

## Why do We need View Presenters?

View should be dumb, they shouldn't do much other than showing data to the user. So where's the correct place to place your view specific logic in Laravel. Like you need the gravatar url of the user to see if the user already has a gravatar set to save the user from uplaoding a new one in your site. Or maybe you save user name as two field in db, as ```first_name``` and ```last_name```, now you need to create user's full name, but you also want apply the function ```ucfirst``` first on them. Or maybe you have users profile pic's filename saved in database, now you need to create profile pic url from that by concatenating the upload directory.

Where should we put this sorts of logic. Eloquent Model? This can seem a very tempting choice, but why should the Model handle View specific logics. Then where is the place to put this kind of things?

Well maybe in their own class whose only responsibility will be to do all the logic needed in view using a specific model.

In laravel, there's ins't any built in way to do 1-to-1 Model-Presenter mapping out of the box but it's incredibly easy to set up one.
 
<!-- more -->

## So what do we need

* define a present mentod or any method in every Eloquent model which will return a presenter instance for that model and if you use anything like ```$model->present()->gravatar_url()``` or ```$model->present->gravatar_url``` both will actually call a function named ```gravatar_url```on the ```Presenter``` class. 
* make it easy for the view to use it, like view shouldn't be requried to know where that url is coming from.
* and finally maybe hide away some common Presenter functionality into an abstract class so every presenter class wont have to implement those

## Implementing the present() method in every Eloquent Model

The responsiblity of the ```present()``` method will be to 

* first somehow dertermine the FQN(Fully Qualified Name) of the Presenter class so we can instantiate them
* After we get that class name, we check if that class exists, and if they doesnt exist they you can either throw an exception( which is better ) or fail silently if you want. And if it's there then just instantiate it and return the instance.

### How to determine the FQN of the Presenter Class

There are several ways we can do that, it entirely depends on how we want to desing our app. We can find the Current Model's class name append ```Presenter``` in their name and search in the same directory or in a separate ```Presenters``` directory which is on a different namespace to find the presenter class.

But it's better not to hard code that logic inside a function and make it more flexible. Like we can look for a ```$presenter``` variable in the Model where we will save the Presenters full path so our funciton can use it. That way we can save the Presenter anywhere we want.

### Creating a Presentable Trait

The functions of the ```present()``` method seems to be same for every models, so we could just use a trait for it and use it in every model. Lets see a reference implementation of the trait,

``` php PresentableTrait

<?php namespace Acme\Core\Presenter;


trait PresentableTrait {

	/* This is where we will save the presenterInstance so use later on the same model object */
    protected $presenterInstance;

    public function present()
    {
        // Check if the preserneter property has been declared on the model and 
        // the class exists
        
        if( ! $this->presenter or ! class_exists($this->presenter)){

        	  // We didn't find a presenter class, throw an exception
        	  // I am assuming we have a PresenterException defined already
            throw new PresenterException('Please set the Presenter path to your Presenter FQN');
        }

		  // The good old Singleton pattern
        if( ! $this->presenterInstance )
        {
            $this->presenterInstance = new $this->presenter($this);
        }

        return $this->presenterInstance;
    }
}

```

The functionality of the trait is fairly straight forward. We have a variable called ```$persenterInterface``` to save the ```Presenter Instance``` for further use. I have added comments on the code to make it easier to understand.

### Creating a base Presenter class

We can ignore this if we want, just adding the ```Presenter Class``` is enough for the functionality. But lets add some sytactic sugar using the ```__get()``` magic method so when you do something like ```$user->present()->gravatar_url``` it actually calls the method with that name. Though ```gravatar_url``` is actually a method on the presenter we will be able to use it like a property. 

Lets check the following abstract Presenter class

``` php Base Presenter Class
<?php namespace Acme\Core\Presenter;


abstract class Presenter {
    
    protected $entity; // This is to store the original model instance

    function __construct($entity)
    {
        $this->entity = $entity;
    }
	
	// Call the function if that exsits, or return the property on the original model
    public function __get($property){
        if(method_exists($this, $property))
        {
            return $this->{$property}();
        }

        return $this->entity->{$property};
    }
}

```

In the above trait we actually passed the model instance to the presenter while instantiating that. And hence we have received that in the constructor of the base class.


## Usage 

Lets see how can we use this in a project with an Eloquent Model.

Lets do it for the ```User``` model. First we will define the ```Presenter``` class.

``` php Acme\Users\UserPresenter.php

<?php namespace Acme\Users;

use Acme\Core\Presenter\Presenter;

class UserPresenter extends Presenter {

    // return users gravatar url
    public function gravatar($size = 30)
    {
        $email = md5($this->email);

        return "//www.gravatar.com/avatar/{$email}?s={$size}";
    }
    
    public function fullname()
    {
    	return ucfirst($this->first_name). ' '.ucfirst($this->last_name);
    	
    }
}

```

We see in the user Presenter we used ```$this->email```, ```$this->first_name``` and ```$this->last_name```. We are being able to do that due the magic getter method we defined which automatically returns the property on the associated model if a function with that name doesn't exist in the ```Presenter``` itself.


Now lets create an ```User``` model.

``` php Acme\Users\User.php

<?php namespace Acme\Users;

use Acme\Core\Presenter\PresentableTrait;

class User extends \Eloquent {

	use PresentableTrait;
	
	protected $presenter = 'Acme\Users\UserPresenter';
	
}

```

And we are done. We can now use it like,

``` php Presenter Usage

$user = User::find(1);

// now we can use
$user->present()->gravatar_url; // this returns users gravatar url

// or 

$user->present()->fullname; // This will return users full name

```

You can use those methods anywhere on any ```model``` instance which has the ```Presenter``` implemented. Be it inside your controller or in your bade templates.


***So now you have a clean way to place all your presentation specific logic. As they are in a single class it's easier to maintain and you can refactor any method's body anytime without worrying about breaking anything on other parts of your application.***


Hope you enjoyed it.

**N. B. This post is inspired by Jeffrey Way's View presenter related lessons on Laracasts.**