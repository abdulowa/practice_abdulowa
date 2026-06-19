# Практика: HoMM

## 1. Описание предметной области и сущностей

Программа моделирует взаимодействие игрока с объектами на игровой карте в пошаговой стратегии. Игрок может взаимодействовать с объектами тремя способами: сражаться с армией охраны, собирать сокровища и присваивать объект себе. Для каждого типа взаимодействия выделен отдельный интерфейс: IOwnable для объектов, которые можно захватывать, IHaveArmy для объектов с армией и IHaveTreasure для объектов с сокровищами. Класс Interaction управляет всеми взаимодействиями через эти интерфейсы, а класс Player содержит логику боя, сбора ресурсов и смерти.

## 2. Диаграмма классов

```mermaid
classDiagram
    direction TB
    
    class IOwnable {
        <<interface>>
        +int Owner
    }
    
    class IHaveArmy {
        <<interface>>
        +Army Army
    }
    
    class IHaveTreasure {
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
        +bool CanBeat(Army army)
        +void Die()
        +void Consume(Treasure treasure)
    }
    
    class Army {
        +int Power
    }
    
    class Treasure {
        +int Amount
    }
    
    class Interaction {
        <<static>>
        +void Make(Player player, object mapObject)
    }
    
    IOwnable <|.. Dwelling
    IOwnable <|.. Mine
    IHaveArmy <|.. Mine
    IHaveArmy <|.. Creeps
    IHaveArmy <|.. Wolves
    IHaveTreasure <|.. Mine
    IHaveTreasure <|.. Creeps
    IHaveTreasure <|.. ResourcePile
    
    Mine --> Army
    Mine --> Treasure
    Creeps --> Army
    Creeps --> Treasure
    Wolves --> Army
    ResourcePile --> Treasure
    
    Interaction ..> Player
    Interaction ..> IOwnable
    Interaction ..> IHaveArmy
    Interaction ..> IHaveTreasure
    Player ..> Army
    Player ..> Treasure
