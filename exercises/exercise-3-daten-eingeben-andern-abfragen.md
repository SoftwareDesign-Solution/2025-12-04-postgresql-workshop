- [3. Daten eingeben, ändern, abfragen](#3-daten-eingeben-ändern-abfragen)
  - [3.1 Daten einfügen (INSERT)](#31-daten-einfügen-insert)
  - [3.2 Daten abfragen (SELECT)](#32-daten-abfragen-select)
  - [3.3 Daten ändern (UPDATE)](#33-daten-ändern-update)
  - [3.4 Daten löschen (DELETE)](#34-daten-löschen-delete)
  - [3.5 Bonusaufgabe: Kombinierte Abfrage](#35-bonusaufgabe-kombinierte-abfrage)

Bearbeitungszeit: 75 Minuten

# 3. Daten eingeben, ändern, abfragen

In dieser Aufgabe lernen Sie, wie man Daten in Tabellen einfügt, verändert, löscht und abfragt.

Verwenden Sie dafür die zuvor erstellten Tabellen products und customers.

## 3.1 Daten einfügen (INSERT)

Fügen Sie folgende Datensätze in die Tabelle products ein:

- Monitor, Preis: 149.90
- Tastatur, Preis: 29.90
- Maus, Preis: 19.90
- Laptop, Preis: 999.00

Fügen Sie außerdem zwei Kunden in die Tabelle customers ein:

- Alice GmbH, Stadt: Berlin
- Bob OHG, Stadt: Hamburg

<details>
<summary>Show solution</summary>
<p>

```postgresql
INSERT INTO products (name, price) VALUES
('Monitor', 149.90),
('Tastatur', 35.90),
('Maus', 19.90),
('Laptop', 999.00);

INSERT INTO customers (name, city) VALUES
('Alice GmbH', 'Berlin'),
('Bob OHG', 'Hamburg'),
('Mustermann GbR', 'München')
```

</p>
</details>

## 3.2 Daten abfragen (SELECT)

Erstellen Sie die folgenden SELECT-Abfragen:

1. Alle Produkte anzeigen
    - Wählen Sie alle Spalten aus der Tabelle products.

    **Erwartetes Ergebnis**

    | id | name | price | created_at | category |
    | -- | ---- | ----: | ---------- | -------- |
    | 1 | Monitor | 149,90 | 2025-11-28 18:16:31.480651 | allgemein |
    | 2 | Tastatur | 35,90 | 2025-11-28 18:16:31.480651 | allgemein |
    | 3 | Maus | 19,90 | 2025-11-28 18:16:31.480651 | allgemein |
    | 4 | Laptop | 999,00 | 2025-11-28 18:16:31.480651 | allgemein |

2. Produkte nach Preis sortieren
    - Sortieren Sie absteigend nach Preis.

    **Erwartetes Ergebnis**

    | id | name | price | created_at | category |
    | -- | ---- | ----: | ---------- | -------- |
    | 4 | Laptop | 999,00 | 2025-11-28 18:16:31.480651 | allgemein |
    | 1 | Monitor | 149,90 | 2025-11-28 18:16:31.480651 | allgemein |
    | 2 | Tastatur | 35,90 | 2025-11-28 18:16:31.480651 | allgemein |
    | 3 | Maus | 19,90 | 2025-11-28 18:16:31.480651 | allgemein |

3. Produkte über 100 Euro filtern
    - Zeigen Sie nur Produkte mit Preis über 100 € an.

    **Erwartetes Ergebnis**

    | id | name | price | created_at | category |
    | -- | ---- | ----: | ---------- | -------- |
    | 1 | Monitor | 149,90 | 2025-11-28 18:16:31.480651 | allgemein |
    | 4 | Laptop | 999,00 | 2025-11-28 18:16:31.480651 | allgemein |

4. Bestimmte Spalten auswählen
    - Geben Sie nur name und price aus.
    - Verwenden Sie Aliase für bessere Lesbarkeit.

    **Erwartetes Ergebnis**

    | produktname | preis_eur |
    | ----------- | --------: |
    | Monitor | 149,90 |
    | Tastatur | 35,90 |
    | Maus | 19,90 |
    | Laptop | 999,00 |

5. Kunden nach Stadt filtern
    - Zeigen Sie alle Kunden aus der Stadt „Berlin“.

    **Erwartetes Ergebnis**

    | id | name | city | created_at |
    | -- | ---- | ---- | ---------- |
    | 1 | Alice GmbH | Berlin | 2025-11-28 19:07:54.266568 |

<details>
<summary>Show solution</summary>
<p>

### 1. Alle Produkte

**SQL-Abfrage**

```postgresql
SELECT * FROM products;
```

### 2. Sortiert nach Preis

**SQL-Abfrage**

```postgresql
SELECT *
FROM products
ORDER BY price DESC;
```

### 3. Produkte > 100 €

**SQL-Abfrage**

```postgresql
SELECT *
FROM products
WHERE price > 100;
```

### 4. Nur bestimmte Spalten mit Alias

**SQL-Abfrage**

```postgresql
SELECT
  name AS produktname,
  price AS preis_eur
FROM products;
```

### 5. Kunden aus Berlin

**SQL-Abfrage**

```postgresql
SELECT *
FROM customers
WHERE city = 'Berlin';
```

</p>
</details>

## 3.3 Daten ändern (UPDATE)

Ändern Sie in der Tabelle products:

1. Den Preis des Produkts Monitor auf 159.90.

    **Erwartetes Ergebnis**

    | id | name | price | created_at | category |
    | -- | ---- | ----: | ---------- | -------- |
    | 1 | Monitor | 149,90 | 2025-11-28 18:16:31.480651 | allgemein |
    | 2 | Tastatur | 35,90 | 2025-11-28 18:16:31.480651 | allgemein |
    | 3 | Maus | 159,90 | 2025-11-28 18:16:31.480651 | allgemein |
    | 4 | Laptop | 999,00 | 2025-11-28 18:16:31.480651 | allgemein |

2. Erhöhen Sie alle Preise um 10 %.

    **Erwartetes Ergebnis**

    | id | name | price | created_at | category |
    | -- | ---- | ----: | ---------- | -------- |
    | 1 | Monitor | 175,89 | 2025-11-28 18:16:31.480651 | allgemein |
    | 2 | Tastatur | 39,49 | 2025-11-28 18:16:31.480651 | allgemein |
    | 3 | Maus | 21,89 | 2025-11-28 18:16:31.480651 | allgemein |
    | 4 | Laptop | 1098,90 | 2025-11-28 18:16:31.480651 | allgemein |

Überprüfen Sie das Ergebnis mit der Abfrage aller Produkte

<details>
<summary>Show solution</summary>
<p>

**1. Einzelnes Produkt ändern**

**SQL-Abfrage**

```postgresql
UPDATE products
SET price = 159.90
WHERE name = 'Monitor';

SELECT * FROM products;
```

**2. Alle Preise um 10% erhöhen**

**SQL-Abfrage**

```postgresql
UPDATE products
SET price = price * 1.10;

SELECT * FROM products;
```

</p>
</details>

## 3.4 Daten löschen (DELETE)

Löschen Sie in der Tabelle products:

1. Das Produkt Maus.

    **Erwartetes Ergebnis**

    | id | name | price | created_at | category |
    | -- | ---- | ----: | ---------- | -------- |
    | 1 | Monitor | 175,89 | 2025-11-28 18:16:31.480651 | allgemein |
    | 2 | Tastatur | 39,49 | 2025-11-28 18:16:31.480651 | allgemein |
    | 4 | Laptop | 1098,90 | 2025-11-28 18:16:31.480651 | allgemein |

2. Alle Produkte, deren Preis unter 35 Euro liegt.

    **Erwartetes Ergebnis**

    | id | name | price | created_at | category |
    | -- | ---- | ----: | ---------- | -------- |
    | 1 | Monitor | 175,89 | 2025-11-28 18:16:31.480651 | allgemein |
    | 2 | Tastatur | 35,49 | 2025-11-28 18:16:31.480651 | allgemein |
    | 4 | Laptop | 1098,90 | 2025-11-28 18:16:31.480651 | allgemein |

Überprüfen Sie das Ergebnis mit der Abfrage aller Produkte

<details>
<summary>Show solution</summary>
<p>

**1. Einzelnes Produkt löschen**

**SQL-Abfrage**

```postgresql
DELETE FROM products
WHERE name = 'Maus';

SELECT * FROM products;
```

**2. Alle Produkte unter 30 € entfernen**

**SQL-Abfrage**

```postgresql
DELETE FROM products
WHERE price < 35;

SELECT * FROM products;
```

</p>
</details>

## 3.5 Bonusaufgabe: Kombinierte Abfrage

Erstellen Sie eine SELECT-Abfrage, die nur Produkte zeigt, deren Name mit „T“ beginnt oder deren Preis unter 50 € liegt.

Sortieren Sie das Ergebnis alphabetisch nach Produktname.

**Erwartetes Ergebnis**

| id | name | price | created_at | category |
| -- | ---- | ----: | ---------- | -------- |
| 3 | Maus | 19,90 | 2025-11-28 18:16:31.480651 | allgemein |
| 2 | Tastatur | 39,49 | 2025-11-28 18:16:31.480651 | allgemein |

<details>
<summary>Show solution</summary>
<p>

**SQL-Abfrage**

```postgresql
SELECT *
FROM products
WHERE name LIKE 'T%'
   OR price < 50
ORDER BY name;
```

</p>
</details>
