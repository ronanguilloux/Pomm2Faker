# Pomm2 Faker client

This package is a Pomm2 client to fill tables with data generated by [Faker](https://github.com/fzaninotto/Faker).

This package is still under development and has no tests hence there are bugs.

## Usage

Using the Client is fairly easy:

```php
<?php

use PommProject\Foundation\Pomm;

use PragmaFabrik\Pomm\Faker\FakerPooler;

use Faker\Factory as FakerFactory;

$loader  = require __DIR__.'/vendor/autoload.php';
$pomm    = new Pomm(['my_db' => ['dsn' => 'pgsql://user:pass@host:port/db_name']]);
// register Faker Pool Manager to the session
$session = $pomm['my_db']
    ->registerClientPooler(new FakerPooler(FakerFactory::create()))
    ;

 /*
Configure how we want data to be generated in the table `student`.
We do not want to insert any data for the `student_id` field nor the
`created_at`, they are both set by the database and will be returned by the
insert query.
We can use Faker's formatters directly using the `setFormatterType` method or
create a custom generator with the `setDefinition` method.
The untouched field `last_section` will be set to NULL by default.
*/
$session->getFaker('student')->getRowDefinition()
    ->unsetDefinition('student_id')
    ->setFormatterType('first_name', 'firstName')
    ->setFormatterType('last_name', 'lastName')
    ->setFormatterType('birthdate', 'dateTime', ['18 years ago'])
    ->setFormatterType('section', 'randomElement', [['A1', 'A2', 'A3']])
    ->setFormatterType('login', 'lastName')
    ->setDefinition('password', function($generator) {
        return sprintf("pass%s", $generator->format('password'));
    })
    ->unsetDefinition('created_at')
    ;

// Populate the database with 10 students and return the rows converted values.
$students = $session->getFaker('student')->save(10);
```

