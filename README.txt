# DrupalCamp Denver - Developing Modules

## First Module

* Info file @demo.info@
* Module file @demo.module@

```php
name = demo
description = Just a simple demo module
core = 7.x

// Hooking in to nodeapi
function demo_nodeapi(&$node, $op_) {
  global $user;
  if ($op == 'view') {
    if ($node->uid == $user->uid {
      $node->title . ' [YOUR ARTICLE]';
    }
  }
}
```

* Anytime someone says hook_something you replace hook with your module name
* You might also involve an install file
** Where you hold logic that only runs during install or update

```php
function tricky_install() {
  drupal_set_message('You just installed Tricky!');
}
```

## Routing a Request

* The @.htaccess@ file routes all requests through @index.php@
** @index.php@ send the request through a bootstrap process and then looks for a @menu.inc@
* Your module can implement @menu@ and then provide urls
** @function mymodule_menu()@
** @return "Some text!"@ would just render some text to the screen
* Based on the request from the browser and what Drupal found it generates the best match
** Is @welcome.html@ an alias?
** Is it a path?
** Essentially use hook_menu to map paths to functions
** The @menu@ system is the router

## Hook System

* The hook system is an event listener pattern that utilizes specially named functions
* Create hooks with @module_invoke_all('foo')@
** Ask all modules if they implement @foo@ and then invoke it
** @yourmodule_foo()@ would use it
** Namespace your hook with you module name to prevent collisions
* Hook Node View Examples

```php
// Appends [YOUR ARTICLE] to the node title if the logged in user is the owner of the node
function demo_node_view($node, $view_node, $language) {
  global $user;
  if ($node->uid == $user->uid) {
    $node->title . ' [YOUR ARTICLE]';
  }
}
```

* The order of hooks getting called is based on weight and alphabetical order
* Objects are implictely passed by reference in PHP 5.2+ which is what Drupal 7 uses
* PHP tip: @var .= ' TEXT'@ will concatenate and update the var

## Captain Module

* Load in to @d7/sites/all/modules/captain@
** Don't load in to the @d7/modules@ folder as it is for core only
* Enable through Module tab in Drupal UI
* @t@ is a Drupal Core function for translating user facing text
** @t@ can also take placeholders so the strings can be dynamic
** @t@ runs text through @check_plain@ which does some string escaping for security
* Nodes have url's and Blocks do not
** Nodes are primary content
** Blocks are secondary content
*** They can contain content
*** They can be placed on many nodes
* @$view_mode@ is the context in which a piece of content is being viewed
** Viewing a full page vs a list or RSS feed
* @$_SESSION@ is a super global for storing stuff related to session
* @devel@ Module provides a number of functions for development
** @dsm@ or @dpm@ will print out in a nicely formatted way the object
* A good tip is to prefix all data being added with your module name for debugging
* @$account@ is the user viewing the page
* @$user@ is the logged in user
* @global@ pulls in a global variable to be used locally
* @user_load@ will load a user object from the database 
