# Практика: Геометрия-2

## Описание предметной области и сущностей

Body - общий абстрактный класс для всех тел.  
Ball, RectangularCuboid, Cylinder и CompoundBody - конкретные виды тел  
CompoundBody хранит набор вложенных тел
IVisitor<TResult> задает методы посещения для каждого типа фигуры  
BoundingBoxVisitor вычисляет минимальный ограничивающий параллелепипед  
BoxifyVisitor заменяет простые тела на параллелепипеды, а структуру составных тел сохраняет

```mermaid
classDiagram
    class Body {
        <<abstract>>
        +Vector3 Position
        +Accept~TResult~(visitor) TResult
    }

    class Ball {
        +double Radius
        +Accept~TResult~(visitor) TResult
    }

    class RectangularCuboid {
        +double SizeX
        +double SizeY
        +double SizeZ
        +Accept~TResult~(visitor) TResult
    }

    class Cylinder {
        +double SizeZ
        +double Radius
        +Accept~TResult~(visitor) TResult
    }

    class CompoundBody {
        +IReadOnlyList~Body~ Parts
        +Accept~TResult~(visitor) TResult
    }

    class IVisitor~TResult~ {
        <<interface>>
        +Visit(ball) TResult
        +Visit(cuboid) TResult
        +Visit(cylinder) TResult
        +Visit(compound) TResult
    }

    class BoundingBoxVisitor {
        +Visit(ball) RectangularCuboid
        +Visit(cuboid) RectangularCuboid
        +Visit(cylinder) RectangularCuboid
        +Visit(compound) RectangularCuboid
    }

    class BoxifyVisitor {
        +Visit(ball) Body
        +Visit(cuboid) Body
        +Visit(cylinder) Body
        +Visit(compound) Body
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

    IVisitor~RectangularCuboid~ <|.. BoundingBoxVisitor
    IVisitor~Body~ <|.. BoxifyVisitor

    Body --> Vector3 : позиция
    CompoundBody o-- Body : вложенные тела

    Body ..> IVisitor~TResult~ : принимает
    BoundingBoxVisitor ..> RectangularCuboid : строит границы
    BoxifyVisitor ..> RectangularCuboid : заменяет простые тела
    BoxifyVisitor ..> CompoundBody : сохраняет вложенность
```
