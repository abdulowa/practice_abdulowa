# Практика: Геометрия-2

## 1. Описание предметной области и сущностей

Программа реализует паттерн Visitor для выполнения различных операций над трехмерными геометрическими телами. Базовый абстрактный класс Body представляет геометрическую фигуру с позицией в пространстве и содержит метод Accept для приема посетителя. Конкретные фигуры - Ball (шар с радиусом), RectangularCuboid (прямоугольный параллелепипед с размерами по осям), Cylinder (цилиндр с радиусом и высотой) и CompoundBody (составное тело, содержащее список частей) - переопределяют Accept, вызывая соответствующий метод посетителя. Интерфейс IVisitor определяет методы Visit для каждого типа фигуры, а два его реализации выполняют разные операции: BoundingBoxVisitor вычисляет минимальный ограничивающий параллелепипед для любой фигуры, а BoxifyVisitor заменяет все простые тела на их ограничивающие параллелепипеды, сохраняя структуру составных тел.

## 2. Диаграмма классов

```mermaid
classDiagram
    direction TB
    
    class IVisitor {
        <<interface>>
        +Visit(Ball ball) Body
        +Visit(RectangularCuboid cuboid) Body
        +Visit(Cylinder cylinder) Body
        +Visit(CompoundBody compound) Body
    }
    
    class Body {
        <<abstract>>
        +Vector3 Position
        +Accept(IVisitor visitor)* Body
    }
    
    class Ball {
        +double Radius
        +Accept(IVisitor visitor) Body
    }
    
    class RectangularCuboid {
        +double SizeX
        +double SizeY
        +double SizeZ
        +Accept(IVisitor visitor) Body
    }
    
    class Cylinder {
        +double SizeZ
        +double Radius
        +Accept(IVisitor visitor) Body
    }
    
    class CompoundBody {
        +IReadOnlyList~Body~ Parts
        +Accept(IVisitor visitor) Body
    }
    
    class BoundingBoxVisitor {
        +Visit(Ball ball) Body
        +Visit(RectangularCuboid cuboid) Body
        +Visit(Cylinder cylinder) Body
        +Visit(CompoundBody compound) Body
    }
    
    class BoxifyVisitor {
        +Visit(Ball ball) Body
        +Visit(RectangularCuboid cuboid) Body
        +Visit(Cylinder cylinder) Body
        +Visit(CompoundBody compound) Body
    }
    
    class Vector3 {
        +double X
        +double Y
        +double Z
    }
    
    Body <|-- Ball
    Body <|-- RectangularCuboid
    Body <|-- Cylinder
    Body <|-- CompoundBody
    
    IVisitor <|.. BoundingBoxVisitor
    IVisitor <|.. BoxifyVisitor
    
    Body *-- Vector3
    CompoundBody o-- Body
    
    Body ..> IVisitor
    Ball ..> IVisitor
    RectangularCuboid ..> IVisitor
    Cylinder ..> IVisitor
    CompoundBody ..> IVisitor
    
    BoundingBoxVisitor ..> RectangularCuboid
    BoxifyVisitor ..> Body
```
