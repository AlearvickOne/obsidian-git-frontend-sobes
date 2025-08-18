Вот вся информация, собранная по вашей задаче, оформленная в формате Markdown (md):

📊 Анализ злоупотреблений через сессии в системе пользователей

Цель: выявить пользователей, которые злоупотребляют доступом, используя аккаунт с нескольких устройств, браузеров или передавая его другим лицам (шаринг аккаунта).

---

🗂 Структура таблиц

1. Таблица sessions — информация о сессиях пользователей
    

```sql
CREATE TABLE sessions (
  id int unsigned NOT NULL AUTO_INCREMENT,
  user_id int NOT NULL DEFAULT '-1',
  visitor_id varchar(100) NOT NULL DEFAULT '',
  browser varchar(100) DEFAULT NULL,
  created_at timestamp NULL DEFAULT CURRENT_TIMESTAMP,
  PRIMARY KEY (id),
  UNIQUE KEY (user_id, visitor_id)
);
```

2. Таблица log_actions — действия пользователей
    

```sql
CREATE TABLE log_actions (
  id int unsigned NOT NULL AUTO_INCREMENT,
  users_id int NOT NULL,
  target_id int NOT NULL COMMENT 'id урока',
  action_id tinyint NOT NULL COMMENT '1 - вход, 2 - просмотр урока, 3 - выполнение задания',
  created_at timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
  PRIMARY KEY (id),
  KEY (users_id, target_id, action_id, created_at)
);
```

---

📌 1. Сбор общей статистики по сессиям и активности  
Создание временной таблицы user_stats:

```sql
CREATE TEMPORARY TABLE user_stats AS
SELECT
  s.user_id,
  COUNT(DISTINCT s.visitor_id) AS total_sessions,
  MIN(la.created_at) AS first_activity,
  MAX(la.created_at) AS last_activity,
  DATEDIFF(MAX(la.created_at), MIN(la.created_at)) + 1 AS span_days,
  COUNT(la.id) AS total_actions
FROM sessions s
JOIN log_actions la ON la.users_id = s.user_id
GROUP BY s.user_id;
```

Рассчитать частоту появления новых сессий:

```sql
SELECT
  *,
  ROUND(total_sessions / span_days, 2) AS sessions_per_day
FROM user_stats;
```

---

📌 2. Распределение по bins для анализа нормы

```sql
SELECT
  FLOOR(total_sessions / span_days) AS sessions_per_day_bin,
  COUNT(*) AS users_count
FROM user_stats
WHERE span_days > 0
GROUP BY sessions_per_day_bin
ORDER BY sessions_per_day_bin;
```

Пример интерпретации:

|sessions_per_day_bin|users_count|Комментарий|
|---|---|---|
|0|31,860|Очень редкая активность|
|1|17,298|Норма (1 сессия в день)|
|2|4,562|Активные, но допустимо|
|3|960|Очень активные|
|>3|~400|Возможный аккаунт-шаринг или боты|

---

📌 3. Поиск подозрительных пользователей по sessions_per_day

```sql
SELECT
  *,
  ROUND(total_sessions / span_days, 2) AS sessions_per_day
FROM user_stats
WHERE total_sessions / span_days > 3;
```

—

📌 4. Поиск пользователей с большим числом сессий за короткое время

```sql
SELECT
  *,
  ROUND(total_sessions / span_days, 2) AS sessions_per_day
FROM user_stats
WHERE total_sessions > 20 AND span_days < 5;
```

---

🚨 5. Поиск user_id с одновременной активностью с разных visitor_id

Условие: два разных visitor_id у одного user_id → действия в пределах 60 секунд друг от друга.

Дополнительные условия:

- Дата сессии >= 2025-05-01
    
- Пользователь имеет >10 уникальных visitor_id
    

Итоговый запрос:

```sql
SELECT DISTINCT la1.users_id AS user_id
FROM log_actions la1
JOIN sessions s1 ON s1.user_id = la1.users_id
JOIN log_actions la2 ON la2.users_id = la1.users_id
JOIN sessions s2 ON s2.user_id = la2.users_id
WHERE
  la1.id < la2.id
  AND s1.visitor_id <> s2.visitor_id
  AND ABS(TIMESTAMPDIFF(SECOND, la1.created_at, la2.created_at)) <= 60
  AND s1.created_at >= '2025-05-01'
  AND s2.created_at >= '2025-05-01'
  AND la1.users_id IN (
    SELECT user_id
    FROM sessions
    WHERE created_at >= '2025-05-01'
    GROUP BY user_id
    HAVING COUNT(DISTINCT visitor_id) > 10
  );
```

—

🎯 Результат:

- Список user_id, у которых зафиксировано одновременное использование аккаунта с разных visitor_id.
    
- Это — наиболее вероятные случаи «передачи аккаунта» или использования бот-сетей.
    

—

💬 Дополнительно:

- Можно расширить вывод, добавив в него browser и action_id.
    
- Для визуального аудита — выгрузить список в CSV и анализировать временные интервалы сессий.
    

Если хочешь, могу подготовить экспорт результатов в CSV, включая все поля (user_id, visitor_id, время действий и browser)

	1.1
		1.2
			1.3