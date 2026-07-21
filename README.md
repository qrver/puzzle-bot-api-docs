# Документация входящих запросов PuzzleBot

В данной статье разобрана документация Входящих запросов от PuzzleBot. При необходимости вы можете ознакомиться с документацией по Исходящим запросам: Исходящие запросы (API).

Для настройки Входящих запросов необходимо сгенерировать API токен (Бот — Настройки — Интеграции — Входящие запросы).

> Важно! Не путать с API ключом от бота из @BotFather.

## Формат

Формат запроса:

`https://api.puzzlebot.top/?token=ТОКЕН_API&method=НАЗВАНИЕ_МЕТОДА`

Пример запроса:

```yaml
https://api.puzzlebot.top/?token=XXX&method=getLinkActivations&link_id=5556&page=1
```

С помощью данного запроса можно получить список пользователей, перешедших по ссылке (`getLinkActivations`) с конкретным идентификатором (`link_id=5556`), состоящий из данных, помещающихся на указанную страницу (`page=1`).

В адрес для отправки запросов необходимо подставить (без пробелов):

- ТОКЕН_API — генерируется в Настройках Бота на вкладке Интеграции
- НАЗВАНИЕ_МЕТОДА — имя метода (пример: `getLinkActivations`) и его параметры (пример: `link_id` и `page`)

Параметры передаются через POST или GET, ответ приходит в формате JSON.

> Важно! Всего можно выполнить 120 запросов в минуту.

## Методы

### Telegram

'tg.*' — любой запрос из Telegram Bot API: вместо `*` указывается любой метод Telegram Bot API в формате официальной документации. Пример:

```yaml
method: tg.unpinAllChatMessages
params:
 chat_id: number  # Telegram id чата, id бота для приватного чата

success result:
 code: 0
 data: Массив всех методов API
```

> Важно! Через методы `sendPhoto`, `sendAudio`, `sendDocument`, `sendVideo`, `sendAnimation`, `sendVoice`, `sendVideoNote`, `sendPaidMedia`, `sendMediaGroup` отправлять файлы в формате `multipart/form-data` НЕЛЬЗЯ. Используйте для этого API Telegram и соответствующие методы.

---

### Системные

Помощь (вывод доступных методов):

```yaml
method: help
params: no

success result:
 code: 0
 data: Массив всех доступных методов API
```

Тест:

```yaml
method: test
params: no

success result:
 code: 0
 data: Тестовый текст с параметрами вашего запроса
```

---

### Общие

#### Модерация

Получение списка пользователей:

```yaml
method: getUsersInChat
params:
 chat_id: number  # Telegram id чата, id бота для приватного чата
 page: number     # номер страницы, начинается с 1, на странице максимум 200 строк
 category_id: number  # id категории для фильтрации пользователей, необязательный параметр

success result:
 code: 0
 data: Массив пользователей [user_id, first_name, last_name, username]
```

Получение информации о пользователе:

```yaml
method: getUserInChatStatus
params:
 user_id: number  # Telegram id пользователя
 chat_id: number  # Telegram id чата, id бота для приватного чата
 page: number     # номер страницы, начинается с 1, на странице максимум 50 строк

success result:
 code: 0
 data: Объект [user_id, chat_id, first_name, last_name, username, joined (1 = присоединился, 0 = вышел, 2 = забанен), joined_date, left_date, ban_date, categories - Массив id категорий]
```

Блокировка пользователя (бан):

```yaml
method: userBan
params:
 tg_chat_id: number  # Telegram id чата
 user_id: number     # Telegram id пользователя
 until_date: number  # timestamp даты окончания бана

success result:
 code: 0
 data: "ok"
```

Разблокировка пользователя (разбан):

```yaml
method: userUnban
params:
 tg_chat_id: number  # Telegram id чата
 user_id: number     # Telegram id пользователя

success result:
 code: 0
 data: "ok"
```

#### Постинг

Получение списка отправленных постов:

```yaml
method: getPostsList
params:
 page: number  # номер страницы, начинается с 1, на странице максимум 50 строк

success result:
 code: 0
 data: Массив постов [id, date_sent]
```

Отправка поста:

```yaml
method: postSend
params:
 chats_ids: string  # список Telegram chat id через запятую (без кавычек), "private" — отправка во все приватные чаты
 categories_ids: string  # список id категорий через запятую (без кавычек) для фильтрации пользователей в приватных чатах
 type: string  # "message" | "media" | "only_command"
 file_id: string  # id файла из Telegram для медиа-типа
 text: string  # текст сообщения для типа message или подпись (caption) для типа media
 pin_post: boolean  # закрепить пост после отправки, если true; по умолчанию false
 command_name: string  # прикрепить команду к посту, по умолчанию не используется
 date: string  # дата в формате UTC (19.04.2024 20:30 или 2024-04-19 20:30) или timestamp

success result:
 code: 0
 data: "ok"
```

Удалить отправленный пост:

```yaml
method: postDelete
params:
 post_id: number  # id поста для удаления

success result:
 code: 0
 data: "ok"
```

#### Категории

Получение списка категорий:

```yaml
method: getChatCategories
params:
 tg_chat_id: number  # Telegram id чата, id бота для приватного чата

success result:
 code: 0
 data: Массив категорий [id, name, is_main]
```

Изменить категории пользователю (за один запрос):

```yaml
method: categoriesChange
params:
 user_id: number  # Telegram id пользователя
 categories: json  # JSON-кодированный объект [add_categories = Массив[[chat_id, category_id],[...]], delete_categories = Массив[[chat_id, category_id],[...]], replace = boolean]

success result:
 code: 0
 data: "ok"
```

Создание категории:

```yaml
method: createChatCategory
params:
 tg_chat_id: number  # Telegram id чата, id бота для приватного чата
 name: string  # название новой категории

success result:
 code: 0
 data: id новой категории
```

Добавить категорию пользователю:

```yaml
method: categoryAdd
params:
 user_id: number
 tg_chat_id: number
 category_id: number

success result:
 code: 0
 data: "ok"
```

Удалить категорию пользователю:

```yaml
method: categoryDelete
params:
 user_id: number
 tg_chat_id: number
 category_id: number

success result:
 code: 0
 data: "ok"
```

Заменить все категории пользователя на указанную:

```yaml
method: categoryReplace
params:
 user_id: number
 tg_chat_id: number
 category_id: number

success result:
 code: 0
 data: "ok"
```

#### Сценарии

Получение списка сценариев:

```yaml
method: getScenariosList
params:
 page: number  # номер страницы, начинается с 1, на странице максимум 50 строк

success result:
 code: 0
 data: Массив сценариев [id, name]
```

Изменить состояние сценариев пользователю:

```yaml
method: scenariosChange
params:
 user_id: number
 scenarios: json  # JSON-кодированный массив [scenario_id : state (0 - остановить, 1 - запустить)]

success result:
 code: 0
 data: "ok"
```

Запустить сценарий пользователю:

```yaml
method: scenarioRun
params:
 user_id: number
 scenario_id: number

success result:
 code: 0
 data: "ok"
```

Остановить сценарий пользователю:

```yaml
method: scenarioStop
params:
 user_id: number
 scenario_id: number

success result:
 code: 0
 data: "ok"
```

---

### Вход

Получение списка ссылок:

```yaml
method: getLinksList
params:
 page: number  # номер страницы, начинается с 1, на странице максимум 50 строк

success result:
 code: 0
 data: Массив ссылок [id, name, type, variable, max_activations, max_links, enabled]
```

Получение списка пользователей по конкретной ссылке:

```yaml
method: getLinkActivations
params:
 link_id: number
 page: number  # номер страницы, начинается с 1, на странице максимум 50 строк

success result:
 code: 0
 data: Массив активаций ссылок [link_id, link_key, link_owner_tg_user_id, user_id, date]
```

---

### Переменные

Изменить переменную:

```yaml
method: variableChange
params:
 variable: string  # имя переменной
 expression: string  # выражение для вычисления значения
 user_id: number  # Telegram id пользователя для изменения персональной переменной

success result:
 code: 0
 data: "ok"
```

Получение значения переменной:

```yaml
method: getVariableValue
params:
 variable: string
 user_id: number  # Telegram id пользователя для получения значения персональной переменной

success result:
 code: 0
 data: "Значение переменной"
```

Очистить переменную:

```yaml
method: variableClear
params:
 variable: string
 user_id: number  # Telegram id пользователя для изменения персональной переменной

success result:
 code: 0
 data: "ok"
```

---

### Таблицы Google

Получить содержимое Google таблицы в формате JSON (только для привязанных аккаунтов):

```yaml
method: getSpreadSheetRows
params:
 spreadsheet_id: string  # id таблицы (spreadsheet)
 email: string  # email для запроса
 sheets_ids: string  # id листов через запятую (по умолчанию не задано — получаются все листы)
 get_rows_cache_time: number  # время кэширования строк в секундах (по умолчанию 5, максимум 86400)
 get_sheets_cache_time: number  # время кэширования списка листов в секундах (по умолчанию 5, максимум 86400)

success result:
 code: 0
 data: Массив объектов таблиц [sheet_id, sheet_title, rows]
```

---

### Магазин

Получение списка товаров:

```yaml
method: getShopItemsList
params:
 page: number  # номер страницы, начинается с 1, на странице максимум 50 строк

success result:
 code: 0
 data: Массив товаров [id, name, article, count_left]
```

Изменить остатки товара:

```yaml
method: shopItemChangeCountLeft
params:
 item_id: number
 count_left: number  # новое количество товара (используйте -1 для бесконечного запаса)

success result:
 code: 0
 data: "ok"
```

---

### Только для ботов

Привязать бота:

```yaml
method: botConnect
params: no

success result:
 code: 0
 data: "ok"
```

Отвязать бота:

```yaml
method: botDisconnect
params: no

success result:
 code: 0
 data: "ok"
```

Включить бота:

```yaml
method: botEnable
params: no

success result:
 code: 0
 data: "ok"
```

Выключить бота:

```yaml
method: botDisable
params: no

success result:
 code: 0
 data: "ok"
```

Получить список чатов:

```yaml
method: getChatsList
params: no

success result:
 code: 0
 data: Массив чатов [chat_id, tg_chat_id, title, description, type]
```

Скачать файл:

```yaml
method: downloadFile
params:
 file_id: string  # id файла в Telegram для скачивания

success result:
 Output file with "Content-Type: application/octet-stream"
```

---

### Конструктор

Получить список команд и условий:

```yaml
method: getCommandsList
params: no

success result:
 code: 0
 data: Массив команд [id, name, group_id]
```

Получить список групп команд:

```yaml
method: getCommandsGroupsList
params: no

success result:
 code: 0
 data: Массив групп команд [id, name]
```

Отправка команды или условия:

```yaml
method: sendCommand
params:
 command_name: string  # имя команды или условия
 tg_chat_id: number  # Telegram id ресурса или пользователя

success result:
 code: 0
 data: "ok"
```

---

### Диалоги

Начать диалог:

```yaml
method: dialogStart
params:
 user_id: number

success result:
 code: 0
 data: "ok"
```

Завершить диалог:

```yaml
method: dialogEnd
params:
 user_id: number

success result:
 code: 0
 data: "ok"
```

---

### Только для ресурсов

Принять заявку на вступление в ресурс:

```yaml
method: joinRequestApprove
params:
 user_id: number
 tg_chat_id: number

success result:
 code: 0
 data: "ok"
```

Отклонить заявку на вступление в ресурс:

```yaml
method: joinRequestDecline
params:
 user_id: number
 tg_chat_id: number

success result:
 code: 0
 data: "ok"
```

Удаление пользователя (кик):

```yaml
method: userKick
params:
 tg_chat_id: number
 user_id: number

success result:
 code: 0
 data: "ok"
```

---

### Триггеры (только для групп)

Получение списка триггеров:

```yaml
method: getTriggersList
params:
 page: number  # номер страницы, начинается с 1, на странице максимум 50 строк

success result:
 code: 0
 data: Массив триггеров [id, chat_id, name, enabled]
```

Отправка триггера:

```yaml
method: triggerSend
params:
 trigger_id: number
 user_id: number

success result:
 code: 0
 data: "ok"
```

---

## Ошибки

| Код | Описание |
|-----:|----------|
| 1 | Неверный формат токена |
| 2 | Неверный формат метода |
| 3 | Токен не найден |
| 4 | Неизвестный метод |
| 40 | Отсутствует параметр `tg_chat_id` или `user_id` |
| 50 | Ошибка при установке webhook |
| 51 | Ошибка при удалении webhook |
| 100 | Внутренняя ошибка |
| 102 | Ошибка при вычислении переменной |
| 104 | Переменная не найдена |
| 200 | Команда/условие не найдено |
| 201 | Пользователь не найден |
| 202 | Чат не найден |
| 203 | Пользователь в чате не найден |
| 204 | Пост не найден |
| 205 | Пост не отправлен |
| 207 | Триггер не найден |
| 208 | Не удалось подтвердить заявку на вступление |
| 209 | Не удалось отклонить заявку на вступление |
| 210 | Категория не найдена |
| 211 | Неизвестный тип поста |
| 212 | Пустой список чатов |
| 429 | Слишком много запросов в минуту/в месяц |

> Пояснение: коды ошибок возвращаются в поле `code` ответа. 0 означает успех.
