# Get the rendered PHP blade template content in Laravel 5

There was a scenario I recently encountered, whereby it was especially useful to retrieve the rendered contents
of the PHP blade template in Laravel 5, before simply returning it to the browser. I wanted access to the HTML
view as a string, that I could further manipulate myself, before returning it to the browser.

Let's jump right in and say that our PHP blade template in Laravel lives at `resources/views/sample.blade.php`,
and this is what the contents are made up of:

```
<div>
  <p>This is {{$name}}.</p>
</div>
```

We've got a simple route in <code>app/Http/routes.php</code>, which calls a method from our controller:

```
Route::get('/whoami', 'SampleController@getName');
```

And within our controller (`app/Http/Controllers/SampleController.php`), we call the all important `render`
method on the returned view:

```
namespace App\Http\Controllers;

use App\Http\Controllers\Controller;

class SampleController extends Controller
{
  public function getName()
  {
    $contents = view('sample', ['name' => 'Code Chewing'])->render();
    // do some other manipulation?
    return $contents;
  }
}
```

The `render` method comes from the `View` class in `vendor/laravel/framework/src/Illuminate/View/View.php`.

### Farewell

You now have an opportunity to access the populated template, in string form, before handing it off to the browser.
