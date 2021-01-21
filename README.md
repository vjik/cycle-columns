# Cycle Typecast

[![Latest Stable Version](https://poser.pugx.org/vjik/cycle-typecast/v/stable.png)](https://packagist.org/packages/vjik/cycle-typecast)
[![Total Downloads](https://poser.pugx.org/vjik/cycle-typecast/downloads.png)](https://packagist.org/packages/vjik/cycle-typecast)
[![Build status](https://github.com/vjik/cycle-typecast/workflows/build/badge.svg)](https://github.com/vjik/cycle-typecast/actions?query=workflow%3Abuild)
[![Mutation testing badge](https://img.shields.io/endpoint?style=flat&url=https%3A%2F%2Fbadge-api.stryker-mutator.io%2Fgithub.com%2Fvjik%2Fcycle-typecast%2Fmaster)](https://dashboard.stryker-mutator.io/reports/github.com/vjik/cycle-typecast/master)
[![static analysis](https://github.com/vjik/cycle-typecast/workflows/static%20analysis/badge.svg)](https://github.com/vjik/cycle-typecast/actions?query=workflow%3A%22static+analysis%22)

The package provides:

- `Typecaster` that help typecast data when mapping in [Cycle ORM](https://cycle-orm.dev/);
- `TypeInterface` that must be implemented by classes used in `Typecaster`;
- classes for `DateTimeImmutable`, `UUID` and `Array` types.

## Installation

The package could be installed with [composer](https://getcomposer.org/download/):

```shell
composer require vjik/cycle-typecast --prefer-dist
```

## General usage

```php
use Cycle\ORM\Mapper\Mapper;
use Cycle\ORM\ORMInterface;
use Vjik\CycleTypecast\Typecaster;
use Vjik\CycleTypecast\ArrayToStringType;
use Vjik\CycleTypecast\DateTimeImmutable\DateTimeImmutableToIntegerType;
use Vjik\CycleTypecast\UuidString\UuidStringToBytesType;

final class UserMapper extends Mapper
{
    private Typecaster $typecaster;

    public function __construct(ORMInterface $orm, string $role)
    {
        // Typecast configuration
        $this->typecaster = new Typecaster([
            'id' => new UuidStringToBytesType(),
            'create_date' => new DateTimeImmutableToIntegerType(),
            'modify_date' => new DateTimeImmutableToIntegerType(),
            'tags' => new ArrayToStringType(','),
        ]);
        
        parent::__construct($orm, $role);
    }

    public function extract($entity): array
    {
        $data = parent::extract($entity);
        
        // Typecast after extract from entity
        $this->typecaster->afterExtract($data);
        
        return $data;
    }

    public function hydrate($entity, array $data)
    {
        // Typecast before hydrate entity
        $this->typecaster->beforeHydrate($data);
        
        return parent::hydrate($entity, $data);
    }
}
```

## Types

### `ArrayToStringType`

```php
new ArrayToStringType(',');
``` 

Entity value: array of strings. For example, `['A', 'B', 'C']`.

Database value: array concatenated into string with delimiter setted in constructor. For example, `A,B,C`.

### `DateTimeImmutableToIntegerType`

```php
new DateTimeImmutableToIntegerType();
```

Entity value: `DateTimeImmutable`.

Database value: timestamp as string (example, `1609658768`).

### `UuidStringToBytesType`

```php
new UuidStringToBytesType();
```

Entity value: string standard representation of the UUID. For example, `1f2d3897-a226-4eec-bd2c-d0145ef25df9`.

Database value: binary string representation of the UUID.

## Testing

### Unit testing

The package is tested with [PHPUnit](https://phpunit.de/). To run tests:

```shell
./vendor/bin/phpunit
```

### Mutation testing

The package tests are checked with [Infection](https://infection.github.io/) mutation framework with
[Infection Static Analysis Plugin](https://github.com/Roave/infection-static-analysis-plugin). To run it:

```shell
./vendor/bin/roave-infection-static-analysis-plugin
```

### Static analysis

The code is statically analyzed with [Psalm](https://psalm.dev/). To run static analysis:

```shell
./vendor/bin/psalm
```

## License

The Cycle Typecast is free software. It is released under the terms of the BSD License.
Please see [`LICENSE`](./LICENSE.md) for more information.
