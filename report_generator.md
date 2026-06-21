# Практика: Генератор отчетов

## 1. Описание предметной области и сущностей

Программа генерирует статистические отчеты по данным погодных измерений (температура и влажность), вычисляя различные показатели и выводя их в разных форматах. Система разделена на две независимые стратегии: форматирование отчета (IRenderer) и вычисление статистики (делегат FuncCalculator). HtmlRenderer и MarkdownRenderer отвечают за визуальное оформление в HTML и Markdown соответственно, а методы Statistics.MeanAndStd и Statistics.Median выполняют математические расчеты. ReportMaker объединяет обе стратегии через конструктор и формирует итоговый отчет, а ReportMakerHelper предоставляет удобные методы для создания четырех комбинаций отчетов (HTML/MeanAndStd, HTML/Median, Markdown/MeanAndStd, Markdown/Median).

## 2. Диаграмма классов

```mermaid
classDiagram
    direction TB

    class IRenderer {
        <<interface>>
        +string Caption
        +MakeCaption(string caption) string
        +BeginList() string
        +EndList() string
        +MakeItem(string valueType, string entry) string
    }

    class HtmlRenderer {
        +string Caption
        +MakeCaption(string caption) string
        +BeginList() string
        +EndList() string
        +MakeItem(string valueType, string entry) string
    }

    class MarkdownRenderer {
        +string Caption
        +MakeCaption(string caption) string
        +BeginList() string
        +EndList() string
        +MakeItem(string valueType, string entry) string
    }

    class ReportMaker {
        -IRenderer _renderer
        -FuncCalculator _statisticsCalculator
        +ReportMaker(IRenderer renderer, FuncCalculator calculator)
        +MakeReport(IEnumerable~Measurement~ measurements) string
    }

    class FuncCalculator {
        <<delegate>>
        +Invoke(IEnumerable~double~ data) object
    }

    class ReportMakerHelper {
        <<static>>
        +MeanAndStdHtmlReport(IEnumerable~Measurement~ data) string
        +MedianMarkdownReport(IEnumerable~Measurement~ data) string
        +MeanAndStdMarkdownReport(IEnumerable~Measurement~ measurements) string
        +MedianHtmlReport(IEnumerable~Measurement~ measurements) string
    }

    class Statistics {
        <<static>>
        +MeanAndStd(IEnumerable~double~ data) MeanAndStd
        +Median(IEnumerable~double~ data) object
    }

    class Measurement {
        +double Temperature
        +double Humidity
    }

    class MeanAndStd {
        +double Mean
        +double Std
    }

    IRenderer <|.. HtmlRenderer
    IRenderer <|.. MarkdownRenderer

    ReportMaker o-- IRenderer
    ReportMaker o-- FuncCalculator

    ReportMaker ..> Measurement
    ReportMaker ..> Statistics

    ReportMakerHelper ..> ReportMaker
    ReportMakerHelper ..> HtmlRenderer
    ReportMakerHelper ..> MarkdownRenderer
    ReportMakerHelper ..> Statistics
    ReportMakerHelper ..> Measurement

    Statistics ..> MeanAndStd
```
