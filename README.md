# Library Management System - PostgreSQL Documentation

## Project Overview

This Library Management System is built using PostgreSQL to manage a collection of books, authors, and patrons. The system supports full CRUD operations and advanced querying capabilities.

## Database Schema

The system consists of three main tables:
- **Authors**: Store author information
- **Books**: Store book details with foreign key to authors
- **Patrons**: Store patron information and borrowed books

## Setup Instructions for pgAdmin

### Prerequisites
- PostgreSQL server installed and running
- pgAdmin installed and configured
- Connection to PostgreSQL server established in pgAdmin

### Database Creation in pgAdmin
1. **Open pgAdmin** and connect to your PostgreSQL server
2. **Right-click on "Databases"** in the browser panel (left side)
3. **Select "Create" > "Database..."**
4. **Enter Database Name**: `LibraryDB`
5. **Click "Save"** to create the database
6. **Expand the LibraryDB** node in the browser to see it's created

## Complete SQL Commands by Sprint

### Sprint 1: Project Setup

#### Table Creation
```sql
-- Create Authors table
CREATE TABLE authors (
    id SERIAL PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    nationality VARCHAR(100),
    birth_year INTEGER,
    death_year INTEGER
);

-- Create Books table
CREATE TABLE books (
    id SERIAL PRIMARY KEY,
    title VARCHAR(255) NOT NULL,
    author_id INTEGER REFERENCES authors(id) ON DELETE CASCADE,
    genres TEXT[],
    published_year INTEGER,
    available BOOLEAN DEFAULT TRUE
);

-- Create Patrons table
CREATE TABLE patrons (
    id SERIAL PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,
    borrowed_books INTEGER[]
);
```

#### Verification in pgAdmin
After creating tables, you can verify by:
1. **Expand LibraryDB** > **Schemas** > **public** > **Tables**
2. **Right-click any table** > **Properties** to view structure
3. **Use Query Tool**: Tools > Query Tool, then run:
```sql
-- View all tables and their row counts
SELECT schemaname, tablename, 
       (SELECT count(*) FROM authors) as authors_count,
       (SELECT count(*) FROM books) as books_count,
       (SELECT count(*) FROM patrons) as patrons_count
FROM pg_tables WHERE schemaname = 'public';
```

### Sprint 2: Insert Data

#### Insert Authors
```sql
INSERT INTO authors (id, name, nationality, birth_year, death_year) VALUES
(1, 'George Orwell', 'British', 1903, 1950),
(2, 'Harper Lee', 'American', 1926, 2016),
(3, 'F. Scott Fitzgerald', 'American', 1896, 1940),
(4, 'Aldous Huxley', 'British', 1894, 1963),
(5, 'J.D. Salinger', 'American', 1919, 2010),
(6, 'Herman Melville', 'American', 1819, 1891),
(7, 'Jane Austen', 'British', 1775, 1817),
(8, 'Leo Tolstoy', 'Russian', 1828, 1910),
(9, 'Fyodor Dostoevsky', 'Russian', 1821, 1881),
(10, 'J.R.R. Tolkien', 'British', 1892, 1973);
```

#### Insert Books
```sql
INSERT INTO books (id, title, author_id, genres, published_year, available) VALUES
(1, '1984', 1, ARRAY['Dystopian', 'Political Fiction'], 1949, TRUE),
(2, 'To Kill a Mockingbird', 2, ARRAY['Southern Gothic', 'Bildungsroman'], 1960, TRUE),
(3, 'The Great Gatsby', 3, ARRAY['Tragedy'], 1925, TRUE),
(4, 'Brave New World', 4, ARRAY['Dystopian', 'Science Fiction'], 1932, TRUE),
(5, 'The Catcher in the Rye', 5, ARRAY['Realist Novel', 'Bildungsroman'], 1951, TRUE),
(6, 'Moby-Dick', 6, ARRAY['Adventure Fiction'], 1851, TRUE),
(7, 'Pride and Prejudice', 7, ARRAY['Romantic Novel'], 1813, TRUE),
(8, 'War and Peace', 8, ARRAY['Historical Novel'], 1869, TRUE),
(9, 'Crime and Punishment', 9, ARRAY['Philosophical Novel'], 1866, TRUE),
(10, 'The Hobbit', 10, ARRAY['Fantasy'], 1937, TRUE);
```

#### Insert Patrons
```sql
INSERT INTO patrons (id, name, email, borrowed_books) VALUES
(1, 'Alice Johnson', 'alice@example.com', ARRAY[]::INT[]),
(2, 'Bob Smith', 'bob@example.com', ARRAY[1, 2]),
(3, 'Carol White', 'carol@example.com', ARRAY[]::INT[]),
(4, 'David Brown', 'david@example.com', ARRAY[3]),
(5, 'Eve Davis', 'eve@example.com', ARRAY[]::INT[]),
(6, 'Frank Moore', 'frank@example.com', ARRAY[4, 5]),
(7, 'Grace Miller', 'grace@example.com', ARRAY[]::INT[]),
(8, 'Hank Wilson', 'hank@example.com', ARRAY[6]),
(9, 'Ivy Taylor', 'ivy@example.com', ARRAY[]::INT[]),
(10, 'Jack Anderson', 'jack@example.com', ARRAY[7, 8]);
```

### Sprint 3: Read Operations (Queries)

#### Get All Books
```sql
-- Basic query
SELECT * FROM books;

#### Get Book by Title
```sql
-- Exact match
SELECT * FROM books WHERE title = 'The Great Gatsby';

-- Case-insensitive search
SELECT * FROM books WHERE LOWER(title) = LOWER('the great gatsby');
```

#### Get All Books by Specific Author
```sql
-- By author ID
SELECT * FROM books WHERE author_id = 1;
```

#### Get All Available Books
```sql
SELECT * FROM books WHERE available = TRUE;
```

### Sprint 4: Update Operations

#### Mark Book as Borrowed
```sql
-- Mark specific book as unavailable
UPDATE books SET available = FALSE WHERE title = '1984';

-- Mark by ID
UPDATE books SET available = FALSE WHERE id = 1;
```

#### Add New Genre to Existing Book
```sql
-- Add genre to book
UPDATE books 
SET genres = array_append(genres, 'Classic Literature') 
WHERE title = '1984';

-- Add multiple genres
UPDATE books 
SET genres = genres || ARRAY['Modern Classic', 'Must Read']
WHERE id = 1;
```

#### Add Borrowed Book to Patron's Record
```sql
-- Add book to patron's borrowed list
UPDATE patrons 
SET borrowed_books = array_append(borrowed_books, 9)
WHERE name = 'Alice Johnson';

-- Add multiple books
UPDATE patrons 
SET borrowed_books = borrowed_books || ARRAY[10]
WHERE email = 'alice@example.com';
```

### Sprint 5: Delete Operations

#### Delete Book by Title
```sql
-- Delete specific book
DELETE FROM books WHERE title = 'The Great Gatsby';
```

#### Delete Author by ID
```sql
-- Delete author (will cascade to books due to foreign key)
DELETE FROM authors WHERE id = 3;
```

### Sprint 6: Advanced Queries

#### Find Books Published After 1950
```sql
SELECT title, published_year FROM books 
WHERE published_year > 1950
ORDER BY published_year;
```

#### Find All American Authors
```sql
SELECT name, birth_year, death_year FROM authors 
WHERE nationality = 'American'
ORDER BY birth_year;
```

#### Set All Books as Available
```sql
UPDATE books SET available = TRUE;

-- Verify the update
SELECT title, available FROM books;
```

#### Find Available Books Published After 1950
```sql
SELECT b.title, a.name as author, b.published_year
FROM books b
JOIN authors a ON b.author_id = a.id
WHERE b.available = TRUE AND b.published_year > 1950
ORDER BY b.published_year DESC;
```

#### Find Authors with "George" in Name
```sql
-- Case-sensitive
SELECT * FROM authors WHERE name LIKE '%George%';

-- Case-insensitive
SELECT * FROM authors WHERE name ILIKE '%george%';
```

#### Increment Published Year 1869 by 1
```sql
-- Update specific year
UPDATE books 
SET published_year = published_year + 1 
WHERE published_year = 1869;

-- Verify the change
SELECT title, published_year FROM books WHERE published_year = 1870;
```

## How to Execute Queries in pgAdmin

### Opening Query Tool
1. **Select LibraryDB** in the browser panel
2. **Click Tools** in the top menu
3. **Select Query Tool** (or use Ctrl+Shift+Q)
4. **New Query Tool window** will open

### Running SQL Commands
1. **Copy and paste** SQL code into the Query Editor
2. **Click the Execute button** or press **F5**
3. **View results** in the Data Output panel below
4. **Check Messages** tab for any errors or confirmations

### pgAdmin Tips
- **Save queries**: File > Save to keep your SQL scripts
- **Multiple queries**: Separate with semicolons, highlight specific query to run just that part
- **Export results**: Right-click on results > Export to save query output
- **View data**: Right-click table > View/Edit Data to browse table contents graphically

This documentation provides a complete reference for the Library Management System. All queries have been tested and are ready for production use.
