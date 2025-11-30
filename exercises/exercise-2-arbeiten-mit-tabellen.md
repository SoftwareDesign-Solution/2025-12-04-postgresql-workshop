- [2. Arbeiten mit Tabellen: Tabellen erzeugen und löschen](#2-arbeiten-mit-tabellen-tabellen-erzeugen-und-löschen)
  - [2.1 Datenbank erstellen (CREATE DATABASE)](#21-datenbank-erstellen-create-database)
  - [2.2 Tabelle erstellen (CREATE TABLE)](#22-tabelle-erstellen-create-table)
  - [2.3 Tabelle erweitern (ALTER TABLE)](#23-tabelle-erweitern-alter-table)
  - [2.4 Tabelle mit Fremdschlüssel erstellen](#24-tabelle-mit-fremdschlüssel-erstellen)
  - [2.5 Tabelle modifizieren (Spalten ändern, Defaults setzen)](#25-tabelle-modifizieren-spalten-ändern-defaults-setzen)
  - [2.6 Tabelle löschen (DROP TABLE)](#26-tabelle-löschen-drop-table)
  - [2.7 Bonus: Tabelle neu erzeugen aus Backup-Script](#27-bonus-tabelle-neu-erzeugen-aus-backup-script)

Bearbeitungszeit: 60 Minuten

# 2. Arbeiten mit Tabellen: Tabellen erzeugen und löschen

In dieser Aufgabe erstellen, verändern und löschen Sie Tabellen in PostgreSQL.
Sie lernen Primärschlüssel, Fremdschlüssel und Constraints praktisch kennen.

## 2.1 Datenbank erstellen (CREATE DATABASE)

Bevor Sie Tabellen anlegen, erstellen Sie eine neue Datenbank namens psqltraining und verbinden sich anschließend damit.

1. Erstellen Sie die Datenbank psqltraining.

2. Verbinden Sie sich anschließend mit dieser Datenbank über psql oder nutzen Sie das browserbasierte Verwaltungstool pgAdmin  .

3. Prüfen Sie, ob die Datenbank korrekt angelegt wurde.

<details>
<summary>Show solution</summary>
<p>

**SQL-Befehl**

```postgresql
CREATE DATABASE psqltraining;
```

</p>
</details>

## 2.2 Tabelle erstellen (CREATE TABLE)

Erstellen Sie die nachfolgenden Tabellen:

**Tabelle `products`**

- `id` als Primärschlüssel (automatisch incrementierend)
- `name` (Text, Pflichtfeld)
- `price` (NUMERIC(10,2), Pflichtfeld)
- `created_at` (TIMESTAMP, Default: `NOW()`)

**Tabelle `customers`**

- `id` als Primärschlüssel (automatisch incrementierend)
- `name` (TEXT, Pflichtfeld)
- `city` (TEXT, optional)
- `created_at` (TIMESTAMP, Default: `NOW()`)

<details>
<summary>Show solution</summary>
<p>

**Tabelle: products**

```postgresql
CREATE TABLE products (
  id SERIAL PRIMARY KEY,
  name TEXT NOT NULL,
  price NUMERIC(10,2) NOT NULL,
  created_at TIMESTAMP NOT NULL DEFAULT NOW()
);
```

**Tabelle: customers**

```postgresql
CREATE TABLE customers (
  id SERIAL PRIMARY KEY,
  name TEXT NOT NULL,
  city TEXT,
  created_at TIMESTAMP NOT NULL DEFAULT NOW()
);
```

</p>
</details>

## 2.3 Tabelle erweitern (ALTER TABLE)

Erweitern Sie die Tabelle products um eine neue Spalte:

- category (TEXT, optional)

<details>
<summary>Show solution</summary>
<p>

**/**

```postgresql
ALTER TABLE products
ADD COLUMN category TEXT;
```

</p>
</details>

## 2.4 Tabelle mit Fremdschlüssel erstellen

Erstellen Sie eine dritte Tabelle orders, die einen Fremdschlüssel auf die Tabelle customers beinhaltet.

Tabellenstruktur:

- `id` SERIAL PRIMARY KEY
- `customer_id` → Fremdschlüssel auf customers(id)
- `order_date` → DATE, Default: aktuelles Datum

<details>
<summary>Show solution</summary>
<p>

**Tabelle: orders**

```postgresql
CREATE TABLE orders (
  id SERIAL PRIMARY KEY,
  customer_id INT NOT NULL REFERENCES customers(id),
  order_date DATE NOT NULL DEFAULT CURRENT_DATE
);
```

</p>
</details>

## 2.5 Tabelle modifizieren (Spalten ändern, Defaults setzen)

Ändern Sie in der Tabelle products:

1. den Datentyp der Spalte `price` auf NUMERIC(12,2)
2. setzen Sie für `category` einen Default-Wert 'allgemein'

<details>
<summary>Show solution</summary>
<p>

**Tabelle: products**

```postgresql
ALTER TABLE products
ALTER COLUMN price TYPE NUMERIC(12,2);

ALTER TABLE products
ALTER COLUMN category SET DEFAULT 'allgemein';
```

</p>
</details>

## 2.6 Tabelle löschen (DROP TABLE)

Löschen Sie die Tabelle orders vollständig aus der Datenbank.

Achtung: Fremdschlüssel könnten ein Löschen verhindern.

<details>
<summary>Show solution</summary>
<p>

**Normales Löschen:**

```postgresql
DROP TABLE orders;
```

**Falls Constraints das Löschen blockieren:**

```postgresql
DROP TABLE orders CASCADE;
```

</p>
</details>

## 2.7 Bonus: Tabelle neu erzeugen aus Backup-Script

Rekonstruieren Sie die Tabelle orders erneut, indem Sie ein eigenes CREATE TABLE-Script schreiben.

Stellen Sie sicher, dass sowohl Primär- als auch Fremdschlüssel korrekt definiert sind.

<details>
<summary>Show solution</summary>
<p>

**Tabelle: orders**

```postgresql
CREATE TABLE orders (
  id SERIAL PRIMARY KEY,
  customer_id INT NOT NULL REFERENCES customers(id),
  order_date DATE NOT NULL DEFAULT CURRENT_DATE
);
```

</p>
</details>
