- [1. Installation von PostgreSQL](#1-installation-von-postgresql)
  - [1.1 Installation von PostgreSQL](#11-installation-von-postgresql)
  - [1.2 Installation mit Docker](#12-installation-mit-docker)
  - [1.3 Installation pgAdmin](#13-installation-pgadmin)

Bearbeitungszeit: 30 Minuten

# 1. Installation von PostgreSQL

In dieser Aufgabe installieren Sie PostgreSQL in drei unterschiedlichen Varianten, um verschiedene Szenarien kennenzulernen.
Ziel ist es, dass Sie am Ende eine funktionierende PostgreSQL-Instanz besitzen und sich erfolgreich damit verbinden können.

## 1.1 Installation von PostgreSQL

Installieren Sie den PostgreSQL-Server lokal auf Ihrem Betriebssystem (Windows, Linux oder macOS).
Nach der Installation sollen Sie überprüfen, ob der Dienst läuft und ob Sie sich erfolgreich über die Kommandozeile verbinden können.

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

<details>
<summary>Show solution</summary>
<p>

**/**

```
```

</p>
</details>

## 1.2 Installation mit Docker

Installieren und starten Sie PostgreSQL über Docker.
Ziel ist es, dass Sie einen Container starten und per psql darauf zugreifen.

<details>
<summary>Show solution</summary>
<p>

**Docker-Installation der PostgreSQL-Datenbank:**

Container starten (Standard-Port 5432):

```bash
docker run --name psqlworkshop \
 -e POSTGRES_PASSWORD=postgres \
 -p 5432:5432 \
 -d postgres:17
```

Status prüfen:

```bash
docker ps
```

Mit `psql` verbinden:

```bash
psql -h localhost -U postgres
```

Container stoppen:

```bash
docker stop pg-training
```

Container löschen:

```bash
docker rm pg-training
```

</p>
</details>

## 1.3 Installation pgAdmin

Installieren Sie pgAdmin als GUI-Tool, richten Sie den Zugriff auf Ihren zuvor installierten PostgreSQL-Server ein und testen Sie eine Verbindung.

### pgAdmin Installation (Beispiel Windows)

1. Installer von <https://www.pgadmin.org/download/>  herunterladen

2. Programm starten

3. „Register new Server“ auswählen

4. Verbindung eintragen:

    - Host: localhost
    - Username: postgres
    - Password: (Postgres-Passwort)

5. Verbindung testen → Sollte erfolgreich sein.

### pgAdmin in Docker (Alternative)

<details>
<summary>Show solution</summary>
<p>

**pgAdmin in Docker (Alternative):**

```bash
docker run -p 5050:80 \
 -e PGADMIN_DEFAULT_EMAIL=admin@example.com \
 -e PGADMIN_DEFAULT_PASSWORD=admin \
 -d dpage/pgadmin4
```

</p>
</details>
