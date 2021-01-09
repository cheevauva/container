# PDIC

Меленький и в тоже время мощный контейнер внедрения зависимостей через публичные свойства

## Терминология

Карта зависимостей - массив, содержащий отношения между классами, переданый в конструктор контейнера;
Компонент - класс описаный в карте зависимостей;
Реестр компонентов - массив ключ-значение предназначенные для хранения экземпляров компонентов, где ключ - название класса, значение - объект;

## Концепция

*  Внедрение зависимостей только через публичные свойства;
*  Наследование зависимостей от классов и трейтов;

## Возможности

* Паттерн "Посредник" (Mediator)
* Паттерн "Локатор Сервисов" (Service Locator)

## Установка

> composer update

## Запуск тестов

> php vendor/bin/phpunit 

## Пример использования

```php
$map = [
    ExampleA::class => [
        'exampleB' => ExampleB::class, // будет запрощен из реестра компонентов, если не найден, то будет заново создан и настроен, после чего помещен в реестр компонентов
    ],
    ExampleG::class => [
        'exampleA' => '*' . ExampleA::class, // будет заново создан и настроен, не будет помещен в реестр компонентов контейнера
        'exampleA1' => ExampleA::class, // будет запрощен из реестра компонентов, если не найден, то будет заново создан и настроен, после чего помещен в реестр компонентов
    ],
];


$container = new \PDIC\Container($map);
$g1 = $container->create(ExampleG::class); // не сохранил отдаваемый объект в реестре компонентов 
$g1->exampleA; // создан новый объект
$g1->exampleA1; // создан новый объект, но помещен в реестр компонентов
$g1->exampleA->exampleB; // создан новый объект, но помещен в реестр компонентов
$g1->exampleA1->exampleB; // объект взят из реестра компонентов

$g2 = $container->get('*' . ExampleG::class); // не сохранил отдаваемый объект в реестре компонентов 
$g2->exampleA; // создан новый объект
$g2->exampleA1; // объект взят из реестра компонентов
$g1->exampleA->exampleB; // объект взят из реестра компонентов
$g1->exampleA1->exampleB; // объект взят из реестра компонентов

$g3 = $container->get(ExampleG::class); // сохранил отдаваемый объект в реестре компонентов 
$g3->exampleA; // создан новый объект
$g3->exampleA1; // объект взят из реестра компонентов
$g3 = $container->get(ExampleG::class); // объект взят из реестра компонентов

$a1 = $container->get(ExampleA::class); // объект взят из реестра компонентов
$a1->exampleB; // объект взят из реестра компонентов

$a2 = $container->create(ExampleA::class); // не сохранил отдаваемый объект в реестре компонентов
$a2->exampleB; // объект взят из реестра компонентов
```