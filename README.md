[![Build status](https://api.travis-ci.org/api-skeletons/doctrine-orm-hydration-module.svg)](http://travis-ci.org/api-skeletons/zf-doctrine-hydration-module)
[![Packagist](https://img.shields.io/packagist/v/api-skeletons/zf-doctrine-hydration-module.svg)](https://packagist.org/packages/api-skeletons/zf-doctrine-hydration-module)
[![Downloads](https://img.shields.io/packagist/dt/api-skeletons/zf-doctrine-hydration-module.svg)](https://packagist.org/packages/api-skeletons/zf-doctrine-hydration-module)

# Doctrine Hydration Module
This module provides a configurable way to create new doctrine hydrators.
By using the configurable API, it is easy to create a custom hydrator for any use case you want.

For ORM, the basic hydrator from the doctrine module is being used. It is possible to configure your own strategies for complex objects like referenced entities.

For MongoDB ODM, a specific hydrator is added. This hydrator will be able to handle Referenced documents and Embedded Documents.
It is also possible to hydrate advanced documents with discriminator maps.

# Supported features 

* 1.0.x: A rewrite and editing of phpro/zf-doctrine-hydration-module requiring PHP 7.4+

# Installation

## Add to composer.json
```
composer require api-skeletons/doctrine-orm-hydration-module
```

## Add to application config
```php
return array(
    'modules' => array(
        'ApiSkeletons\\DoctrineORMHydrationModule',
        // other libs...
    ),
    // Other config
);
```

### Hydrator configuration
```php
return array(
    'doctrine-hydrator' => array(
        'hydrator-manager-key' => array(
            'entity_class' => 'App\Entity\EntityClass',
            'object_manager' => 'doctrine.objectmanager.key.in.servicelocator',
            'by_value' => true,
            'use_generated_hydrator' => true,
            'naming_strategy' => 'custom.naming.strategy.key.in.servicemanager',
            'hydrator' => 'custom.hydrator.key.in.hydratormanager',
            'strategies' => array(
                'fieldname' => 'custom.strategy.key.in.servicemanager',
            ),
            'filters' => array(
                'custom_filter_name' => array(
                    'condition' => 'and', // optional, default is 'or'
                    'filter'    => 'custom.hydrator.filter.key.in.servicemanager',
                ),
            ),
        ),
    ),
);
```

**`entity_class`**
 
This property is used to specify the class of the entity that will be hydrated. You need to make sure that this entity is a mapped doctrine class.

  
**`object_manager`**

You can specify which object manager you want to use for the hydrator. The value is the key of the desired object manager in the service manager.


**`by_value`**

Specify if you want the hydrator to hydrate the entity by value or by reference.


**`use_generated_hydrator`**

This property will only be used with mongoDB ODM and will use the generated hydrators instead of the Doctrine Module Hydrator.
Strategies will not work when this option is set to `true`.


**`naming_strategy`**

You can use a custom naming strategy for the hydrator. Specify the key of the naming strategy in the service manager.
Note that this naming strategy needs to implement `NamingStrategyInterface`.

**`hydrator`**
You can use a custom hydrator instead of the default `DoctrineObject` hydrator. 
Make sure this hydrator implements `HydratorInterface`. 


**`strategies`**

It is possible to customize the hydration strategy of specific properties. 
Configure the property you want to customize with the key of the strategy in the service manager;
Note that this strategy needs to implement `StrategyInterface`.


**`filters`**

This property can be used to apply filters on the Hydrator. 
You can specify a list of custom filters by defining the key of the filter in the service manager and the filter condition as described in the hydrator filter documentation.
Note that this filter needs to implement `FilterInterface`.


From here on, you can get the hydrator by calling `get('hydrator-manager-key')` on the HydratorManager.

# Custom Filters

Custom filters allow you to fine-tune the results of the hydrator's `extract` functionality by determining which fields should be extracted. 

To configure custom filters:
```php
return array(
    'doctrine-hydrator' => array(
        'custom-hydrator' => array(
            // other config
            'filters' => array(
                'custom.filter.name' => array(
                    'condition' => 'and', //optional, default: FilterComposite::CONDITION_OR,
                    'filter' => 'custom.filter', // a name in the Service Manager
                ),
            ),
        ),
    ),
);

```
In this example configuration, the hydrator factory will retrieve `custom.filter` from the Service Manager and inject it as a filter into the hydrator. The filter must implement `Zend\Hydrator\Filter\FilterInterface`. 

The service's `filter($fieldName)` function will be called by the hydrator during `extract` and the field name being extracted will be passed as an argument. The `filter()` function must return a truthy value: if `true` then the field will NOT be extracted.


# Override hydrator:

If the standard DoctrineHydrator is not flexible enough, you can set a custom `hydrator`. This allows you to use an extended DoctrineHydrator or another existing hydrator, and configure it with this module. This setting will override `use_generated_hydrator`.

```php
return array(
    'doctrine-hydrator' => array(
        'custom-hydrator' => array(
            // other config
            'hydrator' => 'Zend\Hydrator\ArraySerializable'
        ),
    ),
);
```

# Testing
This package is fully tested with Cs fixer and PhpUnit. The MongoDB tests require mongodb to be installed on your machine. You can skip these tests by adding a testsuite to the command:
```sh
# Php coding standards:
./vendor/bin/phpcs


# Phpunit:
./vendor/bin/phpunit
```
