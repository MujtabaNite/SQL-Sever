# 🗄️ SQL Management Studio Clone + Calendar App

> **A Java-based file-system database engine with a role-based SQL CLI, paired with a Calendar event manager backed by SQLite — built from scratch without any ORM.**

[![Java](https://img.shields.io/badge/Java-JDK_21-ED8B00?style=for-the-badge&logo=openjdk&logoColor=white)](https://openjdk.org/)
[![SQLite](https://img.shields.io/badge/SQLite-003B57?style=for-the-badge&logo=sqlite&logoColor=white)](https://www.sqlite.org/)
[![SQL Server](https://img.shields.io/badge/SQL_Server-CC2927?style=for-the-badge&logo=microsoftsqlserver&logoColor=white)](https://www.microsoft.com/en-us/sql-server)
[![Status](https://img.shields.io/badge/Status-University_Project-23C16B?style=for-the-badge)]()

---

## 📖 About the Project

This repository contains **two interconnected Java projects** developed across university semesters to demonstrate database concepts, OOP design, and raw JDBC integration:

### 🖥️ Project 1 — SQL Management Studio Clone
A custom command-line database management tool that mimics core SQL Server Management Studio (SSMS) functionality. The engine uses the **local filesystem as storage** (`.txt` files per table), allowing full database and table lifecycle management through a custom query parser — with real SQL Server integration for error logging.

### 📅 Project 2 — Calendar & Special Dates App
A desktop Java application that manages calendar events, reminders, and special dates backed by **SQLite via JDBC**. Demonstrates relational schema design, date-based querying, and persistent local storage without a heavy database server.

---

## ✨ Key Features

### 🖥️ SQL Management Studio Clone

**Authentication System**
- File-based credential store with role separation (`admin` / `user`)
- Role-specific menus with permission enforcement
- Admins can create databases, manage tables, and add new users
- Users can query and manipulate data within existing databases

**Custom Query Engine**
- `CREATE TABLE` — defines schema, persists column metadata
- `SELECT` — supports column projection, `WHERE` filtering
- `INSERT` — validates column count and data types before writing
- `UPDATE` — conditional row updates with `WHERE` clause support
- `DELETE` — safe row removal with column validation
- `Custom Query` mode — parse-and-execute natural SQL-like strings

**Error Logging to Real SQL Server**
- All query errors are caught and logged to a live **Microsoft SQL Server** `ErrorLogs` table via JDBC
- `DBManager` handles the live connection for centralized audit tracking

### 📅 Calendar & Special Dates App
- Add and retrieve special dates (birthdays, deadlines, events)
- Manage reminders with day/month/year and descriptive text
- SQLite backend with JDBC — no server required, fully portable
- File-based fallback storage (`.txt`) for offline/raw mode

---

## 🏗️ Architecture

```
SQL Management Studio Clone:
├── SqlManagementStudio.java     # Main entry point — auth + role routing
├── DBManager.java               # Live SQL Server connection (JDBC) for error logging
├── Database.java                # File-system database management (create, list)
├── Table.java                   # Table schema persistence and listing
├── Query.java                   # Custom SQL query parser and executor
├── SqlException.java            # Custom exception for query errors
└── Autentication/
    ├── User.java                # Abstract base class
    ├── DatabaseAdmin.java       # Admin: full system access
    └── NormalUser.java          # User: read + limited write access

Calendar App:
├── Calender.db                  # SQLite database file
├── Calender_SpecialDates.txt    # Flat-file fallback for special dates
├── Calender_Remainder.txt       # Flat-file fallback for reminders
└── Calender_tables.txt          # Schema definition metadata
```

---

## 🔍 How the Custom Query Engine Works

The engine parses user input and routes to the appropriate handler:

```
User Input → parseAndExecute(query)
                ↓
         Tokenize by whitespace
                ↓
    Command: SELECT / INSERT / UPDATE / DELETE
                ↓
         Read/Write flat-file .txt table
                ↓
     On error → log to SQL Server ErrorLogs
```

**Storage Format** (each table is a `.txt` file):
```
day,month,year,Des          ← header row (column names)
15,8,2024,Birthday          ← data rows (CSV)
26,9,2024,Class
```

**Condition Evaluation:**
```java
// Evaluates: column=value pairs
boolean conditionMet(String[] headers, String[] values, String condition)
// Example: "month=8" → finds all August events
```

---

## 🛠️ Local Setup Guide

### Prerequisites
- JDK 21+
- Microsoft SQL Server (for error logging feature only)
- SQLite JDBC driver (for Calendar app)
- IntelliJ IDEA or any Java IDE

### SQL Management Studio Clone

1. **Clone the repository**
   ```bash
   git clone https://github.com/Mujtabanite/SQL-Server-App.git
   cd SQL-Server-App
   ```

2. **Configure SQL Server connection** in `DBManager.java`:
   ```java
   String url = "jdbc:sqlserver://YOUR-PC\\SQLEXPRESS;" +
                "databaseName=SQLErrors;" +
                "integratedSecurity=true;encrypt=false;";
   ```

3. **Create the ErrorLogs table** in SQL Server:
   ```sql
   CREATE TABLE ErrorLogs (
       id INT IDENTITY(1,1) PRIMARY KEY,
       query NVARCHAR(MAX),
       error_message NVARCHAR(MAX),
       timestamp DATETIME DEFAULT GETDATE()
   );
   ```

4. **Add the default admin credential** to `credentials.txt`:
   ```
   mujtaba,123,admin
   ```

5. **Run** `SqlManagementStudio.java`

### Calendar App

1. Add SQLite JDBC driver to dependencies
2. Run the calendar main class — the `.db` file is created automatically
3. Use the menu to add and query events

---

## 📋 Sample Session

```
Enter username: mujtaba
Enter password: 123
Authentication successful!

Menu:
1. Create Database
2. List Databases
3. Use Database
4. Add New User
5. Errors occurred so far
6. Exit

Enter your choice: 1
Enter database name: SchoolDB
Database created.

Enter your choice: 3
Enter database name: SchoolDB
Using database: SchoolDB

Menu:
1. Create Table
...
Enter your choice: 7
Enter query: SELECT * FROM course WHERE mid=80
[80, 85, 90, 3]
```

---

## 🧠 Concepts Demonstrated

| Concept | Implementation |
|---|---|
| **OOP — Inheritance** | `User` → `DatabaseAdmin` / `NormalUser` |
| **OOP — Polymorphism** | `authenticate()` and `addUser()` overridden per role |
| **File I/O** | `BufferedReader`/`BufferedWriter` for flat-file database |
| **JDBC** | Live SQL Server connection for error logging |
| **SQLite + JDBC** | Calendar app persistent storage |
| **Custom Parser** | SQL-like query tokenization and dispatch |
| **Exception Handling** | Custom `SqlException` + centralized error logging |
| **Design Pattern** | Template Method via abstract `User` class |

---

## 🎓 Academic Context

Developed across **Semesters 3–4** at BIIT Rawalpindi for:
- **Object-Oriented Programming** (OOP patterns, inheritance, encapsulation)
- **Database Systems** (JDBC, relational schema, SQL fundamentals)

---

<div align="center">
  <i>Developed with ❤️ by Muhammad Mujtaba · BIIT Rawalpindi</i>
</div>
