# Подготовка к собеседованию для Spark Data Engineer
[Go Back](#оглавление)
## Оглавление
+ [Spark](#spark)
  + [Spark Core](#spark-core)
  + [Spark SQL](#spark-sql)
  + [Spark Catalist](#spark-catalist)
+ [SQL](#sql)
+ [Оркестраторы](#оркестраторы)
+ [GIT](#git)
+ [Прочие полезности](#прочие-полезности)
  + [Hadoop](#hadoop)
  + Kafka
  + Flink
+ Про разработку и вообще
  + [Принципы разработки](#принципы-разработки)
  + [Алгоритмы](#алгоритмы)
  + [Алгоритмы кластеризации](#алгоритмы-кластеризации)
## Spark
* Структура Spark
### Spark Core
[Go Back](#оглавление)
* Включает функции управления памятью, а также восстановлению в случае аварий, планирования задач в кластере и взаимодействие с хранилищем
### Spark SQL 
[Go Back](#оглавление)
* Механизм запросов SQL, который поддерживает различные источники данных и использует такую структуру данных, как DataFrame
#### Spark Catalist
[Go Back](#оглавление)
* Catalyst — оптимизатор запросов
* Операции, которые вы можете выполнять в приложении Spark, делятся на два типа
  * Преобразования (Transformations): это операции, которые при применении к RDD возвращают ссылку на новый RDD, созданный посредством преобразования. Некоторые из наиболее часто используемых преобразований — это filter и map
    * Узкие преобразования (Narrow transformations) — это когда нет перемещения данных между разделами. Преобразование применяется к данным каждого раздела RDD, и создается новый RDD с тем же числом разделов. Например, filter — это узкое преобразование, потому что фильтрация применяется к данным каждого раздела, а полученные данные представляют раздел во вновь созданном RDD
    * Широкие преобразования (Wide transformations) требуют перемещения данных между разделами или так называемого перемешивания. Данные перемещаются с целью создания нового RDD. Например, sortBy сортирует данные на основе определенного столбца и возвращает новый RDD
  * Действия (Actions): При применении к RDD возвращается значение. Например, count возвращает количество элементов в RDD драйверной программе, или функция collect отправляет данные сами данные
* Операции DataFrame и DataSet разделены на одни и те же категории, поскольку эти API-интерфейсы построены на механизме RDD
* Основной тип данных в Catalyst — это дерево, состоящее из узловых объектов. Каждый узел имеет тип и дочерние узлы (а может их не иметь). Новые типы узлов определены в Scala как подклассы класса TreeNode. Эти объекты неизменяемы, и ими можно управлять с помощью функциональных преобразований
* Отложенные вычисления
----дописааать
### Spark Streaming
* Обработка потоковых данных в режиме реального времени
### MLlib 
* библиотека для машинного обучения
### GraphX 
* Библиотека для работы с графами
### Отличия Spark и PySpark
* Основным отличием является обработка UDF, в том плане, что Python работает кратно медленнее, чем Scala
* Некоторые методы работают иначе, у некоторых разные параметры, но это надо точечно сравнить, общей инфы нет
### Ect
### MapReduce как аналог Spark 
* Минусы MapReduce
  * Модель MapReduce выполняет вычисления за 2 этапа. В первую очередь он разделяет данные на части, передавая их на кластерные узлы для обработки. Потом каждый узел производит обработку данных с отправкой результата на главный узел, где и сформировывается итоговый результат распределенных вычислений
  * MapReduce регулярно обращается к диску, ведь именно там он сохраняет промежуточные и финальные итоги вычислений
  * Для написания хорошего решения на MapReduce понадобится довольно высокий уровень экспертности
* Плюсы Spark 
  * Spark выполняет обработку данных в памяти и, по сути, почти не обращается к диску
  * В Spark существует API для различных языков программирования, в результате чего писать код заметно проще
## Sql
[Go Back](#оглавление)
### Joinы (тут инфы чуть больше чем в обычном Sql, так как spark рулит)
* Виды Join
  * Left (Left Outer) - оставляет все из левой таблицы, добавляет туда то, что нашел в правой. Несцепленные строки из левой заполняет null в колонках правой
  * Right (Right Outer) - аналог Left, но работает в другую сторону
  * Inner - оставляет то, что сцепилось из левой и правой таблицы
  * Full (Full Outer) - сцепляет все что можно слева и спава, остальное в null, все как мы привыкли, в итоге получим все
  * Cross - все на все, умножаем поля слева на поля справа (с точки зрения результирующего количества полей)
  * Semi (Left/Right) - оставляет только то из левой таблицы, что нашлось в правой
  * Anti (Left/Right)  оставляет только то из левой таблицы, что НЕ нашлось в правой
* Механизмы Join
  * Broadcast Hash Join
    * Одна таблица транслируется всем исполнителям, для каждого из которых строится хэш таблица
    * Далее они начинают соединяться без перетасовки
    * Требует много памяти
  * Shuffle Hash Join
    * Две таблицы выравниваются по схеме партиционирования (либо перемешиваются до похожести)
    * Далее базовый Hash Join (хэш таблица строится начиная от меньшей по размеру)
    * Меньшее требование к памяти нежели у Broadcast Hash Join (из-за того, что таблица над меньшим объемом данных)
  * Sort Merge Join
    * Shuffle на минималках, для тех, кому жалко ресурсы
  * Cartesian Join - Декартово соединение
    * Используется исключительно для выполнения перекрестного (Cross) соединения между двумя наборами входных данных
  * Broadcast Nested Loop Join
    * Входной набор транслируется на всех
    * А дальшне цикликом
    * Лучше такое не делать, конечно  
### Оконки
### Всякие СУБД под SQL
#### Greenplam
* Надстройка над PostgresSql для работы с BigData
#### Casandra
* Это нереляционная отказоустойчивая распределенная СУБД, рассчитанная на создание высокомасштабируемых и надёжных хранилищ огромных массивов данных, представленных в виде хэша
#### ClickHouse
* Cтолбцовая система управления базами данных (СУБД) для онлайн-обработки аналитических запросов (OLAP)
## Оркестраторы
[Go Back](#оглавление)
* Задачи решаемые оркестратором
  * Планирование задач — основная функция, позволяющая избавиться от ручного запуска рутинных задач по расчёту витрин, загрузке данных, резервному копированию
  * Управление зависимостями. Часто задачу нужно запустить не только в определённый промежуток времени, но и с учётом статуса других задач
  * Репроцессинг. Если известно, что какая-то задача требует перезапуска (например, были загружены неполные данные на предыдущем этапе), то перезапуска требуют и задачи, зависящие от неё. Кроме того, перезапуск может быть необходим за несколько временных периодов
  * Мониторинг
### Airflow
* В основе концепции Airflow лежит DAG — направленный ациклический граф. Он описывает процессы обработки данных и позволяет объединять задачи, определяя правила их совместной работы
  * https://airflow.apache.org/
* Airflow состоит из нескольких компонентов, но главные из них — scheduler и webserver. Без них ничего не запустится
  * Scheduler (планировщик) отслеживает все задачи и DAGs, а затем запускает экземпляры задач после установки их зависимостей. В фоновом режиме планировщик запускает подпроцесс, который отслеживает и синхронизирует все DAGs в указанном каталоге. По умолчанию он раз в минуту собирает результаты синтаксического анализа DAGs и проверяет, необходимо ли запустить какие-либо активные задачи
    * https://airflow.apache.org/docs/apache-airflow/stable/administration-and-deployment/scheduler.html
  * WebServer (веб-сервер) отвечает за отображение веб-интерфейса и аутентификацию пользователей, а также решает, к какой группе должен относиться тот или иной пользователь в соответствии с конфигурационным файлом
    * https://airflow.apache.org/docs/apache-airflow/stable/security/webserver.html
### Oozie
### Luigi
### NIFI
## GIT
[Go Back](#оглавление)
* Базовые команды с описаниями и примерами - https://github.com/cyberspacedk/Git-commands
* Gitbook - https://git-scm.com/book/ru/v2/
* GIT flow - альтернативная модель ветвления Git, в которой используются функциональные ветки и несколько основных веток
  * Из ветки main создается ветка develop.
  * Из ветки develop создается ветка release.
  * Из ветки develop создаются ветки feature.
  * Когда работа над веткой feature завершается, она сливается в ветку develop.
  * Когда работа над веткой release завершается, она сливается с ветками develop и main.
  * Если в ветке main обнаруживается проблема, из main создается ветка hotfix.
  * Когда работа над веткой hotfix завершается, она сливается с ветками develop и main.
* Магистральная разработка (Trunk Based Development, TBD) - это модель ветвления системы управления версиями, при которой все разработчики работают в одной ветке. Она помогает добиться CI/CD, ускорить поставку программного обеспечения и повысить производительность организации
  * В TBD в основную ветку добавляются в том числе и не до конца реализованные «фичи»
  * Добавление небольших изменений
  * Флаги активных функций
  * Полное покрытие тестами
## Прочие полезности
[Go Back](#оглавление)
### Hadoop
* Hadoop – это свободно распространяемый набор утилит, библиотек и фреймворк для разработки и выполнения распределённых программ, работающих на кластерах из сотен и тысяч узлов. Эта основополагающая технология хранения и обработки больших данных (Big Data) является проектом верхнего уровня фонда Apache Software Foundation.
  * Изначально писался на Java под MapReduce
* Список полезных команд (HDFS) https://docs.arenadata.io/ru/ADH/current/references/hdfs-cheatsheet.html
* Модули
  * HDFS - распределенная файловая система хадупа (Hadoop Distributed File System)
    * Являет собой технологию хранения данных на разных серверах (узлах/нодах)
    * Обеспечивает надежность за счет репликации (дублирования данных)
    * Архитектура
      * NameNode - основонй отдельный сервер для управления пространством имен файловой системы, хранящий дерево файлов, а также мета-данные файлов и каталогов
      * Secondary NameNode - дублирование NameNode для отказоустойчивости и быстрого восстановления. Находится на кластере в единственном экземпляре
      * DataNode - серверА с программным кодом отвечающим за файловые операции и работу с блоками данных
      * Client - пользователь/приложение взаимодействующие через API с HDFS
    * Особенности
      * Число репликаций по-умолчанию 3
      * Размер файла по-умолчанию 64 Мб
      * Спокойно хранит файлы размером больше 10 Gb
      * Не требует дорогого серверного оборудования
      * Репликация асинхронная и осуществляется на уровне кластера, а не на уровне узлов
      * Хорошая оптимизация для потоковых операций
      * Файловые операции выполняются асинхронно для разных файлов
      * Принцип WORM (Write-once and read-many, один раз записать – много раз прочитать) полностью освобождает систему от блокировок типа «запись-чтение». Запись в файл в одно время доступен только одному процессу, что исключает конфликты множественной записи
      * Хорошо настроенное сжатие данных -> экономия дискового пространства
      * Самодиагностика
      * Метаданные сервера имен хранятся в оперативной памяти -> быстрый доступ
    * Недостатки
      * При поломке NameNode кластер не работает, до восстановления
      * Нет репликации для Secondary NameNode
      * Нет возможности редактирования сохраненных файлов
      * Нет проверки целостности данных
      * Нет поддержки реляционных моделей данных
  * YARN - ресурсный менеджер Yet Another Resource Negotiator
    * Архитектура
      * ResourceManager (RM) — менеджер ресурсов, которых отвечает за распределение ресурсов, необходимых для работы распределенных приложений, и наблюдение за узлами кластера, где эти приложения выполняются. ResourceManager включает планировщик ресурсов (Scheduler) и диспетчер приложений (ApplicationsManager, AsM)
      * ApplicationMaster (AM) – мастер приложения, ответственный за планирование его жизненного цикла, координацию и отслеживание статуса выполнения, включая динамическое масштабирование потребления ресурсов, управление потоком выполнения, обработку ошибок и искажений вычислений, выполнение локальных оптимизаций
      * NodeManager (NM) – менеджер узла – агент, запущенный на узле кластера, который отвечает за отслеживание используемых вычислительных ресурсов (CPU, RAM и пр.), управление логами и отправку отчетов об использовании ресурсов планировщику. NodeManager управляет абстрактными контейнерами – ресурсами узла, доступными для конкретного приложения
      * Контейнер (Container) — набор физических ресурсов (ЦП, память, диск, сеть) в одном вычислительном узле кластера
    * Принципы работы YARN 
      * Клиентское приложение отправляет запрос в кластер
      * Менеджер ресурсов выделяет необходимые ресурсы для контейнера и запускает ApplicationMaster для обслуживания этого приложения
      * ApplicationMaster отправляет запрос менеджеру узла NodeManager, включая контекст запуска контейнера Container Launch Context (CLC)
      * ApplicationMaster выделяет контейнеры для приложения в каждом узле и контролирует их работу до завершения работы приложения
      * Для запуска контейнера менеджер узла копирует в локальное хранилище все необходимые зависимости (данные, исполняемые файлы, архивы)
      * По завершении задачи мастер приложения отменяет выделенный контейнер в диспетчере ресурсов, завершая жизненный цикл распределенного задания
      * Клиент может отслеживать состояние распределенного приложения, обращаясь к менеджеру ресурсов или сразу к мастеру приложения
  * Hadoop Common - набор инфраструктурных программных библиотек и утилит, которые используются в других решениях и родственных проектах, в частности, для управления распределенными файлами и создания необходимой инфраструктуры
  * Hadoop MapReduce - платформа программирования и выполнения распределённых MapReduce-вычислений, с использованием большого количества компьютеров (узлов, nodes), образующих кластер
### Kafka
### Flink
### Хранилища
[Go Back](#оглавление)
#### Dwh 
#### DataLake
#### DataVault
### Форматы данных в BigData
[Go Back](#оглавление)
* **Линейные** (строковые)
  * Строки данных хранятся вместе образуя непрерывное хранилище
  * Пониженная скорость чтения и выполнения изберательных запросов
  * Большой расход дискового пространства
  * Лучше подходят для потоковой записи
  * Виды
    * `AVRO`
      * Может использовать компактную бинарную кодировку или человекочитаемый формат JSON  
      * Обеспечивает высокую скорость записи
      * Подходит для Apache Kafka, Flume, DataLake
    * `Sequence`
      * Двоичный формат для хранения данных в виде сериализованных пар ключ/значение 
      * Обеспечивает хороший параллелизм при MapReduce  
* **Колоночные** (столбцовые)
  * Столбцы хранятся вместе, но могут обрабатываться отдельно друг от друга
  * Быстрое чтение данных в изберательных запросах
  * Занимает много оперативной памяти
  * Не подходит для потоковой записи, т.к. файл не может быть восстановлен из-за отсутствия точек синхронизации
  * Занимает меньше дискового пространства
  * Виды
    * `Parquet`
      * Бинарный, колоночно-ориентированный формат
      * Подходит для Kafka, Spark и Hadoop
      * Поддерживает boolean, int32, int64, int96, float, double, byte_array
      * *Достоинства*
        * Экономия места
        * Высокая скорость
        * Возможность реализации собственных схем данных
        * Многие ЯП поддерживают
        * Возможность хранения данных не только в HDFS
        * Простота и удобство работы с файлами
        * Поддержка Apache Spark «по умолчанию»
      * *Недостатки*
        * Строгая типизация данных
        * Отсутствие встроенной (нативной) поддержки в других фреймворках, кроме Apache Spark
        * Отсутствие возможности отслеживать изменение данных и эволюцию схемы
        * Сложность частичной потоковой передачи данных
        * Сильная привязка к метаданным
        * Паркет не является человекочитаемым форматом
    * `RCFile`
      * Гибридный многоколонный формат записей, адаптированный для хранения реляционных таблиц на кластерах и предназначенный для систем использующих MapReduce
    * `ORC`
      * *Достоинства*
        * Один файл на выходе любой задачи — это снижает нагрузку на узел имен (NameNode)
        * Поддерживается тип данных Hive, сложные и десятичные типы данных (list, map, struct, union)
        * Возможно одновременное считывание того же файла различными процессами RecordReader
        * Можно разделить файлы без сканирования этих файлов на предмет наличия маркеров
        * Индексация блоков для каждого столбца
        * Генерация наиболее эффективного графа при оптимизации SQL-запросов
## Про разработку и вообще
[Go Back](#оглавление)
### Принципы разработки
* Главное в принципах и подходах - помнить, что все это рекомендации, которые могут быть, а могут и не быть уместными в конкретном проекте и конкретной команде
* Дейлайте хорошо, а не хорошо не делайте!
#### KISS - Keep It Simple Stupid
* Ваши методы должны быть небольшими, не превышающими 40-50 строк
* Каждый метод должен решать только одну проблему
* У вас в проекте много условий? Убедитесь, что вы разбили их на более мелкие блоки кода
#### YAGNI - You Aren't Gonna Need It
* Не оставлять в коде неиспользующиеся функции, которые "пригодятся когда-то потом"
#### Measure Twice and Cut Once
* Некачественно выполненный этап написания требований обычно приводит к более чем 50% проблем в разработке
* Дважды проверьте все требования проекта, чтобы убедиться, что вы ничего не упускаете и не добавляете лишнего в свой код
#### DRY - Don’t Repeat Yourself
* Избегайте копирования кода в разные места
* Кроме того автоматизируйте всё, что можно автоматизировать
#### Бритва Оккама
* В группе гипотез всегда выбирайте ту, которая имеет наименьшее количество предположений
* *Всегда начинайте с максимально простого кода
#### BDUF - Big Design Up Front
* Разработчик должен сначала завершить проектирование. После этого проект можно реализовать
* Сначала создать общую архитектуру
* Затем необходимо разделить требования на несколько этапов в соответствии с приоритетами
* В процессе разработки начните с этапа с самым высоким приоритетом, постепенно опускаясь до самого низкого
#### Избегайте преждевременной оптимизации
* Дональд Кнут утверждал, что корень всего зла в программировании - преждевременная оптимизация
#### Закон Деметры
* Обеспечить независимость программных объектов друг от друга
* Уменьшить общение или связь между разными классами
* Поместить связанные классы в один и тот же пакет, модуль или каталог для достижения согласованности
#### S.O.L.I.D 
* S - Single Responsibility Principle (SRP) - Принцип единой ответственности
* O - Open/Closed Principle (OCP) - Принцип открытия / закрытия
* L - Liskov Substitution Principle - Принцип замещения Лисков
* I - Interface Segregation Principle - Принцип разделения интерфейса
* D - Dependency Inversion Principle - Принцип инверсии зависимостей
#### CI/CD
* Непрерывная интеграция (CI): короткоживущие функциональные ветки, команда сливает их с основной веткой разработки по несколько раз в день, процессы сборки и тестирования полностью автоматизированы, результат имеем в пределах 10 минут; развертывание выполняется вручную
* Непрерывная доставка (CD): автоматизируется CI + весь процесс релиза ПО. Может состоять из нескольких этапов. Развертывание в продакшен выполняется вручную
* Непрерывное развертывание: CI + CD + полностью автоматизированное развертывание в продакшен
### Алгоритмы
[Go Back](#оглавление)
* О-большое описывает скорость работы алгоритма (не время)
* Простой поиск О(n)
* Бинарный поиск O(log n)
#### Рекурсия
* Рекурсия – когда функция вызывает саму себя. Логика рекурсивной функции как правило состоит из двух ветвей. Длинная ветвь вызывает эту же функцию с другими параметрами, чтобы накопить результат. Короткая ветвь определяет критерий выхода из рекурсии
* Неоптимизированная рекурсия приводит к накладным расходам ресурсов
* Хвостовая рекурсия - это особый вид рекурсии, когда функция заканчивается вызовом самой себя без дополнительных операторов. Когда это условие выполняется, компилятор разворачивает рекурсию в цикл с одним стек-фреймом, просто меняя локальные переменные от итерации к итерации (интерпритатор Python не умеет оптимизировать это)
#### Быстрая сортировка
* На первом этапе выбирают опорный элемент (чаще всего его берут из середины массива)
* Последовательно сравнивают первый элемент массива с последним, второй с предпоследним и т.д. Если элемент слева от опорного элемента больше правого, они меняются местами
* Когда доходят до опорного элемента, итерация считается законченной
* Сложность быстрой сортировки в среднем случае равна N * log(N)
#### Алгоритм Дейкстры
* Используется для нахождения пути с наименьшим весом в взвешенном графе Работает только в направленных ациклических графах (DAG - Directed Acyclic Graph)
* Состоит из 4 шагов:
  * Найти узел с наименьшей стоимостью
  * Обновить стоимость соседей этого узла
  * Повторять пока это не будет сделано для всех узлов
  * Вычислить итоговый путь 
* Не работает с отрицательными весами - для графов с отрицательными весами сущестувет специальный алгоритм, называемый алгоритмом Беллмана-Форда
#### Жадные алгоритмы
* Используются когда вычисление точного решения занимает слишком много времени или когда высокая точность не требуется
* Эффективность приближенного алгоритма оценивается по
  * быстроте
  * близости полученного решения к оптимальному
### Алгоритмы кластеризации 
[Go Back](#оглавление)
* Формально это больше про ML, но не помешает знать, что такое существует
* Кластеризация (или кластерный анализ) — это задача разбиения множества объектов на группы, называемые кластерами. Внутри каждой группы должны оказаться «похожие» объекты, а объекты разных группы должны быть как можно более отличны. Главное отличие кластеризации от классификации состоит в том, что перечень групп четко не задан и определяется в процессе работы алгоритма
* Виды:
  * `Иерархический`
  * `k-средних`
  * `с-средних`
  * `Выделение связных компонент`
  * `Минимально покрывающее дерево`
  * `Послойная кластеризация`
* Ссылка на статью - https://habr.com/ru/articles/101338/
### Docker
### kubernets 
# Справочные материалы
[Go Back](#оглавление)
* https://github.com/OBenner/data-engineering-interview-questions/blob/master/content/spark.md
* https://github.com/yakimka/python_interview_questions/blob/master/questions.md/#Python
