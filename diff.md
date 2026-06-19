# Практика: Дифференцирование

## 1. Описание предметной области и сущностей

Программа реализует символьное дифференцирование математических функций, представленных в виде деревьев выражений LINQ Expression. Основной класс Algebra содержит статический метод Differentiate, который принимает лямбда-выражение и возвращает его производную. Метод рекурсивно обходит дерево выражений, применяя правила дифференцирования для различных типов узлов: константы превращаются в 0, параметр дифференцирования - в 1, для суммы применяется правило суммы, для произведения - правило произведения, а для вызовов Math.Sin и Math.Cos - цепное правило. При попытке дифференцировать неподдерживаемые функции или синтаксические конструкции выбрасывается исключение с информативным сообщением.

## 2. Диаграмма классов

```mermaid
classDiagram
    direction TB

    class Algebra {
        <<static>>
        +Differentiate(Expression~Func~double, double~~ function) Expression~Func~double, double~~
        -Differentiate(Expression expression, ParameterExpression parameter) Expression
        -DifferentiateBinary(BinaryExpression binary, ParameterExpression parameter) Expression
        -DifferentiateMethodCall(MethodCallExpression methodCall, ParameterExpression parameter) Expression
    }

    class Expression {
        <<abstract>>
        +ExpressionType NodeType
    }

    class ConstantExpression {
        +object Value
    }

    class ParameterExpression {
        +string Name
        +Type Type
    }

    class BinaryExpression {
        +Expression Left
        +Expression Right
    }

    class MethodCallExpression {
        +MethodInfo Method
        +ReadOnlyCollection~Expression~ Arguments
    }

    class ExpressionType {
        <<enumeration>>
        Add
        Multiply
        Call
        Constant
        Parameter
    }

    Expression <|-- ConstantExpression
    Expression <|-- ParameterExpression
    Expression <|-- BinaryExpression
    Expression <|-- MethodCallExpression

    BinaryExpression --> Expression : Left
    BinaryExpression --> Expression : Right
    MethodCallExpression --> Expression : Arguments

    Algebra ..> Expression
    Algebra ..> ParameterExpression
    Algebra ..> BinaryExpression
    Algebra ..> MethodCallExpression
    Algebra ..> ExpressionType

    BinaryExpression --> ExpressionType : NodeType
