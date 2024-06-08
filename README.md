# Домашнее задание к занятию "8. EventSource, Websockets"
**CI** [![Build status](https://ci.appveyor.com/api/projects/status/23aalpbxtdx9jaun?svg=true)](https://ci.appveyor.com/project/donecvolsk/eventsource-websockets)

Правила сдачи задания:

1. **Важно**: в рамках этого ДЗ можно использовать любой менеджер пакетов
2. Всё должно собираться через Webpack (включая картинки и стили) и выкладываться на Github Pages через Appveyor
3. В README.md должен быть размещён бейджик сборки и ссылка на Github Pages
4. В качестве результата присылайте проверяющему ссылки на ваши GitHub-проекты
5. Авто-тесты писать не требуется
6. Серверная часть должна быть выложена на [Render](https://render.com/).
   Посмотрите [инструкцию](https://github.com/netology-code/ahj-homeworks/tree/video/docs/render#readme)
   или [документацию](https://render.com/docs/deploy-node-express-app), как развертывать серверную часть на Render.

---

## Чат

### Легенда

В рамках реализации корпоративного портала вам поручили организовать чат, и вы решили для этого
использовать веб-сокеты.

### Описание

Ребята из команды backend уже написали сервер, вам необходимо реализовать клиентскую часть.
Изучите backend, чтобы понять, как нужно взаимодействовать.

При загрузке страницы появляется всплывающее окно, в котором запрашивается никнейм, под которым вы будете
зарегистрированы в чате:

![](./pic/chat.png)

Если такой никнейм свободен, то открывается окно чата, в противном же случае вы должны сообщить пользователю о том, что
никнейм занят и ему необходимо выбрать другой (продумайте, как вы реализуете это).

Общее окно чата:

![](./pic/chat-2.png)

Обратите внимание: сообщения всех участников чата (кроме ваших) выравниваются по левому краю, а ваши - по правому.

Важно: `You` - это не никнейм, это указатель на то, что это Вы.

Важная детально: при отключении пользователя он должен удаляться из списка пользователей в левой части.

---

## Cloud Dashboard (задача со звёздочкой)

Важно: эта задача не является обязательной

### Легенда

В рамках реализации интерфейса для системы управления облачными серверами необходимо организовать для оператора рабочее
место, позволяющее создавать, запускать, останавливать и удалять облачные сервера.

### Описание

Общий вид рабочего места:

![](./pic/cloud.png)

#### Серверная часть

Серверная часть уже написана, она представляет собой комбинированный сервер rest api + sse.

Доступные эндпоинты:
* `GET    "/" `          - массив инстансов
* `GET    "/sse"`        - эндпоинт для подключения по sse
* `POST    "/"`          - создать инстанс
* `DELETE   "/:id"`      - удалить инстанс по идентификатору

SSE
Для получения статусов необходимо подписаться на ивент с названием `message`. 
Ключ, где лежат данные - `data`.

Информация о конкретном инстансе представлена в следующем виде:

```json
{
  "id": "<uuid>",
  "state": "running | stopped",
   "timestamp": "<date>"
}
```

Где `<id>` - автоматически генерируется на сервере, при создании статус - `stopped`.

Должна быть возможность:

* Создания инстанса (id генерируется автоматически)
* Удаление инстанса по ID
* Изменение состояния (с `running` на `stopped` и со `stopped` на `running`)

Обратите внимание, что создание, старт, остановка и удаление инстансов виртуальной машины - это ресурсоёмкая операция,
поэтому на все запросы кроме получения списка инстансов, вы получаете ответ (например, `{"status": "ok"}` для
REST API) через 10 секунду придет обновленный статус.

#### Клиентская часть

Клиентскую часть необходимо написать самостоятельно. Она должна работать следующим образом:

1. При загрузке страницы загружается список инстансов и отрисовывается в виджете управления инстансами (слева)
1. Выполняется подключение по нужному протоколу с началом отслеживания событий. Мы предлагаем вам для разделения событий
   использовать следующие "ключи":

* `received` - сервером получена команда
* `created` - создан новый инстанс
* `started` - инстанс запущен
* `stopped` - инстанс остановлен
* `removed` - инстанс удалён

Виджет управления инстансами (слева) работает следующим образом:

1. Status - отображает текущий статус
1. Actions - кнопки управления состояниями (зависят от текущего статуса) позволяют отправить команду на запуск,
   остановку или удаление инстанса
1. Create new instance - позволяет отправить команду на создание нового инстанса.

Обратите внимание, в виджете управления состояние должно быть синхронизировано с текущим состоянием на сервере. Т.е.
если вы получаете информацию с сервера о том, что инстанс стартовал, то это должно быть отображено как в Worklog'е, так
и в виджете управления инстансами.
