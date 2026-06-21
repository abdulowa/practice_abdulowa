# Практика: Сбои

## 1. Описание предметной области и сущностей

Программа предназначена для анализа сбоев оборудования и формирования отчета об устройствах, на которых до заданной даты происходили критические неисправности. В системе выделены следующие сущности: Device (устройство с идентификатором и названием), Failure (запись о сбое с типом, датой и ссылкой на устройство) и FailureType (перечисление типов неисправностей). Основной класс ReportMaker содержит метод FindDevicesFailedBeforeDate, который фильтрует серьезные сбои (UnexpectedShutdown и HardwareFailures) по дате и возвращает список названий проблемных устройств. Устаревший метод FindDevicesFailedBeforeDateObsolete сохранен для обратной совместимости и преобразует старые форматы данных для вызова нового метода.

## 2. Диаграмма классов

```mermaid
classDiagram
    direction LR
    
    class FailureType {
        <<enumeration>>
        UnexpectedShutdown = 0
        ShortNonResponding = 1
        HardwareFailures = 2
        ConnectionProblems = 3
    }

    class Device {
        -int id
        -string name
        +Device(int id, string name)
        +int Id$
        +string Name$
    }

    class Failure {
        -FailureType type
        -DateTime date
        -int deviceId
        +Failure(FailureType type, DateTime date, int deviceId)
        +bool IsSerious()
        +bool IsBeforeDate(DateTime targetDate)
        +FailureType Type$
        +DateTime Date$
        +int DeviceId$
    }

    class ReportMaker {
        +List~string~ FindDevicesFailedBeforeDate(DateTime date, Failure[] failures, Device[] devices)$
        +List~string~ FindDevicesFailedBeforeDateObsolete(int day, int month, int year, int[] failureTypes, int[] deviceId, object[][] times, List~Dictionary~string, object~~ devices)$
    }

    Failure --> FailureType
    Failure --> Device
    ReportMaker ..> Failure
    ReportMaker ..> Device
```
