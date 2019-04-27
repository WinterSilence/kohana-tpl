## Template module for Koseven framework (Kohana fork)

Based on the [KO7_View](../ko7/mvc/views), has a number of additional methods.
Supported template engines:
- Native PHP
- [Smarty](http://smarty.net)
- [Twig](http://twig.symfony.com)
- [Fenom](http://github.com/bzick/fenom)

Contains [Controller_Layout](http://github.com/WinterSilence/kohana-template/blob/master/classes/KO7/Controller/Layout.php), 
extended [KO7_Controller_Template](https://github.com/koseven/koseven/blob/devel/system/classes/KO7/Controller/Template.php).

### Views
~~~
// Create view using default adapter
$view = Template::factory('news/list', ['news' => $posts]);
// Create view using Twig adapter
$view = Template::factory('news/list', ['news' => $posts], 'twig');
~~~
~~~
// Change current adapter
$view->adapter('smarty');
~~~
~~~
// Delete only local variables
$view->clear();
// Delete all variables
$view->clear(TRUE);
~~~
~~~
// Render content
$content = $view->render();
// Change template and adapter, render content and delete local variables
$content = $view->render($template, $adapter, TRUE);
~~~

### KO7 helper classes in template

**Smarty**

Calling KO7 helpers occurs without any problems.
~~~
<base href="{URL::base()}">
<title>{$title|default:'...'}</title>
<meta charset="{KO7::$charset}">
~~~

**Twig**

To use a helper is necessary to register them in config option`template.twig.globals`.
Use a dot instead of a double colon for dividing the class and method in template.
~~~
<base href="{{ URL.base() }}">
<title>{{ title|default('...') }}</title>
<meta charset="{{ KO7.charset }}">
~~~

**Fenom**

Accepted only if config option `template.fenom.options.disable_call` is `FALSE`.
~~~
<base href="{$.call.URL::base()}">
<title>{$title ?: '...'}</title>
<meta charset="{$.call.KO7::charset}">
~~~

### Controller_Layout

The controller uses 2 nested template:
- `$this->template` - main content
- `$this->layout` - wrapper/theme for main content, `$this->layout->content` contains `$this->template`

This approach allows incrementally generate page content. 
It is necessary for the formation of convenient page head container 
and the convenience of connecting widgets/snippets in the theme/page.

If `$this->template` filename not set, it's automatically generated by request (directory, controller and action).

~~~
class Controller_Frontend_News extends Controller_Layout {
	public function action_list()
	{
		// $this->template = 'frontend/news/list'
		// Adds banner to layout using  HMVC request
		$this->layout->banner = Request::factory('banners/news')->execute();
		// Adds news to template using ORM
		$this->template->news = ORM::factory('News')->find_all();
	}
}
~~~
