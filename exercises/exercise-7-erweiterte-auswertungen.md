- [7. Erweiterte Auswertungen](#7-erweiterte-auswertungen)
  - [7.1 Subqueries – einfache Unterabfragen](#71-subqueries--einfache-unterabfragen)
  - [7.2 Korrelierte Subquery](#72-korrelierte-subquery)
  - [7.3 Window Functions – laufende Nummer](#73-window-functions--laufende-nummer)
  - [7.4 Window Functions – Ranking](#74-window-functions--ranking)
  - [7.5 Window Functions – OVER PARTITION BY](#75-window-functions--over-partition-by)
  - [7.6 CTE – Umsätze pro Produkt](#76-cte--umsätze-pro-produkt)
  - [7.7 Bonusaufgabe: Mehrstufige CTE-Kette](#77-bonusaufgabe-mehrstufige-cte-kette)
  - [7.8 Bonusaufgabe: Kombination aus Subquery + Window + CTE](#78-bonusaufgabe-kombination-aus-subquery--window--cte)

Bearbeitungszeit: 90 Minuten

# 7. Erweiterte Auswertungen

In dieser Aufgabe vertiefen Sie komplexere SQL-Auswertungen mit Subqueries, Window Functions und CTEs (WITH-Klausel).

Verwenden Sie dafür die Tabellen products, customers, orders und order_items.

## 7.1 Subqueries – einfache Unterabfragen

Erstellen Sie eine Abfrage, die alle Produkte anzeigt, deren Preis über dem Durchschnittspreis aller Produkte liegt.

<details>
<summary>Show solution</summary>
<p>

**SQL-Abfrage**

```postgresql
SELECT *
FROM products
WHERE price > (SELECT AVG(price) FROM products);
```

**Erwartetes Ergebnis**

| id | name | price | created_at | category |
| -: | ---- | ----: | ---------- | -------- |
| 4 | Laptop | 1098,90 | 2025-11-28 18:16:31.480651 | allgemein |

</p>
</details>

## 7.2 Korrelierte Subquery

Ermitteln Sie für jedes Produkt die Summe aller verkauften Mengen aus der Tabelle order_items, und geben Sie aus:

- Produktname
- Gesamtverkaufte Menge

Verwenden Sie dazu eine korrelierte Subquery.

<details>
<summary>Show solution</summary>
<p>

**SQL-Abfrage**

```postgresql
SELECT
  p.name,
  (SELECT COALESCE(SUM(oi.quantity), 0)
   FROM order_items oi
   WHERE oi.product_id = p.id) AS total_quantity
FROM products p;
```

**Erwartetes Ergebnis**

| name | total_quantity |
| ---- | -------------: |
| Tastatur | 2 |
| Laptop | 1 |
| Monitor | 4 |

</p>
</details>

## 7.3 Window Functions – laufende Nummer

Nummerieren Sie alle Produkte nach absteigendem Preis.
Geben Sie aus:

- Laufende Nummer (ROW_NUMBER())
- Produktname
- Preis

<details>
<summary>Show solution</summary>
<p>

**SQL-Abfrage**

```postgresql
SELECT
  ROW_NUMBER() OVER (ORDER BY price DESC) AS rownum,
  name,
  price
FROM products;
```

**Erwartetes Ergebnis**

| rownum | name | price |
| -----: | ---- | ----: |
| 1	| Laptop | 1098.90 |
| 2	| Monitor | 175.89 |
| 3	| Tastatur | 39.49 |

</p>
</details>

## 7.4 Window Functions – Ranking

Erstellen Sie ein Ranking der umsatzstärksten Produkte.
Verwenden Sie:

- JOIN zwischen products und order_items
- SUM(quantity * price)
- RANK() OVER (ORDER BY total_revenue DESC)

Geben Sie aus:

- Produktname
- Gesamtumsatz
- Rang

<details>
<summary>Show solution</summary>
<p>

**SQL-Abfrage**

```postgresql
SELECT
  p.name,
  SUM(oi.quantity * oi.price) AS total_revenue,
  RANK() OVER (ORDER BY SUM(oi.quantity * oi.price) DESC) AS revenue_rank
FROM products p
JOIN order_items oi ON p.id = oi.product_id
GROUP BY p.name;
```

**Erwartetes Ergebnis**

| name | total_revenue | revenue_rank |
| ---- | ------------: | -----------: |
| Laptop | 999.00 | 1 |
| Monitor | 209.60 | 2 |
| Tastatur | 59.80 | 3 |

</p>
</details>

## 7.5 Window Functions – OVER PARTITION BY

Berechnen Sie in einer Abfrage:

- Produktname
- Kategorie
- Preis
- Durchschnittspreis innerhalb der Kategorie (über Window, nicht über GROUP BY)

Hinweis: Verwenden Sie `AVG(price) OVER (PARTITION BY category)`.

<details>
<summary>Show solution</summary>
<p>

**SQL-Abfrage**

```postgresql
SELECT
  name,
  category,
  price,
  AVG(price) OVER (PARTITION BY category) AS avg_in_category
FROM products;
```

**Erwartetes Ergebnis**

| name | category | price | avg_in_category |
| ---- | -------- | ----: | --------------: |
| Tastatur | allgemein | 39.49 | 438.0933333333333333 |
| Laptop | allgemein | 1098.90 | 438.0933333333333333 |
| Monitor | allgemein | 175.89 | 438.0933333333333333 |

</p>
</details>

## 7.6 CTE – Umsätze pro Produkt

Erstellen Sie einen CTE, der für jedes Produkt den Gesamtumsatz berechnet:

- CTE-Name: `product_totals`
- Gesamter Umsatz: `SUM(quantity * price)`

Geben Sie danach eine Abfrage aus, die nur Produkte mit einem Umsatz > 200 anzeigt.

<details>
<summary>Show solution</summary>
<p>

**SQL-Abfrage**

```postgresql
WITH product_totals AS (
  SELECT
    p.id,
    p.name,
    SUM(oi.quantity * oi.price) AS total_revenue
  FROM products p
  JOIN order_items oi ON p.id = oi.product_id
  GROUP BY p.id, p.name
)
SELECT *
FROM product_totals
WHERE total_revenue > 200;
```

**Erwartetes Ergebnis**

| id | name | total_revenue |
| -: | ---- | ------------: |
| 1 | Monitor | 209.60 |
| 4 | Laptop | 999.00 |

</p>
</details>

## 7.7 Bonusaufgabe: Mehrstufige CTE-Kette

Erstellen Sie eine CTE-Kette aus zwei CTEs:

CTE 1: customer_totals

- Berechnet den Gesamtumsatz pro Kunde
- Name, Customer-ID, Umsatz

CTE 2: ranked_customers

- Vergibt ein Ranking über den Umsatz
- Sortierung: Höchster Umsatz zuerst

Geben Sie danach die Top 3 Kunden aus.

<details>
<summary>Show solution</summary>
<p>

**SQL-Abfrage**

```postgresql
WITH customer_totals AS (
  SELECT
    c.id AS customer_id,
    c.name,
    SUM(oi.quantity * oi.price) AS total_revenue
  FROM customers c
  JOIN orders o ON c.id = o.customer_id
  JOIN order_items oi ON o.id = oi.order_id
  GROUP BY c.id, c.name
),
ranked_customers AS (
  SELECT
    customer_id,
    name,
    total_revenue,
    RANK() OVER (ORDER BY total_revenue DESC) AS revenue_rank
  FROM customer_totals
)
SELECT *
FROM ranked_customers
WHERE revenue_rank <= 3;
```

**Erwartetes Ergebnis**

| customer_id | name | total_revenue | revenue_rank |
| ----------: | ---- | ------------: | -----------: |
| 2 | Bob OHG | 1058.70 | 1 |
| 1 | Alice GmbH | 209.70 | 2 |

</p>
</details>

## 7.8 Bonusaufgabe: Kombination aus Subquery + Window + CTE

Erstellen Sie eine vollständige Analyse:

1. Finden Sie nur Produkte, die teurer sind als der Durchschnitt ihrer Kategorie (Subquery oder Window).
2. Berechnen Sie in einem CTE den Gesamtumsatz dieser Produkte.
3. Erstellen Sie eine Window-Function, die diese Produkte nach Umsatz rankt.
4. Geben Sie die Top 5 aus.

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
revenue_calc AS (
  SELECT
    f.id,
    f.name,
    f.category,
    SUM(oi.quantity * oi.price) AS total_revenue
  FROM filtered f
  JOIN order_items oi ON f.id = oi.product_id
  GROUP BY f.id, f.name, f.category
),
ranked AS (
  SELECT
    *,
    RANK() OVER (ORDER BY total_revenue DESC) AS revenue_rank
  FROM revenue_calc
)
SELECT *
FROM ranked
WHERE revenue_rank <= 5;
```

**Erwartetes Ergebnis**

| id | name | category | total_revenue | revenue_rank |
| -: | ---- | -------- | ------------: | -----------: |
| 4 | Laptop | allgemein | 999.00 | 1 |

</p>
</details>
