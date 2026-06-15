# Практика «HoMM»

## Описание предметной области и сущностей

В игре есть объекты карты, с которыми взаимодействует игрок. Разные объекты могут обладать разными возможностями: иметь владельца, охраняться армией или содержать сокровища

Player описывает игрока. Он может проверять, способен ли победить армию, получать сокровища и погибать при неудачном сражении

IOwnable выделяет объекты, которые можно присвоить игроку

IGuarded выделяет объекты, перед которыми нужно сначала пройти бой

IValuable выделяет объекты, из которых можно получить сокровища

Interaction выполняет взаимодействие через интерфейсы, не зная конкретный тип объекта карты

```mermaid
classDiagram
    class IOwnable {
        <<interface>>
        +int Owner
    }

    class IGuarded {
        <<interface>>
        +Army Army
    }

    class IValuable {
        <<interface>>
        +Treasure Treasure
    }

    class Dwelling {
        +int Owner
    }

    class Mine {
        +int Owner
        +Army Army
        +Treasure Treasure
    }

    class Creeps {
        +Army Army
        +Treasure Treasure
    }

    class Wolves {
        +Army Army
    }

    class ResourcePile {
        +Treasure Treasure
    }

    class Player {
        +int Id
        +int Gold
        +bool Dead
        +CanBeat(army) bool
        +Consume(treasure)
        +Die()
    }

    class Army {
        +int Power
    }

    class Treasure {
        +int Amount
    }

    class Interaction {
        +Make(player, mapObject)
    }

    IOwnable <|.. Dwelling
    IOwnable <|.. Mine

    IGuarded <|.. Mine
    IGuarded <|.. Creeps
    IGuarded <|.. Wolves

    IValuable <|.. Mine
    IValuable <|.. Creeps
    IValuable <|.. ResourcePile

    IGuarded --> Army : Army
    IValuable --> Treasure : Treasure

    Interaction ..> Player : меняет состояние
    Interaction ..> IGuarded : проверяет бой
    Interaction ..> IOwnable : назначает владельца
    Interaction ..> IValuable : выдает награду

    Player ..> Army : сравнивает силы
    Player ..> Treasure : получает золото
```
