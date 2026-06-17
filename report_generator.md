# Практика: Генератора отчетов

## Описание предметной области и сущностей

отчет разделен на несколько простых частей. формат отвечает за внешний вид, статистика отвечает за расчет значения, а профиль отчета собирает эти части вместе

ReportMaker - основной класс, который строит отчет по готовому профилю  
ReportProfile - настройки отчета: формат, способ расчета и список показателей  
MeasurementColumn - описание одного показателя, например температуры или влажности  
IReportFormat - интерфейс оформления отчета  
HtmlFormat - оформление отчета в HTML  
MarkdownFormat - оформление отчета в Markdown  
IReportStatistics - интерфейс вычисления статистики  
MeanAndStdStatistics - считает среднее и стандартное отклонение  
MedianStatistics - считает медиану  
ReportMakerHelper - статический класс с готовыми методами для четырех нужных вариантов отчета

```mermaid
classDiagram
    class ReportMaker {
        -ReportProfile profile
        +ReportMaker(profile)
        +MakeReport(measurements) string
    }

    class ReportProfile {
        +IReportFormat Format
        +IReportStatistics Statistics
        +IReadOnlyList~MeasurementColumn~ Columns
        +ReportProfile(format, statistics, columns)
    }

    class MeasurementColumn {
        +string Name
        +Func~Measurement, double~ Select
        +MeasurementColumn(name, select)
    }

    class IReportFormat {
        <<interface>>
        +Caption(caption) string
        +BeginList() string
        +Item(name, value) string
        +EndList() string
    }

    class HtmlFormat {
        +Caption(caption) string
        +BeginList() string
        +Item(name, value) string
        +EndList() string
    }

    class MarkdownFormat {
        +Caption(caption) string
        +BeginList() string
        +Item(name, value) string
        +EndList() string
    }

    class IReportStatistics {
        <<interface>>
        +string Caption
        +Calculate(values) object
    }

    class MeanAndStdStatistics {
        +string Caption
        +Calculate(values) object
    }

    class MedianStatistics {
        +string Caption
        +Calculate(values) object
    }

    class ReportMakerHelper {
        <<static>>
        +MeanAndStdHtmlReport(data) string
        +MedianMarkdownReport(data) string
        +MeanAndStdMarkdownReport(data) string
        +MedianHtmlReport(data) string
    }

    class Measurement {
        +double Temperature
        +double Humidity
    }

    class MeanAndStd {
        +double Mean
        +double Std
        +ToString() string
    }

    IReportFormat <|.. HtmlFormat
    IReportFormat <|.. MarkdownFormat

    IReportStatistics <|.. MeanAndStdStatistics
    IReportStatistics <|.. MedianStatistics

    ReportMaker *-- ReportProfile : использует профиль
    ReportProfile o-- IReportFormat : формат
    ReportProfile o-- IReportStatistics : расчет
    ReportProfile *-- MeasurementColumn : показатели

    MeasurementColumn ..> Measurement : выбирает значение
    MeanAndStdStatistics ..> MeanAndStd : создает результат

    ReportMakerHelper ..> ReportProfile : собирает профиль
    ReportMakerHelper ..> ReportMaker : запускает отчет
    ReportMakerHelper ..> HtmlFormat : HTML
    ReportMakerHelper ..> MarkdownFormat : Markdown
    ReportMakerHelper ..> MeanAndStdStatistics : среднее
    ReportMakerHelper ..> MedianStatistics : медиана
```
