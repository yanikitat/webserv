# webserv

### Цель проекта:
Реализовать HTTP сервер по стандарту RFC 7230-7235 с возможностью настройки сервера с помощью конфигурационного файла

### Описание проекта:
Учебный проект [Школы 21](https://21-school.ru/). Многопоточный, событийно ореинтированный сервер на языке С++ с возможностью конфигурации
виртуальных серверов, роутеров и обработки CGI скриптов.

### Стек технологий:
* С++
* HTTP-протокол

### HTTP протокл:
Реализованы основные методы HTTP протокола: GET, HEAD, POST, PUT, с возможностью указать разрешенные методы в конфигурационном файле. Метод POST обрабатывается
через CGI скрипты (для какого формата файла будет вызываться CGI-handler указывается в конфигурационном файле). Сервер обрабатывает тело запроса
как по заголовку content-length, так и по transfer-encoding.

### Воркеры:
Это бонусная часть проекта. Логика воркеров реализована через пул потоков. Здесь воркеры работают по аналогии с nginx - потоки не создаются на каждого клиента,
их колличество задано изначально. Процесс отправляет клиента в пул, если клиент готов отправить нам запрос или принять ответ (это проверяется в основном цикле
через select). Обработка клиента настроена с помощью конечного автомата - у клиента есть состояние, в зависимости от которого воркер вызывает нужный обработчик
(чтение запроса, составление ответа, отправка ответа). После обработки клиент удаляется из пула.

### Конфигурационный файл:
Сервер принимает файл в качестве аргумента или использует дефолтный, если аргумент пуст. В конфигурационном файле пользователь имеет возможность настроить сервер.
Структура файла проста - в блочной структуре server мы можем указать:
* хост(ip)
* порт
* имена сервера
* страницы ошибок
* блочная директива location

В директиве location мы указываем:
* path, по которому будет определятся, какой location обрабатывает запрос
* корневая директория
* разрешенные методы
* лимит тела запроса клиента
* индексная страница
* автоиндекс
* путь к cgi интерпритатору

### Автоиндексация:
Если запрос пришел на директорию, автоиндекс разрешен и индексная страница отсутсвует - ответ отправляется в виде листнинга текущей директории, с ссылками на поддериктории, родительские директории или файлы

### CGI:
Сервер устанавливает необходимые переменные окружения, отправляет тело запроса в cgi-скрипт, получает и обрабатывает ответ. В случае неудачного исполнения скрипта
сервер отправляет ответ с кодом 502
