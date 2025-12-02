# Useful Commands  

## Run the Code

### Frontend  
npx serve .  

### Backend  
go run main.go  

## Add new data in the database
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

## Install PostgreSQL locally
Linux (Ubuntu/Debian):    
sudo apt update  
sudo apt install postgresql postgresql-contrib  
sudo systemctl start postgresql  

## Linux access postgres console  
sudo -i -u postgres  
psql  
\c moodtracker 

## Access the PostgreSQL command line & its commands (Mac)
psql -U postgres  

\c moodtracker  

SELECT * FROM devices;  

SELECT * FROM prototypedevice;  

curl -X POST http://localhost:8080/addMood \  
-H "Content-Type: application/json" \  
-d '{"device_id":1,"mood":3}'  

SELECT * FROM prototypedevice WHERE entry_date = CURRENT_DATE;  

## Create project database and user
Table names are case senstive, everything is in lower case  

CREATE DATABASE moodtracker;  

CREATE USER admin WITH PASSWORD 'password';  

CREATE TABLE devices (  
    id SERIAL PRIMARY KEY,           -- numeric ID for JWT / foreign key  
    device_id VARCHAR(64) UNIQUE NOT NULL,  -- public device name like "device1"  
    password VARCHAR(255) NOT NULL,  
    reminder1 TIME DEFAULT NULL,  
    reminder2 TIME DEFAULT NULL,  
    reminder3 TIME DEFAULT NULL  
);  

CREATE TABLE prototypedevice (  
    id BIGSERIAL PRIMARY KEY,  
    device_id INTEGER NOT NULL REFERENCES devices(id) ON DELETE CASCADE,  
    entry_date DATE NOT NULL,  
    mood SMALLINT NOT NULL  
);  

-- Replace <username> with the role your Go app uses --    
GRANT SELECT, INSERT, UPDATE, DELETE ON TABLE devices TO admin;  
GRANT SELECT, INSERT, UPDATE, DELETE ON TABLE prototypedevice TO admin;  

-- Replace 'admin' with your DB user if different --    
GRANT USAGE, SELECT, UPDATE ON SEQUENCE prototypedevice_id_seq TO admin;   

## Alter Table
ALTER TABLE devices ADD COLUMN last_triggered DATE DEFAULT NULL;  

## Quit database
\q

## List all databases
\l

## List the relations in a database
\dt

## In API folder terminal  
Install the PostgreSQL driver    
go get github.com/lib/pq     

## Golang Code 

package main  

import (  
	"database/sql"  
	"fmt"  
	"log"  

	_ "github.com/lib/pq"  
)  

func main() {  
	connStr := "user=admin password=password dbname=moodtracker sslmode=disable"  
	db, err := sql.Open("postgres", connStr)  
	if err != nil {  
		log.Fatal(err)  
	}  
	defer db.Close()  

	err = db.Ping()  
	if err != nil {  
		log.Fatal("Cannot connect to database:", err)  
	}  

	fmt.Println("Connected to PostgreSQL successfully!")  
}  
