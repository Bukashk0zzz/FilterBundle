# Symfony Filter Bundle

[![Code Coverage](https://img.shields.io/codecov/c/github/Bukashk0zzz/FilterBundle.svg?style=flat-square)](https://codecov.io/github/Bukashk0zzz/FilterBundle)
[![License](https://img.shields.io/packagist/l/Bukashk0zzz/filter-bundle.svg?style=flat-square)](https://packagist.org/packages/Bukashk0zzz/filter-bundle)
[![Latest Stable Version](https://img.shields.io/packagist/v/Bukashk0zzz/filter-bundle.svg?style=flat-square)](https://packagist.org/packages/Bukashk0zzz/filter-bundle)
[![Total Downloads](https://img.shields.io/packagist/dt/Bukashk0zzz/filter-bundle.svg?style=flat-square)](https://packagist.org/packages/Bukashk0zzz/filter-bundle)

About
-----
This bundle add a service that can be used to filter object values based on annotations. [Laminas filter](https://github.com/laminas/laminas-filter) old [ZendFilters](https://github.com/zendframework/zend-filter) used for filtering.
Also bundle can filter your forms if it finds a annotated entity attached. If `auto_filter_forms` enabled entities will be filtered before they are validated.
[Laminas filters doc](https://docs.laminas.dev/laminas-filter/)

Installation Symfony Flex
------------

```bash
composer config extra.symfony.allow-contrib true
composer require bukashk0zzz/filter-bundle
```

Installation without Symfony Flex
------------

```bash
composer require bukashk0zzz/filter-bundle
```

Add the bundle to `app/AppKernel.php`

```php
$bundles = array(
	// ... other bundles
	new Bukashk0zzz\FilterBundle\Bukashk0zzzFilterBundle(),
);
```

Configuration
-------------

Add this to your `config.yml`:

```yaml
bukashk0zzz_filter:
    # Enable if you need auto filtering form data before constraint(Validation) check
    auto_filter_forms: false
```

Usage
------

Bundle provides one annotation which allow filter fields in your entities.

Add the next class to the `use` section of your entity class.

```php
use Bukashk0zzz\FilterBundle\Annotation\FilterAnnotation as Filter;
```

Annotation `@Filter` has one required option *filter* which value should be name of Laminas filter class.
It can be set like this `@Filter("StringTrim")` or `@Filter(filter="AppBundle\Filter\MyCustomFilter")`.

`AppBundle\Filter\MyCustomFilter` - in this example, must be class that extends `\Laminas\Filter\AbstractFilter`

Also there is one not required option `options` - it must be array type and will pass to Laminas filter using `setOptions` method from Laminas filter. 

Example entity
--------------

```php
<?php
namespace AppBundle\Entity;

use Bukashk0zzz\FilterBundle\Annotation\FilterAnnotation as Filter;

/**
 * User Entity
 */
class User
{
    #[Filter(parameters: [
        'filter' => 'StripTags',
        'options' => ['allowTags' => 'br']
    ])]
    #[Filter(parameters: ['filter' => 'StringTrim'])]
    #[Filter(parameters: ['filter' => 'StripNewlines'])]
    protected $name;

    #[Filter(parameters: ['filter' => 'StripTags'])]
    #[Filter(parameters: ['filter' => 'StringTrim'])]
    #[Filter(parameters: ['filter' => 'AppBundle\Filter\MyCustomFilter'])]
    protected $about;
}

```

Using filter service
--------------------

Use the `bukashk0zzz_filter.filter` service along with annotations in the Entity to filter data.

```php
public function indexAction()
{

    $entity = new \Acme\DemoBundle\Entity\SampleEntity();
    $entity->name = "My <b>name</b>";
    $entity->email = " email@mail.com";

    $filterService = $this->get('bukashk0zzz_filter.filter');
    $filterService->filterEntity($entity);

    return ['entity' => $entity];
}
```

Copyright / License
-------------------

See [LICENSE](https://github.com/bukashk0zzz/FilterBundle/blob/master/LICENSE)
