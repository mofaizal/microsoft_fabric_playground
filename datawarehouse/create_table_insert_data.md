
## Ralph Kimball's Star Schema and the Provided Data Model

Ralph Kimball is a renowned data warehousing expert who popularized the star schema model. This model is widely used due to its simplicity, performance, and ease of understanding.

To build a Power BI data modeling presentation using the Kimball methodology and focusing on student data, here's a structured approach that you can follow for your slide content. 


## Create Studnet DIM and Fact Table

```SQL 

CREATE TABLE dim_student (
    student_id INTEGER NOT NULL, -- Surrogate Key
    student_roll_number VARCHAR(50), -- Alternate Key (from source system)
    student_name VARCHAR(100),
    gender VARCHAR(10),
    birth_date DATE
);


CREATE TABLE dim_school (
    school_id INTEGER NOT NULL, -- Surrogate Key
    school_code VARCHAR(50), -- Alternate Key (from source system)
    school_name VARCHAR(150),
    district VARCHAR(100),
    state VARCHAR(100)
);


CREATE TABLE dim_subject (
    subject_id INTEGER NOT NULL, -- Surrogate Key
    subject_code VARCHAR(50), -- Alternate Key (from source system)
    subject_name VARCHAR(100)
);


CREATE TABLE dim_teacher (
    teacher_id INTEGER NOT NULL,  -- Surrogate key
    teacher_name VARCHAR(100),
    subject VARCHAR(100),
    experience_years INTEGER,
    school_id INTEGER -- Foreign key from the school dimension
);


CREATE TABLE fact_exam_results (
    student_id INTEGER NOT NULL,
    teacher_id INTEGER,
    subject VARCHAR(100),
    exam_date DATE,
    marks INTEGER,
    school_id INTEGER,
    pass_mark VARCHAR(50) -- 'Pass' or 'Fail' based on a rule
);

```

## Insert Data into Tables 

```SQL

INSERT INTO dim_student (student_id, student_roll_number, student_name, gender, birth_date) VALUES
(1, 'SR001', 'Amit Kumar', 'Male', '2008-01-15'),
(2, 'SR002', 'Priya Sharma', 'Female', '2007-04-21'),
(3, 'SR003', 'Rahul Singh', 'Male', '2009-07-30'),
(4, 'SR004', 'Sneha Iyer', 'Female', '2008-03-11'),
(5, 'SR005', 'Vikram Reddy', 'Male', '2007-09-18');


INSERT INTO dim_school (school_id, school_code, school_name, district, state) VALUES
(1, 'SC001', 'Chennai Public School', 'Chennai', 'Tamil Nadu'),
(2, 'SC002', 'Coimbatore High School', 'Coimbatore', 'Tamil Nadu'),
(3, 'SC003', 'Madurai Matriculation School', 'Madurai', 'Tamil Nadu');


INSERT INTO dim_subject (subject_id, subject_code, subject_name) VALUES
(1, 'SUB001', 'Mathematics'),
(2, 'SUB002', 'Science'),
(3, 'SUB003', 'English'),
(4, 'SUB004', 'History');


INSERT INTO dim_teacher (teacher_id, teacher_name, subject, experience_years, school_id) VALUES
(1, 'Mr. Anil Kumar', 'Mathematics', 10, 1),
(2, 'Ms. Lakshmi Nair', 'Science', 8, 1),
(3, 'Mr. Ramesh Babu', 'English', 12, 2),
(4, 'Ms. Supriya Devi', 'History', 6, 3);


INSERT INTO fact_exam_results (student_id, teacher_id, subject, exam_date, marks, school_id, pass_mark) VALUES
(1, 1, 'Mathematics', '2023-04-10', 75, 1, 'Pass'),
(2, 2, 'Science', '2023-04-11', 55, 1, 'Pass'),
(3, 3, 'English', '2023-04-12', 40, 2, 'Pass'),
(4, 4, 'History', '2023-04-13', 32, 3, 'Fail'),
(5, 1, 'Mathematics', '2023-04-14', 65, 1, 'Pass');

```

## Create View with Inner Join 

```SQL

CREATE VIEW vw_student_exam_results AS
SELECT
    s.student_name,
    sch.school_name,
    t.teacher_name,
    sub.subject_name,
    f.marks,
    f.exam_date,
    f.pass_mark
FROM fact_exam_results f
INNER JOIN dim_student s ON f.student_id = s.student_id
INNER JOIN dim_school sch ON f.school_id = sch.school_id
INNER JOIN dim_teacher t ON f.teacher_id = t.teacher_id
INNER JOIN dim_subject sub ON t.subject = sub.subject_name;


CREATE VIEW vw_all_students_results AS
SELECT
    s.student_name,
    sch.school_name,
    t.teacher_name,
    sub.subject_name,
    COALESCE(f.marks, 0) AS marks,
    f.exam_date,
    COALESCE(f.pass_mark, 'Not Attempted') AS pass_mark
FROM dim_student s
LEFT JOIN fact_exam_results f ON s.student_id = f.student_id
LEFT JOIN dim_school sch ON f.school_id = sch.school_id
LEFT JOIN dim_teacher t ON f.teacher_id = t.teacher_id
LEFT JOIN dim_subject sub ON t.subject = sub.subject_name;


SELECT student_name, school_name, teacher_name, subject_name, marks, exam_date, pass_mark
FROM vw_student_exam_results
ORDER BY exam_date DESC;

```

