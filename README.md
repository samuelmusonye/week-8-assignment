-- Create the database for the Library Management System
CREATE DATABASE LibraryManagement;
USE LibraryManagement;

-- Table: Publishers
-- Stores publisher information (One-to-Many with Books)
CREATE TABLE Publishers (
    publisher_id INT AUTO_INCREMENT PRIMARY KEY,
    publisher_name VARCHAR(100) NOT NULL,
    address VARCHAR(255),
    UNIQUE (publisher_name) -- Ensure publisher names are unique
);

-- Table: Books
-- Stores book details, linked to a publisher
CREATE TABLE Books (
    book_id INT AUTO_INCREMENT PRIMARY KEY,
    isbn VARCHAR(13) NOT NULL UNIQUE, -- ISBN is unique
    title VARCHAR(255) NOT NULL,
    publication_year INT,
    publisher_id INT,
    FOREIGN KEY (publisher_id) REFERENCES Publishers(publisher_id) ON DELETE SET NULL, -- One-to-Many
    CHECK (publication_year > 0) -- Ensure valid publication year
);

-- Table: Authors
-- Stores author information
CREATE TABLE Authors (
    author_id INT AUTO_INCREMENT PRIMARY KEY,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    nationality VARCHAR(50)
);

-- Table: Book_Authors
-- Junction table for Many-to-Many relationship between Books and Authors
CREATE TABLE Book_Authors (
    book_id INT,
    author_id INT,
    PRIMARY KEY (book_id, author_id), -- Composite primary key
    FOREIGN KEY (book_id) REFERENCES Books(book_id) ON DELETE CASCADE,
    FOREIGN KEY (author_id) REFERENCES Authors(author_id) ON DELETE CASCADE
);

-- Table: Members
-- Stores library member information
CREATE TABLE Members (
    member_id INT AUTO_INCREMENT PRIMARY KEY,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    join_date DATE NOT NULL,
    status ENUM('active', 'inactive') DEFAULT 'active'
);

-- Table: Member_Contacts
-- Stores contact details for members (One-to-One with Members)
CREATE TABLE Member_Contacts (
    member_id INT PRIMARY KEY, -- One-to-One relationship
    email VARCHAR(100) NOT NULL UNIQUE, -- Unique email
    phone VARCHAR(15),
    address VARCHAR(255),
    FOREIGN KEY (member_id) REFERENCES Members(member_id) ON DELETE CASCADE
);

-- Table: Loans
-- Tracks books borrowed by members (One-to-Many from Members and Books)
CREATE TABLE Loans (
    loan_id INT AUTO_INCREMENT PRIMARY KEY,
    book_id INT,
    member_id INT,
    loan_date DATE NOT NULL,
    return_date DATE,
    actual_return_date DATE,
    FOREIGN KEY (book_id) REFERENCES Books(book_id) ON DELETE CASCADE,
    FOREIGN KEY (member_id) REFERENCES Members(member_id) ON DELETE CASCADE,
    CHECK (return_date IS NULL OR return_date >= loan_date), -- Ensure return_date is valid
    CHECK (actual_return_date IS NULL OR actual_return_date >= loan_date) -- Ensure actual_return_date is valid
);
