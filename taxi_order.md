# Практика: TaxiOrder

## Описание предметной области и сущностей

В этой версии заказ такси сделан как доменная модель. Основная логика переходов между состояниями перенесена в TaxiOrder, а TaxiApi только создает нужные объекты и вызывает методы заказа

TaxiOrder - основная сущность заказа. В ней хранятся клиент, адрес отправления, адрес назначения, водитель, статус и методы управления заказом

OrderTimeline - отдельный объект для временных отметок заказа: создание, назначение водителя, начало поездки, завершение и отмена

Driver - сущность водителя. Содержит имя водителя и данные автомобиля.

Car - объект-значение с данными автомобиля.

PersonName - объект-значение для имени и фамилии.

Address - объект-значение для адреса.

DriversRepository - репозиторий, который по id возвращает водителя и не работает с заказами.

TaxiApi - внешний слой, который сохраняет старый публичный интерфейс и делегирует действия TaxiOrder.

```mermaid
classDiagram
    class Entity~TId~ {
        <<abstract>>
        +TId Id
    }

    class ValueType~T~ {
        <<abstract>>
    }

    class TaxiOrder {
        -OrderTimeline timeline
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
        +TaxiOrder(id, clientName, start, creationTime)
        +UpdateDestination(destination) void
        +AssignDriver(driver, assignmentTime) void
        +UnassignDriver() void
        +Cancel(cancelTime) void
        +StartRide(startTime) void
        +FinishRide(finishTime) void
        +GetDriverFullInfo() string
        +GetShortOrderInfo() string
    }

    class OrderTimeline {
        +DateTime CreationTime
        +DateTime DriverAssignmentTime
        +DateTime CancelTime
        +DateTime StartRideTime
        +DateTime FinishRideTime
        +MarkDriverAssigned(time) void
        +MarkCanceled(time) void
        +MarkRideStarted(time) void
        +MarkRideFinished(time) void
        +GetLastProgressTime(status) DateTime
    }

    class Driver {
        +PersonName Name
        +Car Car
        +Driver(id, name, car)
    }

    class Car {
        +string Model
        +string Color
        +string PlateNumber
        +Car(model, color, plateNumber)
    }

    class PersonName {
        +string FirstName
        +string LastName
        +PersonName(firstName, lastName)
    }

    class Address {
        +string Street
        +string Building
        +Address(street, building)
    }

    class DriversRepository {
        +GetDriver(driverId) Driver
    }

    class TaxiApi {
        -DriversRepository driversRepo
        -Func~DateTime~ currentTime
        -int idCounter
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

    class TaxiOrderStatus {
        <<enumeration>>
        WaitingForDriver
        WaitingCarArrival
        InProgress
        Finished
        Canceled
    }

    Entity~int~ <|-- TaxiOrder
    Entity~int~ <|-- Driver

    ValueType~Car~ <|-- Car
    ValueType~PersonName~ <|-- PersonName
    ValueType~Address~ <|-- Address

    ITaxiApi~TaxiOrder~ <|.. TaxiApi

    TaxiOrder *-- OrderTimeline : временные этапы
    TaxiOrder *-- PersonName : клиент
    TaxiOrder *-- Address : маршрут
    TaxiOrder o-- Driver : назначенный водитель
    TaxiOrder --> TaxiOrderStatus : состояние

    Driver *-- PersonName : имя
    Driver *-- Car : автомобиль

    TaxiApi --> DriversRepository : получает водителя
    TaxiApi ..> TaxiOrder : вызывает методы
    TaxiApi ..> PersonName : создает клиента
    TaxiApi ..> Address : создает адреса

    DriversRepository ..> Driver : возвращает
    DriversRepository ..> PersonName : создает имя
    DriversRepository ..> Car : создает авто

    OrderTimeline ..> TaxiOrderStatus : выбирает дату
```
