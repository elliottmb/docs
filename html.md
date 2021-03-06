# Forms & HTML

- [Installation](#installation)
- [Opening A Form](#opening-a-form)
- [CSRF Protection](#csrf-protection)
- [Form Model Binding](#form-model-binding)
- [Labels](#labels)
- [Text, Text Area, Password & Hidden Fields](#text)
- [Checkboxes and Radio Buttons](#checkboxes-and-radio-buttons)
- [File Input](#file-input)
- [Number Input](#number)
- [Date Input](#date)
- [Drop-Down Lists](#drop-down-lists)
- [Buttons](#buttons)
- [Custom Macros](#custom-macros)
- [Generating URLs](#generating-urls)

<a name="installation"></a>
## Installation

Begin by installing this package through Composer. Edit your project's `composer.json` file to require `laravelcollective/html`.

```json
"require": {
    "laravelcollective/html": "5.1.*"
}
```

Next, update Composer from the Terminal:

    composer update

Next, add your new provider to the `providers` array of `config/app.php`:

```php
  'providers' => [
    // ...
    Collective\Html\HtmlServiceProvider::class,
    // ...
  ],
```

Finally, add two class aliases to the `aliases` array of `config/app.php`:

```php
  'aliases' => [
    // ...
      'Form' => Collective\Html\FormFacade::class,
      'Html' => Collective\Html\HtmlFacade::class,
    // ...
  ],
```

> Looking to install this package in <a href="http://lumen.laravel.com" target="_blank">Lumen</a>? First of all, making this package compatible with Lumen will require some core changes to Lumen, which we believe would dampen the effectiveness of having Lumen in the first place. Secondly, it is our belief that if you need this package in your application, then you should be using Laravel anyway.

<a name="opening-a-form"></a>
## Opening A Form

#### Opening A Form

```php
{!! Form::open(['url' => 'foo/bar']) !!}
	//
{!! Form::close() !!}
```

By default, a `POST` method will be assumed; however, you are free to specify another method:

```php
echo Form::open(['url' => 'foo/bar', 'method' => 'put'])
```

> **Note:** Since HTML forms only support `POST` and `GET`, `PUT` and `DELETE` methods will be spoofed by automatically adding a `_method` hidden field to your form.

You may also open forms that point to named routes or controller actions:

```php
echo Form::open(['route' => 'route.name'])

echo Form::open(['action' => 'Controller@method'])
```

You may pass in route parameters as well:

```php
echo Form::open(['route' => ['route.name', $user->id]])

echo Form::open(['action' => ['Controller@method', $user->id]])
```

If your form is going to accept file uploads, add a `files` option to your array:

```php
echo Form::open(['url' => 'foo/bar', 'files' => true])
```

<a name="csrf-protection"></a>
## CSRF Protection

#### Adding The CSRF Token To A Form

Laravel provides an easy method of protecting your application from cross-site request forgeries. First, a random token is placed in your user's session. If you use the `Form::open` method with `POST`, `PUT` or `DELETE` the CSRF token will be added to your forms as a hidden field automatically. Alternatively, if you wish to generate the HTML for the hidden CSRF field, you may use the `token` method:

```php
echo Form::token();
```

#### Attaching The CSRF Filter To A Route

```php
Route::post('profile', 
    [
        'before' => 'csrf',
        function()
        {
            //
        }
    ]
);
```

<a name="form-model-binding"></a>
## Form Model Binding

#### Opening A Model Form

Often, you will want to populate a form based on the contents of a model. To do so, use the `Form::model` method:

```php
echo Form::model($user, ['route' => ['user.update', $user->id]])
```

Now, when you generate a form element, like a text input, the model's value matching the field's name will automatically be set as the field value. So, for example, for a text input named `email`, the user model's `email` attribute would be set as the value. However, there's more! If there is an item in the Session flash data matching the input name, that will take precedence over the model's value. So, the priority looks like this:

1. Session Flash Data (Old Input)
2. Explicitly Passed Value
3. Model Attribute Data

This allows you to quickly build forms that not only bind to model values, but easily re-populate if there is a validation error on the server!

> **Note:** When using `Form::model`, be sure to close your form with `Form::close`!

<a name="labels"></a>
## Labels

#### Generating A Label Element

```php
echo Form::label('email', 'E-Mail Address');
```

#### Specifying Extra HTML Attributes

```php
echo Form::label('email', 'E-Mail Address', ['class' => 'awesome']);
```

> **Note:** After creating a label, any form element you create with a name matching the label name will automatically receive an ID matching the label name as well.

<a name="text"></a>
## Text, Text Area, Password & Hidden Fields

#### Generating A Text Input

```php
echo Form::text('username');
```

#### Specifying A Default Value

```php
echo Form::text('email', 'example@gmail.com');
```

> **Note:** The *hidden* and *textarea* methods have the same signature as the *text* method.

#### Generating A Password Input

```php
echo Form::password('password', ['class' => 'awesome']);
```

#### Generating Other Inputs

```php
echo Form::email($name, $value = null, $attributes = []);
echo Form::file($name, $attributes = []);
```

<a name="checkboxes-and-radio-buttons"></a>
## Checkboxes and Radio Buttons

#### Generating A Checkbox Or Radio Input

```php
echo Form::checkbox('name', 'value');

echo Form::radio('name', 'value');
```

#### Generating A Checkbox Or Radio Input That Is Checked

```php
echo Form::checkbox('name', 'value', true);

echo Form::radio('name', 'value', true);
```

<a name="number"></a>
## Number

#### Generating A Number Input

```php
echo Form::number('name', 'value');
```

<a name="date"></a>
## Date

#### Generating A Date Input

```php
echo Form::date('name', \Carbon\Carbon::now());
```

<a name="file-input"></a>
## File Input

#### Generating A File Input

```php
echo Form::file('image');
```

> **Note:** The form must have been opened with the `files` option set to `true`.

<a name="drop-down-lists"></a>
## Drop-Down Lists

#### Generating A Drop-Down List

```php
echo Form::select('size', ['L' => 'Large', 'S' => 'Small']);
```

#### Generating A Drop-Down List With Selected Default

```php
echo Form::select('size', ['L' => 'Large', 'S' => 'Small'], 'S');
```

#### Generating a Drop-Down List With an Empty Placeholder

This will create an `<option>` element with no value as the very first option of your drop-down.

```php
echo Form::select('size', ['L' => 'Large', 'S' => 'Small'], null, ['placeholder' => 'Pick a size...']);
```

#### Generating A Grouped List

```php
echo Form::select('animal',[
	'Cats' => ['leopard' => 'Leopard'],
	'Dogs' => ['spaniel' => 'Spaniel'],
]);
```

#### Generating A Drop-Down List With A Range

```php
echo Form::selectRange('number', 10, 20);
```

#### Generating A List With Month Names

```php
echo Form::selectMonth('month');
```

<a name="buttons"></a>
## Buttons

#### Generating A Submit Button

```php
echo Form::submit('Click Me!');
```

> **Note:** Need to create a button element? Try the *button* method. It has the same signature as *submit*.

<a name="custom-macros"></a>
## Custom Macros

#### Registering A Form Macro

It's easy to define your own custom Form class helpers called "macros". Here's how it works. First, simply register the macro with a given name and a Closure:

```php
Form::macro('myField', function()
{
	return '<input type="awesome">';
});
```

Now you can call your macro using its name:

#### Calling A Custom Form Macro

```php
echo Form::myField();
```

<a name="generating-urls"></a>
##Generating URLs

#### link_to

Generate a HTML link to the given URL.

```php
echo link_to('foo/bar', $title = null, $attributes = [], $secure = null);
```

#### link_to_asset

Generate a HTML link to the given asset.

```php
echo link_to_asset('foo/bar.zip', $title = null, $attributes = [], $secure = null);
```

#### link_to_route

Generate a HTML link to the given named route.

```php
echo link_to_route('route.name', $title = null, $parameters = [], $attributes = []);
```

#### link_to_action

Generate a HTML link to the given controller action.

```php
echo link_to_action('HomeController@getIndex', $title = null, $parameters = [], $attributes = []);
```
