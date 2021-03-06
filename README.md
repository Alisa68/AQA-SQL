### Задача №1 - Скоро deadline

Случилось то, что обычно случается ближе к дедлайну: никто ничего не успевает и винит во всём остальных других.

Разработчикам не особо до вас ("им ведь нужно пилить новые фичи"), поэтому они подготовили для вас сборку, работающую с СУБД и даже приложили схему БД (см. файл [schema.sql](https://github.com/netology-code/aqa-homeworks/blob/master/sql/schema.sql)), но при этом сказали "остальное вам нужно сделать самим, там не сложно" 😈.

Что вам нужно сделать:

1.Внимательно изучить схему
2. Создать Docker Container на базе MySQL 8 (прописать создание БД, пользователя, пароля)
3. Запустить SUT ([app-deadline.jar](https://github.com/netology-code/aqa-homeworks/blob/master/sql/app-deadline.jar)): для указания параметров подключения к БД можно использовать:
либо переменные окружения DB_URL, DB_USER, DB_PASS
либо указать их через флаги командной строки при запуске: -P:jdbc.url=..., -P:jdbc.user=..., -P:jdbc.password=... (внимание: при запуске флаги не нужно указывать через запятую!). Данное приложение не использует файл application.properties в качестве конфигурации, конфигурационный файл находится внутри jar архива.
либо можете схитрить и попробовать подобрать значения, зашитые в саму SUT
А дальше выясняется куча забавных вещей 😈. Рекомендуем вам попробовать разобраться самим, но если будет сложно, загляните в подсказку.

### Проблема первая: SUT не стартует
`Подсказка`
Проблема: SUT не создаёт самостоятельно таблицы в БД.

Поэтому вам нужно сходить на сайт-описание Docker Image MySQL и посмотреть, как при инициализации скармливать схему (будет использоваться технология volumes).

### Проблема вторая: SUT валится при повторном перезапуске
`Подсказка`
Проблема: SUT вставляет в БД демо-данные, а поскольку там есть ограничение уникальности, это приводит к ошибкам.

Поэтому вам нужно где-то настроить вычистку данных за SUT.

### Проблема третья (опциональна): пароли
Если вы решите вдруг генерировать пользователей, чтобы под ними тестировать "Вход в приложение", то не должны удивляться тому, что в базе данных пароль пользователя хранится в зашифрованном виде.

Попытка его записать туда в открытом виде ни к чему хорошему не приведёт.

Настойчивые требования к разработчикам "раскрыть" алгоритм генерации пароля - ни к чему не привели.

Что же делать?

`Подсказка`
Если вы добрались до этого шага и всё-таки успешно запустили SUT, то вы уже герой!

Но теперь выяснилась следующая забавная информация: разработчики фронтенда поругались с разработчиками бэкенда и вы можете протестировать только "Вход в систему".

Внимательно посмотрите, как и куда сохраняются коды генерации в СУБД и напишите тест, который взяв информацию из БД о сгенерированном коде позволит вам протестировать "Вход в систему" через веб-интерфейс.

P.S. Неплохо бы ещё проверить, что при трёхкратном неверном вводе пароля система блокируется.

Итого в результате у вас должно получиться:

+ docker-compose.yml*
+ app-deadline.jar
+ schema.sql
+ код ваших авто-тестов
Если ваша система не поддерживает Docker, то вам придётся (к сожалению) вручную установить MySQL на свой компьютер и отрабатывать тесты уже на ней. В этом случае положите в репозиторий файлик README.md, в котором опишите последовательность действий (со скриншотами) для установки сервера MySQL и загрузки в него файла schema.sql.

#### Программа предназначена для тестирования входа в систему через веб-интерфейс.

#### Убедитесь, что у Вас установлен Docker, версии не ниже 3.8.

#### Инструкции по установке
1. Скачайте SQLhomework;
2. Разверните базу, используя команду `docker-compose up -d`
3. Запустите приложение с помощью команды `java -jar ./artifacts/app-deadline.jar`
4. Tесты готовы к запуску. Обратите внимание, перед повторным запуском автотестов приложение должно быть перезапущено.

