- [5. Suchen und sortieren, gruppieren und berechnen](#5-suchen-und-sortieren-gruppieren-und-berechnen)
  - [5.1 Daten sortieren (ORDER BY)](#51-daten-sortieren-order-by)
  - [5.2 Suchen mit LIKE und ILIKE (Pattern Matching)](#52-suchen-mit-like-und-ilike-pattern-matching)
  - [5.3 Aggregatfunktionen (COUNT, SUM, AVG, MIN, MAX)](#53-aggregatfunktionen-count-sum-avg-min-max)
  - [5.4 Gruppieren mit GROUP BY](#54-gruppieren-mit-group-by)

Bearbeitungszeit: 60 Minuten

# 5. Suchen und sortieren, gruppieren und berechnen

In dieser Aufgabe lernen Sie, wie Sie Daten sortieren, filtern, gruppieren und mit Aggregatfunktionen auswerten.

Verwenden Sie dafür die bereits angelegten Tabellen **products**, **customers**, **orders** und **order_items**.

## 5.1 Daten sortieren (ORDER BY)

Erstellen Sie die folgenden Abfragen für die Tabelle products:

1. Alle Produkte aufsteigend nach Name sortiert.

    **Erwartetes Ergebnis**

    | id | name | price | created_at | category |
    | -- | ---- | ----: | ---------- | -------- |
    | 4	| Laptop |	1098.90	| 2025-11-28 18:16:31.480651 | allgemein |
    | 1	| Monitor |	175.89	| 2025-11-28 18:16:31.480651 | allgemein |
    | 2	| Tastatur |	35.49	| 2025-11-28 18:16:31.480651 | allgemein |

2. Alle Produkte absteigend nach Preis sortiert.

    **Erwartetes Ergebnis**

    | id | name | price | created_at | category |
    | -- | ---- | ----: | ---------- | -------- |
    | 4	| Laptop |	1098.90	| 2025-11-28 18:16:31.480651 | allgemein |
    | 1	| Monitor |	175.89	| 2025-11-28 18:16:31.480651 | allgemein |
    | 2	| Tastatur |	35.49	| 2025-11-28 18:16:31.480651 | allgemein |

3. Alle Produkte zuerst nach category (aufsteigend) und innerhalb der Kategorie nach price (absteigend) sortiert.

    **Erwartetes Ergebnis**

    | id | name | price | created_at | category |
    | -- | ---- | ----: | ---------- | -------- |
    | 4	| Laptop |	1098.90	| 2025-11-28 18:16:31.480651 | allgemein |
    | 1	| Monitor |	175.89	| 2025-11-28 18:16:31.480651 | allgemein |
    | 2	| Tastatur |	39.49	| 2025-11-28 18:16:31.480651 | allgemein |

<details>
<summary>Show solution</summary>
<p>

### 1. Nach Name aufsteigend

**SQL-Abfrage**

```postgresql
SELECT *
FROM products
ORDER BY name ASC;
```

### 2. Nach Preis absteigend

**SQL-Abfrage**

```postgresql
SELECT *
FROM products
ORDER BY price DESC;
```

### 3. Zuerst nach category, dann nach price (absteigend)

**SQL-Abfrage**

```postgresql
SELECT *
FROM products
ORDER BY category ASC, price DESC;
```

</p>
</details>

## 5.2 Suchen mit LIKE und ILIKE (Pattern Matching)

Erstellen Sie folgende Abfragen:

1. Finden Sie alle Produkte, deren Name mit dem Buchstaben „M“ beginnt.

    **Erwartetes Ergebnis**

    | id | name | price | created_at |
    | -- | ---- | ----: | ---------- |
    | 1	| Monitor |	175.49	| 2025-11-28 18:16:31.480651 |

2. Finden Sie alle Produkte, deren Name die Zeichenkette „top“ enthält (z. B. „Laptop“).

    **Erwartetes Ergebnis**

    | id | name | price | created_at |
    | -- | ---- | ----: | ---------- |
    | 4	| Laptop |	1098.90	| 2025-11-28 18:16:31.480651 |
  
3. Finden Sie alle Produkte, deren Name „monitor“ enthält – unabhängig von Groß-/Kleinschreibung.

    **Erwartetes Ergebnis**

    | id | name | price | created_at |
    | -- | ---- | ----: | ---------- |
    | 1	| Monitor |	175.89	| 2025-11-28 18:16:31.480651 |

<details>
<summary>Show solution</summary>
<p>

### 1. Name beginnt mit 'M'

**SQL-Abfrage**

```postgresql
SELECT *
FROM products
WHERE name LIKE 'M%';
```

### 2. Name enthält 'top'

**SQL-Abfrage**

```postgresql
SELECT *
FROM products
WHERE name LIKE '%top%';
```

### 3. Name enthält 'monitor' (case-insensitive)

**SQL-Abfrage**

```postgresql
SELECT *
FROM products
WHERE name ILIKE '%monitor%';
```

**Erwartetes Ergebnis**

| id | name | price | created_at |
| -- | ---- | ----: | ---------- |
| 1	| Monitor |	175.89	| 2025-11-28 18:16:31.480651 |

</p>
</details>

## 5.3 Aggregatfunktionen (COUNT, SUM, AVG, MIN, MAX)

Nutzen Sie die Tabelle order_items, um Kennzahlen zu berechnen:

1. Ermitteln Sie die Anzahl aller Bestellpositionen.

    **Erwartetes Ergebnis**

    | position_count |
    | -------------: |
    | 4 |
  
2. Ermitteln Sie die Gesamtmenge (SUM der quantity) aller Positionen.

    **Erwartetes Ergebnis**

    | total_quantity |
    | -------------: |
    | 7 |
  
3. Ermitteln Sie den Gesamtumsatz über alle Bestellpositionen (Hinweis: quantity * price).

    **Erwartetes Ergebnis**

    | total_revenue |
    | -------------: |
    | 1268.40 |
  
4. Ermitteln Sie den höchsten Einzelpreis (MAX(price)) und den niedrigsten Einzelpreis (MIN(price)).

      **Erwartetes Ergebnis**

    | max_price | min_price |
    | --------: | --------: |
    | 999.00 | 19.90 |

<details>
<summary>Show solution</summary>
<p>

### 1. Anzahl aller Bestellpositionen

**SQL-Abfrage**

```postgresql
SELECT COUNT(*) AS position_count
FROM order_items;
```

### 2. Gesamtmenge

**SQL-Abfrage**

```postgresql
SELECT SUM(quantity) AS total_quantity
FROM order_items;
```

### 3. Gesamtumsatz

**SQL-Abfrage**

```postgresql
SELECT SUM(quantity * price) AS total_revenue
FROM order_items;
```

### 4. Höchster und niedrigster Einzelpreis

**SQL-Abfrage**

```postgresql
SELECT
  MAX(price) AS max_price,
  MIN(price) AS min_price
FROM order_items;
```

</p>
</details>

## 5.4 Gruppieren mit GROUP BY

Erstellen Sie Auswertungen für die Tabelle products bzw. order_items:

1. Gruppieren Sie Produkte nach category und geben Sie aus:
    - Kategorie
    - Anzahl der Produkte je Kategorie (COUNT(*))

    **Erwartetes Ergebnis**

    | category | product_count |
    | --------- | -----------: |
    | allgemein | 3 |

2. Ermitteln Sie pro Kategorie den durchschnittlichen Produktpreis (AVG(price)).

    **Erwartetes Ergebnis**

    | category | avg_price |
    | --------- | -------: |
    | allgemein | 438.09 |

3. Ermitteln Sie pro Produkt (über order_items) den Gesamtumsatz und geben Sie Produkt-ID und Gesamtumsatz aus.

    **Erwartetes Ergebnis**

    | product_id | total_revenue |
    | ---------: | ------------: |
    | 1 | 209.60 |
    | 2 | 59.80 |
    | 4 | 999.00 |

<details>
<summary>Show solution</summary>
<p>

### 1. Anzahl Produkte je Kategorie

**SQL-Abfrage**

```postgresql
SELECT
  category,
  COUNT(*) AS product_count
FROM products
GROUP BY category;
```

### 2. Durchschnittspreis je Kategorie

**SQL-Abfrage**

```postgresql
SELECT
  category,
  AVG(price) AS avg_price
FROM products
GROUP BY category;
```

### 3. Gesamtumsatz je Produkt (über order_items)

**SQL-Abfrage**

```postgresql
SELECT
  product_id,
  SUM(quantity * price) AS total_revenue
FROM order_items
GROUP BY product_id;
```

</p>
</details>

## 5.5 Gruppen filtern (HAVING)

Auf Basis der vorherigen Gruppierungen:

1. Zeigen Sie nur Kategorien, in denen es mehr als 1 Produkt gibt.

    **Erwartetes Ergebnis**

    | category | product_count |
    | -------- | ------------: |
    | allgemein | 3 |
  
2. Zeigen Sie nur Produkte (über order_items), deren Gesamtumsatz größer als 200 ist.

    **Erwartetes Ergebnis**

    | product_id | total_revenue |
    | ---------: | ------------: |
    | 1 | 209.60 |
    | 3 | 999.00 |

Verwenden Sie hierfür GROUP BY in Kombination mit HAVING.

<details>
<summary>Show solution</summary>
<p>

### 1. Nur Kategorien mit mehr als 1 Produkt

**SQL-Abfrage**

```postgresql
SELECT
  category,
  COUNT(*) AS product_count
FROM products
GROUP BY category
HAVING COUNT(*) > 1;
```

### 2. Nur Produkte mit Gesamtumsatz > 200

**SQL-Abfrage**

```postgresql
SELECT
  product_id,
  SUM(quantity * price) AS total_revenue
FROM order_items
GROUP BY product_id
HAVING SUM(quantity * price) > 200;
```

</p>
</details>

## 5.6 Bonusaufgabe: Kombinierte Abfrage mit JOIN + GROUP BY

Erstellen Sie eine Abfrage, die für jede Kategorie den Gesamtumsatz aller Produkte dieser Kategorie berechnet.

Hinweise:

- JOIN products mit order_items über product_id
- Gruppierung nach category
- Aggregat SUM(quantity * price)

**Erwartetes Ergebnis**

| category | total_revenue |
| -------- | ------------: |
| allgemein | 1268.40 |

<details>
<summary>Show solution</summary>
<p>

**SQL-Abfrage**

```postgresql
SELECT
  p.category,
  SUM(oi.quantity * oi.price) AS total_revenue
FROM products p
JOIN order_items oi
  ON p.id = oi.product_id
GROUP BY p.category
ORDER BY total_revenue DESC;
```

</p>
</details>
