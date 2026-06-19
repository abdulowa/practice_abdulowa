# Практика: Контрольный разряд

## 1. Описание предметной области и сущностей

Программа вычисляет контрольные разряды для различных стандартов нумерации: UPC (штрих-коды товаров), ISBN-10 (книжные номера) и Luhn (банковские карты, IMEI). Контрольный разряд - это дополнительная цифра, вычисляемая по остальным цифрам номера для обнаружения ошибок при вводе или считывании. Класс Extensions содержит общие методы-расширения: извлечение цифр числа (с младшего или старшего разряда), суммирование с весовыми коэффициентами, вычисление контрольной цифры по модулю 10 и 11, а также дополнение последовательности нулями слева. Класс ControlDigitAlgo реализует три алгоритма, используя общие методы расширения для минимизации специфичного кода.

## 2. Диаграмма классов

```mermaid
classDiagram
    direction TB

    class Extensions {
        <<static>>
        +GetDigitsFromLeastSignificant(this long) IEnumerable~int~
        +GetDigitsFromMostSignificant(this long) IEnumerable~int~
        +SumWithFactors(this IEnumerable~int~, Func~int,int~) int
        +CalculateCheckDigitMod10(int sum) int
        +CalculateCheckDigitMod11(int sum) char
        +PadLeft(this IEnumerable~int~, int) IEnumerable~int~
    }

    class ControlDigitAlgo {
        <<static>>
        +Upc(long number) int
        +Isbn10(long number) char
        +Luhn(long number) int
    }

    class IEnumerable~int~ {
        <<interface>>
    }

    class Func~int,int~ {
        <<delegate>>
    }

    ControlDigitAlgo ..> Extensions
    Extensions ..> IEnumerable~int~
    Extensions ..> Func~int,int~
    ControlDigitAlgo ..> IEnumerable~int~
