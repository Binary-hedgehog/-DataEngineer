# Подготовка к собеседованию по Scala
## Оглавление
[Go Back](#оглавление)
---
## Другие файлы
* [Spark](https://github.com/Binary-hedgehog/-DataEngineer/blob/main/Spark.md)
* [Python](https://github.com/Binary-hedgehog/-DataEngineer/blob/main/Python.md)
* [SQL](https://github.com/Binary-hedgehog/-DataEngineer/blob/main/SQL.md)
* [Общая информация](https://github.com/Binary-hedgehog/-DataEngineer/blob/main/Common.md)
* [Вопросы работодателю](https://github.com/Binary-hedgehog/-DataEngineer/blob/main/Questions%20for%20employer.md)
---
## Типы переменных
[Go Back](#оглавление)
* Изменяемость
    * в Scala все переменные делятся на две подгруппы mutable и imutable - то есть изменяемые и неизменяемые
* val - переменная данного типа инициализируется в начале работы программы и **не может** изменяться
* lazy val - переменная данного типа инициализируется в момент первого обращения к ней и **не может** изменяться
* var - переменная данного типа инициализируется в начале работы программы и **может** изменяться
* def - переменная данного типа инициализируется каждый раз при её вызове
---
## Типы данных
[Go Back](#оглавление)
### Основные типы
#### Числовые
* Целые
    * Byte - 8-битовое число 
    * Short - 16-битовое число 
    * Int - 32-битовое число 
    * Long - 64-битовое число 
* С плавающей точкой
    * Соответствуют стандарту [IEEE754](https://ru.wikipedia.org/wiki/IEEE_754-2008)
    * Float - 32-битовое число одинарной точности
    * Double - 64-битовое число двойной точности
#### Символьные 
* Char - 16-битовый Unicode-символ
* String - последовательность из Char
    * Неформатируемые строки оборачиваются в `"""your string"""`, вместо `"your string"`
        * Такие строки игнорируют управляющие последовательности и позволяют писать в несколько строк
        ``` Scala
        println("""Welcome to         
                   Scala, dude""")    
        // Welcome to
        //                Scala, dude
        ``` 
    * Интерполяция строк
        * **s** - после знака `$` к выражению применяется метод `toString`
           ``` Scala
           val a = 12  
           println(s"$a less then ${a * 2}") // 12 less then 24
           ``` 
        * **raw** - ведет себя как **s**, но игнорирует управляющие последовательности символьных литералов
           ``` Scala
           println(raw"AA\\bb") // AA\\bb
           ``` 
        * **f** - форматируемые строки, использующие синтаксис `java.util.Formatter`, в остальном ведет себя как **s**
           ``` Scala
           println(f"${math.Pi}%.5f") // 3.14159
           ``` 
#### Остальные
* Boolean - логический тип, принимающий значения `true` или `false`
* Unit - данный тип используется когда какая либо фкнкция/метод ничего не возвращают (что не очень функционально)
    * На самом деле они возврщают unit-значение `()`
   ``` Scala
   println("hi") == () // true
   ```
### Коллекции
* Any, Any val, Any seq, Nothing, Коллекции, 
* Множества, отображения
---
## Функции
[Go Back](#оглавление)
### Немножко про возможности функций в Scala
``` Scala
// Это функция
(x: Int) => x + 1
// Повторяющиеся аргументы
def foo(args: String*) = for {i <- args}{print(i)}
foo("aa", "?") // aa?
// Именованные аргументы
foo(args = "1")
// Аргументы по умолчанию
def bar(i: Int = 2) = println(i)
bar() // 2
// А это называется функциональное значение
val a = bar
```
* Когда функция без аргументов изменяет какие-либо внешние переменные своим вызовом (использует замыкания) стоит ставить пустые скобки `()`, в остальных же случаях их нужно опускать
``` Scala
 class Temp {
   private var count = 0
   def inc(): Unit = count += 1
   def getCount: Int = count
 }
val t = new Temp()
println(t.getCount) // 0
t.inc()
println(t.getCount) // 1
```
### Замыкания
* Замыкания - это когда функция может использовать и изменять переменные из внешней области видимости
``` Scala
var a = 2 // свободная переменная для функций foo и bar 
def bar(x: Int) = x * a
def foo(x: Int): Int = {
   a = x - a
   x + a
}
println(foo(6)) // 10
println(a)      // 4
println(bar(3)) // 12
```
### Каррирование
* Это когда у функции появляется несколько списков аргументов
* Соответственно мы можем последовально проициализировать `x`, а потом уже `y` (см. пример ниже)
``` Scala
// Обычная функция
def plus(x: Int, y: Int) = x + y
// Каррированная функция
def plus(x: Int)(y: Int) = x + y
// А вот что в такой момент происходит под капотом
def plus(x: Int) = (y: Int) => x + y
```
### Хвостовая рекурсия
### Функциональные значения?
---
## ООП
[Go Back](#оглавление)
наследование?
### Class
* Классы - это шаблоны объектов
   ``` Scala
   // Создание класса
   class TempClass1
   class TempClass2(y: Int)
   class SomeClass {
    def str: String = ""
    // Пример инкапсуляции
    private val s = 1
   }
   // Инициализация класса, в Scala 3 без ()
   val a = new TempClass1() 
   val b = new TempClass2(2)
   
  // Более интересный класс, с наследованием
  class SomeOtherClass(val x: String) extends SomeClass { // 'val x' используется чтобы объявить переменную, к которой можно будет получать доступ извне класса (см. chooseMax)
    // Устанавливаем требования к инициализации класса переменной 
    require( x != "" ) // функция объекта Predef
    // переопределение родительской функции
    override def str: String = x + "!" 
    // Используем экземпляр созданного класса
    def chooseMax(abc: SomeOtherClass): SomeOtherClass = 
      if (x.length < abc.x.length) abc else this // 'this' - ключевое слово обращения к экземпляру данного класса
    // Вспомогательный конструктор, который позволяет инициализировать данный класс, например, через 2 переменные
    def this(a: String, b:String) = this(a + b)
  }
   ```
### Object

### Trait
### Case class
### Abctract class
* `abstract` указывает на то, что у класса могут быть абстрактные члены
    * Абстракнтные члены класса - объекты не имеющие конкретной реализации
    * Из-за этого невозможно создать экземпляр данного класса
``` Scala
 // Абстрактный класс
 abstract class Some {
   // Абстрактный метод
   def foo: List[String]
   def len: Int = foo.length
 }

 new Some() // error
```
### Наследование
* Что не наследуется
    * Приватные элементы суперкласса
    * Элемент суперкласса с таким же именем и параметрами, который реализован в подклассе
`final def`, `final class`
---
## ФП
* map, flatMap, filter, foldLeft, for comprehension, yeild
* "Моноид" "Монада" "Композиция монад"
## Option,
## Исключения
## 

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
* метод - функция включенная в состав объекта
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
