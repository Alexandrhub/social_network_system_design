# Social network - System Design

System Design социальной сети путешественников для курса по System Design
https://balun.courses/courses/system_design

## Требования

### Функциональные требования

- публикация постов из путешествий с фотографиями, небольшим описанием и привязкой к конкретному месту путешествия;
- оценка и комментарии постов других путешественников;
- подписка на других путешественников, чтобы следить за их активностью;
- поиск популярных мест для путешествий и просмотр постов с этих мест в виде ТОПа мест по странам и городам;
- общение с другими путешественниками в личных сообщениях;
- просмотр ленты других путешественников;

### Нефункциональные требования

- DAU - 10 000 000;
- Доступность 99.95%;
- Сервис доступен только в СНГ;
- Срок хранения постов и фотографий - до удаления аккаунта пользователем(условно бессрочно);
- Средняя длина комментариев - 100 символов (utf-8 4 байта);
- Средняя длина поста - 500 символов (utf-8 4 байта);
- Средний размер медиа - 1 Мб;
- Среднее число опубликованных постов в день от пользователя - 1;
- Среднее число прочитанных постов в день пользователем - 20;
- Среднее число отправленных сообщений в день пользователем - 5;
- Среднее число прочитанных сообщений в день пользователем- 40;
- Среднее соотношение сообщений к сообщениями с медиа - 0.1 (10%);
- Среднее соотношение постов к постам с медиа - 0.8 (80%);
- Время ответа для отправки сообщения - 5 сек;
- Время ответа для получения сообщения - 10 сек;
- В каждом сообщении максимум 5 медиа файлов;

## Расчеты системы хранения данных

### RPS

#### Создание постов

$$
\frac{10 \space млн}{(24 \space ч \times 3600 \space с)} = \sim 116 \space rps
$$

#### Чтение постов

$$
\frac{10 \space млн \times 20 \space постов \space в \space день}{(24 \space ч \times 3600 \space с)} = \sim 2315 \space rps
$$

#### Создание сообщений

$$
\frac{10 \space млн \times 5 \space сообщений \space в \space день}{(24 \space ч \times 3600 \space с)} = \sim 579 \space rps
$$

#### Чтение сообщений

$$
\frac{10 \space млн \times 40 \space сообщений \space в \space день}{(24 \space ч \times 3600 \space с)} = \sim 4630 \space rps
$$

### Траффик

#### Создание постов

$$
{116 \space rps \times 500 \times 4 \space байт} = \sim 0.2 \space МБ/c
$$

#### Чтение постов

$$
{2315 \space rps \times 500 \times 4 \space байт} = \sim 4.6 \space МБ/c
$$

#### Создание сообщений

$$
{579 \space rps \times 100 \times 4 \space байт} = \sim 0.2 \space МБ/c
$$

#### Чтение сообщений

$$
{4630 \space rps \times 100 \times 4 \space байт} = \sim 1.9 \space МБ/c
$$

#### Создание файлов

$$
{(116 \space rps \times 0.8 + 579 \space rps \times 0.1) \times 5} = \sim 735.5 \space МБ/c
$$

#### Чтение файлов

$$
{(12731 \space rps \times 0.8 + 25463 \space rps \times 0.1) \times 5} = \sim 63655 \space МБ/c
$$

#### Входящий траффик (создание постов + создание сообщений + создание файлов)

$$
{0.2 \space МБ/c + 0.2 \space МБ/c + 735.5 \space МБ/c} = \sim 736 \space МБ/c
$$

#### Исходящий траффик(чтение постов + чтение сообщений + чтение файлов)

$$
{4.6 \space МБ/c + 1.9 \space МБ/c + 63655 \space МБ/c} = \sim 63661.5 \space МБ/c
$$

### Хранение

### Размер БД для хранения на 5 лет

$$
{735.5 \space МБ/c \times 36000 \space с \times 24 \space ч \times 365 \space д \times 5 \space лет} = \sim 115 \space ПБ
$$

### Необходимое число дисков (16 ТБ)

$$
\frac{115 \space ПБ \times 1000}{16 \space TB} = \sim 7188 \space дисков
$$

### Необходимое число дисков (16 ТБ) при репликации 2-factor

$$
\frac{115 \space ПБ \times 1000 \times 3}{16 \space TB} = \sim 21563 \space дисков
$$
