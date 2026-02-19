```markdown
# Entry Classification

## Work Domains → Categories

Based on user's work context (see [ABOUT.md](ABOUT.md)):

### Стартап — Продукт
Разметка данных, CVAT, агенты, MVP, пилоты, клиенты

**Keywords:** разметка, CVAT, агент, MVP, пилот, клиент, метрики, качество, скорость, аннотация

**→ Category:** task (p1-p2) → Singularity App

### Стартап — Бизнес
Бизнес-модель, CustDev, монетизация, unit-экономика, фондирование

**Keywords:** бизнес-модель, монетизация, CustDev, интервью, ICP, unit-экономика, pricing, выручка, burn rate

**→ Category:** task (p1-p2) или idea → thoughts/

### AI & Tech — Обучение
Инструменты, модели, фреймворки, навыки

**Keywords:** OpenClaw, Claude Code, ML, LLM, API, вайб-кодинг, продуктовый дизайн, курс, туториал

**→ Category:** learning → thoughts/learnings/

### AI & Tech — R&D
Эксперименты, прототипы, технические гипотезы

**Keywords:** прототип, proof-of-concept, эксперимент, архитектура, мультиагент, pipeline, интеграция, тест

**→ Category:** project → thoughts/projects/

### Личное
Здоровье, семья, чтение, хобби

**Keywords:** тренировка, чекап, семья, книга, охота, рыбалка, путешествие

**→ Category:** task (p3-p4) или reflection

---

## Decision Tree

```
Entry text contains...
│
├─ Стартап + дедлайн/клиент? ────────────────────> TASK (p1-p2)
│  (разметка, CVAT, клиент, пилот, MVP, дедлайн)
│
├─ Бизнес-модель/CustDev? ──────────────────────> TASK (p1-p2) или IDEA
│  (монетизация, интервью, pricing, unit-экономика)
│
├─ Техническая задача/эксперимент? ──────────────> PROJECT
│  (прототип, агент, интеграция, тест, API)
│
├─ Изучаю что-то новое? ────────────────────────> LEARNING
│  (узнал, модель, фреймворк, туториал)
│
├─ Стратегическое мышление? ─────────────────────> PROJECT
│  (стратегия, план, масштабирование, долгосрочно)
│
├─ Личный инсайт? ──────────────────────────────> REFLECTION
│  (понял, осознал, вывод)
│
└─ Идея нового подхода/фичи? ───────────────────> IDEA
   (а что если, можно попробовать, гипотеза)
```

## Apply Decision Filters

Перед сохранением спроси:
- Это приближает к запуску MVP и первым клиентам?
- Это можно использовать в стартапе?
- Какими средствами и ресурсами решать, и какой результат?
- Это открывает новые возможности для бизнеса?

Если да на 2+ вопроса → повысить приоритет.

---

## Photo Entries

For `[photo]` entries:

1. Analyze image content via vision
2. Determine domain:
   - Скриншот CVAT/кода → Стартап — Продукт или AI & Tech — R&D
   - Схема/диаграмма → AI & Tech или Стартап — Бизнес
   - Текст/статья → Learning
3. Add description to daily file

---

## Output Locations

| Category | Destination | Priority |
|----------|-------------|----------|
| task (стартап — продукт) | Singularity App | p1-p2 |
| task (стартап — бизнес) | Singularity App | p1-p2 |
| task (личное) | Singularity App | p3-p4 |
| idea | thoughts/ideas/ | — |
| reflection | thoughts/reflections/ | — |
| project | thoughts/projects/ | — |
| learning | thoughts/learnings/ | — |

---

## File Naming

```
thoughts/{category}/{YYYY-MM-DD}-short-title.md
```

Examples:
```
thoughts/ideas/2026-02-24-pricing-per-task-model.md
thoughts/projects/2026-02-24-openclaw-cvat-integration.md
thoughts/learnings/2026-02-24-cvat-api-endpoints.md
thoughts/reflections/2026-02-24-custdev-insights.md
```

---

## Thought Structure

Use preferred format:

```markdown
---
date: {YYYY-MM-DD}
type: {category}
domain: {Стартап — Продукт|Стартап — Бизнес|AI & Tech — Обучение|AI & Tech — R&D|Личное}
tags: [tag1, tag2]
---

## Context
[Что привело к мысли]

## Insight
[Ключевая идея]

## Implication
[Что это значит для стартапа/продукта/стратегии]

## Next Action
[Конкретный шаг — не абстрактный]
```

---

## Anti-Patterns (ИЗБЕГАТЬ)

При создании мыслей НЕ делать:
- Абстрактные рассуждения без Next Action
- Академическая теория без применения к стартапу
- Повторы без синтеза (кластеризуй похожие!)
- Хаотичные списки без приоритетов
- Задачи типа "подумать о..." (конкретизируй!)
- Задачи, не связанные с текущими приоритетами (стартап, обучение, здоровье)

---

## MOC Updates

After creating thought file, add link to:
```
MOC/MOC-{category}s.md
```

Group by domain when relevant:
```markdown
## Стартап — Продукт
- [[2026-02-24-openclaw-cvat-integration]] - Интеграция OpenClaw + CVAT

## Стартап — Бизнес
- [[2026-02-24-pricing-per-task-model]] - Модель монетизации за задачу

## AI & Tech — Обучение
- [[2026-02-24-cvat-api-endpoints]] - Изучение CVAT API
```