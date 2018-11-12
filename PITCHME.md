# Namespaces

The\worst\part\is\the\backslashes

But\you\get\over\it

---

## Logical grouping of your things

What it looks like:

```php
namespace SomeNamespace\MyFeature

class Widget {
    ...
}
```

---

## What it means inside Widget

* Think:  `cd SomeNamespace\MyFeature`
* Global namespace:  `\`
* Everything else is relative
* No, you can't  `../`

---

## What it means outside Widget

Reference:  `\SomeNamespace\MyFeature\Widget`
* Aliases (`use`)
   * `use \Name\Path [as LocalAlias]`
   * Local alias defaults to the last component of the name
   * It's just string replacement

---

## What we do
* \iFixit -- all the things
   * \iFixit\Framework: Everything not feature specific
   * \iFixit\Admin: Features related to Admin
   * ... The future is unwritten

Classes should be grouped by feature, not type

---

## Notes:

* PSR4: Namespaces should match directories
  * Don't need to run  `composer install`
* Functions can be namespaced and used just like classes

* All classes are public :-(

---

## Caching

The old way allowed related
things to be separated

* Which backend
* Key format
* Expiration time
* function to cache

Easy to mistake

---

## Cached

```php
function cachedSomething(): Cached {
   return Cached::byKey("something-$id", function() use ($id) {
      return Something::find($id);
   }, CACHE_HOUR, Cache::getBackend());
}

$something = cachedSomething()->get()

cachedSomething()->delete()
```

---
