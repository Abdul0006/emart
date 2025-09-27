# 🏗 KP Skills Marketplace – Prototype Architecture & Database Schema

## 🔑 Overview
This system is a **multi-vendor marketplace** for KP Govt institutes where students can upload products, customers can buy them, and the admin can manage sales, payments, and reporting.

---

## 📐 System Architecture (High-Level)

```
                        +----------------------+
                        |   Customer (Buyer)   |
                        +----------------------+
                                  |
                                  v
                        +----------------------+
                        |  Web / Mobile App    |
                        |  (React / Flutter)   |
                        +----------------------+
                                  |
                                  v
                        +----------------------+
                        |   API Gateway        |
                        | (Django REST API)    |
                        +----------------------+
                                  |
        ----------------------------------------------------
        |                          |                        |
        v                          v                        v
+------------------+      +------------------+     +------------------+
| Authentication   |      | Product Service  |     | Order Service    |
| (Users, Roles)   |      | (CRUD, Approval) |     | (Cart, Checkout) |
+------------------+      +------------------+     +------------------+
        |                          |                        |
        -----------------------------------------------------
                                  |
                                  v
                        +----------------------+
                        |  Payment Service     |
                        | (JazzCash, Easypaisa)|
                        +----------------------+
                                  |
                                  v
                        +----------------------+
                        | Reporting & Analytics|
                        | (Sales, Payouts)     |
                        +----------------------+
                                  |
                                  v
                        +----------------------+
                        | PostgreSQL Database  |
                        +----------------------+
                                  |
                                  v
                        +----------------------+
                        | File Storage (S3)    |
                        | (Product Images)     |
                        +----------------------+
```

---

## 📊 Database Schema (ERD)

### **Institutes**
| Field       | Type         | Notes            |
|-------------|--------------|------------------|
| id (PK)     | SERIAL       | Primary key      |
| name        | VARCHAR(255) | Institute name   |
| district    | VARCHAR(100) | District name    |
| address     | TEXT         | Address          |
| created_at  | TIMESTAMP    | Default now()    |

---

### **Students**
| Field          | Type         | Notes                        |
|----------------|--------------|------------------------------|
| id (PK)        | SERIAL       | Primary key                  |
| institute_id   | INT (FK)     | References Institutes(id)    |
| name           | VARCHAR(255) | Student full name            |
| email          | VARCHAR(255) | Unique                       |
| phone          | VARCHAR(20)  |                              |
| password_hash  | TEXT         | Hashed password              |
| created_at     | TIMESTAMP    | Default now()                |

---

### **Products**
| Field        | Type         | Notes                                   |
|--------------|--------------|-----------------------------------------|
| id (PK)      | SERIAL       | Primary key                             |
| student_id   | INT (FK)     | References Students(id)                 |
| title        | VARCHAR(255) | Product name                            |
| description  | TEXT         |                                         |
| price        | NUMERIC(10,2)|                                         |
| stock        | INT          | Quantity available                      |
| image_url    | TEXT         | Stored in S3 / Cloudinary              |
| status       | VARCHAR(20)  | (pending/approved/rejected)             |
| created_at   | TIMESTAMP    | Default now()                           |

---

### **Orders**
| Field        | Type         | Notes                                        |
|--------------|--------------|----------------------------------------------|
| id (PK)      | SERIAL       | Primary key                                  |
| customer_name| VARCHAR(255) |                                              |
| customer_email| VARCHAR(255)|                                              |
| customer_phone| VARCHAR(20) |                                              |
| status       | VARCHAR(20)  | (pending/paid/shipped/completed/cancelled)   |
| total_amount | NUMERIC(10,2)|                                              |
| created_at   | TIMESTAMP    | Default now()                                |

---

### **OrderItems**
| Field        | Type         | Notes                        |
|--------------|--------------|------------------------------|
| id (PK)      | SERIAL       | Primary key                  |
| order_id     | INT (FK)     | References Orders(id)        |
| product_id   | INT (FK)     | References Products(id)      |
| quantity     | INT          |                              |
| price        | NUMERIC(10,2)| Snapshot of product price    |

---

### **Payments**
| Field          | Type         | Notes                                |
|----------------|--------------|--------------------------------------|
| id (PK)        | SERIAL       | Primary key                          |
| order_id (FK)  | INT          | References Orders(id)                |
| amount         | NUMERIC(10,2)|                                      |
| payment_method | VARCHAR(50)  | (jazzcash/easypaisa/bank/stripe)     |
| transaction_id | VARCHAR(255) | From gateway                         |
| status         | VARCHAR(20)  | (success/failed/pending)             |
| created_at     | TIMESTAMP    | Default now()                        |

---

### **Reports (Generated via SQL Views / Aggregation)**
- **Institute Sales Report**
  - institute_id
  - total_sales
  - total_products
  - total_orders
  - payout_amount
  - month/year

---

## ✅ Recommended Tech Stack
- **Backend:** Python (Django REST Framework)  
- **Frontend:** React.js (Web), Flutter/React Native (Mobile)  
- **Database:** PostgreSQL  
- **Payments:** JazzCash/Easypaisa API integration  
- **File Storage:** AWS S3 / Cloudinary  
- **Reports:** Django Admin + SQL Views  
