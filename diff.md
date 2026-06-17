# Практика «Дифференцирование»

## Описание предметной области и сущностей

В этой задаче функция представлена как дерево LINQ Expressions. Нужно построить новое выражение, которое соответствует производной исходной функции.

Algebra - точка входа. Получает лямбда-выражение, берет из него параметр и запускает дифференцирование.

ExpressionDifferentiator - основной обходчик выражения. Он разбирает константы, параметр, сложение, умножение и вызовы функций.

FunctionRules - отдельный класс с правилами для поддерживаемых математических функций. Сейчас в нем есть правила для Sin и Cos.

Expression - базовый тип узла дерева выражения из System.Linq.Expressions.  
ParameterExpression - переменная, по которой идет дифференцирование.  
BinaryExpression - узел для сложения и умножения.  
MethodCallExpression - узел вызова функции, например Math.Sin или Math.Cos.

```mermaid
classDiagram
    class Algebra {
        <<static>>
        +Differentiate(function) Expression~Func~double, double~~
    }

    class ExpressionDifferentiator {
        -ParameterExpression variable
        -FunctionRules functions
        +ExpressionDifferentiator(variable)
        +Differentiate(expression) Expression
        -DifferentiateParameter(parameter) Expression
        -DifferentiateSum(expression) Expression
        -DifferentiateProduct(expression) Expression
        -DifferentiateFunctionCall(expression) Expression
    }

    class FunctionRules {
        -MethodInfo SinMethod
        -MethodInfo CosMethod
        +TryDifferentiate(call, differentiate, derivative) bool
    }

    class Expression {
        <<System.Linq.Expressions>>
    }

    class ParameterExpression {
        <<System.Linq.Expressions>>
    }

    class BinaryExpression {
        <<System.Linq.Expressions>>
    }

    class MethodCallExpression {
        <<System.Linq.Expressions>>
    }

    class MethodInfo {
        <<System.Reflection>>
    }

    Algebra ..> ExpressionDifferentiator : запускает
    Algebra ..> Expression : принимает и возвращает

    ExpressionDifferentiator o-- FunctionRules : правила функций
    ExpressionDifferentiator --> ParameterExpression : переменная
    ExpressionDifferentiator ..> Expression : разбирает дерево
    ExpressionDifferentiator ..> BinaryExpression : сумма и произведение
    ExpressionDifferentiator ..> MethodCallExpression : вызов функции

    FunctionRules --> MethodInfo : Sin и Cos
    FunctionRules ..> MethodCallExpression : проверяет метод
    FunctionRules ..> Expression : строит производную
```
