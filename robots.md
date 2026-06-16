# Практика: Роботы

## Описание предметной области и сущностей

В решении робот разделен на три слоя. AI создает команду, Device выполняет команду, а CommandSession связывает эти две части на один шаг работы

IRobotAI<TCommand> - ковариантный источник команд
IDevice<TCommand> - контравариантный исполнитель команд  
RobotAI<TCommand> - базовый класс для генераторов команд  
Device<TCommand> -базовый класс для устройств 
CommandSession<TCommand> - объект, который получает команду от AI и сразу передает ее устройству  
RobotOfCommand на диаграмме соответствует классу Robot<TCommand> в коде 
RobotFactory на диаграмме соответствует статическому классу Robot в коде
MovementText - отдельный formatter строк движения

```mermaid
classDiagram
    class IRobotAI~TCommand~ {
        <<interface>>
        <<out TCommand>>
        +GetCommand() TCommand
    }

    class IDevice~TCommand~ {
        <<interface>>
        <<in TCommand>>
        +ExecuteCommand(command) string
    }

    class RobotAI~TCommand~ {
        <<abstract>>
        +GetCommand() TCommand
    }

    class Device~TCommand~ {
        <<abstract>>
        +ExecuteCommand(command) string
    }

    class ShooterAI {
        -int counter
        +GetCommand() IShooterMoveCommand
    }

    class BuilderAI {
        -int counter
        +GetCommand() IMoveCommand
    }

    class Mover {
        +ExecuteCommand(command) string
    }

    class ShooterMover {
        +ExecuteCommand(command) string
    }

    class CommandSession~TCommand~ {
        -IRobotAI~TCommand~ ai
        -IDevice~TCommand~ device
        +RunStep() string
    }

    class RobotOfCommand {
        -CommandSession~TCommand~ session
        +Start(steps) IEnumerable~string~
        +Create(ai, device) RobotOfCommand
    }

    class RobotFactory {
        <<static>>
        +Create~TCommand~(ai, device) RobotOfCommand
    }

    class MovementText {
        <<static>>
        +MoveTo(command) string
        +MoveWithCover(command) string
    }

    class IMoveCommand {
        <<interface>>
        +Point Destination
    }

    class IShooterMoveCommand {
        <<interface>>
        +bool ShouldHide
    }

    class ShooterCommand {
        +Point Destination
        +bool Shoot
        +bool ShouldHide
    }

    class BuilderCommand {
        +Point Destination
        +bool Build
    }

    class Point {
        +double X
        +double Y
    }

    IRobotAI~TCommand~ <|.. RobotAI~TCommand~
    IDevice~TCommand~ <|.. Device~TCommand~

    RobotAI~IShooterMoveCommand~ <|-- ShooterAI
    RobotAI~IMoveCommand~ <|-- BuilderAI

    Device~IMoveCommand~ <|-- Mover
    Device~IShooterMoveCommand~ <|-- ShooterMover

    IMoveCommand <|-- IShooterMoveCommand
    IShooterMoveCommand <|.. ShooterCommand
    IMoveCommand <|.. BuilderCommand

    ShooterCommand --> Point : Destination
    BuilderCommand --> Point : Destination

    CommandSession~TCommand~ o-- IRobotAI~TCommand~ : получает
    CommandSession~TCommand~ o-- IDevice~TCommand~ : передает

    RobotOfCommand *-- CommandSession~TCommand~ : цикл работы
    RobotFactory ..> RobotOfCommand : создает

    Mover ..> MovementText : форматирует
    ShooterMover ..> MovementText : форматирует
    MovementText ..> IMoveCommand : читает координаты
    MovementText ..> IShooterMoveCommand : читает укрытие
```
