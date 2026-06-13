# Практика Сбои
## Описание предметной области и сущностей

В системе обрабатываются сведения об устройствах и событиях сбоев, которые были зафиксированы для этих устройств. Цель отчета - найти устройства, у которых до заданной даты произошел серьезный сбой

1. Device -сущность устройства. Хранит идентификатор Id и название Name которое затем попадает в итоговый отчет

2. Failure - сущность сбоя. Хранит тип сбоя Type, идентификатор связанного устройства DeviceId и дату события Date. Также содержит методы IsSerious() и HappenedBefore(..) чтобы проверка сбоя не была размазана по коду отчета

3. FailureType - перечисление возможных типов сбоев: UnexpectedShutdown, ShortNonResponding, HardwareFailure, ConnectionProblem. Оно заменяет числовые коды из исходного варианта программы

4. ReportMaker - класс формирования отчета. Получает коллекции устройств и сбоев, выбирает серьезные сбои раньше заданной даты и возвращает имена устройств, подходящих под это условие


```mermaid
classDiagram
    class Failure {
        +FailureType Type
        +int DeviceId
        +DateTime Date
        +Failure(type, deviceId, date)
        +IsSerious() bool
        +HappenedBefore(date) bool
    }

    class Device {
        +int Id
        +string Name
        +Device(id, name)
    }

    class FailureType {
        <<enumeration>>
        UnexpectedShutdown
        ShortNonResponding
        HardwareFailure
        ConnectionProblem
    }

    class ReportMaker {
        +FindDevicesFailedBeforeDate(date, failures, devices) List~string~
        +FindDevicesFailedBeforeDateObsolete(day, month, year, failureTypes, deviceId, times, devices) List~string~
    }

    Failure --> FailureType : хранит категорию сбоя
    ReportMaker ..> Failure : читает события
    ReportMaker ..> Device : формирует список имен
```
