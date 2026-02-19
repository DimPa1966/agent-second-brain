```markdown
# Singularity App Integration

## API Overview

**Base URL:** `https://api.singularity-app.com/v2/`
**Auth:** Bearer token (создаётся в личном кабинете → «Доступ к API»)
**Документация:** [Swagger](https://api.singularity-app.com/v2/api)
**MCP:** Поддерживается — конфигурация скачивается из личного кабинета → «MCP-конфигурация»

### Доступные операции (CRUD)

| Сущность | Endpoints |
|----------|-----------|
| Задачи | GET/POST/PATCH/DELETE `/v2/task` |
| Проекты | GET/POST/PATCH/DELETE `/v2/project` |
| Привычки | GET/POST/PATCH/DELETE `/v2/habit` |
| Теги | GET/POST/PATCH/DELETE `/v2/tag` |
| Чек-листы | GET/POST/PATCH/DELETE `/v2/checklist-item` |
| Канбан | POST `/v2/kanban-task-status`, `/v2/kanban-status` |
| Секции | GET/POST/PATCH/DELETE `/v2/task-group` |
| Отметка привычки | POST `/v2/habit-progress` |

**Ограничения API:**
- Повторяющиеся задачи через API создать нельзя
- Совместные проекты не возвращаются в GET `/v2/project`

---

## Pre-Creation Checklist

### 1. Check Workload (REQUIRED)

```
GET /v2/task
  ?startDateFrom={today}
  &startDateTo={today+7days}
  &includeRemoved=false
  &includeArchived=false
```

Build workload map:
```
Mon: 2 tasks (тренировка 11-14)
Tue: 3 tasks  ← at limit
Wed: 1 task (тренировка 11-14)
Thu: 3 tasks  ← at limit
Fri: 2 tasks (тренировка 11-14)
Sat: 0 tasks
Sun: 0 tasks
```

**Note:** Пн/Ср/Пт — тренировки 11:00–14:00, доступного времени меньше.

### 2. Check Duplicates (REQUIRED)

```
GET /v2/task
  — просмотреть список задач по ключевым словам
```

If similar exists → mark as duplicate, don't create.

---

## Priority Mapping

Singularity App использует 3 уровня приоритета:

| Singularity | Значение | Использование |
|-------------|----------|---------------|
| 0 | Высокий | Срочные задачи стартапа, дедлайны, блокеры |
| 1 | Обычный | Важные задачи: CustDev, обучение, R&D |
| 2 | Низкий | Стратегические, долгосрочные, опциональные |

### Priority by Domain

Based on user's work context (see [ABOUT.md](ABOUT.md)):

| Domain | Default Priority | Override |
|--------|-----------------|----------|
| Стартап — Продукт (MVP, клиенты, пилоты) | 0 | — |
| Стартап — Бизнес (CustDev, модель) | 0–1 | — |
| AI & Tech — R&D (прототипы, эксперименты) | 1 | приближает к MVP → 0 |
| AI & Tech — Обучение | 1–2 | нужно для текущей задачи → 1 |
| Личное (здоровье, семья) | 2 | — |

### Priority Keywords

| Keywords in text | Priority |
|-----------------|----------|
| срочно, критично, дедлайн, блокер | 0 (высокий) |
| важно, приоритет, до конца недели, клиент | 0 (высокий) |
| нужно, надо, не забыть, изучить | 1 (обычный) |
| стратегия, долгосрочно, когда будет время | 2 (низкий) |

### Apply Decision Filters for Priority Boost

If entry matches 2+ filters → boost priority by 1 level:
- Это приближает к запуску MVP и первым клиентам?
- Это можно использовать в стартапе?
- Какими средствами и ресурсами решать, и какой результат?
- Это открывает новые возможности для бизнеса?

---

## Date Mapping

Singularity API принимает даты в ISO формате: `YYYY-MM-DD`

| Context | Дата (поле "start") |
|---------|---------------------|
| **Срочная задача стартапа** | сегодня |
| **До конца недели** | ближайшая пятница |
| **На следующей неделе** | следующий понедельник |
| **Стратегическая/R&D** | +7 дней |
| **Не указано** | +3 дня |

### Russian → ISO Date

| Russian | Дата |
|---------|------|
| сегодня | {today} |
| завтра | {today+1} |
| послезавтра | {today+2} |
| в понедельник | {next monday} |
| в пятницу | {next friday} |
| на этой неделе | {this friday} |
| на следующей неделе | {next monday} |
| через неделю | {today+7} |
| 15 января | 2026-01-15 |

---

## Task Creation

```json
POST /v2/task
{
  "title": "Task title",
  "start": "2026-02-24",
  "priority": 1,
  "note": "→ Weekly focus → Goal: MVP + первые клиенты"
}
```

### Task Title Style

User prefers: прямота, ясность, конкретика

✅ Good:
- "Изучить CVAT API: эндпоинты для автоматизации разметки"
- "Провести CustDev-интервью с ML-командой"
- "Создать тестового агента в OpenClaw"

❌ Bad:
- "Подумать о CVAT"
- "Что-то с разметкой"
- "Разобраться с агентами"

### Workload Balancing

If target day has 3+ tasks:
1. Find next day with < 3 tasks
2. Use that day instead
3. Mention in report: "сдвинуто на {day} (перегрузка)"
4. Учитывать тренировки Пн/Ср/Пт — в эти дни эффективная ёмкость ниже

---

## Project Detection

| Keywords | Project |
|----------|---------|
| разметка, CVAT, агент, MVP, пилот, клиент | Стартап |
| OpenClaw, Claude Code, ML, курс, туториал | Обучение |
| тренировка, чекап, книга, семья | Личное |

If unclear → без проекта (Inbox).

---

## Habit Tracking

Привычки Дмитрия для трекинга через API:

| Привычка | Расписание |
|----------|-----------|
| Тренировка | Пн, Ср, Пт |
| Чтение | 2+ часа в неделю |

Отметка привычки:
```json
POST /v2/habit-progress
{
  "habit": "{habit_id}",
  "date": "2026-02-24",
  "progress": 2
}
```

`progress`: 0 = без изменений, 1 = не выполнена (сохраняет серию), 2 = выполнена

---

## Anti-Patterns (НЕ СОЗДАВАТЬ)

Based on user preferences:

- ❌ "Подумать о..." → конкретизируй действие
- ❌ "Разобраться с..." → что именно сделать?
- ❌ Абстрактные задачи без Next Action
- ❌ Дубликаты существующих задач
- ❌ Задачи без дат
- ❌ Задачи, не связанные с текущими приоритетами

---

## Error Handling

CRITICAL: Никогда не предлагай "добавить вручную".

If API call fails:
1. Include EXACT error message in report
2. Continue with next entry
3. Don't mark as processed
4. User will see error and can debug

WRONG output:
  "Не удалось добавить (API недоступен). Добавь вручную: Task title"

CORRECT output:
  "Ошибка создания задачи: [exact error from API]"
```