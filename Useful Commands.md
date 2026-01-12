# Useful Commands

## Prerequisites

* Go installed  
* Node.js and npm installed  
* PostgreSQL installed and running  

This contains all essential commands and instructions needed to run, develop and test the Mood Tracker project locally. It is intended as a quick reference for anyone working with this repository.

## Running the Project

### Frontend

```bash
npx serve .
```

### Backend

```bash
go run main.go
```

## PostgreSQL Installation

### Linux (Ubuntu / Debian)

Install and start PostgreSQL locally:

```bash
sudo apt update
sudo apt install postgresql postgresql-contrib
sudo systemctl start postgresql
```

## Accessing PostgreSQL

### Linux: Access PostgreSQL Console

```bash
sudo -i -u postgres
psql
```

Connect to the project database:

```sql
\c moodtracker
```

### macOS: Access PostgreSQL Command Line

```bash
psql -U postgres
```

Connect to the project database:

```sql
\c moodtracker
```

## Database Setup

> **Note:** Table names are case sensitive. All table names are in lowercase.

### Create Database and User

```sql
CREATE DATABASE moodtracker;

CREATE USER admin WITH PASSWORD 'password';
```

### Create Tables

#### Devices Table

```sql
CREATE TABLE devices (
    id SERIAL PRIMARY KEY,                  -- numeric ID for JWT / foreign key
    device_id VARCHAR(64) UNIQUE NOT NULL,  -- public device name (e.g. "device1")
    password VARCHAR(255) NOT NULL,
    reminder1 TIME DEFAULT NULL,
    reminder2 TIME DEFAULT NULL,
    reminder3 TIME DEFAULT NULL
);
```

#### Prototype Device Table

```sql
CREATE TABLE prototypedevice (
    id BIGSERIAL PRIMARY KEY,
    device_id INTEGER NOT NULL REFERENCES devices(id) ON DELETE CASCADE,
    entry_date DATE NOT NULL,
    mood SMALLINT NOT NULL
);
```

### Grant Permissions

```sql
GRANT SELECT, INSERT, UPDATE, DELETE ON TABLE devices TO admin;
GRANT SELECT, INSERT, UPDATE, DELETE ON TABLE prototypedevice TO admin;

GRANT USAGE, SELECT, UPDATE ON SEQUENCE prototypedevice_id_seq TO admin;
```

## Altering Tables

Add a column to track when a device was last triggered:

```sql
ALTER TABLE devices ADD COLUMN last_triggered DATE DEFAULT NULL;
```

## Common PostgreSQL Commands

### List All Databases

```sql
\l
```

### List Tables in Current Database

```sql
\dt
```

### View SQL Command Help

```sql
\h
```

### Quit PostgreSQL

```sql
\q
```

## Querying Data

View all registered devices:

```sql
SELECT * FROM devices;
```

View all mood entries:

```sql
SELECT * FROM prototypedevice;
```

## API Testing

### Send Mood Data (Example)

```bash
curl -X POST http://localhost:8080/addMood \
-H "Content-Type: application/json" \
-d '{"device_id":1,"mood":3}'
```

```bash
curl -X POST http://172.20.10.4:8080/addMood \
-H "Content-Type: application/json" \
-d '{"device_id":1,"mood":3}'
```


## Backend Dependencies

Install the PostgreSQL driver for Go (run inside the API folder):

```bash
go get github.com/lib/pq
```

## Test Data Generation

### Insert Random Mood Data (Last 30 Days)

```sql
DO $$
DECLARE
    start_date DATE := current_date - INTERVAL '29 days';
    d DATE;
    i INT;
    dev_id INT;
BEGIN
    SELECT id INTO dev_id FROM devices WHERE device_id = 'device1';

    FOR i IN 0..29 LOOP
        d := start_date + i;
        INSERT INTO prototypedevice (device_id, entry_date, mood) VALUES
            (dev_id, d, (FLOOR(random() * 3 + 1))::SMALLINT),
            (dev_id, d, (FLOOR(random() * 3 + 1))::SMALLINT),
            (dev_id, d, (FLOOR(random() * 3 + 1))::SMALLINT);
    END LOOP;
END $$;
```

### Delete Today’s Entries for a Device

```sql
DELETE FROM prototypedevice
WHERE device_id = (
    SELECT id FROM devices WHERE device_id = 'device1'
)
AND entry_date = current_date;
```
