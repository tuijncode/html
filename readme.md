# Html

<a href="https://packagist.org/packages/tuijncode/html"><img src="https://poser.pugx.org/tuijncode/html/d/total.svg" alt="Total Downloads"></a>
<a href="https://packagist.org/packages/tuijncode/html"><img src="https://poser.pugx.org/tuijncode/html/v/stable.svg" alt="Latest Stable Version"></a>
<a href="https://packagist.org/packages/tuijncode/html"><img src="https://poser.pugx.org/tuijncode/html/license.svg" alt="License"></a>

## Install

```
composer require tuijncode/html
```

## Important

Since this package internally uses strict comparisons (**===** instead of **==**) be careful when passing numeric values into your forms. Values in HTML are submitted as strings and Laravel old values stored in flash session are strings.

In this example, this package will correctly insert **selected** HTML attribute into the radio input - because the passed value '1' strictly equals to the old submitted value in the session **'1'**:

```
{{ Form::radio('category_id', '1') }} // '1' === '1' - comparing passed value and old session value
```
However, in this example, the passed integer value **1** is not strictly equal to the old submitted string value **'1'** in the session and the **selected** HTML attribute will not be inserted:

## Opening A Form

### Opening A Form

```
{!! Form::open(['url' => 'foo/bar']) !!}
    //
{!! Form::close() !!}
```

By default, a **POST** method will be assumed; however, you are free to specify another method:

```
{{ Form::open(['url' => 'foo/bar', 'method' => 'put']) }}
```

_**Note:** Since HTML forms only support **POST** and **GET**, **PUT** and **DELETE** methods will be spoofed by automatically adding a **_method** hidden field to your form._

You may also open forms that point to named routes or controller actions:

```
{{ Form::open(['route' => 'route.name']) }}

{{ Form::open(['action' => 'Controller@method']) }}
```

You may pass in route parameters as well:

```
{{ Form::open(['route' => ['route.name', $user->id]]) }}

{{ Form::open(['action' => ['Controller@method', $user->id]]) }}
```

If your form is going to accept file uploads, add a **files** option to your array:

```
{{ Form::open(['url' => 'foo/bar', 'files' => true]) }}
```

# CSRF Protection

## Adding The CSRF Token To A Form

Laravel provides an easy method of protecting your application from cross-site request forgeries. First, a random token is placed in your user's session. If you use the **Form::open** method with **POST**, **PUT** or **DELETE** the CSRF token will be added to your forms as a hidden field automatically. Alternatively, if you wish to generate the HTML for the hidden CSRF field, you may use the **token** method:

```
{{ Form::token() }}
```

## Attaching The CSRF Filter To A Route

```
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

# Form Model Binding

## Opening A Model Form

Often, you will want to populate a form based on the contents of a model. To do so, use the **Form::model** method:

```
{{ Form::model($user, ['route' => ['user.update', $user->id]]) }}
```

Now, when you generate a form element, like a text input, the model's value matching the field's name will automatically be set as the field value. So, for example, for a text input named **email**, the user model's **email** attribute would be set as the value. However, there's more! If there is an item in the Session flash data matching the input name, that will take precedence over the model's value. So, the priority looks like this:

1. Session Flash Data (Old Input)
2. Explicitly Passed Value
3. Model Attribute Data

This allows you to quickly build forms that not only bind to model values, but easily re-populate if there is a validation error on the server!

_**Note:** When using Form::model, be sure to close your form with **Form::close**!_

## Form Model Accessors

Laravel's [Eloquent Accessor](https://laravel.com/docs/10.x/eloquent-mutators) allow you to manipulate a model attribute before returning it. This can be extremely useful for defining global date formats, for example. However, the date format used for display might not match the date format used for form elements. You can solve this by creating two separate accessors: a standard accessor, and/or a form accessor.

To define a form accessor, create a **formFooAttribute** method on your model where **Foo** is the "camel" cased name of the column you wish to access. In this example, we'll define an accessor for the **date_of_birth** attribute. The accessor will automatically be called by the HTML Form Builder when attempting to pre-fill a form field when **Form::model()** is used.

You must include the FormAccessible trait in your model definition for this to work.

```
<?php

namespace App;

use Carbon\Carbon;
use Illuminate\Database\Eloquent\Model;
use Tuijncode\Html\Eloquent\FormAccessible;

class User extends Model
{
    use FormAccessible;

    /**
     * Get the user's date of birth.
     *
     * @param  string  $value
     * @return string
     */
    public function getDateOfBirthAttribute($value)
    {
        return Carbon::parse($value)->format('m/d/Y');
    }

    /**
     * Get the user's date of birth for forms.
     *
     * @param  string  $value
     * @return string
     */
    public function formDateOfBirthAttribute($value)
    {
        return Carbon::parse($value)->format('Y-m-d');
    }
}
```

# Labels

## Generating A Label Element

```
{{ Form::label('email', 'E-Mail Address') }}
```
## Specifying Extra HTML Attributes

```
{{ Form::label('email', 'E-Mail Address', ['class' => 'awesome']) }}
```

_**Note:** After creating a label, any form element you create with a name matching the label name will automatically receive an ID matching the label name as well._

# Text, Text Area, Password & Hidden Fields

## Generating A Text Input

```
{{ Form::text('username') }}
```

## Specifying A Default Value

```
{{ Form::text('email', 'example@gmail.com') }}
```

_**Note:** The hidden and textarea methods have the same signature as the text method._

## Generating A Password Input

```
{{ Form::password('password', ['class' => 'awesome']) }}
```

## Generating Other Inputs

```
{{ Form::email($name, $value = null, $attributes = []) }}
{{ Form::file($name, $attributes = []) }}
```

# Checkboxes and Radio Buttons

## Generating A Checkbox Or Radio Input

```
{{ Form::checkbox('name', 'value') }}
{{ Form::radio('name', 'value') }}
```

## Generating A Checkbox Or Radio Input That Is Checked

```
{{ Form::checkbox('name', 'value', true) }}
{{ Form::radio('name', 'value', true) }}
```
# Number

## Generating A Number Input

```
{{ Form::number('name', 'value') }}
```

# Date

## Generating A Date Input

```
{{ Form::date('name', \Carbon\Carbon::now()) }}
```

# File Input

## Generating A File Input

```
{{ Form::file('image') }}
```

_**Note:** The form must have been opened with the files option set to true._

# Drop-Down Lists

## Generating A Drop-Down List

```
{{ Form::select('size', ['L' => 'Large', 'S' => 'Small']) }}
```

## Generating A Drop-Down List With Selected Default

```
{{ Form::select('size', ['L' => 'Large', 'S' => 'Small'], 'S') }}
```

## Generating a Drop-Down List With an Empty Placeholder

This will create an **<option>** element with no value as the very first option of your drop-down.

```
{{ Form::select('size', ['L' => 'Large', 'S' => 'Small'], null, ['placeholder' => 'Pick a size...']) }}
```

## Generating A Grouped List

```
{{ Form::select('animal',[
    'Cats' => ['leopard' => 'Leopard'],
    'Dogs' => ['spaniel' => 'Spaniel'],
]) }}
```

## Generating A Drop-Down List With A Range

```
{{ Form::selectRange('number', 10, 20) }}
```

## Generating A List With Month Names

```
{{ Form::selectMonth('month') }}
```

# Buttons

## Generating A Submit Button

```
{{ Form::submit('Click Me!') }}
```

_**Note:** Need to create a button element? Try the button method. It has the same signature as submit._

# Custom Macros

## Registering A Form Macro

It's easy to define your own custom Form class helpers called "macros". Here's how it works. First, simply register the macro with a given name and a Closure:

```
Form::macro('myField', function()
{
    return '<input type="awesome">';
});
```

Now you can call your macro using its name:

## Calling A Custom Form Macro

```
{{ Form::myField() }}
```

# Custom Components

## Registering A Custom Component

Custom Components are similar to Custom Macros, however instead of using a closure to generate the resulting HTML, Components utilize [Laravel Blade Templates](https://laravel.com/docs/10.x/blade). Components can be incredibly useful for developers who use [Twitter Bootstrap](https://getbootstrap.com/), or any other front-end framework, which requires additional markup to properly render forms.

Let's build a Form Component for a simple Bootstrap text input. You might consider registering your Components inside a Service Provider's **boot** method.

```
Form::component('bsText', 'components.form.text', ['name', 'value', 'attributes']);
```

Notice how we reference a view path of **components.form.text**. Also, the array we provided is a sort of method signature for your Component. This defines the names of the variables that will be passed to your view. Your view might look something like this:

```
// resources/views/components/form/text.blade.php
<div class="form-group">
    {{ Form::label($name, null, ['class' => 'control-label']) }}
    {{ Form::text($name, $value, array_merge(['class' => 'form-control'], $attributes)) }}
</div>
```

_Custom Components can also be created on the **Html** facade in the same fashion as on the **Form** facade._

### Providing Default Values

When defining your Custom Component's method signature, you can provide default values simply by giving your array items values, like so:

```
{{ Form::component('bsText', 'components.form.text', ['name', 'value' => null, 'attributes' => []]) }}
```

## Calling A Custom Form Component

Using our example from above (specifically, the one with default values provided), you can call your Custom Component like so:

```
{{ Form::bsText('first_name') }}
```

This would result in something like the following HTML output:

```
<div class="form-group">
    <label for="first_name">First Name</label>
    <input type="text" name="first_name" value="" class="form-control">
</div>
```

# Generating URLs

## link_to

Generate a HTML link to the given URL.

```
{{ link_to('foo/bar', $title = null, $attributes = [], $secure = null) }}
```

## link_to_asset

Generate a HTML link to the given asset.

```
{{ link_to_asset('foo/bar.zip', $title = null, $attributes = [], $secure = null) }}
```

## link_to_route

Generate a HTML link to the given named route.

```
{{ link_to_route('route.name', $title = null, $parameters = [], $attributes = []) }}
```

## link_to_action

```
{{ link_to_action('HomeController@getIndex', $title = null, $parameters = [], $attributes = []) }}
```




