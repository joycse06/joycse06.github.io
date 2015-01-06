---
layout: post
title: "Command Driven Design: The world of DTOs, Handlers and Domain Events"
date: 2015-01-05 13:04:18 -0500
comments: true
categories: web architecture
---

Command driven desing is quite a new buzzword in php world which the community is adopting quickly. Lets dive into some theoritical details of whats and whys of this desing patterns. Code examples will be based on ```Laravel```.


## Coming out from the CRUD mentality

We developer like to think in terms of CRUD(CREATE, READ, UPDATE, DELETE) like kind of one-to-one mapping with business needs and out models but all business needs doesn't go well with CRUD.

<!-- more -->

Like there's a task named **Charge client** for some kind of service. This action is not easily mappable to CRUD with any model. And there can be tasks which affects more than one model in complex ways. Bending the tasks or the client to think in terms of CRUD is not the solution, rather this bending should be the opposite. We developers should look for patterns to bend to our thinking process to align more with the complex business needs. Command driven design is a solution to this problem where you can have commands like ```RegisterUserCommand``` , ```PublishJobCommand```, ```UpdateUserProfileCommand```, ```SubscribeUserToSilverPlanCommand``` and any others use cases you have in your application.

There are several benefits of this using this pattern:

1. You can have really skinny controllers. HTTP itself is very simple and controllers are just gateways to your application, it's better not do all kinds of business things in your controllers. That breaks the ```Single Responsibility``` principle of ```SOLID```. Using commands you can easily achieve this.

2. You can have better seperation of concerns in your application. With all your busiess entities seperated from others parts of the application, command from all kinds of business tasks and unified command pipeline for handling command.

Let me give you an example, With everything properly set up, lets see how a ```user register``` action method of registration controller will look like,

```php RegistrationController@register

pubic function register()
{
	
	$this->exectute(RegisterUserCommand::class);
	
	Redirect::home();
}	
```	

Just see how clean it looks. Dont think it doesnt perform validation or other tasks required for user registration.

Lets list the tasks the above action is doing under the hood.

* Firstly the execute command might be coming from a ```Trait``` which does all the heavy lifting. It creates a DTO(Data Transfer Object) with required constructor parameters required for user registration like username, email etc from the POST data.
* Then it call the proper vaidator for that command, you can determine yourself how the command bus will determine the FQN(Fully Qualified Name) of the vaidator class. After determining that the command bus will pass the command object to the Validator for input validation and checking business constraints. If the input from user doesnt pass the validation then it might throw a ```FormValidationException``` for which we can set up a global exception handler and just send a response to client from that handler without returning the control to the controller action at all.
* And if the validation passes, the command bus then determine the ```handler``` class for the command call maybe the ```handle()``` method on that class and that method create the ```user``` save it to database of any kind which is also decoupled from the ```handler``` itself. 
* After creating user the handler dispatch ```UserHasRegistered``` event, for which we can listen for using another listener class. Once that listener class has been invoked it can do all sorts of background tasks like, sending welcome email, adding users to a mailing list, sending confirmation email and anything else your business require. And all of that can go into the queue without keeping user waiting for this tasks.


From the face of all sorts of tasks it's doing, it may seem intimidating at first glance. But once you have basic command bus and event dispatching system set up then just by following some convetions you can work pretty fast.

The real benefit of it maintanence. If you have application with a lifecycle of several years, if you don't follow best practices it can soon become  your headace. Even as the sole developer you will fear to touch it after sometime when its already a mess with controller actions doing all sort of things which necessarily controllers concern.

Another benefit of this approach is you can expose API for your project pretty easily as you have everything conatained already, exposting is just setting up new ```routes``` for it and You are done. Because you are doing all your heavy lifting in their self contained objects.

Lets clarify some terms:

##DTO

 ```Data Transfer Objects``` are instances of simple classes with public properties to pass between different componets of your application. A simple ```RegisterUserCommand``` DTO can look like (Taken from one of my personal project)

```php Saphira/Users/Commands/RegisterUserCommand.php

<?php namespace Saphira\Users\Commands;

use Saphira\Core\CommandBus\CommandInterface;

class RegisterUserCommand implements CommandInterface{
     
    public $email;
    
    public $password;
    
    public $password_confirmation;

        
    public $username;

    public $confirmation_code;
       
    public function __construct($username, $email, $password, $password_confirmation){

        $this->email = $email;
        $this->password = $password;
        $this->username = $username;
        $this->confirmation_code = bin2hex(openssl_random_pseudo_bytes(16));
        $this->password_confirmation = $password_confirmation;
    }

}

```

as you see it has everything I need for user registration. And the cool thing is that the parameter on the the constructor of this class is auto-magically resolved form ```laravel's Input::all()``` store where all get and post variables are stored.

Likewise, A sample ```RegisterUserHandler``` can look like,

```php Saphira/Users/Handlers/RegisterUserHandler.php

<?php namespace Saphira\Users\Handlers;


use Saphira\Core\CommandBus\CommandInterface;
use Saphira\Core\CommandBus\CommandHandlerInterface;
use Saphira\Core\Events\DispatchableTrait;
use Saphira\Users\User;
use Saphira\Users\UserRepository;

class RegisterUserHandler implements CommandHandlerInterface{

    use DispatchableTrait;

    protected $repository;

    public function __construct(UserRepository $repository)
    {

        $this->repository = $repository;
    }

    public function handle(CommandInterface $command)
    {
        
        $user = User::register(
            $command->username, $command->email, $command->password,
            $command->confirmation_code
        );

        $this->repository->save($user);

        $this->dispatchEventsFor($user);

        return $user;
    }
}

```

And with these two and some other classes( like ```EventListener```, ```RegisterUserValidator``` etc) placed in proper directories my ```RegistrationController@store``` method is as simple as following

```php RegistrationController.php

public function store()
{
        $user = $this->execute(RegisterUserCommand::class);
        
        Auth::login($user);

        Flash::success('Your registration has been successful. You are logged in now.');

        return Redirect::home();
}
```

I dont have any input validation or anything in this action as they are all done in the command pipeline. 


## Conclusion

This kinda patterns takes some time to sink your head in them, but once you have the core set up then its really feels much better working on the project and you feel confident and better about it's long term maintenance. I have learned most of it from this [Laracasts](https://laracasts.com/series/commands-and-domain-events) series and from many other sources. I will be writting more about it. Stay tuned! 

Hope you enjoyed it!