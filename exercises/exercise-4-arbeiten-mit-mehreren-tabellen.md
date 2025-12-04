- [4. Arbeiten mit mehreren Tabellen: Tabellen verbinden (JOINs)](#4-arbeiten-mit-mehreren-tabellen-tabellen-verbinden-joins)
  - [4.1 Tabellen anlegen und befüllen](#41-tabellen-anlegen-und-befüllen)
  - [4.2 Kunden und ihre Bestellungen (INNER JOIN)](#42-kunden-und-ihre-bestellungen-inner-join)
  - [4.3 Alle Bestellpositionen inkl. Produktname (JOINS)](#43-alle-bestellpositionen-inkl-produktname-joins)
  - [4.4 Kunden ohne Bestellungen (LEFT JOIN)](#44-kunden-ohne-bestellungen-left-join)

Bearbeitungszeit: 45 Minuten

# 4. Arbeiten mit mehreren Tabellen: Tabellen verbinden (JOINs)

In dieser Aufgabe arbeiten Sie mit mehreren Tabellen gleichzeitig.

Sie erstellen JOIN-Abfragen zwischen customers, orders, products und order_items.

## 4.1 Tabellen anlegen und befüllen

Damit die folgenden JOIN-Aufgaben sinnvoll durchgeführt werden können, legen Sie zunächst Beispieldaten in den Tabellen orders und order_items an.

1. Legen Sie für zwei Kunden je eine Bestellung an.
2. Fügen Sie zu jeder Bestellung mindestens zwei Positionen in der Tabelle order_items ein.
3. Verwenden Sie dabei bestehende Produkte aus der products-Tabelle.

**Beispielwerte (können leicht variieren):**

- Bestellung 1: Kunde Alice GmbH
- Bestellung 2: Kunde Bob OHG
- Produkte: Monitor, Tastatur, Maus, Laptop

Falls die Tabellen orders und order_items noch nicht existieren, können Sie diese wie folgt anlegen:

```postgresql
CREATE TABLE orders (
  id SERIAL PRIMARY KEY,
  customer_id INT NOT NULL REFERENCES customers(id),
  order_date DATE NOT NULL DEFAULT CURRENT_DATE
);

CREATE TABLE order_items (
  order_id INT NOT NULL REFERENCES orders(id),
  product_id INT NOT NULL REFERENCES products(id),
  quantity INT NOT NULL CHECK (quantity > 0),
  price NUMERIC(10,2) NOT NULL,
  PRIMARY KEY (order_id, product_id)
);
```

<details>
<summary>Show solution</summary>
<p>

### 1. Bestellungen anlegen

```postgresql
INSERT INTO orders (customer_id, order_date) VALUES
(1, '2025-01-10'),
(2, '2025-01-20');
```

Bild

### 2. Bestellpositionen anlegen

> Wir gehen davon aus, dass die Produkte folgende IDs haben:
Monitor = 1, Tastatur = 2, Maus = 3, Laptop = 4

```postgresql
INSERT INTO order_items (order_id, product_id, quantity, price) VALUES
-- Bestellung 1 (Alice)
(1, 1, 1, 149.90),   -- Monitor
(1, 2, 2, 29.90),    -- Tastatur

-- Bestellung 2 (Bob)
(2, 1, 3, 19.90),    -- Monitor
(2, 4, 1, 999.00);   -- Laptop
```

Bild

</p>
</details>

## 4.2 Kunden und ihre Bestellungen (INNER JOIN)

Erstellen Sie eine Abfrage, die folgende Informationen ausgibt:

- Bestell-ID
- Bestelldatum
- Name des Kunden

Verwenden Sie dafür einen INNER JOIN zwischen orders und customers.

**Erwartetes Ergebnis**

| order_id | order_date | customer |
| -------- | ---------- | -------- |
| 1 | 2025-01-10 | Alice GmbH |
| 2 | 2025-01-10 | Bob OHG |

<details>
<summary>Show solution</summary>
<p>

**SQL-Abfrage**

```postgresql
SELECT
  o.id AS order_id,
  o.order_date,
  c.name AS customer
FROM orders o
INNER JOIN customers c
  ON o.customer_id = c.id;
```

</p>
</details>

## 4.3 Alle Bestellpositionen inkl. Produktname (JOINS)

Erstellen Sie eine Abfrage, die die Positionen jeder Bestellung anzeigt:

- Bestell-ID
- Produktname
- Menge (`quantity`)
- Einzelpreis (`price`)

Gesamtpreis pro Position (Menge × Preis)

Binden Sie dazu die Tabellen orders, order_items und products mit JOINs ein.

**Erwartetes Ergebnis**

| order_id | product | quantity | price | total_position |
| -------- | ------- | -------: | ----: | -------------: |
| 1	| Monitor |	1 |	149.90 |	149.90 |
| 1	| Tastatur |	2 |	29.90 |	59.80 |
| 2	| Laptop |	1 |	999.00	| 999.00 |

<details>
<summary>Show solution</summary>
<p>

**SQL-Abfrage**

```postgresql
SELECT
  o.id AS order_id,
  p.name AS product,
  oi.quantity,
  oi.price,
  oi.quantity * oi.price AS total_position
FROM orders o
JOIN order_items oi ON o.id = oi.order_id
JOIN products p     ON oi.product_id = p.id;
```

</p>
</details>

## 4.4 Kunden ohne Bestellungen (LEFT JOIN)

Erstellen Sie eine Abfrage, die alle Kunden anzeigt – auch solche ohne Bestellung.

Geben Sie aus:

- Kundenname
- Anzahl der Bestellungen

Hinweis: Verwenden Sie LEFT JOIN und COUNT um die Anzahl der Bestellungen zu ermitteln.

**Erwartetes Ergebnis**

| id | name | order_count |
| -- | ---- | ----------: |
| 1 | Alice GmbH | 1 |
| 2 | Bob OHG | 1 |

<details>
<summary>Show solution</summary>
<p>

**SQL-Abfrage**

```postgresql
SELECT
  c.id,
  c.name,
  COUNT(o.id) AS order_count
FROM customers c
LEFT JOIN orders o
  ON c.id = o.customer_id
GROUP BY c.id, c.name
ORDER BY c.name;
```

</p>
</details>
