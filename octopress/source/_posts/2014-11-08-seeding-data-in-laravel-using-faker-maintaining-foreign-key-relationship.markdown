---
layout: post
title: "Seeding data in Laravel using Faker maintaining foreign key relationship"
date: 2014-11-08 01:42:32 -0500
comments: true
categories: web laravel
---

[Faker](https://github.com/fzaninotto/Faker) is a great composer package for generating random data which can be used for seeding database tables for testing purposes in Laravel and other frameworks.

Suppose we have a users table and a companies table with the following structure.

```
# Table Structure
users:
    id
    username
    password
companies:
    id
    user_id
    name

```
<!-- more -->

For the sake of simplicity lets assume we dont have any other fields in those tables.

Now if we want to insert seed data in those two tables how would we do that in laravel. We would need two seeders one for ```users``` table and another for ```companies``` table.

Suppose we have Models corresponding to those two entities are already there as ```User``` and ```Company``` in Laravel.

<!-- more -->

adding seed users in users table is pretty straight forward. We can do it like

``` php UsersTableSeeder.php

<?php
use Faker\Factory as Faker;
use Illuminate\Database\Seeder;
class UsersTableSeeder extends Seeder{
    public function run()
    {
        $faker = Faker::create();
        $candidates = Candidate::lists('id');
        foreach(range(1, 50) as $index)
        {
            User::create([
                'username' => $faker->userName(),
                'password' => 'secret'
            ]);
        }
    }
}
```

The above seeder will create 50 users for us in database. Now comes the real problem which is the purpose of this blog post. In ```companies``` table we have a field named ```user_id``` we can just use random integers into that field as that will break relationships between tables. Whatever we want to put in this field that user has to be present in the database already for the models relationships to work properly.

Fortunately, with Laravel thats easy too. Lets have a look at the companies seeder.

``` php CompaniesTableSeeder

<?php

use Illuminate\Database\Seeder;
use Faker\Factory as Faker;

class CompaniesTableSeeder extends Seeder{
    public function run(){
        $faker = Faker::create();
        // following line retrieve all the user_ids from DB
        $users = User::all()->lists('id');
        foreach(range(1,50) as $index){
            $company = Company::create([
                'name' => $faker->company,
                'user_id' => $faker->randomElement($users),
            ]);
        }
    }
}

```

So first step is to retrieve all the users's id we inserted in ```UsersTableSeeder``` then randomly assign those in the ```user_id``` field of companies table.

We can get the value of any column of a table using ```lists(COLUMN_NAME)``` Eloquent method. Here we need the value of ```id``` column. Now after executing ```$users = User::all()->lists('id');``` we will have an array filled with all current users IDs from DB, so in that time those are all valid users.

Faker has a method named ```randomElement()``` which takes an array and randomly returns value from that array. So the ```user_id``` column of the ```companies``` table will be filled with real user id from DB. And after proper relationships are set we will be able to do things like

``` php

$company = Company::find(1);
$company_owner = $company->user(); // return a User object

```

Thats how you can seed your database using ```Faker``` maintaining model relationships.

One thing remember, for this case you must have to run ```UsersTableSeeder``` before running ```CompaniesTableSeeder```.

Thats all for today. Hope you enjoyed it.

