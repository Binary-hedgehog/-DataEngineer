# Подготовка к собеседованию по Scala

## Типы переменных
* val - переменная данного типа инициализируется в начале работы программы и **не может** изменяться
* lazy val - переменная данного типа инициализируется в момент первого обращения к ней и **не может** изменяться
* var - переменная данного типа инициализируется в начале работы программы и **может** изменяться
* def - переменная данного типа инициализируется каждый раз при её вызове
---
## Типы данных -- TODO
* mutable, imutable
### Основные типы
#### Числовые
* Byte
* Short
* Int
* Long
* Float
* Double
#### Символьные 
* Char
* String
#### Остальные
* Boolean
* Unit '()'
### Коллекции



* Any, Any val, Any seq, Nothing, Коллекции, 
* Множества, отображения
---
## ООП
### class
* Классы - это шаблоны объектов
``` Scala
class SomeClass
val a = new SomeClass() // без () в Scala 3
class SomeOtherClass(x: String) extends SomeClass {
  require( x != "" ) // функция объекта Predef, которая автоматически импортируется в каждый пакет Scala
}
```
### object

### trait
### case class
### abctract class
### Наследование
---
## ФП
* map, flatMap, filter, foldLeft, for comprehension, yeild
* "Моноид" "Монада" "Композиция монад"
## Option
## Интерполяция строк """, s, raw, f

## implicit
## Future
## Either
## Pattern matching
## Given
## Пакеты


## Дополниетльные вопросы
* Nil, null, none, nothing
* Какие фреймворки поддерживает Scala
* Что такое объекты, методы, операторы, операнды, литералы, инфексная, постфиксная и преФиксная нотации
* Что это за ЯП


* Знание синтаксиса Scala и стандартной библиотеки: коллекций, Future, Option, Either и так далее
* Умение писать функциональный код (без Mutable State, While Loops и так далее) и пользоваться Cats и ZIO (если был опыт)
* What is a companion object?
* What is a functor?
* What is a applicative?
* Explain the actor model.
*  В чем разница между конкурентностью и параллелизмом?
*  


## Источники
* https://github.com/Jarlakxen/Scala-Interview-Questions?tab=readme-ov-file#reactive-programming-questions
* https://proglib.io/p/4-scala-questions
