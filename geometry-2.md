# Практика «Геометрия-2»

## Описание предметной области и сущностей

отличия от прошлой версии решения: фигуры остаются простыми объектами с параметрами и методом Accept. Операции над ними вынесены в visitor-классы

Body - базовый абстрактный тип для всех геометрических тел, хранит позицию в пространстве
Ball - шар, задаётся радиусом
Cylinder - цилиндр с радиусом основания и высотой  
RectangularCuboid - прямоугольный параллелепипед с размерами по трём осям  
CompoundBody - составное тело, хранит список вложенных тел  
Vector3 - структура для хранения координат точки в трёхмерном пространстве
IVisitor<TResult> - обобщённый интерфейс операции над телами, позволяет добавлять новые операции без изменения иерархии фигур  
AxisLimits - вспомогательный класс, инкапсулирующий минимальные и максимальные координаты по осям X, Y, Z.  
EnvelopeVisitor - посетитель, вычисляющий координатные границы тела в виде AxisLimits 
BoundingBoxVisitor - посетитель, строящий итоговый ограничивающий параллелепипед через делегирование EnvelopeVisitor
BoxifyVisitor - посетитель, заменяющий простые тела на их ограничивающие параллелепипеды, а составные тела пересобирающий из преобразованных частей

```mermaid
classDiagram
    class Body {
        <<abstract>>
        +Vector3 Position
        +Accept~TResult~(IVisitor~TResult~ visitor) TResult
    }

    class Ball {
        +double Radius
        +Accept~TResult~(IVisitor~TResult~ visitor) TResult
    }

    class Cylinder {
        +double SizeZ
        +double Radius
        +Accept~TResult~(IVisitor~TResult~ visitor) TResult
    }

    class RectangularCuboid {
        +double SizeX
        +double SizeY
        +double SizeZ
        +Accept~TResult~(IVisitor~TResult~ visitor) TResult
    }

    class CompoundBody {
        +IReadOnlyList~Body~ Parts
        +Accept~TResult~(IVisitor~TResult~ visitor) TResult
    }

    class IVisitor~TResult~ {
        <<interface>>
        +Visit(Ball ball) TResult
        +Visit(RectangularCuboid cuboid) TResult
        +Visit(Cylinder cylinder) TResult
        +Visit(CompoundBody compound) TResult
    }

    class AxisLimits {
        +double MinX
        +double MinY
        +double MinZ
        +double MaxX
        +double MaxY
        +double MaxZ
        +ForBall(Ball ball) AxisLimits
        +ForCuboid(RectangularCuboid cuboid) AxisLimits
        +ForCylinder(Cylinder cylinder) AxisLimits
        +Join(AxisLimits other) AxisLimits
        +ToCuboid() RectangularCuboid
    }

    class EnvelopeVisitor {
        +Visit(Ball ball) AxisLimits
        +Visit(RectangularCuboid cuboid) AxisLimits
        +Visit(Cylinder cylinder) AxisLimits
        +Visit(CompoundBody compound) AxisLimits
    }

    class BoundingBoxVisitor {
        +Visit(Ball ball) RectangularCuboid
        +Visit(RectangularCuboid cuboid) RectangularCuboid
        +Visit(Cylinder cylinder) RectangularCuboid
        +Visit(CompoundBody compound) RectangularCuboid
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
    Body <|-- Cylinder
    Body <|-- RectangularCuboid
    Body <|-- CompoundBody

    IVisitor~AxisLimits~ <|.. EnvelopeVisitor
    IVisitor~RectangularCuboid~ <|.. BoundingBoxVisitor
    IVisitor~Body~ <|.. BoxifyVisitor

    Body --> Vector3 : Position
    CompoundBody o-- Body : Parts

    Body ..> IVisitor~TResult~ : Accept
    EnvelopeVisitor ..> AxisLimits : вычисляет
    AxisLimits ..> RectangularCuboid : ToCuboid
    BoundingBoxVisitor ..> EnvelopeVisitor : использует
    BoxifyVisitor ..> EnvelopeVisitor : использует
    BoxifyVisitor ..> CompoundBody : пересобирает
```
