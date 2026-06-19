# Практика: GraphViz

## Описание предметной области и сущностей

В задаче сделан fluent API для сборки графа в формате DOT. Хранение графа и вывод строки не переписываются заново: для этого используются готовые классы Graph, GraphNode, GraphEdge и DotFormatWriter

DotGraphBuilder - точка входа. Создает направленный или ненаправленный граф

GraphBuilder - основной объект цепочки. Он хранит Graph, добавляет вершины и ребра, а в конце возвращает DOT-строку через Build

PendingElement<TAttributes> - промежуточный шаг после добавления вершины или ребра. Через него можно либо настроить текущий элемент методом With, либо сразу продолжить цепочку

AttributeSet<TSelf> - общий базовый класс для общих атрибутов color, fontsize и label

NodeAttributes - набор атрибутов вершины. Дополнительно поддерживает shape

EdgeAttributes - набор атрибутов ребра. Дополнительно поддерживает weight

NodeShape - перечисление доступных форм вершины

```mermaid
classDiagram
    class DotGraphBuilder {
        +DirectedGraph(graphName) GraphBuilder
        +UndirectedGraph(graphName) GraphBuilder
    }

    class GraphBuilder {
        -Graph graph
        +Start(graphName, directed) GraphBuilder
        +AddNode(name) PendingElement~NodeAttributes~
        +AddEdge(sourceNode, destinationNode) PendingElement~EdgeAttributes~
        +Build() string
    }

    class PendingElement~TAttributes~ {
        -GraphBuilder owner
        -TAttributes attributes
        +With(configure) GraphBuilder
        +AddNode(name) PendingElement~NodeAttributes~
        +AddEdge(sourceNode, destinationNode) PendingElement~EdgeAttributes~
        +Build() string
    }

    class AttributeSet~TSelf~ {
        <<abstract>>
        -Dictionary values
        +Color(color) TSelf
        +FontSize(fontSize) TSelf
        +Label(label) TSelf
        #Set(name, value) TSelf
    }

    class NodeAttributes {
        +Shape(shape) NodeAttributes
    }

    class EdgeAttributes {
        +Weight(weight) EdgeAttributes
    }

    class NodeShape {
        <<enumeration>>
        Box
        Ellipse
    }

    class Graph {
        +AddNode(name) GraphNode
        +AddEdge(sourceNode, destinationNode) GraphEdge
        +ToDotFormat() string
    }

    class GraphNode {
        +Name string
        +Attributes Dictionary
    }

    class GraphEdge {
        +SourceNode string
        +DestinationNode string
        +Attributes Dictionary
    }

    class DotFormatWriter {
        +Write(graph)
    }

    AttributeSet~NodeAttributes~ <|-- NodeAttributes
    AttributeSet~EdgeAttributes~ <|-- EdgeAttributes

    DotGraphBuilder ..> GraphBuilder
    GraphBuilder o-- Graph
    GraphBuilder ..> PendingElement~NodeAttributes~
    GraphBuilder ..> PendingElement~EdgeAttributes~

    PendingElement~TAttributes~ --> GraphBuilder
    PendingElement~TAttributes~ ..> NodeAttributes
    PendingElement~TAttributes~ ..> EdgeAttributes

    NodeAttributes ..> NodeShape

    Graph *-- GraphNode
    Graph *-- GraphEdge
    Graph ..> DotFormatWriter
```
