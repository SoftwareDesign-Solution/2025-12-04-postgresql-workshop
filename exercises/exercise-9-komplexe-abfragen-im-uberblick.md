- [9. Komplexe Abfragen im Überblick](#9-komplexe-abfragen-im-überblick)
  - [9.1 Komplexe Abfrage – Umsatz pro Kunde (CTE + JOIN + GROUP BY)](#91-komplexe-abfrage--umsatz-pro-kunde-cte--join--group-by)
  - [9.2 Kombination aus mehreren CTEs (mehrstufig)](#92-kombination-aus-mehreren-ctes-mehrstufig)
  - [9.3 Komplexe Abfrage mit mehreren Joins und mehreren Filtern](#93-komplexe-abfrage-mit-mehreren-joins-und-mehreren-filtern)
  - [9.4 Komplexe Abfrage mit Subquery UND CTE](#94-komplexe-abfrage-mit-subquery-und-cte)
  - [9.5 Bonusaufgabe: Sehr komplexe Abfrage (kombiniert alles)](#95-bonusaufgabe-sehr-komplexe-abfrage-kombiniert-alles)

Bearbeitungszeit: 60 Minuten

# 9. Komplexe Abfragen im Überblick

In diesem Aufgabenblatt kombinieren Sie verschiedene SQL-Bausteine wie CTEs, Joins, Aggregationen und Filter.
Zusätzlich lernen Sie Performance-Grundlagen wie Indexes und die Analyse von Abfragen mit EXPLAIN kennen.

Die Aufgaben basieren auf den Tabellen:

- products
- customers
- orders
- order_items

## 9.1 Komplexe Abfrage – Umsatz pro Kunde (CTE + JOIN + GROUP BY)

Erstellen Sie eine Abfrage mit einem CTE, der:

1. für jeden Kunden den Gesamtumsatz berechnet
2. dazu customers, orders und order_items verbindet
3. Umsatz = SUM(quantity * price)

Geben Sie danach alle Kunden mit einem Umsatz über 200 € aus.

**Erwartetes Ergebnis**

| customer_id | customer_name | total_revenue |
| ----------: | ------------- | ------------: |
| 2 | Bob OHG | 1058.70 |
| 1 | Alice GmbH | 209.70 |

<details>
<summary>Show solution</summary>
<p>

**SQL-Abfrage**

```postgresql
WITH customer_totals AS (
  SELECT
    c.id AS customer_id,
    c.name AS customer_name,
    SUM(oi.quantity * oi.price) AS total_revenue
  FROM customers c
  JOIN orders o       ON c.id = o.customer_id
  JOIN order_items oi ON o.id = oi.order_id
  GROUP BY c.id, c.name
)
SELECT *
FROM customer_totals
WHERE total_revenue > 200;
```

</p>
</details>

## 9.2 Kombination aus mehreren CTEs (mehrstufig)

Erstellen Sie eine mehrstufige CTE-Kette:

CTE 1: `product_revenue`

- Berechnet Umsatz pro Produkt

CTE 2: `ranked_products`

- Vergibt ein Ranking über den Umsatz (höchster zuerst)

Geben Sie das Ranking der Top 3 Produkte aus.

**Erwartetes Ergebnis**

| id | name | total_revenue | revenue_rank |
| -: | ---- | ------------: | -----------: |
| 8 | Laptop | 999.00 | 1 |
| 5 | Monitor | 209.60 | 2 |
| 6 | Tastatur | 59.80 | 3 |

<details>
<summary>Show solution</summary>
<p>

**SQL-Abfrage**

```postgresql
WITH product_revenue AS (
  SELECT
    p.id,
    p.name,
    SUM(oi.quantity * oi.price) AS total_revenue
  FROM products p
  JOIN order_items oi ON p.id = oi.product_id
  GROUP BY p.id, p.name
),
ranked_products AS (
  SELECT
    id,
    name,
    total_revenue,
    RANK() OVER (ORDER BY total_revenue DESC) AS revenue_rank
  FROM product_revenue
)
SELECT *
FROM ranked_products
WHERE revenue_rank <= 3;
```

</p>
</details>

## 9.3 Komplexe Abfrage mit mehreren Joins und mehreren Filtern

Erstellen Sie eine Abfrage, die:

- für jede Bestellung:

  - Bestell-ID
  - Kunde
  - Anzahl Positionen
  - Gesamtumsatz

ausgibt

Filtern Sie anschließend nur Bestellungen, deren Gesamtumsatz > 150 € ist.

**Erwartetes Ergebnis**

| order_id | customer | position_count | total_revenue |
| -------: | -------- | -------------: | ------------: |
| 1 | Alice GmbH | 2 | 209.70 |
| 2 | Bob OHG | 2 | 1058.70 |

<details>
<summary>Show solution</summary>
<p>

**SQL-Abfrage**

```postgresql
SELECT
  o.id AS order_id,
  c.name AS customer,
  COUNT(oi.product_id) AS position_count,
  SUM(oi.quantity * oi.price) AS total_revenue
FROM orders o
JOIN customers c  ON o.customer_id = c.id
JOIN order_items oi ON o.id = oi.order_id
GROUP BY o.id, c.name
HAVING SUM(oi.quantity * oi.price) > 150;
```

</p>
</details>

## 9.4 Komplexe Abfrage mit Subquery UND CTE

Erstellen Sie eine Abfrage, die:

1. Zunächst in einem CTE alle Produkte auswählt, die über dem Durchschnittspreis ihrer Kategorie liegen
2. Danach JOINs nutzt, um deren Gesamtumsatz zu berechnen
3. Abschließend nur Produkte mit Umsatz > 200 € ausgibt

**Erwartetes Ergebnis**

| id | name | category | total_revenue |
| -: | ---- | -------- | ------------: |
| 8 | Laptop | allgemein | 999.00 |

Hinweis: Nutzen Sie Window Functions in der Subquery oder in einem CTE.

<details>
<summary>Show solution</summary>
<p>

**SQL-Abfrage**

```postgresql
WITH category_avg AS (
  SELECT
    id,
    name,
    category,
    price,
    AVG(price) OVER (PARTITION BY category) AS avg_in_category
  FROM products
),
filtered AS (
  SELECT *
  FROM category_avg
  WHERE price > avg_in_category
),
filtered_revenue AS (
  SELECT
    f.id,
    f.name,
    f.category,
    SUM(oi.quantity * oi.price) AS total_revenue
  FROM filtered f
  JOIN order_items oi ON f.id = oi.product_id
  GROUP BY f.id, f.name, f.category
)
SELECT *
FROM filtered_revenue
WHERE total_revenue > 200
ORDER BY total_revenue DESC;
```

</p>
</details>

## 9.5 Bonusaufgabe: Sehr komplexe Abfrage (kombiniert alles)

Erstellen Sie eine Abfrage, die:

1. Alle Kunden findet, deren Bestellungen
2. Produkte enthalten, die über dem Durchschnitt ihrer Kategorie liegen
3. Danach Umsatz dieser Bestellungen berechnet
4. Das Ganze nach Kunden gruppiert
5. Und am Ende nur Kunden mit Umsatz > 300 € anzeigt

Verwenden Sie:

- 2–3 CTEs
- JOINs
- Window Functions
- Aggregation
- HAVING

**Erwartetes Ergebnis**

| name | revenue |
| ---- | ------: |
| Bob OHG | 999.00 |

<details>
<summary>Show solution</summary>
<p>

**SQL-Abfrage**

```postgresql
WITH category_avg AS (
  SELECT
    id,
    name,
    category,
    price,
    AVG(price) OVER (PARTITION BY category) AS avg_cat
  FROM products
),
filtered_products AS (
  SELECT *
  FROM category_avg
  WHERE price > avg_cat
),
order_revenue AS (
  SELECT
    o.customer_id,
    SUM(oi.quantity * oi.price) AS revenue
  FROM orders o
  JOIN order_items oi ON o.id = oi.order_id
  JOIN filtered_products fp ON fp.id = oi.product_id
  GROUP BY o.customer_id
)
SELECT
  c.name,
  orv.revenue
FROM order_revenue orv
JOIN customers c ON c.id = orv.customer_id
WHERE orv.revenue > 300
ORDER BY orv.revenue DESC;
```

</p>
</details>
