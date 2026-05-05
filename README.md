# Mood-Tracking Wrist Device

## Overview
This project is a simple mood-tracking system that combines a physical device with a backend and a web dashboard.  
The goal was to help social work students monitor a patient’s emotional state over time.  

The user records their mood using a wrist device with 3 buttons:  
Green -> Good  
Yellow -> Average  
Red -> Poor  

Each input is confirmed with LED feedback and the device sends reminders using a buzzer.  

All mood data is sent to a backend system, stored in a database and shown in a dashboard as daily, weekly and monthly summaries.   

## How the System Works
1. The user presses a button on the device to record mood
2. The device sends the data to the backend using WiFi
3. The backend stores the data in a database
4. The dashboard shows mood trends and summaries
5. The device checks for reminders and alerts the user when needed

## Technologies Used

### Hardware
- Arduino Uno WiFi Rev2
- Buttons (input)
- LEDs (feedback)
- Buzzer (reminders)

### Backend
- Go (Golang)
- REST API (Handler-Service-Repository structure)
- PostgreSQL database
- JWT authentication

### Frontend
- React (dashboard for visualization and control)

## Main Features
- Simple mood input using 3 buttons
- LED feedback for each input
- Reminder notifications using buzzer
- Cloud-based data storage
- Daily, weekly and monthly mood summaries
- Dashboard to visualize mood patterns
- Custom reminder time settings
- Secure login using JWT

## API Endpoints (Main Ones)
- `/api/login` -> login and get JWT token
- `/addMood` -> send mood data from device
- `/getAlerts` -> check if reminder is active
- `/mood/` -> get mood summaries (daily / weekly / monthly)
- `/reminders` -> set and get reminder times

## Project Structure
The backend follows a layered architecture:  
- Handlers -> handle HTTP requests
- Services -> business logic
- Repositories -> database operations

Data is split into:  
- Device -> device info, login, reminder settings
- Mood -> mood entries with timestamps

## Setup
Since the codebase and commands are already included in this repository, you can follow them directly to run the project.  

General steps:  
1. Set up PostgreSQL database
2. Run the Go backend
3. Run the React frontend
4. Connect the Arduino device

## Conclusion
This project shows how a simple hardware device can be connected with a backend and dashboard to create a useful system.
It combines embedded systems, backend development and frontend visualization into one complete solution.
