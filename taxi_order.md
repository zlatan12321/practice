# Практика: TaxiOrder

## Описание предметной области и сущностей

Программа управляет заказом такси от его создания до завершения или отмены.

TaxiOrder - сам управляет изменениями заказа и проверяет допустимость действий. Клиент, адрес отправления, адрес назначения и водитель хранятся в нем только один раз.

ProgressRecord - одна запись в истории заказа. В ней сохраняется полученный статус и время перехода. Текущий статус TaxiOrder берется из последней записи истории.

PersonName - имя и фамилия человека.

Address - улица и номер дома.

Driver - водитель с именем и автомобилем.

Car - сведения об автомобиле: модель, цвет и государственный номер.

DriversRepository - получает водителя по идентификатору и ничего не знает о заказах.

TaxiApi - сохраняет прежний интерфейс программы, но сам не изменяет заказ. Все действия передаются методам TaxiOrder.

## Диаграмма классов

```mermaid
classDiagram
    direction LR

    class Entity~TId~ {
        +TId Id
    }

    class ValueType~T~ {
        <<abstract>>
    }

    class ITaxiApi~TOrder~ {
        <<interface>>
        +CreateOrderWithoutDestination(firstName, lastName, street, building) TOrder
        +UpdateDestination(order, street, building) void
        +AssignDriver(order, driverId) void
        +UnassignDriver(order) void
        +Cancel(order) void
        +StartRide(order) void
        +FinishRide(order) void
        +GetDriverFullInfo(order) string
        +GetShortOrderInfo(order) string
    }

    class TaxiApi {
        -DriversRepository driversRepository
        -Func~DateTime~ getCurrentTime
        -int nextOrderId
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

    class DriversRepository {
        +GetDriver(driverId) Driver
    }

    class TaxiOrder {
        -List~ProgressRecord~ history
        +PersonName ClientName
        +Address Start
        +Address Destination
        +Driver Driver
        +TaxiOrderStatus Status
        +DateTime CreationTime
        +DateTime DriverAssignmentTime
        +DateTime CancelTime
        +DateTime StartRideTime
        +DateTime FinishRideTime
        +UpdateDestination(destination) void
        +AssignDriver(driver, assignmentTime) void
        +UnassignDriver() void
        +Cancel(cancelTime) void
        +StartRide(startTime) void
        +FinishRide(finishTime) void
        +GetDriverFullInfo() string
        +GetShortOrderInfo() string
        -AddProgress(status, time) void
        -FindLastTime(status) DateTime
        -EnsureStatus(allowedStatuses) void
    }

    class ProgressRecord {
        +TaxiOrderStatus Status
        +DateTime Time
    }

    class PersonName {
        +string FirstName
        +string LastName
    }

    class Address {
        +string Street
        +string Building
    }

    class Driver {
        +PersonName Name
        +Car Car
    }

    class Car {
        +string Model
        +string Color
        +string PlateNumber
    }

    class TaxiOrderStatus {
        <<enumeration>>
        WaitingForDriver
        WaitingCarArrival
        InProgress
        Finished
        Canceled
    }

    Entity~TId~ <|-- TaxiOrder
    Entity~TId~ <|-- Driver

    ValueType~T~ <|-- ProgressRecord
    ValueType~T~ <|-- PersonName
    ValueType~T~ <|-- Address
    ValueType~T~ <|-- Car

    ITaxiApi~TOrder~ <|.. TaxiApi

    TaxiApi --> DriversRepository : хранит ссылку
    TaxiApi ..> TaxiOrder : создает и вызывает команды
    DriversRepository ..> Driver : возвращает

    TaxiOrder "1" *-- "1..*" ProgressRecord : история состояний
    TaxiOrder "1" o-- "1" PersonName : клиент
    TaxiOrder "1" o-- "1..2" Address : адреса поездки
    TaxiOrder "1" o-- "0..1" Driver : назначение

    Driver "1" o-- "1" PersonName : имя
    Driver "1" o-- "1" Car : автомобиль

    ProgressRecord --> TaxiOrderStatus : фиксирует
