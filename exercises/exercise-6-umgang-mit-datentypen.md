- [6. Umgang mit Datentypen](#6-umgang-mit-datentypen)
  - [6.1 Wichtige Datentypen verwenden](#61-wichtige-datentypen-verwenden)
  - [6.2 Typkonvertierung (CAST)](#62-typkonvertierung-cast)
  - [6.3 JSONB-Daten speichern](#63-jsonb-daten-speichern)
  - [6.4 JSONB: Werte auslesen](#64-jsonb-werte-auslesen)
  - [6.5 Arrays verwenden (optionale PostgreSQL-Spezialität)](#65-arrays-verwenden-optionale-postgresql-spezialität)

Bearbeitungszeit: 60 Minuten

# 6. Umgang mit Datentypen

In dieser Aufgabe lernen Sie die wichtigsten Datentypen von PostgreSQL kennen und wenden Typkonvertierungen (CAST), JSONB und spezielle PostgreSQL-Datentypen praktisch an.

## 6.1 Wichtige Datentypen verwenden

Erstellen Sie eine neue Tabelle data_types_demo mit folgenden Spalten:

- `id` → SERIAL PRIMARY KEY
- `title` → TEXT, Pflichtfeld
- `amount` → NUMERIC(10,2), optional
- `quantity` → INT, optional
- `active` → BOOLEAN, Default: TRUE
- `created_at` → TIMESTAMP, Default: NOW()

Fügen Sie anschließend mindestens zwei Datensätze ein.

<details>
<summary>Show solution</summary>
<p>

**SQL-Abfrage**

```postgresql
CREATE TABLE data_types_demo (
  id SERIAL PRIMARY KEY,
  title TEXT NOT NULL,
  amount NUMERIC(10,2),
  quantity INT,
  active BOOLEAN DEFAULT TRUE,
  created_at TIMESTAMP DEFAULT NOW()
);

INSERT INTO data_types_demo (title, amount, quantity)
VALUES 
  ('Beispiel A', 199.99, 5),
  ('Beispiel B', 49.50, 10);
```

</p>
</details>

## 6.2 Typkonvertierung (CAST)

Führen Sie folgende Typkonvertierungen durch:

1. Konvertieren Sie eine Textzahl '42' in einen Integer.

    **Erwartetes Ergebnis**

    | int4 |
    | ---: |
    | 42 |

2. Konvertieren Sie das Datum '2025-01-01' in einen TIMESTAMP.

    **Erwartetes Ergebnis**

    | timestamp |
    | --------- |
    | 2025-01-01 00:00:00 |

3. Konvertieren Sie die Zahl 123.45 in TEXT.

    **Erwartetes Ergebnis**

    | text |
    | ---- |
    | 123.45 |

<details>
<summary>Show solution</summary>
<p>

### 1. Textzahl '42' in einen Integer

**SQL-Befehl**

```postgresql
SELECT '42'::INT;
```

Bild

### 2. Datum '2025-01-01' in einen TIMESTAMP

**SQL-Befehl**

```postgresql
SELECT '2025-01-01'::TIMESTAMP;
```

### 3. Zahl 123.45 in TEXT

**SQL-Befehl**

```postgresql
SELECT 123.45::TEXT;
```

</p>
</details>

## 6.3 JSONB-Daten speichern

Erstellen Sie eine Tabelle product_attributes mit folgenden Spalten:

- `id` SERIAL PRIMARY KEY
- `product_id` INT, Pflichtfeld
- `attributes` JSONB, Pflichtfeld

Fügen Sie anschließend zwei Datensätze ein:

- Für einen Monitor: Auflösung, Größe in Zoll
- Für eine Tastatur: Layout, wired = true/false

<details>
<summary>Show solution</summary>
<p>

**Tabelle erstellen**

```postgresql
CREATE TABLE product_attributes (
  id SERIAL PRIMARY KEY,
  product_id INT NOT NULL REFERENCES products(id),
  attributes JSONB NOT NULL
);
```

**Beispieldaten einfügen**

```postgresql
INSERT INTO product_attributes (product_id, attributes)
VALUES 
  (1, '{"resolution": "1920x1080", "size_inch": 24}'),
  (2, '{"layout": "DE", "wired": true}');
```

</p>
</details>

## 6.4 JSONB: Werte auslesen

Lesen Sie aus der Tabelle product_attributes:

1. Die resolution aller Produkte aus.

    **Erwartetes Ergebnis**

    | product_id | resolution |
    | ---------: | ---------- |
    | 1 | 1920x1080 |
    | 2 | [null] |

2. Alle Attribute als formatierten JSON-Text.

    **Erwartetes Ergebnis**

    | product_id | json_text |
    | ---------: | --------- |
    | 5	| {"size_inch": 24, "resolution": "1920x1080"} |
    | 6	| {"wired": true, "layout": "DE"} |

3. Nur jene Zeilen, bei denen das Attribut "wired": true gesetzt ist.

    **Erwartetes Ergebnis**

    | id | product_id | attributes |
    | -: | ---------: | ---------- |
    | 2 | 2 | {"wired": true, "layout": "DE"} |

<details>
<summary>Show solution</summary>
<p>

### 1. Einzelnes JSON-Feld auslesen

**SQL-Abfrage**

```postgresql
SELECT 
  product_id, 
  attributes->>'resolution' AS resolution
FROM product_attributes;
```

### 2. JSON formatiert anzeigen

**SQL-Abfrage**

```postgresql
SELECT
  product_id,
  attributes::TEXT AS json_text
FROM product_attributes;
```

### 3. Alle Produkte, bei denen wired = true

**SQL-Abfrage**

```postgresql
SELECT *
FROM product_attributes
WHERE attributes->>'wired' = 'true';
```

</p>
</details>

## 6.5 Arrays verwenden (optionale PostgreSQL-Spezialität)

Erstellen Sie eine Tabelle tags_demo:

- id SERIAL PRIMARY KEY
- tags TEXT[] (Array aus Textwerten)

Fügen Sie zwei Datensätze ein:

- '{"red", "blue", "green"}'
- '{"small", "medium"}''

Selektieren Sie anschließend alle Zeilen, bei denen das Array das Element 'blue' enthält.

**Erwartetes Ergebnis**

| id | tags |
| -: | ---- |
| 1 | {red,blue,green} |

<details>
<summary>Show solution</summary>
<p>

**Tabelle erstellen**

```postgresql
CREATE TABLE tags_demo (
  id SERIAL PRIMARY KEY,
  tags TEXT[]
);
```

**Daten einfügen**

```postgresql
INSERT INTO tags_demo (tags)
VALUES
  ('{red,blue,green}'),
  ('{small,medium}');
```

**Nach Element filtern**

```postgresql
SELECT *
FROM tags_demo
WHERE 'blue' = ANY(tags);
```

</p>
</details>

## 6.5 Bonusaufgabe: Benutzerdefinierter Datentyp

Erstellen Sie einen benutzerdefinierten PostgreSQL-Datentyp address_type mit den Feldern:

- street TEXT
- zipcode TEXT
- city TEXT

Erstellen Sie anschließend eine Tabelle customer_addresses mit:

- id SERIAL PRIMARY KEY
- customer_id INT NOT NULL REFERENCES customers(id)
- address des neuen Typs address_type

Fügen Sie mindestens einen Datensatz ein.

Überprüfen Sie das Ergebnis, in dem Sie den Kunden inkl. Adresse in getrennten Spalten ausgeben

**Erwartetes Ergebnis**

| id | name | street | zipcode | city |
| -: | ---- | ------ | ------- | ---- |
| 1 | Alice GmbH | Musterstraße 1 | 10115 | Berlin |

<details>
<summary>Show solution</summary>
<p>

**Custom-Type definieren**

```postgresql
CREATE TYPE address_type AS (
  street TEXT,
  zipcode TEXT,
  city TEXT
);
```

**Tabelle erstellen**

```postgresql
CREATE TABLE customer_addresses (
  id SERIAL PRIMARY KEY,
  customer_id INT NOT NULL REFERENCES customers(id),
  address address_type
);
```

**Datensatz einfügen**

```postgresql
INSERT INTO customer_addresses (customer_id, address)
VALUES
  (1, ('Musterstraße 1', '10115', 'Berlin'));
```

**SQL-Abfrage**

```postgresql
SELECT
  c.id,
  c.name,
  (ca.address).street, (ca.address).zipcode, (ca.address).city
 FROM customers c 
 JOIN customer_addresses ca
   ON c.id = ca.customer_id
```

</p>
</details>
