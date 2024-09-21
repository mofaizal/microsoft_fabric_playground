
## Dynamic data masking in Fabric data warehousing

Dynamic data masking limits sensitive data exposure by masking it to nonprivileged users. It can be used to greatly simplify the design and coding of security in your application.

Dynamic data masking helps prevent unauthorized viewing of sensitive data by enabling administrators to specify how much sensitive data to reveal, with minimal effect on the application layer. Dynamic data masking can be configured on designated database fields to hide sensitive data in the result sets of queries. 

```SQL

IF NOT EXISTS (SELECT * FROM sys.schemas WHERE name = 'HR')
BEGIN
    EXEC('CREATE SCHEMA HR');
END;
GO

CREATE TABLE HR.Employees (
    EmployeeID INT,
    FirstName VARCHAR(50),
    LastName VARCHAR(50),
    SSN CHAR(12),
    Salary DECIMAL(10, 2),
    Department VARCHAR(50),
    Email VARCHAR(100),
    PhoneNumber VARCHAR(20),
    DateOfBirth DATE,
    Address VARCHAR(300)
);

```

```SQL
INSERT INTO HR.Employees (EmployeeID, FirstName, LastName, SSN, Salary, Department, Email, PhoneNumber, DateOfBirth, Address)
VALUES
  ('1001','அருள்', 'செல்வன்', '123-456-7890', 50000.00, 'Sales', 'arul.selvan@example.com', '+91 1234567890', '1990-01-01', '123 சென்னை மேற்கு தெரு, சென்னை, தமிழ்நாடு 600001'),
  ('1002','பாரதி', 'தேவி', '987-654-3211', 60000.00, 'Marketing', 'bharathi.devi@example.com', '+91 9876543211', '1991-02-02', '456 கோயம்புத்தூர் தெற்கு தெரு, கோயம்புத்தூர், தமிழ்நாடு 641001'),
  ('1003','கவிதா', 'மூர்த்தி', '555-123-4561', 70000.00, 'Finance', 'kavitha.murthy@example.com', '+91 5551234561', '1992-03-03', '789 மதுரை கிழக்கு தெரு, மதுரை, தமிழ்நாடு 625001'),
  ('1004','சக்தி', 'வேல்', '444-555-6661', 80000.00, 'IT', 'sakthi.vel@example.com', '+91 4445556661', '1993-04-04', '1011 திருச்சி மேற்கு தெரு, திருச்சி, தமிழ்நாடு 620001'),
  ('1005','ஆதித்யா', 'குமார்', '123-456-7891', 50000.00, 'Sales', 'adithya.kumar@example.com', '+91 1234567891', '1994-05-05', '1234 சேலம் கிழக்கு தெரு, சேலம், தமிழ்நாடு 636001'),
  ('1006','ஜெயா', 'லக்ஷ்மி', '987-654-3212', 60000.00, 'Marketing', 'jaya.lakshmi@example.com', '+91 9876543212', '1995-06-06', '456 நீலகிரி மேற்கு தெரு, உதகமண்டலம், தமிழ்நாடு 643230'),
  ('1007','மகேஷ்', 'பாண்டியன்', '555-123-4562', 70000.00, 'Finance', 'mahesh.pandian@example.com', '+91 5551234562', '1996-07-07', '789 காஞ்சிபுரம் கிழக்கு தெரு, காஞ்சிபுரம், தமிழ்நாடு 631501'),
  ('1008','பிரியா', 'சங்கர்', '444-555-6662', 80000.00, 'IT', 'priya.shankar@example.com', '+91 4445556662', '1997-08-08', '1011 தஞ்சாவூர் மேற்கு தெரு, தஞ்சாவூர், தமிழ்நாடு 613001'),
  ('1009','விஜய்', 'சேது', '123-456-7892', 50000.00, 'Sales', 'vijay.seth@example.com', '+91 1234567892', '1998-09-09', '1234 திருநெல்வேலி கிழக்கு தெரு, திருநெல்வேலி, தமிழ்நாடு 627001'),
  ('1010','மீனாட்சி', 'சரவணன்', '987-654-3213', 60000.00, 'Marketing', 'meenakshi.saravanan@example.com', '+91 9876543213', '1999-10-10', '456 திருப்பூர் தெற்கு தெரு, திருப்பூர், தமிழ்நாடு 641601'),
  ('1011','அரவிந்த்', 'குமாரசாமி', '555-123-4563', 70000.00, 'Finance', 'aravind.kumarasamy@example.com', '+91 5551234563', '2000-11-11', '789 ஈரோடு கிழக்கு தெரு, ஈரோடு, தமிழ்நாடு 638001'),
  ('1012','தீபிகா', 'கிருஷ்ணன்', '444-555-6663', 80000.00, 'IT', 'deepika.krishnan@example.com', '+91 4445556663', '2001-12-12', '1011 கரூர் மேற்கு தெரு, கரூர், தமிழ்நாடு 639001'),
  ('1013','கிருஷ்ணன்', 'ராமசாமி', '123-456-7893', 50000.00, 'Sales', 'krishnan.ramasamy@example.com', '+91 1234567893', '2002-01-01', '1234 ராமநாதபுரம் கிழக்கு தெரு, ராமநாதபுரம், தமிழ்நாடு 623501'),
  ('1014','பிரியங்கா', 'சந்திரசேகரன்', '987-654-3214', 60000.00, 'Marketing', 'priyanka.chandrasekaran@example.com', '+91 9876543214', '2003-02-02', '456 நாகப்பட்டினம் தெற்கு தெரு, நாகப்பட்டினம், தமிழ்நாடு 611101'),
  ('1015','அஜித்', 'ராஜா', '555-123-4564', 70000.00, 'Finance', 'ajith.raja@example.com', '+91 5551234564', '2004-03-03', '789 வேலூர் கிழக்கு தெரு, வேலூர், தமிழ்நாடு 632001'),
  ('1016','மஞ்சு', 'கோவிந்தன்', '444-555-6664', 80000.00, 'IT', 'manju.govindan@example.com', '+91 4445556664', '2005-04-04', '1011 விருதுநகர் மேற்கு தெரு, விருதுநகர், தமிழ்நாடு 626201'),
  ('1017','கார்த்திக்', 'சுப்பிரமணியன்', '123-456-7894', 50000.00, 'Sales', 'karthik.subramaniam@example.com', '+91 1234567894', '2006-05-05', '1234 திண்டுக்கல் கிழக்கு தெரு, திண்டுக்கல், தமிழ்நாடு 624001')

```

```SQL
 SELECT * FROM HR.Employees;
```

Apply Dynamic Data Masking to Sensitive Columns

```SQL
ALTER TABLE HR.Employees
ALTER COLUMN SSN ADD MASKED WITH (FUNCTION = 'partial(5,"XXXXXXX",0)');
```


```SQL 
ALTER TABLE HR.Employees
ALTER COLUMN Salary ADD MASKED WITH (FUNCTION = 'random(1, 12)');
```

Security consideration: bypassing masking using inference or brute-force techniques

```SQL
SELECT  FirstName, Salary FROM HR.Employees
WHERE Salary > 9999 and Salary < 100001;

```
