# Financial Risk and Compliance Backend (Spring Boot)

A Spring Boot REST API that stores customers and transactions in MySQL, raises a fraud alert for high-value transactions, and records AML compliance reports. Built as a university backend project to practise a layered Spring architecture.

## Tech Stack

* Java 17
* Spring Boot 4.0
* Spring Data JPA / Hibernate
* MySQL
* Lombok
* Maven
* Postman (API testing)

## What the API Does

| Endpoint | Behaviour |
|----------|-----------|
| `POST /customers`, `GET /customers` | Create and list customers |
| `POST /transactions`, `GET /transactions` | Save a transaction with a timestamp and list transactions. Any transaction above 10,000 creates a HIGH-severity "High Value Transaction" fraud alert |
| `GET /fraud/alerts` | List fraud alerts |
| `POST /compliance/report` | Save an AML report; the request body becomes the report details |
| `GET /risk/analyze/{amount}` | Runs `python3 ai/predict.py <amount>` and returns the script's first line of output |

**Current limits**

* Fraud detection is one rule (an amount threshold), not a trained model.
* The Python scoring script `ai/predict.py` is not included in this repository, so `/risk/analyze` returns `AI_ANALYSIS_ERROR` until you add a script at that path.
* `RiskProfile` and `AuditLog` entities and repositories exist, but no endpoint writes to them yet.

## Run It

1. Create the database:

   ```sql
   CREATE DATABASE financial_risk_system;
   ```

2. Set your MySQL credentials as environment variables (the app reads them in `application.properties`):

   ```bash
   export DB_USERNAME=root
   export DB_PASSWORD=your_mysql_password
   ```

3. Start the app (needs Maven installed):

   ```bash
   mvn spring-boot:run
   ```

The API runs at `http://localhost:8080`.

## Architecture

```
Controller → Service → Repository → MySQL
                ↓
     PythonRiskAnalyzer (external script hook)
```

## Project Structure

```
src/main/java/com/enterprise/risk/
├── ai/           # PythonRiskAnalyzer: calls the external Python script
├── config/
├── controller/   # REST endpoints
├── entity/       # JPA entities
├── repository/   # Spring Data repositories
└── service/      # Business logic, fraud rule, compliance reports
```
