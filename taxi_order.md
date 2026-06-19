# Практика: TaxiOrder

## 1. Описание предметной области и сущностей

Система управляет жизненным циклом заказов такси от создания до завершения поездки. Заказ (TaxiOrder) является корнем агрегата, инкапсулирующим всю бизнес-логику: назначение и отмена водителя, начало и завершение поездки, обновление адреса назначения. Каждое действие проверяется на допустимость в текущем статусе (WaitingForDriver, WaitingCarArrival, InProgress, Finished, Canceled). Вспомогательные объекты-значения PersonName, Address и Car обеспечивают неизменяемость и сравнение по содержимому. Репозиторий водителей (DriversRepository) отвечает только за получение данных о водителе по идентификатору, а TaxiApi выступает тонким фасадом, делегирующим все операции методам заказа.

## 2. Диаграмма классов

```mermaid
classDiagram
    direction TB

    class ValueType~T~ {
        <<abstract>>
        +Equals(object) bool
        +GetHashCode() int
        +ToString() string
    }

    class Entity~TId~ {
        <<abstract>>
        +TId Id
        +Equals(object) bool
        +GetHashCode() int
    }

    class PersonName {
        +string FirstName
        +string LastName
        +PersonName(firstName, lastName)
        +ToString() string
    }

    class Address {
        +string Street
        +string Building
        +Address(street, building)
        +ToString() string
    }

    class Car {
        +string Color
        +string Model
        +string PlateNumber
        +Car(color, model, plateNumber)
    }

    class Driver {
        +PersonName Name
        +Car Car
        +Driver(id, name, car)
        +GetFullInfo() string
    }

    class DriverInfo {
        +int Id
        +string FirstName
        +string LastName
        +string CarColor
        +string CarModel
        +string CarPlateNumber
        +DriverInfo(id, firstName, lastName, carColor, carModel, carPlateNumber)
    }

    class TaxiOrder {
        -TaxiOrderStatus _status
        -DateTime _creationTime
        -DateTime _driverAssignmentTime
        -DateTime _cancelTime
        -DateTime _startRideTime
        -DateTime _finishRideTime
        -PersonName _clientName
        -Address _start
        -Address _destination
        -Driver _driver
        +TaxiOrder(id, clientName, start, creationTime)
        +TaxiOrderStatus Status
        +PersonName ClientName
        +Address Start
        +Address Destination
        +Driver Driver
        +DateTime CreationTime
        +DateTime DriverAssignmentTime
        +DateTime CancelTime
        +DateTime StartRideTime
        +DateTime FinishRideTime
        +UpdateDestination(Address) void
        +AssignDriver(Driver, DateTime) void
        +UnassignDriver() void
        +Cancel(DateTime) void
        +StartRide(DateTime) void
        +FinishRide(DateTime) void
        +GetLastProgressTime() DateTime
        +GetShortOrderInfo() string
        +GetDriverFullInfo() string
    }

    class TaxiOrderStatus {
        <<enumeration>>
        WaitingForDriver
        WaitingCarArrival
        InProgress
        Finished
        Canceled
    }

    class DriversRepository {
        +GetDriverById(int) DriverInfo
    }

    class TaxiApi {
        -DriversRepository _driversRepo
        -Func~DateTime~ _currentTime
        -int _idCounter
        +TaxiApi(DriversRepository, Func~DateTime~)
        +CreateOrderWithoutDestination(firstName, lastName, street, building) TaxiOrder
        +UpdateDestination(order, street, building) void
        +AssignDriver(order, driverId) void
        +UnassignDriver(order) void
        +Cancel(order) void
        +StartRide(order) void
        +FinishRide(order) void
        +GetDriverFullInfo(order) string
        +GetShortOrderInfo(order) string
    }

    ValueType~T~ <|-- PersonName
    ValueType~T~ <|-- Address
    ValueType~T~ <|-- Car
    Entity~TId~ <|-- Driver
    Entity~TId~ <|-- TaxiOrder

    Driver *-- PersonName
    Driver *-- Car
    TaxiOrder *-- PersonName
    TaxiOrder *-- Address : Start
    TaxiOrder *-- Address : Destination
    TaxiOrder o-- Driver

    TaxiOrder --> TaxiOrderStatus

    TaxiApi --> DriversRepository
    TaxiApi --> Func~DateTime~
    TaxiApi ..> TaxiOrder
    TaxiApi ..> PersonName
    TaxiApi ..> Address

    DriversRepository ..> DriverInfo
