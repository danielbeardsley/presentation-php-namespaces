## Namespaces

The\worst\part\is\the\backslashes

But\you\get\over\it

---

### Logical grouping of your things

What it looks like:

@[1] (namespace declaration applies to whole file)
@[3-5] (All further declarations are in the namespace)
```php
namespace SomeNamespace\MyFeature

class Widget {
    ...
}
```

---

### What it means inside Widget

* Think:  `cd SomeNamespace\MyFeature`
* Global namespace:  `\`
* Everything else is relative
* No, you can't  `../`

---

### What it means outside Widget

#### Reference
`\SomeNamespace\MyFeature\Widget`

#### Aliases (`use`)
   * php: `use \Name\Path [as LocalAlias]`
   * Local alias defaults to the last component of the name
   * It's just string replacement

---

### What we do

* `\iFixit` -- all the things
   * `\iFixit\Framework`: Everything not feature specific
   * `\iFixit\Admin`: Features related to Admin
   * ... The future is unwritten

Classes should be grouped by feature, not type

---

### Notes:

* PSR4: Namespaces should match directories
  * Don't need to run  `composer install`
* Functions can be namespaced and used just like classes

* All classes are public :-(

---

## Caching

Four Decisions

@ul

* Which backend: `Cache::getBackend()`
* Key format: `"something-$id"`
* Expiration time: `CACHE_DAY`
* function to cache: `Guide::find()`

@ulend

---

### `class Cached {`

Like a promise, kinda.

* Represents a key/value pair in memcache
* Retreival: `->get()`
* Disposal: `->delete()`

---

### Making one

```php
Cached::byKey(
   $key,
   $valueFunction,
   $expireTime = CACHE_FOREVER,
   $cacheBackend
)
```

Also `Cached::memoizedByKey()`

---

### The Old Way

@[3,16] (Which Backend)
@[2,11-13,16] (Cache Key)
@[4] (Expiration)
@[7-10] (Value Function)

```php
function getTitleCached($guideid) {
   $key = getTitleCacheKey($guideid);
   return Cache::getBackend()->getAndSet(
      $key, 'getTitle', CACHE_HOUR)
}

function getTitle($guideid) {
   return $title;
}

function getTitleCacheKey($guideid) {
   return "guide-title-$guideid";
}

function deleteTitleCache($guideid) {
   Cache::getBackend()->delete(getTitleCacheKey($guideid));
}
```

---

### With `Cached`

@[2-5] (Static factory function)
@[1-6] (Usage: function that returns `Cached`)
@[8-12] (Callers get an object)
```php5
function cachedSomething(): Cached {
   return Cached::byKey("something-$id",
   function() use ($id) {
      return Something::find($id);
   }, CACHE_HOUR, Cache::getBackend());
}

// Then later:
$something = cachedSomething()->get()

// When things change:
cachedSomething()->delete()
```

