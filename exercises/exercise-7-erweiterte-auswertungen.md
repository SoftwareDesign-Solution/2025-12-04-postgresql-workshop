- [7. Erweiterte Auswertungen](#7-erweiterte-auswertungen)
  - [7.1 Subqueries – einfache Unterabfragen](#71-subqueries--einfache-unterabfragen)
  - [7.2 Korrelierte Subquery](#72-korrelierte-subquery)
  - [7.3 Window Functions – laufende Nummer](#73-window-functions--laufende-nummer)
  - [7.4 Window Functions – Ranking](#74-window-functions--ranking)
  - [7.5 Window Functions – OVER PARTITION BY](#75-window-functions--over-partition-by)

Bearbeitungszeit: 60 Minuten

# 7. Erweiterte Auswertungen

In dieser Aufgabe vertiefen Sie komplexere SQL-Auswertungen mit Subqueries, Window Functions und CTEs (WITH-Klausel).

Verwenden Sie dafür die Tabellen products, customers, orders und order_items.

## 7.1 Subqueries – einfache Unterabfragen

Erstellen Sie eine Abfrage, die alle Produkte anzeigt, deren Preis über dem Durchschnittspreis aller Produkte liegt.

**Erwartetes Ergebnis**

| id | name | price | created_at | category |
| -: | ---- | ----: | ---------- | -------- |
| 4 | Laptop | 1098,90 | 2025-11-28 18:16:31.480651 | allgemein |

<details>
<summary>Show solution</summary>
<p>

**SQL-Abfrage**

```postgresql
SELECT *
FROM products
WHERE price > (SELECT AVG(price) FROM products);
```

</p>
</details>

## 7.2 Korrelierte Subquery

Ermitteln Sie für jedes Produkt die Summe aller verkauften Mengen aus der Tabelle order_items, und geben Sie aus:

- Produktname
- Gesamtverkaufte Menge

Verwenden Sie dazu eine korrelierte Subquery.

**Erwartetes Ergebnis**

| name | total_quantity |
| ---- | -------------: |
| Tastatur | 2 |
| Laptop | 1 |
| Monitor | 4 |

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

</p>
</details>

## 7.3 Window Functions – laufende Nummer

Nummerieren Sie alle Produkte nach absteigendem Preis.
Geben Sie aus:

- Laufende Nummer (ROW_NUMBER())
- Produktname
- Preis

**Erwartetes Ergebnis**

| rownum | name | price |
| -----: | ---- | ----: |
| 1	| Laptop | 1098.90 |
| 2	| Monitor | 175.89 |
| 3	| Tastatur | 39.49 |

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

**Erwartetes Ergebnis**

| name | total_revenue | revenue_rank |
| ---- | ------------: | -----------: |
| Laptop | 999.00 | 1 |
| Monitor | 209.60 | 2 |
| Tastatur | 59.80 | 3 |

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

</p>
</details>

## 7.5 Window Functions – OVER PARTITION BY

Berechnen Sie in einer Abfrage:

- Produktname
- Kategorie
- Preis
- Durchschnittspreis innerhalb der Kategorie (über Window, nicht über GROUP BY)

**Erwartetes Ergebnis**

| name | category | price | avg_in_category |
| ---- | -------- | ----: | --------------: |
| Tastatur | allgemein | 39.49 | 438.0933333333333333 |
| Laptop | allgemein | 1098.90 | 438.0933333333333333 |
| Monitor | allgemein | 175.89 | 438.0933333333333333 |

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

</p>
</details>
