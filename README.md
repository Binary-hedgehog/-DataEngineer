# Подготовка к собеседованию для Spark Data Engineer
* [Python](https://github.com/Binary-hedgehog/-DataEngineer/blob/main/Python.md)
* [Scala](https://github.com/Binary-hedgehog/-DataEngineer/blob/main/Scala.md)  -- todo
## Оглавление
+ [Spark](#spark)  -- todo
  + [Структуры данных в Spark](#Структуры-данных-в-Spark)
  + [Процесс вычислений](#Процесс-вычислений)
  + [Основные компоненты Spark](#Основные-компоненты-Spark)
  + [Оптимизация в Spark](#Оптимизация-в-Spark)
  + [Ect](#Ect)
+ [SQL](#sql)  -- todo
+ [Оркестраторы](#оркестраторы)
+ [GIT](#git)
+ [Прочие полезности Big Data](#прочие-полезности)
  + [Hadoop](#hadoop)
  + [Kafka](#Kafka)
  + [Flink](#Flink)
  + [Хранилища](#Хранилища)  -- todo
  + [Форматы данных в BigData](#Форматы-данных-в-BigData)
+ [Про разработку](#Про-разработку)
  + [Принципы разработки](#принципы-разработки)
  + [Алгоритмы](#алгоритмы)
  + [Тестирование](#Тестирование)
  + [Алгоритмы кластеризации](#алгоритмы-кластеризации)
  + [Docker](#Docker)  -- todo
  + [Kubernetes](#Kubernetes)  -- todo
+ [Источники](#Источники)
---
## Spark
[Go Back](#оглавление)
* Apache Spark — это фреймворк для обработки и анализа больших объёмов информации
* Чем хорош Spark?
    * Поддерживает такие языки программирования как Scala, Java, Python, R и SQL
    * Обработка данных в оперативной памяти позволяет значительно ускорить выполнение программы относительно MapReduce
    * Использование отложенных вычислений - это означает, что операции над данными проводятся только перед непосредственным использованием результатов этих операций. Благодаря этому вычислительные мощности не тратятся на вычисления, которые понадобятся когда-то в будущем
    * Хорошо поддерживает параллелизим
    * Если на кластере есть Hadoop, то он из коробки поддерживает Spark, но для работы Spark Hadoop не обязателен
* Структура Spark
![Структура Spark](https://habrastorage.org/getpro/habr/upload_files/289/a96/15b/289a9615bb238133ebb9a35940b1fece.png)
### Структуры данных в Spark
[Go Back](#оглавление)
#### RDD - Resilient Distributed Dataset
* Это определенный набор объектов, разбитых на блоки partitions. RDD может быть представлен как в виде структурированных наборов данных так и неструктурированных
* Блоком, или партицией, можно считать цельную логическую неизменяемую часть данных, создать которую можно в том числе посредством преобразования (transformations) уже существующих блоков
* Partitions могут храниться на разных узлах кластера
* RDD отказоустойчивы и могут быть восстановлены в случае сбоя
* Data source API позволяет RDD формироваться из любых источников включая текстовые файлы, при этом необязательно даже структурированных
#### DataFrame
* Это набор типизированных записей, разбитых на блоки. Иными словами — таблица, состоящая из строк и столбцов
* Блоки могут обрабатываться на разных нодах кластера. DataFrame может быть представлен только в виде структурированных или полуструктурированных данных
* Данные представлены именованным набором столбцов, напоминая таблицы в реляционных БД
* Можно создать из RDD. После подобного transformation вернуться к RDD уже не получится. То есть исходный RDD не подлежит восстановлению после трансформации в DataFrame
* Data source API позволяет обрабатывать разные форматы файлов (AVRO, CSV, JSON, а также из систем хранения HDFS, HIVE таблиц, MySQL)
#### DataSet
* Это набор записей типа Row (колонка), разбитых на блоки
* Функционал Spark позволяет преобразовывать как RDD так и DataFrame в сам DataSet
* Dataset API также поддерживает различные форматы данных
### Процесс вычислений
[Go Back](#оглавление)
* В процесс вычислений вовлечены несколько основных его частей
![a](https://habrastorage.org/r/w1560/getpro/habr/upload_files/f35/582/063/f355820631bc7e8205852bdbeb24b3f1.png)
#### Driver
* Исполняет код программы
* Планирует и запускает работу executors
* Каждое приложение Spark содержит драйвер, который инициирует различные операции в кластере. Процесс драйвера запускает пользовательский код, который создает SparkContext, создает RDDs и выполняет преобразования и действия. Если драйвер завершает работу, приложение закончено
#### Executor
* Исполняет код от driver
* Передает driver'у информацию о процессе вычислений
#### Cluster Manager
* Занимается управлением реальными машинами кластера и контролирует выделение ресурсов (он же ресурсный менеджер)
* Виды: **Standalone cluster**, **Apache Mesos** и **YARN**
#### Spark Application
* ***TODO***
### Основные компоненты Spark
[Go Back](#оглавление)
#### Spark Core
* Включает функции управления памятью, для этого имеется специально API
* Восстановление в случае аварий
* Планирование и управление задачами на кластере
* Взаимодействие с хранилищем, распределение данных
#### Spark SQL 
* Механизм запросов SQL
* Использует диалект SQL Hive Query Language (HQL)
* Умеет работать с множеством различных источников данных, таких как таблицы Hive, Parquet и JSON
* Позволяет смешивать в одном приложении запросы SQL с программными конструкциями на Python, Java и Scala, поддерживаемыми абстракцией RDD, и таким способом комбинировать SQL со сложной аналитикой
#### Spark Streaming
* Модуль Streaming содержит методы для обработки данных в реальном времени. Он отбирает отдельные блоки из общего потока данных, переводит их в форму мультимножества и передаёт в другие модули Spark
* Spark Streaming имеет API для управления потоками данных, соответствующий модели RDD, поддерживаемой компонентом Spark Core
#### MLlib 
* Эта библиотека для машинного обучения включает в себя различные алгоритмы классификации, регрессии, кластеризации данных и так далее
#### GraphX 
* Библиотека содержит объекты и методы, которые упрощают обход и анализ графовых структур. Кроме того, в GraphX есть готовый набор алгоритмов для решения типовых задач графовой аналитики: ранжирования страниц, SVD++, подсчёта треугольников, обнаружения сообществ и так далее
* GraphX дополняет Spark RDD API возможностью создания ориентированных графов с произвольными свойствами, присваиваемыми каждой вершине или ребру. Также GraphX поддерживает разнообразные операции управления графами (такие как subgraph и rnapVertices) и библиотеку обобщенных алгоритмов работы с графами
### Оптимизация в Spark
[Go Back](#оглавление)
#### Lazy evaluations
* Операции в Spark делятся на два типа
  * Преобразования (Transformations): это операции, которые при применении к RDD возвращают ссылку на новый RDD, созданный посредством преобразования. Некоторые из наиболее часто используемых преобразований — это filter и map
    * Узкие преобразования (Narrow transformations) — это когда нет перемещения данных между разделами. Преобразование применяется к данным каждого раздела RDD, и создается новый RDD с тем же числом разделов. Например, filter — это узкое преобразование, потому что фильтрация применяется к данным каждого раздела, а полученные данные представляют раздел во вновь созданном RDD
    * Широкие преобразования (Wide transformations) требуют перемещения данных между разделами или так называемого перемешивания. Данные перемещаются с целью создания нового RDD. Например, sortBy сортирует данные на основе определенного столбца и возвращает новый RDD
  * Действия (Actions): При применении к RDD возвращается значение. Например, count возвращает количество элементов в RDD драйверной программе, или функция collect отправляет данные сами данные
* Ленивые (отложенные) вычисления позволяют отодвигать вычисления всех преобразований до момента применения действия
#### Catalyst Optimizer 
* Catalyst — оптимизатор запросов, который рабоатет на Driver
* Основной тип данных в Catalyst — это дерево, состоящее из узловых объектов. Каждый узел имеет тип и дочерние узлы (а может их не иметь). Новые типы узлов определены в Scala как подклассы класса TreeNode. Эти объекты неизменяемы, и ими можно управлять с помощью функциональных преобразований
* К узлам дерева Catalyst применяет набор правил для их оптимизации, которая выполняется в четыре этапа, как показано на диаграмме ниже
![b](https://spark-school.ru/wp-content/uploads/2021/02/1-1.png)
* Здесь, логический план составляет дерево, которое описывает, что нужно сделать; тогда как физический план точно описывает, как нужно делать
1. Анализ - определить тип каждого передаваемого столбца и действительно ли существуют столбцы, которые вы используете
2. Логическая оптимизация - оптимизатор затрат использует статистику и мощности машины для поиска наиболее эффективного плана выполнения вместо простого применения набора правил
3. Физический план - генерирует несколько физических планов на основе логического плана для выбора наиболее эффективного из всех предложенных
4. Генерация кода 
* Создание байт-кода Java для запуска на каждой машине
* Catalyst использует QuasiQuotes, специальную функцию Scala для преобразования дерева задания в абстрактное синтаксическое дерево (AST), которое затем компилирует и запускает сгенерированный код

-- ну тут еще надо поковырять на разных ресурсах
#### Tungsten 
--- доделать
#### Самостоятельная оптимизация
* Использование DataFrame вместо RDD, так как последнии проигрывают в плане оптимизации вычислений
* Кэшировать данные чтобы избегать дополнительного чтения и преобразования при повторяющихся Actions
* Партиционировать данные, при этом стараться избегать перекосов
* Помнить, что Join это дорого
* Фильтровать неиспользуемые данные
* Стараться не использовать UDF (особенно в PySpark) так как они не оптимизируются
### Ect
[Go Back](#оглавление)
#### Отличия Spark и PySpark
* Основным отличием является обработка UDF, в том плане, что Python работает кратно медленнее, чем Scala
* Некоторые методы работают иначе, у некоторых разные параметры, но это надо точечно сравнить, общей инфы нет
#### MapReduce как аналог Spark 
* Минусы MapReduce
  * Модель MapReduce выполняет вычисления за 2 этапа. В первую очередь он разделяет данные на части, передавая их на кластерные узлы для обработки. Потом каждый узел производит обработку данных с отправкой результата на главный узел, где и сформировывается итоговый результат распределенных вычислений
  * MapReduce регулярно обращается к диску, ведь именно там он сохраняет промежуточные и финальные итоги вычислений
  * Для написания хорошего решения на MapReduce понадобится довольно высокий уровень экспертности
* Плюсы Spark 
  * Spark выполняет обработку данных в памяти и, по сути, почти не обращается к диску
  * В Spark существует API для различных языков программирования, в результате чего писать код заметно проще
---
## SQL
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
---
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
* Это серверная система планирования выполнения рабочих процессов и повторяющихся задач в экосистеме Hadoop
* Рабочие процессы в Oozie представлены в виде DAG-цепочки
* Oozie поддерживает запуск задач Hadoop MapReduce, Apache Hive, Pig, Sqoop, Spark, операций HDFS, UNIX Shell, SSH и электронной почты, а также может быть расширен для поддержки дополнительных действий
* Задачи могут быть настроены для регулярного выполнения или разово при возникновении некоторого события
* Oozie целесообразно использовать, когда поток обработки включает несколько шагов, каждый из которых зависит от предыдущего. Например, запуск скрипта для проверки наличия ожидаемых входных данных перед их фактической обработкой
* Oozie использует контейнер oozie-launcher для каждой запускаемой задачи. Когда несколько заданий запускаются одновременно, есть вероятность, что в кластере запущены все oozie-launcher, но ни одно из заданий Spark. Пока все эти контейнеры oozie-launcher ожидают завершения своих соответствующих заданий Spark, фактически ни одно из них не запущено. Это означает, что кластер заполнен oozie-launcher’ами, но практическая работа не выполняется
* Поскольку для каждого типа задач (Pig, Hive, Sqoop, Spark и пр.) в Oozie требуются собственные jar-файлы и версии библиотек, это может привести к тому, что разные несовместимые версии jar-библиотеки могут вызывать сбои в заданиях
### Luigi
* Фреймворк на языке Python для построения сложных последовательностей по выполнению зависимых задач
* Довольно большая часть фреймворка направлена на преобразования данных из различных источников (MySql, Mongo, redis, hdfs) и с помощью различных инструментов (от запуска процесса до выполнения задач разных типов на кластере Hadoop)
* Самое главное преимущество фреймворка — возможность выстраивать последовательности зависимых задач
* Фреймворк разрешает зависимости, отслеживает граф выполнения, управляет запуском задач, обрабатывает ошибки с возможностью перезапуска нужных задач, распределяет ресурсы рабочих процессов с возможностью параллельной работы независимых частей графа задач
* Отсутствие механизма запуска задач по расписанию, что вызывает необходимость использования crontab
* Трудности масштабирования из-за слишком тесной связи DAG-задач с cron-заданиями, что ограничивает количество рабочих процессов
* Luigi не может автоматически распределять задачи между worker’ами на разных узлах
* Перезапуск конвейеров данных невозможен
* Неудобство GUI
* Отсутствие предварительной проверки выполнения задачи
### Livy
* Это REST-интерфейс для кластера Spark, который позволяет запускать и отслеживать отдельные задания Spark, напрямую используя фрагменты кода Spark или предварительно скомпилированные jar-файлы
* Можно использовать для запуска «асинхронных» заданий Spark, которые могут быть запущены в любое время, и не требуют обязательного отклика
* Можно применять для оркестрации рабочих процессов с опросом API для ответа о состоянии задания
* Миграция между версиями Spark выполняется автоматически: если задание выполняется в более новой версии Spark, Livy просто запустит его
* Не требуется разрабатывать дополнительные workflow-файлы и свойства для запуска заданий, а используется только простой HTTP-интерфейс
* Не является полноценной системой управления потоками работ, а используется исключительно для запуска и отслеживания отдельных заданий Spark
---
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
---
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
* Это распределенная система обмена сообщениями между серверными приложениями в режиме реального времени
* Основы кластера Kafka — это **продюсер**, **брокер** и **консумер**. Продюсер пишет сообщения в **лог** брокера, а консумер его читает
* **Лог** — это упорядоченный поток событий во времени. Событие происходит, попадает в конец лога и остаётся там неизменным
* **Продюсеры** — это приложения для записи событий в кластер Kafka
    * Один продюсер может писать в один или несколько топиков
    * Продюсеры самостоятельно партицируют данные в топиках и сами определяют алгоритм партицирования: он может быть как банальный round-robin и hash-based, так и кастомный
    * Продюсер сам выбирает размер **батча** и число ретраев при отправке сообщений. Протокол Kafka предоставляет гарантии доставки всех трёх семантик: at-most once, at-least once и exactly-once
* **Брокеры** — это "локация/сервер" где хранятся события
    * Кластер Kafka состоит из брокеров
    * Все брокеры соединены друг с другом сетью и действуют сообща, образуя единый кластер
    * Когда мы говорим, что продюсеры пишут события в Kafka-кластер, то подразумеваем, что они работают с брокерами в нём
* **Топик** — это логическое разделение категорий сообщений на группы (формально один топик - один лог)
    * Топики в Kafka разделены на партиции
    * Увеличение партиций увеличивает параллелизм чтения и записи
    * Партиции находятся на одном или нескольких брокерах, что позволяет кластеру масштабироваться
    * Партиции хранятся на локальных дисках брокеров и представлены набором лог-файлов — **сегментов**
    * Каждое сообщение в таком логе определяется порядковым номером — **оффсетом**. Этот номер монотонно увеличивается при записи для каждой партиции
    * Лог-файлы на диске устаревают по времени или размеру. Настроить это можно глобально или индивидуально в каждом топике
    * Для отказоустойчивости, партиции могут реплицироваться. Число реплик или **фактор репликации** настраивается как глобально по умолчанию, так и отдельно в каждом топике
    * Реплики партициий могут быть **лидерами** или **фолловерами**. Традиционно консумеры и продюсеры работают с лидерами, а фолловеры только догоняют лидера
* **Консумеры** это приложения которые могут читать события из брокеров
    * Один консумер может читать один или несколько топиков
* Чтение лога
    * Сегмент тоже удобно представить как обычный лог-файл: каждая следующая запись добавляется в конец файла и не меняет предыдущих записей. Фактически это очередь FIFO (First-In-First-Out) и Kafka реализует именно эту модель
    * Семантически и физически сообщения внутри сегмента не могут быть удалены, они иммутабельны. Всё, что мы можем — указать, как долго Kafka-брокер будет хранить события через настройку политики устаревания данных или **Retention Policy**
    * Числа внутри сегмента — это реальные сообщения системы, у которых есть порядковые номера или оффсеты, что монотонно увеличиваются со временем. У каждой партиции свой собственный счётчик, и он никак не пересекается с другими партициями
    * Начальная позиция первого сообщения в логе называется **log-start offset**. Позиция сообщения, записанного последним — **log-end offset**. Позиция консумера сейчас — **current offset**
    * Расстояние между конечным оффсетом и текущим оффсетом консумера называют **лагом** — это первое, за чем стоит следить в своих приложениях
* Событие — это пара ключ-значение
    * Ключ партицирования может быть любой: числовой, строковый, объект или вовсе пустой
    * Значение тоже может быть любым — числом, строкой или объектом в своей предметной области, который вы можете как-то сериализовать (JSON, Protobuf, …) и хранить
* Кластер Kafka позволяет изолировать консумеры и продюсеры друг от друга. Продюсер ничего не знает о консумерах при записи данных в брокер, а консумер ничего не знает о продюсере данных
* Zookeeper — это выделенный кластер серверов для образования кворума-согласия и поддержки внутренних процессов Kafka
    * Помогает обнаружить сбои контроллера, выбрать новый и сохранить работоспособность кластера Kafka
### Flink
* Apache Flink – это фреймворк и движок для statefull вычислений над неограниченными и ограниченными потоками данных
* Программы можно писать как в параллельном режиме, так и пайплайнам
* Позволяет реализовать последовательность заданий (batch) и поток заданий (stream)
* Достаточно хорошо оптимизирован и обладает высокой пропускной способностью и низкими задержками
* Поддерживает приложения на Java, Scala, Python и SQL
* Задачи в Flink устойчивы к отказам и используют строго одну семантику
* Flink не обладает собственной системой хранения данных, но использует коннекторы для различных источников данных, таких как Apache Kafka, HDFS, Apache Cassandra, ElasticSearch, Amazon Kinesis и другие.
* Для разработки программ есть три основных API - DataStream API, Table API и Python API. В рамках данной статьи мы рассмотрим только  DataStream API
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
---
## Про разработку
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
### Тестирование
[Go Back](#оглавление)
* Unit-тесты
    * Модульные тесты проверяют, правильно ли работает каждый отдельный модуль
    * Модульные тесты не должны проверять внешние зависимости или взаимодействия
    * Вы должны писать и запускать модульные тесты параллельно со своим кодом
* Интеграционные тесты
    * Интеграционные тесты проверяют взаимодействие между двумя (или больше, чем двумя) отдельными юнитами вашего кода
    * Интеграционные тесты также проверяют интеграцию вашего кода с внешними зависимостями, вроде соединений с базой данных или сторонними API
    * Интеграционные тесты это следующий шаг после модульных тестов
* Функциональное тестирование
    * Функциональное тестирование может быть определено как тестирование отдельных функций модулей
    * Это относится к тестированию программного продукта на индивидуальном уровне, чтобы проверить его функциональность
    * Оно сильно отличается от модульного или интеграционного тестирования; вы не можете написать бесчисленное множество тест-кейсов для функционального тестирования, поскольку оно является более сложным, чем модульное
* Системный тест
    * Автоматизированные тесты, проверяющие работу всей интегрированной системы (предельный случай интеграционных тестов)
    * Системное — это тестирование программы в целом
    * Для небольших проектов это, как правило, ручное тестирование — запустил, пощелкал, убедился, что (не) работает
    * Системные тесты покрывают 10 % системы. Это объясняется тем, что они предназначены для проверки правильности не поведения системы, а ее конструкции
* Регрессионное тестирование
    * Это может быть любой вид теста из описанных выше, который пишется после того, как была обнаружена проблема
    *  Тест должен эмулировать в точности шаги для воспроизведения проблемы. Наличие такого теста после исправления проблемы дает гарантию, что точно такой же баг, больше не появится в системе
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
* Ссылка на статью с подробным описанием - https://habr.com/ru/articles/101338/
### Docker
[Go Back](#оглавление)
### Kubernetes 
[Go Back](#оглавление)
---
# Источники
[Go Back](#оглавление)
* https://github.com/OBenner/data-engineering-interview-questions/blob/master/content/spark.md
* https://github.com/yakimka/python_interview_questions/blob/master/questions.md/#Python
* https://habr.com/ru/companies/kuper/articles/738634/
* https://habr.com/ru/articles/828984/
* https://spark-school.ru/blog/how-catalyst-works/
