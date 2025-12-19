- [8. Erweiterte Auswertungen mit Common Table Expressions](#8-erweiterte-auswertungen-mit-common-table-expressions)
  - [8.1 CTE – Umsätze pro Produkt](#81-cte--umsätze-pro-produkt)
  - [8.2 Mehrstufige CTE-Kette](#82-mehrstufige-cte-kette)
  - [8.3 Kombination aus Subquery + Window + CTE](#83-kombination-aus-subquery--window--cte)

Bearbeitungszeit: 60 Minuten

# 8. Erweiterte Auswertungen mit Common Table Expressions

In dieser Aufgabe vertiefen Sie komplexere SQL-Auswertungen mit CTEs (WITH-Klausel).

Verwenden Sie dafür die Tabellen products, customers, orders und order_items.

## 8.1 CTE – Umsätze pro Produkt

Erstellen Sie einen CTE, der für jedes Produkt den Gesamtumsatz berechnet:

- CTE-Name: `product_totals`
- Gesamter Umsatz: `SUM(quantity * price)`

Geben Sie danach eine Abfrage aus, die nur Produkte mit einem Umsatz > 200 anzeigt.

**Erwartetes Ergebnis**

| id | name | total_revenue |
| -: | ---- | ------------: |
| 1 | Monitor | 209.60 |
| 4 | Laptop | 999.00 |

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

</p>
</details>

## 8.2 Mehrstufige CTE-Kette

Erstellen Sie eine CTE-Kette aus zwei CTEs:

CTE 1: customer_totals

- Berechnet den Gesamtumsatz pro Kunde
- Name, Customer-ID, Umsatz

CTE 2: ranked_customers

- Vergibt ein Ranking über den Umsatz
- Sortierung: Höchster Umsatz zuerst

Geben Sie danach die Top 3 Kunden aus.

**Erwartetes Ergebnis**

| customer_id | name | total_revenue | revenue_rank |
| ----------: | ---- | ------------: | -----------: |
| 2 | Bob OHG | 1058.70 | 1 |
| 1 | Alice GmbH | 209.70 | 2 |

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

</p>
</details>

## 8.3 Kombination aus Subquery + Window + CTE

Erstellen Sie eine vollständige Analyse:

1. Finden Sie nur Produkte, die teurer sind als der Durchschnitt ihrer Kategorie (Subquery oder Window).
2. Berechnen Sie in einem CTE den Gesamtumsatz dieser Produkte.
3. Erstellen Sie eine Window-Function, die diese Produkte nach Umsatz rankt.
4. Geben Sie die Top 5 aus.

**Erwartetes Ergebnis**

| id | name | category | total_revenue | revenue_rank |
| -: | ---- | -------- | ------------: | -----------: |
| 4 | Laptop | allgemein | 999.00 | 1 |

<details>
<summary>Show solution</summary>
<p>

**SQL-Abfrage**

```postgresql
-- 1. Schritt: Abfrage "Durchschnittspreis der Kategorien"
SELECT
  id,
  name,
  category,
  price,
  AVG(price) OVER (PARTITION BY category) AS avg_in_category
FROM products

-- 2. Schritt: "Durchschnittspreis der Kategorien" in CTE-Abfrage
WITH category_avg AS (
  SELECT
    id,
    name,
    category,
    price,
    AVG(price) OVER (PARTITION BY category) AS avg_in_category
  FROM products
),

-- 3. Schritt: Filtern nach Produkten, deren Preis größer als der Durchschnittspreis ist
filtered AS (
  SELECT *
  FROM category_avg
  WHERE price > avg_in_category
),

-- 4. Schritt: Gesamtumsatz der Produkte aus Schritt 3 ermitteln
revenue_calc AS (
  SELECT
    f.id,
    f.name,
    f.category,
    SUM(oi.quantity * oi.price) AS total_revenue
  FROM filtered f -- Subquery
  JOIN order_items oi ON f.id = oi.product_id
  GROUP BY f.id, f.name, f.category
),

-- 5. Schritt: Ranking über das Ergebnis aus Schritt 4
ranked AS (
  SELECT
    *,
    RANK() OVER (ORDER BY total_revenue DESC) AS revenue_rank
  FROM revenue_calc
)

-- 6. Schritt: Ausgabe der Produkte, wo das Ranking kleiner/gleich 5 ist
SELECT *
FROM ranked
WHERE revenue_rank <= 5;

-- Gesamtlösung
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

</p>
</details>
