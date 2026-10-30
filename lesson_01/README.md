# Задание 1 · своя коллекция и шесть запросов

**Студент:** Бацаценко Алеся Андреевна

Стенд курса поднят из папки `stend` (`docker compose up -d`), Compass подключён
строкой `mongodb://localhost:27017`. Свою коллекцию собрала в базе `helpdesk`,
коллекция `tickets`: пять заявок сервисной службы.

## Что лежит в папке

- [`seed.json`](seed.json) — пять документов, загружаются в Compass через
  **ADD DATA → Import JSON or CSV file** без правок;
- [`queries.md`](queries.md) — шесть запросов с найденными числами и разбор
  сломанного запроса;
- `screenshot.png` — Compass с коллекцией и выполненным запросом.

## Форма документа

```js
{
  _id: "t-001",
  number: "HT-001",
  created: ISODate("2026-09-02T09:15:00Z"),
  customer: { name: "ООО «Вектор»", contact: "vetor@example.ru" },
  priority: "высокий",
  status: "решен",
  hours: 4,
  tags: ["доступ", "сеть"],
  solution: { engineer: "Миронов", hours: 3 }
}
```

В документах есть вложенный объект (`customer`, `solution`), массив (`tags`),
число (`hours`) и строки (`number`, `status`). Форма намеренно разная:
у `t-004` внутри `customer` добавлен телефон, у `t-005` есть поле `comment`,
которого нет у остальных, а `solution` в трёх документах пустой — `null`.

## Проверено в Compass

| Запрос | Найдено |
|---|---|
| `{ customer.name: "Сомова" }` | 1 из 5 |
| `{ "customer.contact": "somova@example.ru" }` | 1 из 5 |
| `{ tags: "печать" }` | 2 из 5 |
| `{ hours: { $gt: 3 } }` | 2 из 5 |
| `{ comment: { $exists: true } }` | 1 из 5 |
| `{ hours: { $gt: "3" } }` | 0 из 5 |

После загрузки `seed.json` числа можно перепроверить в Compass и на приёмке.

## Как загрузить

1. Compass → **ADD DATA → Import JSON or CSV file** → `seed.json`;
2. база `helpdesk` (если её нет — **Create database**), коллекция `tickets`;
3. формат определяется автоматически; даты записаны расширенно
   (`{"$date": "..."}`), поэтому попадут в базу типом Date.

Проверка без Compass:

```bash
docker exec -i hh-mongo mongosh helpdesk --quiet --eval 'db.tickets.countDocuments()'   # 5
docker exec -i hh-mongo mongosh helpdesk --quiet --eval 'db.tickets.find({ tags: "печать" }).count()'   # 2
```

## Сдача

Файлы: `seed.json`, `queries.md`, `screenshot.png`. Ветка `hw-01`,
Pull Request в свою `main`.
