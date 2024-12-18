# Подготовка к собеседованию по Python
## Оглавление
* [Python](#Python)
* [Типы данных](#Типы-данных)
    * [Последовательности](#Последовательности)
    * [Хэш таблица](#Хэш-таблица)
* [Итераторы](#Итераторы)
* [Генераторы](#Генераторы)
* [Контекстный менеджер](#Контекстный-менеджер) -- TODO примеры создания
* [Функции](#Функции)
    * [args и kwargs](#args-и-kwargs)
    * [Хвостовая рекурсия](#Хвостовая-рекурсия)
    * [Lambda](#Lambda)
    * [Функции высшего порядка](#Функции-высшего-порядка)
* [Декораторы](#Декораторы)
    * [Популярные декораторы](#Популярные-декораторы)
* [Исключения](#Исключения)
* [ООП](#ООП)
    * [Магические методы](#Магические-методы)
    * [Наследование](#Наследование)
    * [Singleton](#Singleton)
    * [Миксины](#Миксины)
* [Дескрипторы](#Дескрипторы)
* [Метаклассы](#Метаклассы)
* [Память](#Память)
    * [Счетчик ссылок](#Счетчик-ссылок)
    * [Сборщик мусора](#Сборщик-мусора)
    * [GIL](#GIL)
* [Модули/пакеты](#Модули/пакеты)
* [Интерпретатор](#Интерпретатор)
* [Либы](#Либы)
* [Линтеры и аннотация типов](#Линтеры-и-аннотация-типов)
* [Источники](#Источники)
---
## Другие файлы
* [Spark](https://github.com/Binary-hedgehog/-DataEngineer/blob/main/Spark.md)
* [Scala](https://github.com/Binary-hedgehog/-DataEngineer/blob/main/Scala.md)
* [SQL](https://github.com/Binary-hedgehog/-DataEngineer/blob/main/SQL.md)
* [Общая информация](https://github.com/Binary-hedgehog/-DataEngineer/blob/main/Common.md)
* [Вопросы работодателю](https://github.com/Binary-hedgehog/-DataEngineer/blob/main/Questions%20for%20employer.md)
---
## Python
[Go Back](#оглавление)
* Это императивный ЯП с динамической типизацией, который поддерживает парадигму ООП и немного может в функциональное программирование
* Все в Python - это объекты
* Это интерпретируемый ЯП
* Easy to start - hard to master _(личное мнение)_
---
## Типы данных
[Go Back](#оглавление)
* Неизменяемые
  * Числовые
      * Целочисленные
      * С плавающей точкой
      * Комплексные
  * Логические (boolean)
  * Последовательности
      * Строка
          * Функции `encode()` `decode()` предназначены для работы с байтстроками
      * Кортеж
      * FrozenSet
* Изменяемые - созданные объекты могут быть изменены или дополнены
  * Последовательности
      * Словари
          * Ключом словаря может быть **любой** неизменяемый объект
          * `items()`, `keys()` и `values()` возвращают соответствующие объекты словаря в виде последовательности
      * Списки
      * Множества
          * Представляют собой последовательность с уникальными элементами
### Последовательности 
* Последовательностью в Python называется итерабельный объект, который поддерживает эффективный доступ к элементам с использованием целочисленных индексов через специальный метод `__getitem__()` и поддерживает метод `__len__()`
* Последовательности также опционально могут реализовывать методы `count()`, `index()`, `__contains__()` и `__reversed__()` и другие
* Две последовательности равны, если они имеют одинаковый тип, равную длину и соответствующие элементы обоих последовательностей равны
* Копирование последовательности посредством `copy()` создает новый объект самой последовательности, но не создает новые объекты членов последовательности, просто переносит ссылки на них. Для того, чтобы рекурсивно копировать последовательность нужно использовать `deepcopy()`
    * Поможет избежать ошибок при обработке вложенных изменяемых последовательностей
#### Диапазоны
* Диапазоны – неизменяемые последовательности чисел, которые задаются началом, концом и шагом
* Пример диапазона: `range(1, 10, 2)`
### Хэш таблица
* Хэш-таблица это разреженный массив (массив, в котором имеются незаполненные позиции)
* В хэш-таблице _dict_ каждому элементу соответствует ячейка, содержащая два поля: ссылку на ключ и ссылку на значение элемента
* Поскольку размер всех ячеек одинаков, доступ к отдельной ячейке производится по смещению
* Для помещения элемента в хэш-таблицу нужно первым делом вычислить хэш-значение ключа элемента. Это делает встроенная функция `hash()`
* Хэш-функции — это функции, получающие на входе данные, обычно строку, и возвращающие число. При многократном вызове хэш-функции с одинаковыми входными данными она всегда будет возвращать одно и то же число, и возвращаемое число всегда будет находиться в гарантированном интервале. Этот интервал зависит от хэш-функции: в некоторых используются 32-битные целочисленные значения (то есть от 0 до 4 миллиардов), в других интервалы гораздо больше
    * Коллизия - когда два разных входных значения дают одно значение на выходе
* Доступ к данным хэш таблицы происходят за О(1)
* Хэш таблица применяется при формировании _Dict, Set, FrozenSet_
---
## Итераторы
[Go Back](#оглавление)
* Итерабельный объект – это объект, который возвращает свои элементы по одному за раз
    * В них определён метод `__iter__()` или `__getitem__()`
* `__iter__()` - возвращает объект, который может быть использован для итерации по элементам
    * Такой объект обязан иметь метод `__next__()` - этот объект и будет **итератором**
* Повторяемый вызов метода `__next__()` итератора или передача его встроенной функции `next()` возвращает последующие элементы потока
* Если больше не осталось данных, выбрасывается исключение _StopIteration_. После этого итератор исчерпан и любые последующие вызовы его метода `__next__()` снова генерируют исключение _StopIteration_
* Итератор обязан иметь метод `__iter__`, который возвращает сам объект итератора, так что любой итератор также является итерабельным объектом и может быть использован почти везде, где принимаются итерабельные объекты
* Может быть написан с помощью генератора
* Используется для прохода по всем значениям коллекции
---
## Генераторы
[Go Back](#оглавление)
* В зависимости от контекста, может означать либо функцию-генератор, либо итератор генератора (чаще всего, последнее). Методы `__iter__` и `__next__` у генераторов создаются автоматически
    * Из-за этого в Python любой генератор является итератором
* Создает последовательности (возвращает итератор)
* Как создать генератор
    * `(x for x in seq)`
    * `yield` в теле функции вместо `return`
### Генераторные функции
* Функция, в теле которой встречается ключевое слово `yield`
* `yield` замораживает состояние функции-генератора и возвращает текущее значение. После следующего вызова `__next__()` функция-генератор продолжает своё выполнение с того места, где она была приостановлена
### Генераторы последовательностей
* Генератором можно удобно создавать списки или словари
* Генератор списков пример: `[i**2 for i in a if i != 2]`
* Генератор словаря пример: `{k: v for k, v in zip(keys, values)}`
---
## Контекстный менеджер
[Go Back](#оглавление)
* Контекстный менеджер - объект, который определяет логику входа и выхода в блок кода внутри этого объекта
* События входа и выхода из блока определены методами `__enter__` и `__exit__`
    * Для создания своего контекстного менеджера у объекта нужно определить эти методы
        * https://habr.com/ru/articles/739326/
    * `__exit__` должен содержать следующие аргументы `(exc_class, exc_instance, traceback)` так как он будет возвращать их в случае выхода из блока по причине исключения
* `with` - гарантирует что будет выполнен вход и выход из того, что внутри оператора
* Пример контекстного менеджера - класс, порожденный функцией `open`. Он гарантирует, что файл будет закрыт даже в том случае, если внутри блока возникнет ошибка
---
## Функции
[Go Back](#оглавление)
* Аргументы в функции передаются в виде ссылок на объекты
   * Это значит, что изменяемые объекты могут быть изменены изнутри функции
* Аргументы функции могут быть заданы по умолчанию
   * При этом стоит избегать делать такими аргументами изменяемые объекты
### args и kwargs
* Выражения `*args` и `**kwargs` объявляют в сигнатуре функции
* Они означают, что внутри функции будут доступны переменные с именами `args` и `kwargs` (без звездочек)
* `args` – это кортеж, который накапливает позиционные аргументы
* `kwargs` – словарь именованных аргументов, где ключ – имя параметра, значение – значение параметра
* Если в функцию не передано никаких параметров, переменные будут соответственно равны пустому кортежу и пустому словарю, а не `None`
### Хвостовая рекурсия
* Это особый вид рекурсии, когда функция заканчивается вызовом самой себя без дополнительных операторов, тем самым не забивая стэк
* Интерпретатор Python не умеет оптимизировать хвостовую рекурсию, поэтому обычно её заменяют другими механизмами, например, циклами или обычной рекурсией
### Lambda
* Анонимная функция - не занимает место в пространстве имен
* Определяется одной строкой без использования ключевого слова `def`, но с помощью `lambda`
* Пример `val = lambda x: x * 2 + 1`
### Функции высшего порядка 
* Это функции, которые принимают другие функции в качестве аргументов или возвращают функции как результат
* Примеры таких функций
  * Сюда входят функции `map()`, `filter()`, `reduce()` представленные в модуле `functools`
  * Декораторы
---
## Декораторы
[Go Back](#оглавление)
* Читаем статьи и радуемся жизни
    * https://habr.com/ru/articles/141411/
    * https://habr.com/ru/articles/141501/
* Декоратор - паттерн проектирования, когда один объект изменяет поведение другого
* Декоратором может быть любой вызываемый объект: функция, лямбда, класс, экземпляр класса. В последнем случае определите метод `__call__`
* Декоратор является примером **замыкания** - функция, находящаяся целиком в теле другой функции. При этом вложенная внутренняя функция содержит ссылки на локальные переменные внешней функции
* В простом виде декоратор, это функция, которая принимает в качестве аргумента
``` Python 
def decorator(bar): # декорирующая функция
    def wrapper(): # функция переопределяющая поведение декорируемой функции
        print(1)
        bar()
        print(2)
    return wrapper

@decorator  # это аналог записи temp = decorator(temp)
def temp():
    print(0)
```
* Чтобы передать декорируемой функции агрументы можно надо сделать так
``` Python
def decorator(bar):
    def wrapper(*args, **kwargs):
        print(1)
        bar(args, kwargs)
        print(2)
    return wrapper
```
* Чтобы передать какие-то аргументы в декоратор
    * Такаим образом мы получили **фабрику декораторов**
    * Аргументы фабрики декораторов инициализируются лишь раз во время декорирования функции, изминить их потом уже нельзя
``` Python
def super_decorator(*args1, **kwargs1):
    print(args1, kwargs1)
    def decorator(bar):
        def wrapper(*args, **kwargs):
            print(1)
            bar(args, kwargs)
            print(2)
        return wrapper
    return decorator

@super_decorator() 
def temp():
    print(0)
```
* 
### Популярные декораторы
* `@classmethod`
    * Позволяет функции использовать атрибуты класса, а не атрибуты экземпляра класса, в котором был вызван
   ``` Python
   class A:
       a = 2
   
       def foo1(self):
           print(self.a)
   
       @classmethod
       def foo2(cls):
           print(cls.a)
   
   b = A()
   b.a = 3
   b.foo1()  # 3
   b.foo2()  # 2
   ```
* `@staticmethod`
    * Функция, которая ничего не знает о классе или экземпляре класса и может быть вызвана без инициализации класса
   ``` Python
   class A:
       @staticmethod
       def foo(x):
           print(x)
   
   A.foo(7)  # 7 - без инициализации класса
   ```
* `@property`
    * Декоратор, который позволяет определить доступ к атрибутам класса
        * Например, при помощи него можно сделать имитацию инкапсуляции, определив у атрибута только `getter`
    * Позволяет определить `getter`, `setter`, `deleter`
   ``` Python
   class MyClass:
       def __init__(self):
           self._value = None
    
       @property  # по факту это getter
       def value(self):
           return self._value
    
       @value.setter
       def value(self, value):
           self._value = value
    
       @value.deleter
       def value(self):
           del self._value
   ```
---
## Исключения
[Go Back](#оглавление)
* Обработка исключений — механизм, предназначенный для описания реакции программы на ошибки во время выполнения и другие возможные проблемы (исключения)
* Пример кода
``` Python
try:
    1/0
except ZeroDivisionError:
    print("why?")
else:
    print("ok")
finally:
    raise Exception("...")
```
* `try` пытается выполнить определенный кусок кода
* `except` попытается отловить указанную ошибку
* `else` выполниться, если не будет выполнен блок `except`
* `finally` выполнится в конце
* `raise` сгенерирует исключение (остановит выполнение программы) 
---
## ООП
[Go Back](#оглавление)
* Python придерживается основных парадигм ООП -
    * Абстракция - это процесс выделения общих характеристик и функциональности объектов или системы, игнорируя детали реализации
    * Наследование - абстрактный тип данных может наследовать данные и функциональность некоторого существующего типа, способствуя повторному использованию компонентов программного обеспечения
    * Полиморфизм - это способность обьекта использовать методы производного класса, который не существует на момент создания базового
    * Инкапсуляция - это подразумевает скрытие внутренней реализации объекта от внешнего мира
        * Однако в Python инкапсуляция условная (на уровне соглашения)
        ``` Python
        class A:
           _a = 1   # protected - доступен на уровне класса и в дочерних классах
           __b = 2  # private - доступен только на уровне класса
        
        A()._a     # не выдаст ошибку
        A().__b    # а это уже ошибка 
        A()._A__b  # опять не выдаст ошибку
        ```
        * Проблему инкапсуляции можно решить при помощи
            * Дескрипторов
            * Библиотеки `accessify` - https://habr.com/ru/articles/443192/
            ``` Python
            from accessify import protected, private
            
            class MyClass:
                @protected
                def a(self):
                    pass
      
                @private
                def b(self):
                   pass
            ```
### Магические методы
* Магическими метода называют методы, имена которых начинаются и заканчиваются двойным подчеркиванием
* Магические они потому, что почти никогда не вызываются явно. Их вызывают встроенные функции или синтаксические конструкции. Например, функция `len()` вызывает метод `__len__()`
* Популярные методы
    * `__new__` - обрабатывает создание объекта
    * `__init__` - обрабатывает инициализацию объекта
        * Вызывается после `__new__`
    * `__dir__` - возвращает список атрибутов указанного объекта в алфавитном порядке
    * `__slots__` - жестко фиксирует набор полей класса
### Наследование
* Для обращение к родительскому классу нужно использовать ключевое слово `super`
* MRO – method resolution order, порядок разрешения методов. Алгоритм, по которому следует искать метод в случае, если у класса два и более родителей
    * Сначала экземпляр
    * Затем его класс
    * Далее все суперклассы его класса с обходом сначала с глубину, а затем слева направо
### Singleton
* Это класс, который создается единожды и не создает новых объектов себя при повторной инициализации
* Есть много способов создать Singleton объект, пример через метакласс ниже
``` Python
class Singleton(type):
    _instances = {}
    def __call__(cls, *args, **kwargs):
        if cls not in cls._instances:
            cls._instances[cls] = super(Singleton, cls).__call__(*args, **kwargs)
        return cls._instances[cls]

class MyClass(BaseClass, metaclass=Singleton):
    pass
```
### Миксины
* Паттерн проектирования в ООП, когда в цепочку наследования добавляется небольшой класс-помощник
``` Python
class NowMixin(object):
    def now():
        return datetime.datetime.utcnow()
```
* Тогда любой класс, наследованный с этим миксином, будет иметь метод `now()`
* В названия миксинов принято добавлять слово `Mixin`, так как не существует никакого механизма для понимания полноценный это класс или миксин
---
## Дескрипторы
[Go Back](#оглавление)
* Дескриптор - это объект, с помощью которого можно контролировать доступ к атрибутам
* Это происходит через определение методов `__get__`, `__set__`, и `__delete__` у дескриптора
``` Python
class Descriptor:
    def __get__(self, instance, owner):
        return self.__value

    def __set__(self, instance, value):
        self.__value = value

    def __delete__(self):
        del self.__value

class A:
   x = Descriptor()

a = A()
# a.x - Это будет ошибка, так как атрибут __value на данный момент еще не определен
```
* А еще есть необязательный метод `__set_name__` благодаря которому можно задать значение по умолчанию
``` Python
class Descriptor:
    def __set_name__(self, owner, name):
        self.private_name = '_' + name

    def __get__(self, instance, owner):
        return getattr(instance, self.private_name, None)

    def __set__(self, instance, value):
        setattr(instance, self.private_name, value)


class B:
    x = Descriptor()

a = A()
a.x # None
```
* Дескрипторы могут решить проблему Python с инкапсуляцией
* `property()` как раз работает как дескриптор
* Тут можно посмотреть подробнее - https://habr.com/ru/companies/otus/articles/801595/
---
## Метаклассы
[Go Back](#оглавление)
* Метаклассы - классы, создающие классы
* `type` - это метакласс, который Питон внутренне использует для создания всех классов
    * `type(obj)` с одним аргументом возвращает объект класса аргумента `obj.__class__`
    * `type(name, bases, dict)` с тремя аргументами он создает новый класс
        * _name_ - имя нового класса
        * _bases_ - кортеж с родительскими классами
        * _dict_ - словать с пространством имен класс (то есть какая-то переменная и её значение)
---
## Память
[Go Back](#оглавление)
* Python — это язык с управляемой памятью
* При запуске Python-программы создается новый процесс, в рамках которого операционная система выделяет пул ресурсов, включая виртуальное адресное пространство. В эту память загружается интерпретатор Python вместе со всеми необходимыми ему для работы данными, включая код вашей программы
* Оставшаяся свободная виртуальная память может использоваться для хранения информации об объектах Python’а. Для управления этой памятью в CPython используется специальный механизм, который называется **аллокатор**. Он используется каждый раз, когда вам нужно создать новый объект
* **Аллокатор** состоит из:
   * **Арена** 
      * Большой непрерывный кусок памяти (обычно 256 килобайт), содержит несколько **пулов** (страниц виртуальной памяти операционной системы)
      * Арены организованы в двусвязный список и отсортированы от самой заполненной к самой свободной
      * Арены — единственное место, в котором происходит запрос и освобождение памяти в Python
   * **Пул**
      * Одна страница виртуальной памяти (обычно 4 килобайта)
      * Каждый пул предназначен для хранения блоков одинакового размера (то есть в одном пуле могут быть только блоки одного и тоже размера, при этом в арене могут быть пулы с разными размерами блоков)
      * Каждый пул может быть в одном из трех состояний — used, full и empty.
          * Full означает, что пул полностью занят и не может принять новые объекты.
          * Empty — что пул полностью пустой и может быть использован для хранения новых объектов (при этом пустой пул может быть использован для хранения объектов любого размера).
          * Used — это пул, который используется для хранения объектов, но при этом еще не заполнен полностью
   * **Блок**
      * Маленький кусочек памяти, используемый для хранения одного объекта
      * Могут быть размером 8, 16, 24, 32 …. 512 байт
* Когда Python’у нужно расположить какой-то объект в оперативной памяти он ищет подходящую арену
* Если такой нету, он запрашивает новую арену у операционной системы
* Если какая-то арена полностью освобождается от объектов, она возвращается назад операционной системе и память освобождается
* Python поддерживает списки пулов каждого из размеров и отдельно список пустых пулов. Если вы хотите создать новый объект, то Python сначала пытается найти used-пул с нужным размером блока и расположить объект там. Если used-пула нет, тогда берется empty-пул, и объект создается в нем (а сам пул из статуса empty переводится в статус used). Если и empty-пулов нет, то запрашивается новая арена
* Таким образом, чем компактнее живут объекты в рамках арен, тем ниже общее потребление оперативной памяти программой
### Счетчик ссылок
* Каждый объект в Python — это, в первую очередь, объект, унаследованный от базового класса _PyObject_
* _PyObject_ содержит всего два поля: `ob_refcnt` — количество ссылок, и `ob_type` — указатель на другой объект, тип данного объекта
* Когда счетчик ссылок объекта становится 0 - объект удаляется из памяти
* Счетчик ссылок подвержен проблемам в многопоточной среде, чтобы этого избежать есть **GIL**
* Счетчик ссылок не позволяет отслеживать ситуации с кольцевыми зависимостями, когда объект A содержит ссылку на B, а B — на A
### Сборщик мусора
* Garbage collector основан на идее поколений
* GC отслеживает объекты (объекты-контейнеры, которые могут содержать ссылки на другие объекты) и смотрит, доступны ли они из основного кода на Python. Если нет, то сборщик их удаляет. Если да — оставляет
* В отличие от счетчика ссылок, механизм сборки мусора не работает постоянно. Он запускается от случая к случаю на основе эвристик
* GC разделяет объекты на три поколения. Каждый новый объект начинает свой путь с первого поколения
* Если объект переживает раунд сборки мусора, он переходит к более старому поколению
* В младших поколениях сборка происходит чаще, чем в старших. Идея простая: чем дольше живет объект, тем с большей вероятностью он проживет еще
### GIL
* В любой момент может выполняться только один поток Python
* Глобальная блокировка интерпретатора — GIL — тщательно контролирует выполнение тредов
* GIL гарантирует каждому потоку эксклюзивный доступ к переменным интерпретатора
* Принцип работы прост. Потоки удерживают GIL, пока выполняются. Однако они освобождают его при блокировании для операций ввода-вывода. Каждый раз, когда поток вынужден ждать, другие, готовые к выполнению, потоки используют свой шанс запуститься
* Когда поток начинает работу, он выполняет захват GIL. Спустя какое-то время планировщик процессов решает, что текущий поток поработал достаточно, и передает управление следующему потоку. Поток №2 видит, что GIL захвачен, так что он не продолжает работу, а погружает себя в сон, уступая процессор потоку №1
* В поздних версиях GIL может быть удержан потоком не дольше 5 мс. GIL также освобождается, если поток совершает системный вызов, работает с диском или сетью
---
## Модули/пакеты
[Go Back](#оглавление)
* Модуль – функционально законченный фрагмент программы, оформленный в виде отдельного файла с исходным кодом или поименованной непрерывной её части. Модули позволяют разбивать сложные задачи на более мелкие в соответствии с принципом модульности. Файл, который содержит исходный код на языке Python, является модулем. 
    * Название модуля доступно в его глобальной переменной `__name__`. Если модуль не импортирован, а запущен как скрипт, то `__name__` устанавливается в значение `"__main__"`
* Пакеты - объединение нескольких модулей. Пакеты служат как пространства имён для модулей и способ их структурирования. Любой пакет является модулем, но не каждый модуль является пакетом. Как правило, модули представляются в виде файлов, а пакеты – каталогов в файловой системе (но не всегда). Для того, чтобы каталог был пакетом, в нём должен находиться файл __init__.py. Он автоматически выполняется при импортировании соответствующего модуля и может содержать определённые действия для инициализации или быть пустым
    * Пакеты могут объединяться в библиотеки
---
## Интерпретатор
[Go Back](#оглавление)
* Это программа, конвертирующая ваши инструкции (Python код) в байт код и выполняющая их
* Интерпретатор CPython работает в 4 шага
    * Лексический анализ
    * Парсинг
        * Парсер забирает инструкции и формирует AST (Абстрактное Синтаксическое Дерево)
    * Компиляция
        * Компилятор затем преобразует AST в одни (или несколько) объектов кода (байткод + обвязка)
    * Интерпретация
        * Виртуальная машина Python (PVM) выполняет каждый объект
* Другие интерпретаторы
    * IronPython
    * Anaconda
    * PyPy
    * Jython
---
## Либы 
[Go Back](#оглавление)
* Ниже перечислены некоторые популярные библиотеки, коих, на самом деле, более 137000
### Asyncio
* Библиотека для асинхронного программирования
* Из-за особенностей GIL имеет смысл в местах, где потоки зависят от внешних источников (например, ожидание ответа от сервера)
* Добавляет конструкции `async/await` и многое другое
### NumPy
* Библиотека для решения математических задач, в широком смысле этого понятия
* Написана на C и Fortran, за счет чего работает быстрее
* Является родительской библиотекой для многих других (scikit-learn, SciPy, Pandas)
### Pandas
* Библиотека для работы с большими данными
* Для работы требуется компилятор C/C++ так как это обеспечивает быстродействие работы
### PyTest
* Это среда тестирования, основанная на Python. Ее используют для написания и выполнения тестового кода
### Django
* Бесплатный и свободный фреймворк для веб-приложений, написанный на Python
---
## Линтеры и аннотация типов 
[Go Back](#оглавление)
* [PEP8](https://peps.python.org/pep-0008/) - документ, описывающий общепринятый стиль написания кода на языке Python
* Линтер - это инструмент для анализа исходного кода с целью выявления проблем, структурных ошибок, стилевых нарушений и других недочетов
    * Популярные линтеры Python
        * Pycodestyle
        * Pylint
        * Vulture
        * Flake8
        * Prospector
        * Pylama
        * autopep8
        * yapf
        * black
* Начиная с **Python 3.6** всем объектам можно аннотировать тип. Для этого надо импортировать `typing`
    * Таким образом можно закрыть некоторые проблемы языка с динамической типизацией
``` Python
def foo(x: int) -> str:
    return str(x)
```
---
## Источники
[Go Back](#оглавление)
* [https://github.com/yakimka/python_interview_questions/blob/master/questions.md/#Python](https://github.com/yakimka/python_interview_questions/tree/master?tab=readme-ov-file)
* https://habr.com/ru/companies/ruvds/articles/747084/
* https://habr.com/ru/companies/otus/articles/801595/
* https://stackoverflow.com/questions/6760685/what-is-the-best-way-of-implementing-singleton-in-python
* https://habr.com/ru/articles/264609/
* https://pythonchik.ru/osnovy/kak-rabotaet-python-interpretator
* https://docs-python.ru/tutorial/vstroennye-funktsii-interpretatora-python/klass-type/
