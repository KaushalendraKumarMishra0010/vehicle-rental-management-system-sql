# Vehicle Rental Management System - SQL Project

A comprehensive relational database system designed to manage vehicle rentals, customer records, and booking transactions for a rental agency. This project showcases data modeling, schema creation, data insertion, and deep business analytical query writing using MySQL.

---

## Project Overview

This project simulates a real-world scenario where a vehicle rental company provides cars and bikes to customers on a short-term basis. As a Data Analyst / SQL Developer, the goal was to build a structured database to track available vehicles, streamline customer bookings, manage payments, and extract data-driven insights to boost business revenue and operational efficiency.

### Key Business Insights Solved:
* Tracking vehicle availability and maintenance status.
* Identifying popular vehicle models and vehicle types (Cars vs. Bikes).
* Monitoring total revenue, average bill value, and pending payment statuses.
* Identifying high-value customers (Top spending analysis).

---

## Database Architecture & Design

The system is designed with a fully normalized relational structure, using explicit Primary Key (PK) and Foreign Key (FK) references to maintain high data integrity.

### Data Relationships Explained

| Source Table | Relationship Type | Destination Table | Linking Column | Business Logic |
| :--- | :---: | :--- | :--- | :--- |
| **CUSTOMER** | **1 : Many** | **BOOKING** | `CID` | One customer can make multiple separate vehicle bookings over time. |
| **VEHICLE** | **1 : Many** | **BOOKING** | `VID` | One specific vehicle can be rented out multiple times across different booking dates. |

### Relational Schema Blueprint
* **CUSTOMER (1)** table connects to **BOOKING (Many)** via the `CID` (Customer ID) field.
* **VEHICLE (1)** table connects to **BOOKING (Many)** via the `VID` (Vehicle ID) field.
* **BOOKING** acts as the central junction/transaction table that captures which customer rented which vehicle, when, and for how much.

### Table Schemas

1. **VEHICLE Table:** Contains inventory details for cars and bikes.
   * Columns: `VID` (Primary Key), `Vehicle_Name`, `Vehicle_Type`, `Brand`, `Model_Year`, `Status`, `Fuel_Type`, `Rent_Per_Day`.
2. **CUSTOMER Table:** Maintains customer profiles and contact data.
   * Columns: `CID` (Primary Key), `Customer_Name`, `Phone`, `Age`, `Licence_Number`, `Address`.
3. **BOOKING Table:** Links customer transactions with inventory metrics.
   * Columns: `BID` (Primary Key), `CID` (Foreign Key), `VID` (Foreign Key), `Booking_Date`, `Start_Date`, `End_Date`, `Payment_Status`, `Bill_Amount`.

---

## Tech Stack Used
* Database Management System: MySQL / SQL Server
* SQL Concepts: Data Definition Language (DDL), Data Manipulation Language (DML), Subqueries, Joins (INNER, LEFT), Aggregate Functions, Window Functions (DENSE_RANK), Grouping & Filtering (GROUP BY, HAVING).

---

## Getting Started / Installation

To run this project locally, follow these steps:

1. Clone the repository to your local system:
```bash
git clone https://github.com/KaushalendraKumarMishra0010/vehicle-rental-management-system-sql.git
```

2. Open your preferred SQL Editor (e.g., MySQL Workbench, DBeaver, or command line).

3. Create the database and use it:
```sql
CREATE DATABASE vehicle_rental_db;
USE vehicle_rental_db;
```

4. Run the database initialization and data insertion scripts to populate the tables.

---

## SQL Analytics & Business Case Analysis

The project includes structured scripts answering 20 essential business questions divided into four logical analytical steps:

### Section A: Basic Queries & Filtering
Filtering transactional records based on conditional criteria like status, type, and age bands.
* **Example:** Display all vehicles that are currently Available.
```sql
SELECT *
FROM vehicle
WHERE Status='Available';
```

### Section B: Multi-Table JOINS
Linking normalized schemas to generate readable reports mapping customers directly to rented assets.
* **Example:** Display customer name, phone number, vehicle name and bill amount for all bookings.
```sql
SELECT c.Customer_Name, c.Phone, v.Vehicle_Name, b.Bill_Amount
FROM customer c
JOIN booking b ON c.CID = b.CID
JOIN vehicle v ON b.VID = v.VID;
```

### Section C: Aggregations & Metrics
Calculating business health summaries to keep a pulse on financial statistics.
* **Example:** Find the total revenue generated from Cars and Bikes separately.
```sql
SELECT v.Vehicle_Type, SUM(b.Bill_Amount) AS Total_Revenue
FROM booking b
JOIN vehicle v ON b.VID = v.VID
GROUP BY v.Vehicle_Type;
```

### Section D: Deep Analytics (GROUP BY, HAVING & Window Functions)
Extracting actionable intelligence about rental frequencies and identifying top users using advanced analytical operations.
* **Example:** Find vehicle types having more than 3 bookings.
```sql
SELECT v.Vehicle_Type, COUNT(b.BID) AS Number_of_Bookings
FROM vehicle v
JOIN booking b ON v.VID = b.VID
GROUP BY v.Vehicle_Type
HAVING COUNT(b.BID) > 3;
```

---

## Sample Complex Query Preview

Here is an example of a high-business-value window query used in the project to track user loyalty and rank premium spenders:

```sql
-- Finding top 3 premium customers based on total cash collection
SELECT Customer_Name, Number_of_Bookings, Total_Spending
FROM (
    SELECT c.Customer_Name, 
           COUNT(b.BID) AS Number_of_Bookings,
           SUM(b.Bill_Amount) AS Total_Spending,
           DENSE_RANK() OVER (ORDER BY SUM(b.Bill_Amount) DESC) AS Revenue_Rank
    FROM customer c
    JOIN booking b ON c.CID = b.CID
    GROUP BY c.CID, c.Customer_Name
) T
WHERE Revenue_Rank <= 3;
```
