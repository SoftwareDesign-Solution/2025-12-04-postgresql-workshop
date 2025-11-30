- [1. Installation von PostgreSQL](#1-installation-von-postgresql)
  - [1.1 PostgreSQL lokal installieren (Standalone)](#11-postgresql-lokal-installieren-standalone)
  - [1.2 PostgreSQL und pgAdmin via Docker installieren (docker-compose)](#12-postgresql-und-pgadmin-via-docker-installieren-docker-compose)
  - [1.3 pgAdmin lokal installieren (Alternative zur Docker-Variante)](#13-pgadmin-lokal-installieren-alternative-zur-docker-variante)

Bearbeitungszeit: 30 Minuten

# 1. Installation von PostgreSQL

In dieser Aufgabe installieren Sie PostgreSQL auf zwei möglichen Wegen:

1. Standalone-Installation (Windows, Linux, macOS)
2. Docker-Installation mittels der bereitgestellten Datei
`psqltraining-docker.yml`, die PostgreSQL und pgAdmin gemeinsam startet.

Anschließend installieren Sie pgAdmin (entweder lokal oder im Docker-Container).

Ziel ist es, dass Sie am Ende eine funktionierende PostgreSQL-Instanz besitzen und sich erfolgreich damit verbinden können.

## 1.1 PostgreSQL lokal installieren (Standalone)

Installieren Sie PostgreSQL auf Ihrem Betriebssystem.
Prüfen Sie anschließend, ob der Dienst läuft und ob Sie sich erfolgreich verbinden können.

### Installation unter Windows (Beispiel)

1. Installer von <https://www.postgresql.org/download/> herunterladen

2. Standardinstallation durchführen

3. Prüfen, ob der PostgreSQL-Dienst läuft

    ```bash
    services.msc
    ```

4. Mit psql verbinden:

    ```bash
    psql -U postgres
    ```

### Installation unter Linux

```bash
sudo apt update
sudo apt install postgresql postgresql-contrib
sudo systemctl status postgresql
sudo -u postgres psql
```

### Installation unter macOS (Homebrew)

```bash
brew install postgresql
brew services start postgresql
psql postgres
```

## 1.2 PostgreSQL und pgAdmin via Docker installieren (docker-compose)

Mit der Datei psqltraining-docker.yml können Sie PostgreSQL und pgAdmin gemeinsam starten.
Diese Datei ist im Schulungsrepository enthalten.

1. docker-compose starten
2. prüfen, ob beide Container laufen
3. pgAdmin im Browser <https://localhost:5050/> öffnen
4. Verbindung zum PostgreSQL-Datenbankserver einrichten

### Inhalt der Datei `psqltraining-docker.yml`

```yaml
services:
  postgres:
    image: postgres:latest
    container_name: psqltraining
    environment:
      POSTGRES_PASSWORD: postgres
      POSTGRES_USER: postgres
      POSTGRES_DB: postgres
    ports:
      - "5432:5432"
    networks:
      - pgnet
    restart: unless-stopped

  pgadmin:
    image: dpage/pgadmin4
    container_name: pgadmin
    environment:
      PGADMIN_DEFAULT_EMAIL: admin@example.com
      PGADMIN_DEFAULT_PASSWORD: admin
    ports:
      - "5050:80"
    networks:
      - pgnet
    restart: unless-stopped

networks:
  pgnet:
    driver: bridge
```

### 1. docker-compose starten

```bash
docker compose -f psqltraining-docker.yml up -d
```

### 2. Containerstatus prüfen

```bash
docker ps
```

Es sollten laufen:

- postgres
- dpage/pgadmin4

### 3. pgAdmin aufrufen

Im Browser:

```bash
https://localhost:5050/
```

### 4. Login-Daten

- E-Mail: admin@example.com
- Passwort: admin

### 5. Neuen Server in pgAdmin registrieren

**Register → New Server**

**General**

- Name: psqltraining

**Connection**

- Host: postgres
  
  (Service-Name aus der docker-compose-Datei)
- Port: 5432

- Username: postgres

- Password: postgres

- Database: psqltraining

Speichern → Verbindung erfolgreich

## 1.3 pgAdmin lokal installieren (Alternative zur Docker-Variante)

Falls Sie pgAdmin nicht über Docker nutzen möchten, können Sie es auch lokal installieren.

1. Installer von <https://www.pgadmin.org/download/> herunterladen
2. pgAdmin installieren
3. Verbindung zum PostgreSQL-Server anlegen

**pgAdmin starten → Register → New Server**

**General**

- Name: psqltraining

**Connection**

- Host: localhost

- Port: 5432

- Username: postgres

- Password: postgres
