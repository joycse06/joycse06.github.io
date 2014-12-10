---
layout: post
title: "Creating a very simple Artisan Startover Command in Laravel"
date: 2014-12-08 02:40:12 -0500
comments: true
categories: web laravel
---

While working on a recent project using Laravel I felt the need for a quick startover command, as we were brainstorming with ```domain``` objects and ```businees logic``` our migrations were changing frequently. So after each pull/change in migration I needed to reset the migrations, remigrate, seed database with updated seeder classes.

So I looked into creating a simple artisan command. The command is fairly straight forward, I am including the code for the command below.

<!-- more -->

``` php StartOverCommand.php

<?php

use Illuminate\Console\Command;
use Symfony\Component\Console\Input\InputOption;
use Symfony\Component\Console\Input\InputArgument;

class StartOverCommand extends Command {

	/**
	 * The console command name.
	 *
	 * @var string
	 */
	protected $name = 'appname:startover';

	/**
	 * The console command description.
	 *
	 * @var string
	 */
	protected $description = 'Reset Migration, remigrate, seed and Start Over';

	/**
	 * Execute the console command.
	 *
	 * @return mixed
	 */
	public function fire()
	{
		$this->info("Resetting the Migration.....");
        $this->call('migrate:reset');
        $this->info("Recreating schemas....");
        $this->call('migrate');
        $this->info('Seeding data.....');
        $this->call('db:seed');

	}
}

```
Only the relevant parts of the file is included here. BTW, you can create a boiler plate command using the ```php artisan command:make StartOverCommand``` command from terminal.

the name you set above will be the actual command, I have set it like ```appname:startover``` Its always a good practice to namespace things whenever possible.

Then the method that gets fired when the command is exectuted from terminal is ```fire()``` method. It does the same things I stated above which I did manually earlier.

After you add this file in ```app/commands/``` directory of your ```laravel``` installation, you have to add the command by adding the line  ```Artisan::add(new StartOverCommand);``` on your ```app/start/artisan.php``` file.

After all the above steps are done you can use it like ```php artisan appname:startover```  and it will reset migration, migrate migrations and then seed the tables.

Thanks for bearing with me. Hope you enjoyed it!

[Complete command file code](https://gist.github.com/joycse06/c03da3aaa31e416cfb0f)