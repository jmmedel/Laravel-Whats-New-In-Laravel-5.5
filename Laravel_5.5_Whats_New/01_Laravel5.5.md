

At the time of this writing, Laravel 5.5 is not released yet. It is slated, as the next major release, for release in July 2017. To play around with new features and changes, you need to grab the dev release of Laravel using this Laravel Installer command:

Get Ahead with PHP 7.0+
The Next LTS (Long Term Support) Release
Welcome Back: Whoops!
"vendor:publish" Gets a Provider Prompt
Email Themes
Fresh Migrations
Automatic Package Discovery
Frontend Presets
Error Pages: Design Improvement
Custom Error Reporting
Streamlined Request Validation
Exception Helper Functions
Custom Validation Rules
Model Factory Generators
Miscellany
Conclusion
laravel new project --dev
Get Ahead with PHP 7.0+
Laravel 5.5 will require PHP 7.0+. Along with developer features, PHP 7 includes speed improvements which will decrease CPU load by half. So, you should definitely make this upgrade. And if you are using Laravel 5, making this upgrade will be a lot easier since you using the latest version of PHP.

The Next LTS (Long Term Support) Release
After Laravel 5.1, Laravel 5.5 is scheduled to be the next LTS release. This includes two years of bug fixes and three years of security updates.

Welcome Back: Whoops!
Whoops PHP Package

Whoops, an error-handling framework, which used to be used by Laravel 4, was removed with the release of Laravel 5.0. But with Laravel 5.5, it's coming back pre-installed. What Whoops actually does is that it makes the annoying PHP errors and exceptions a little less annoying by changing how they appear. As you can see in the image that it sports the lines of the file in which the error or exception occurred and traces back the operation till inception. And not forgetting to mention, it has zero dependencies (currently('pre-laravel-55')).

"vendor:publish" Gets a Provider Prompt
In previous versions of Laravel, running the vendor:publish command will publish views, configs, migrations and other resources of all vendors. But in Laravel 5.5, running this command will prompt you to select a provider or tag, making it easier to publish only the ones you want. You can also bypass this prompt by specifying the --all' or '--provider' flag with thepublish` command.

Email Themes
Laravel 5.5 adds the ability to specify a custom theme directly to Mailable classes. You can create a CSS style-sheet like this:

touch resources/views/vendor/mail/html/themes/batman.css
And then specify this filename as a property in your Mailable class.

class SendInvoice extends Mailable
{
    protected $theme = 'batman';
    ...
}
Rendering Mailables to the Browser
It can be tedious to test your email templates across email clients. One way to test them is to render them to the browser so that you can make instant changes. But it is not an easy task. Luckily, Laravel 5.5 adds the facility to display them directly from your routes.

You can create a Mailable like this:

php artisan make:mail UserWelcome --markdown=emails.user.subscription.canceled
And then render it through a route:

Route::get('/no/way', function () {
    return new App\Mail\UserSubscriptionCanceled();
});
There are other tools like Litmus, which solve this problem, but they are pretty expensive relative to this tediously trivial task.

Fresh Migrations
Laravel 5.5 adds another Artisan command to the migrate: namespace. This is similar to migrate:refresh but rather than rolling back your existing migrations, it drops all tables and migrates them from start. The difference between rolling back migrations and dropping tables is that rolling back migrations runs the drop method for each of them. Whilst, the migrate:fresh command simply drops all off of the tables and starts from scratch.

Automatic Package Discovery
In Laravel 5.4 and all versions before, pulling a Laravel package required registering its service providers and adding aliases. Laravel 5.5 adds the ability for packages to automatically register service providers and add aliases through the package's composer.json file like this:

"extra": {
    "laravel": {
        "providers": [
            "The\\Dark\\Knight\\BatmanServiceProvider"
        ],
        "aliases": {
            "Bar": "The\\Dark\\Knight\\Batman"
        }
    }
}
If you are a package developer, then things can't be much easier for your package users.

Frontend Presets
Out of the box, Laravel includes some CSS and JavaScript scaffolding to help accelerate coding the nitty-gritty. Although you could remove them and start over with your personal preferences, the suggestion was only limited to the Vue framework. Laravel 5.5 introduces 3 frontend presets: Bootstrap, Vue, React and an option choose your own. With Vue being the default preset, you can change the preset to react using this command:

php artisan preset react
You can change react in the above command to vue, bootstrap or none based on your preference.

Error Pages: Design Improvement
In Laravel 5.5, a little change is made in the design of the error pages like 404 or 50* errors: there are some design additions with Flexbox getting the error message centered on the page.

Before Laravel 5.5:

Before Laravel 5.5

In Laravel 5.5:

In Laravel 5.5

Custom Error Reporting
Laravel 5.5 adds support for defining a report method on any custom exception. In Laravel 5.4, you had to check in the Handler class's report method if a particular exception was thrown. So that you could do something like send an email or report to your app's monitoring service. You were doing something like this:

...

class Handler extends ExceptionHandler
{
    ...

    public function report(Exception $exception)
    {
        if ($exception instanceof CustomException) {
            // Send email
        }

        parent::report($exception);
    }
}
But with Laravel 5.5, you can get rid of this and register the report method on your custom exception class. Laravel checks to see if there is a report method on your exception class and if it does: calls it.

Streamlined Request Validation
Laravel 5.5 comes with two changes to Request feature. One is that you can now directly call the validate method on your Request instance. So instead of using the controller validator, you can call the validator on your Request instance. You also no longer need to pass the request as the first argument to the validator. Here is an example:

...

public function store()
{
    request()->validate([
        'title' => 'required',
        'body' => 'required'
    ]);

    return Post::create(request(['title', 'body']));
}
The second change made is that the validator returns the request data which you can store in a variable and pass on to the create method of the model.

...

public function store()
{
    $post = request()->validate([
        'title' => 'required',
        'body' => 'required'
    ]);

    // $data = request()->only('title', 'body');

    return Post::create($post);
}
You need to be careful with this since the data returned by the validator will only contain the fields defined in the rules. This adds a little security but you can loose data if some fields were not defined rules for. To avoid this trap, you can add the field with an empty rule like this:

public function store()
{
    $post = request()->validate([
        'title' => 'required',
        'body' => 'required',
        'fieldWithNoRules' => '',
        'andAnotherOne' => ''
    ]);

    // $data = request()->only('title', 'body');

    return Post::create($post);
}
Exception Helper Functions
Coming to Laravel 5.5 are two new helper functions to help you throw exceptions more elegantly. They are: throw_if and throw_unless, and they do exactly what they say. If you want to throw an exception based on a condition then these may help you reduce a conditional block to a single line. They both accept three arguments with the third being optional. First one is a boolean, second is the exception class and third is the exception message passed in case if you didn't pass with the instantiation of the exception in the second argument. throw_if throws the exception if the boolean is positive and throw_unless throws the exception when the boolean is negative. Here are the examples:

// For `throw_if:

$foo = true;
throw_if($foo, new BarException('Foo is true'));
// or 
throw_if($foo, BarException::class, 'Foo is true');

// For `throw_unless:

$phoo = false;
throw_unless($phoo, new BazException('Phoo is false'));
// or
throw_unless($phoo, BazException::class, 'Phoo is false');
Custom Validation Rules
Laravel 5.5 is coming with a new feature for adding custom validation rules. Custom validation rules are nothing new but with Laravel 5.5 you can have a dedicated class to handle the validation. To define a custom validation rule, you need to create a class with two methods: passes and message. You can place this class anywhere like in the App\Rules namespace. The passes method accepts two arguments: attribute and value, which you can use to validate the field.

<?php

namespace App\Rules;

use Illuminate\Contracts\Validation\Rule;

class CustomRule implements Rule
{   
    /**
     * Determine if the validation rule passes.
     *
     * @param  string  $attribute
     * @param  mixed  $value
     * @return bool
     */
    public function passes($attribute, $value)
    {
        // must return true or false for the validation to pass or fail
    }

    /**
     * Get the validation error message.
     *
     * @return string
     */
    public function message()
    {
        // return a string here for the failing condition
    }
}
Your custom rule should implement the Laravel's Illuminate\Contracts\Validation\Rule contract. You can use this custom validation rule anywhere like in a Form Request class or in the controller validator or the validator from the Request instance. If you are using a custom rule then you can't pass a string with rules separated by a comma. You need to pass each rule as a single element grouped in an array like this:

$request->validate([
    'someField' => [
        'required', 'min:4', new CustomRule()
    ]
]);
You may also inject any dependency to the constructor if you want. This class-based approach makes custom validation a lot easier.

Model Factory Generators
In Laravel 5.5 you can easily generate model factories with a new Artisan command called make:factory. Here is an example:

php artisan make:factory PostFactory
This will make a new file called PostFactory.php in the database/factories folder. What makes it a lot better is that you can also generate a factory while making a model.

php artisan make:model Post -f
You may also pass the -c flag to add a controller and the -m flag to add a migration. This will help in quickly whipping up a resource.

Miscellany
One minor change is that if you forget to include the CSRF field in a form then it presents you with better inactivity error page. So, if you see something like that, then be sure that it's related to CSRF.

There is another minor change that will help you in building APIs. Now you get a JSON stack trace rather than HTML markup if an error occurs. This makes it a lot prettier to look at rather than the ugly markup if you are using a tool like Postman.

Conclusion
I hope this guide gets you prepared for the forthcoming goodness. So, This is all there is coming to Laravel 5.5.

Happy Coding!

