# Практика: Роботы

## 1. Описание предметной области и сущностей

Программа моделирует систему управления роботами, состоящую из интеллектуальной системы (AI), генерирующей команды, и исполнительного устройства (Device), выполняющего эти команды. Основная задача - обеспечить строгую типизацию с использованием ковариантности (out T) для интерфейса IRobotAI и контравариантности (in T) для интерфейса IDevice, что позволяет гибко комбинировать AI, возвращающие более конкретные команды, с устройствами, принимающими более общие команды. В системе реализованы два типа роботов: ShooterAI с командой IShooterMoveCommand и BuilderAI с командой IMoveCommand, а также устройства Mover и ShooterMover для выполнения соответствующих команд.

## 2. Диаграмма классов

```mermaid
classDiagram
    direction TB

    class IRobotAI~out TCommand~ {
        <<interface>>
        +GetCommand() TCommand
    }

    class IDevice~in TCommand~ {
        <<interface>>
        +ExecuteCommand(TCommand command) string
    }

    class RobotAI~TCommand~ {
        <<abstract>>
        +GetCommand()* TCommand
    }

    class Device~TCommand~ {
        <<abstract>>
        +ExecuteCommand(TCommand command)* string
    }

    class ShooterAI {
        -int _counter
        +GetCommand() IShooterMoveCommand
    }

    class BuilderAI {
        -int _counter
        +GetCommand() IMoveCommand
    }

    class Mover {
        +ExecuteCommand(IMoveCommand command) string
    }

    class ShooterMover {
        +ExecuteCommand(IShooterMoveCommand command) string
    }

    class Robot~TCommand~ {
        -IRobotAI~TCommand~ _ai
        -IDevice~TCommand~ _device
        +Robot(IRobotAI~TCommand~ ai, IDevice~TCommand~ device)
        +Start(int steps) IEnumerable~string~
    }

    class Robot {
        <<static>>
        +Create~TCommand~(IRobotAI~TCommand~ ai, IDevice~TCommand~ device) Robot~TCommand~$
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
        +bool ShouldHide
        +ForCounter(int counter)$ ShooterCommand
    }

    class BuilderCommand {
        +Point Destination
        +ForCounter(int counter)$ BuilderCommand
    }

    class Point {
        +double X
        +double Y
    }

    IRobotAI~out TCommand~ <|.. RobotAI~TCommand~
    IDevice~in TCommand~ <|.. Device~TCommand~

    RobotAI~TCommand~ <|-- ShooterAI
    RobotAI~TCommand~ <|-- BuilderAI
    Device~TCommand~ <|-- Mover
    Device~TCommand~ <|-- ShooterMover

    IMoveCommand <|-- IShooterMoveCommand
    IShooterMoveCommand <|.. ShooterCommand
    IMoveCommand <|.. BuilderCommand

    Robot~TCommand~ o-- IRobotAI~TCommand~
    Robot~TCommand~ o-- IDevice~TCommand~

    ShooterCommand --> Point
    BuilderCommand --> Point

    Robot ..> Robot~TCommand~
```
