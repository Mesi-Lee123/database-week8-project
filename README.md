# Library Management Database

## Overview
The **Library Management Database** is a fully normalized (3NF) relational database designed to manage library operations. It tracks books, authors, categories, members, and loan activities. The database is implemented in **MySQL** with proper indexing, foreign key constraints, and query optimization strategies.

---

## Database Name
```

LibraryDB

````

---

## Tables and Structure

### 1. Authors
Stores author information.

| Column      | Data Type    | Constraints                   | Description                  |
|------------|--------------|-------------------------------|------------------------------|
| author_id  | INT          | PRIMARY KEY, AUTO_INCREMENT   | Unique identifier for author |
| first_name | VARCHAR(50)  | NOT NULL                      | Author's first name          |
| last_name  | VARCHAR(50)  | NOT NULL                      | Author's last name           |

**Indexes:**  
- `idx_last_name` on `last_name` for fast author searches.

---

### 2. Categories
Stores book categories.

| Column        | Data Type    | Constraints                  | Description                      |
|---------------|-------------|-------------------------------|----------------------------------|
| category_id   | INT         | PRIMARY KEY, AUTO_INCREMENT  | Unique identifier for category   |
| category_name | VARCHAR(50) | NOT NULL, UNIQUE             | Name of the category             |

---

### 3. Books
Stores book information.

| Column         | Data Type    | Constraints                  | Description                         |
|----------------|-------------|-------------------------------|-------------------------------------|
| book_id        | INT         | PRIMARY KEY, AUTO_INCREMENT  | Unique book identifier              |
| title          | VARCHAR(100)| NOT NULL                     | Book title                          |
| isbn           | VARCHAR(20) | NOT NULL, UNIQUE             | ISBN number (unique for each book) |
| category_id    | INT         | NOT NULL, FOREIGN KEY        | References `Categories(category_id)`|
| published_year | YEAR        |                               | Year the book was published        |
| total_copies   | INT         | NOT NULL                     | Total copies available in library  |

**Indexes:**  
- `idx_title` on `title` for fast searches.  
- `idx_category` on `category_id` for category queries.

---

### 4. BookAuthors
Junction table to link books and authors (Many-to-Many).

| Column     | Data Type | Constraints                            | Description                         |
|------------|-----------|----------------------------------------|-------------------------------------|
| book_id    | INT       | PRIMARY KEY (book_id, author_id), FK   | References `Books(book_id)`         |
| author_id  | INT       | PRIMARY KEY (book_id, author_id), FK   | References `Authors(author_id)`     |

**Indexes:**  
- `idx_author_id` on `author_id` for author-based queries.

---

### 5. Members
Stores library member information.

| Column     | Data Type    | Constraints                  | Description                       |
|------------|-------------|-------------------------------|-----------------------------------|
| member_id  | INT         | PRIMARY KEY, AUTO_INCREMENT  | Unique member identifier          |
| first_name | VARCHAR(50) | NOT NULL                     | Member's first name               |
| last_name  | VARCHAR(50) | NOT NULL                     | Member's last name                |
| email      | VARCHAR(100)| NOT NULL, UNIQUE             | Member's email address            |
| phone      | VARCHAR(20) |                               | Contact phone number              |
| join_date  | DATE        | NOT NULL                     | Date member joined the library   |

**Indexes:**  
- `idx_last_name` on `last_name` for fast member search.

---

### 6. Loans
Tracks borrowing activity of books by members.

| Column      | Data Type | Constraints                        | Description                                   |
|------------|-----------|------------------------------------|-----------------------------------------------|
| loan_id    | INT       | PRIMARY KEY, AUTO_INCREMENT        | Unique loan identifier                        |
| book_id    | INT       | NOT NULL, FOREIGN KEY              | References `Books(book_id)`                   |
| member_id  | INT       | NOT NULL, FOREIGN KEY              | References `Members(member_id)`               |
| loan_date  | DATE      | NOT NULL                           | Date the book was borrowed                    |
| due_date   | DATE      | NOT NULL                           | Date the book is due for return               |
| return_date| DATE      | NULL                               | Date the book was returned                    |
| status     | ENUM      | NOT NULL                           | 'borrowed', 'returned', 'overdue'            |

**Indexes:**  
- `idx_status` on `status` for overdue queries.  
- `idx_member_id` on `member_id` for member loan history.  
- `idx_book_id` on `book_id` for book loan queries.

---

## Relationship Overview

| Table 1     | Table 2     | Relationship   | Description |
|------------|------------|----------------|-------------|
| Authors    | BookAuthors | Many-to-Many   | A book can have multiple authors and an author can write multiple books. |
| Categories | Books       | One-to-Many    | Each book belongs to one category. |
| Books      | Loans       | One-to-Many    | A book can have multiple loans. |
| Members    | Loans       | One-to-Many    | A member can have multiple loans. |

All relationships enforce **foreign key constraints** to maintain **referential integrity**.

---

## Normalization & Optimization

- Fully normalized **up to 3NF**:
  - No repeating groups.
  - All non-key attributes depend on the whole primary key.
  - No transitive dependencies.
- Indexes on frequently queried columns for faster performance.
- Composite primary key in `BookAuthors` prevents duplicate book-author entries.
- Foreign keys enforce referential integrity.
- Unique constraints on `isbn`, `email`, and `category_name` prevent duplicates.

---

## Usage Instructions

1. **Create Database:**
```sql
CREATE DATABASE LibraryDB;
USE LibraryDB;
````

2. **Run Table Creation Scripts** (SQL file) to create all tables with constraints and indexes.

3. **Insert Sample Data:**

```sql
INSERT INTO Categories(category_name) VALUES ('Fiction');
INSERT INTO Authors(first_name, last_name) VALUES ('Jane', 'Austen');
INSERT INTO Books(title, isbn, category_id, published_year, total_copies)
VALUES ('Pride and Prejudice', '111-222333444', 1, 1813, 5);
```

4. **Query Examples:**

* **Find all books by a specific author:**

```sql
SELECT b.title, b.isbn
FROM Books b
JOIN BookAuthors ba ON b.book_id = ba.book_id
JOIN Authors a ON ba.author_id = a.author_id
WHERE a.last_name = 'Austen';
```

* **List overdue books:**

```sql
SELECT l.loan_id, m.first_name, m.last_name, b.title
FROM Loans l
JOIN Members m ON l.member_id = m.member_id
JOIN Books b ON l.book_id = b.book_id
WHERE l.status = 'overdue';
```

---

## Notes

* Scalable for large libraries with thousands of books, members, and loans.
* Can be extended with `BookCopies`, `Staff`, or `MemberAddresses` tables.
* Optimized for query performance using indexes and foreign key constraints.

---

## Author

**Lawrence Omanya** – Database Designer & Developer# database-week8-project
