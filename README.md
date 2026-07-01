# SQL-11
CREATE TABLE departments (
    id SERIAL PRIMARY KEY,
    name VARCHAR(50)
);

CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    username VARCHAR(50),
    email VARCHAR(100),
    age INT,
    status VARCHAR(20),
    department_id INT
);

ALTER TABLE users ADD CONSTRAINT fk_users_department FOREIGN KEY (department_id) REFERENCES departments(id);

INSERT INTO departments (name) 
SELECT 'Department ' || i FROM generate_series(1, 10);

INSERT INTO users (username, email, age, status, department_id)
SELECT 
    'user_' || i,
    'Email_' || i || '@Example.com', 
    (random() * 60 + 18)::INT,       
    CASE WHEN i % 2 = 0 THEN 'active' ELSE 'inactive' END,
    (random() * 9 + 1)::INT           
FROM generate_series(1, 100000) AS i;

EXPLAIN SELECT * FROM users WHERE email = 'Email_50000@Example.com';

CREATE INDEX idx_users_email ON users(email);

EXPLAIN ANALYZE SELECT * FROM users WHERE email = 'Email_50000@Example.com';

CREATE INDEX idx_users_age_status ON users(age, status);

EXPLAIN ANALYZE SELECT * FROM users WHERE age = 25 AND status = 'active';

CREATE INDEX idx_users_lower_email ON users(LOWER(email));

EXPLAIN ANALYZE SELECT * FROM users WHERE LOWER(email) = 'email_50000@example.com';

CREATE INDEX idx_users_active_only ON users(id) WHERE status = 'active';

EXPLAIN ANALYZE SELECT * FROM users WHERE id = 50000 AND status = 'active';

CREATE INDEX idx_users_department_id ON users(department_id);

EXPLAIN ANALYZE 
SELECT u.*, d.name 
FROM users u 
JOIN departments d ON u.department_id = d.id 
WHERE u.department_id = 5;
