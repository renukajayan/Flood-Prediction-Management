# Flood Prediction & Rescue Management

An academic Android project combining rescue request management, location tracking, and a flood prediction screen. The mobile app uses Java/XML and Volley to communicate with a PHP backend backed by MySQL.

**Current status:** legacy academic prototype, originally uploaded in 2020. The checked-in Python prediction script is a placeholder; this repository does not contain a working trained flood prediction model.

## Features represented in the source

- Separate user and rescuer registration/login flows.
- Users submit rescue requests with emergency details, needs, hazards, people/pets counts, and coordinates.
- Rescuers browse and accept requests and update their status.
- Map/location screens support tracking victims and rescuers.
- A prediction screen sends rainfall and water-level values to PHP.

## Technology

| Component | Technology |
| --- | --- |
| Mobile interface | Android, Java, XML, AndroidX |
| HTTP requests | Volley 1.1.1 |
| Maps | Google Maps Android SDK 17.0.0 |
| Backend | PHP with mysqli |
| Database | MySQL; SQL export included |
| Prediction integration | PHP invokes Python; model implementation missing |

## Repository guide

| Path | Purpose |
| --- | --- |
| `FloodPrediction/Android/FloodRescureSystem/` | Android Studio project (original folder spelling retained) |
| `FloodPrediction/php/flood_prediction/` | PHP endpoints |
| `FloodPrediction/php/flood_prediction/Prediction/` | PHP/Python bridge and CSV files |
| `FloodPrediction/floodprediction.sql` | Schema with original sample records removed |
| Original prebuilt APK | Omitted because it may embed the old Maps key; rebuild from source |

## How the application works

1. `IPActivity.java` saves the backend host entered in the app.
2. Login/registration activities send requests to the corresponding PHP scripts.
3. PHP reads/writes the MySQL database.
4. Rescue request activities collect details and coordinates; request list/detail screens display the results.
5. Rescuer actions update assignments/status. Location services and tracking activities provide location-related flows.
6. `FloodPredictionActivity.java` posts `rain` and `water` to `Prediction/py.php`.
7. That script writes `test.csv`, invokes `python test.py`, and returns its output.

**Prediction limitation:** `test.py` currently only prints fixed sample text. Although `train.csv` is present, the checked-in script does not train or run a classifier. Do not describe this version as a verified Naive Bayes model or claim prediction accuracy.

## Local setup guide

These steps describe the source configuration; the full system has not been run or verified during migration.

### 1. Get the source

```bash
git clone https://github.com/renukajayan/Flood-Prediction-Management.git
cd Flood-Prediction-Management
```

### 2. Set up PHP and MySQL

- Install a local Apache/PHP/MySQL stack such as XAMPP.
- Copy `FloodPrediction/php/flood_prediction` into the web server document root (for example, `htdocs/flood_prediction`).
- Create a database named `floodprediction`.
- Import `FloodPrediction/floodprediction.sql` into that database. Original sample records were removed during migration.
- Edit `connection.php` to match your local database host, username, password, and database name.
- Start Apache and MySQL.

The original database connection assumes localhost, user root, an empty password, and database floodprediction. Use dedicated credentials for any rebuilt environment.

### 3. Open the Android project

Open `FloodPrediction/Android/FloodRescureSystem` in Android Studio.

Original build configuration:

- Android Gradle Plugin 3.5.3; Gradle wrapper 5.4.1.
- Compile/target SDK 29; minimum SDK 23.
- Build Tools 29.0.3.
- JCenter repository references.

This older build may require compatible older tooling or a deliberate dependency/Gradle migration. A modern Android Studio installation may not build it unchanged.

### 4. Configure networking and maps

- Enter the backend computer's LAN IP in the app's IP dialog, without `http://` or a path.
- Keep the phone and server on the same reachable network. With the standard Android emulator, the host computer is normally reachable at `10.0.2.2`.
- The app constructs URLs such as `http://HOST/flood_prediction/userLogin.php`.
- Configure your own restricted Google Maps API key in the debug/release `google_maps_api.xml` resources, and review the web map configuration.
- Allow location permission for location-related screens.

### 5. Understand the Python bridge

The PHP prediction endpoint requires Python on the server PATH, permission to execute it, and write access to its CSV directory. Running the included script produces placeholder output only. A real prediction implementation, validated dataset, evaluation, and safer request handling are future work.

## Main files to study

All Java files below are under `app/src/main/java/com/aumento/floodrescuresystem/`.

| File | What to learn |
| --- | --- |
| `IPActivity.java` | Server configuration |
| `LoginActivity.java`, `SignupActivity.java` | Authentication/registration requests |
| `RescueRequestActivity.java` | Form collection and request submission |
| `RescueRequestListActivity.java`, `RequestDetailsActivity.java` | Listing and viewing requests |
| `RescueRequestStatusActivity.java` | Request progress |
| `TrackVictimActivity.java`, `RescuerTrackActivity.java` | Tracking screens |
| `Utils/GPS_service.java` | Location service |
| `Utils/GlobalPreference.java` | Shared preference storage |
| `FloodPredictionActivity.java` | Volley POST and response display |

Backend reading order: `connection.php` → `userRegister.php`/`userLogin.php` → `insertRequest.php` → `getRescueList.php` → `acceptRequest.php` → `updateStatus.php` → location endpoints.

Database tables: `user` stores user accounts; `rescuer` stores rescuer accounts and coordinates; `rescue_request` stores request details, user/rescuer identifiers, coordinates, and status.

## Limitations and improvement plan

This is a learning prototype, not an emergency service. Before any public deployment:

- Replace SQL string interpolation with prepared statements.
- Hash passwords and introduce server-side authentication/authorization.
- Validate inputs, use HTTPS, and move configuration out of source.
- Remove personal/sample records and replace any exposed API keys.
- Update obsolete dependencies and map APIs.
- Replace the shared prediction CSV with request-isolated processing.
- Implement and evaluate an actual prediction model.
- Add meaningful API, workflow, and device tests.

The included Android example tests are templates, not evidence of end-to-end correctness.

## Explanation:

> This academic project connects an Android Java app to a PHP/MySQL backend for rescue requests and location-related workflows. Volley sends HTTP requests, PHP handles database operations, and Google Maps supports tracking screens. The repository also contains a rainfall/water-level prediction interface, but its committed Python script is a placeholder, so that component needs restoration before I can demonstrate real predictions.

flow: screen → Volley request → PHP endpoint → database → response → screen.
