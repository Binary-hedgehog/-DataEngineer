# Подготовка к собеседованию по Scala

## Типы переменных
* mutable, imutable

* val - переменная данного типа инициализируется в начале работы программы и **не может** изменяться
* lazy val - переменная данного типа инициализируется в момент первого обращения к ней и **не может** изменяться
* var - переменная данного типа инициализируется в начале работы программы и **может** изменяться
* def - переменная данного типа инициализируется каждый раз при её вызове
---
## Типы данных
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
* Boolean - логический тип, принимающий значения `true` или `false`
* Unit - данный тип используется когда какая либо фкнкция/метод ничего не возвращают (что не очень функционально)
    * На самом деле они возврщают unit-значение ()
``` Scala
println("hi") == () // true
```
### Коллекции
* Any, Any val, Any seq, Nothing, Коллекции, 
* Множества, отображения
---
## ООП
наследование?
### class
* Классы - это шаблоны объектов
``` Scala
class TempClass1
class TempClass2(y: Int)
val a = new TempClass1() // Инициализация класса, в Scala 3 без ()

class SomeClass {
  def str: String = ""
}
class SomeOtherClass(val x: String) extends SomeClass { // 'val x' используется чтобы объявить переменную, к которой можно будет получать доступ извне класса (см. chooseMax)
  require( x != "" ) // функция объекта Predef, которая устанавливает требования к инициализации класса переменной
  override def str: String = x + "!" // переопределение родительской функции
  def chooseMax(abc: SomeOtherClass): SomeOtherClass = {
    if (x.length < abc.x.length) abc else this // 'this' - ключевое слово обращения к экземпляру данного класса
  }
  def this(a: String, b:String) = this(a + b) // Вспомогательный конструктор, который позволяет инициализировать данный класс через 2 переменные
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
* Predef


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
