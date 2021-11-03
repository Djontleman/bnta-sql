# Exercise Solutions
In collaboration with Anand, our solutions to the tasks

## Setting Up Database
* Draw an entity relationship diagram to show the structure of the tables and the relationships between them. Each table should have enough columns to capture all the data shown in the JSON above.
* Set up the tables in a postgres database. You can set them up using the psql REPL, a GUI like Postico or PGAdmin or by writing an SQL file like the one in the previous task.
* Populate the tables with some of your own data (you don't need to use more cereal mascots, unless you want to). Don't worry about the capacity restriction on enclosures for now, checking the would be handled by the back-end before the data gets sent to the database.

```sql
CREATE DATABASE safari;

\c safari

CREATE TABLE enclosure(id SERIAL PRIMARY KEY, name VARCHAR(255), capacity INT, closedForMaintenance BOOLEAN);

CREATE TABLE animal(id SERIAL PRIMARY KEY, name VARCHAR(255), type VARCHAR(255), age INT, enclosure_id INT REFERENCES enclosure(id));


INSERT INTO enclosure (name, capacity, closedForMaintenance) VALUES (‘big cat field’, 20, false);

INSERT INTO animal (name, type, age, enclosure_id) VALUES (‘Tony’, ‘Tiger’, 59, 1);

SELECT * FROM animal INNER JOIN enclosure ON animal.enclosure_id = enclosure.id;

CREATE TABLE staff(id SERIAL PRIMARY KEY, name VARCHAR(255), employeeNumber INT);

CREATE TABLE assignment(id SERIAL PRIMARY KEY, employee_id INT REFERENCES staff(id), enclosure_id INT REFERENCES enclosure(id), day VARCHAR(255));

INSERT INTO staff (name, employeeNumber) VALUES (‘Captain Rik’, 12345);

INSERT INTO assignment (employee_id, enclosure_id, day) VALUES (1, 1, ‘Tuesday’);

```

## Main Exercises
Write queries to find:
1) The names of the animals in a given enclosure
```sql
SELECT animal.name FROM animal INNER JOIN enclosure ON animal.enclosure_id = enclosure.id WHERE enclosure.id = 1;

```
2) The names of the staff working in a given enclosure
```sql
SELECT s.name FROM staff as s
INNER JOIN assignment AS a
ON a.employee_id = s.id
INNER JOIN enclosure as e
	ON e.id = a.enclosure_id
WHERE e.id = 1;

```

## Extensions
Additional setup:
```sql
INSERT INTO enclosure (name, capacity, closedForMaintenance) VALUES (‘penguinEnclosure’, 1000, true);

INSERT INTO animal (name, type, age, enclosure_id) VALUES (‘Alex’, ‘Lion’, 27, 1);

INSERT INTO animal (name, type, age, enclosure_id) VALUES (‘Skipper’, ‘Penguin’, 10, 2);
INSERT INTO animal (name, type, age, enclosure_id) VALUES (‘Rico’, ‘Penguin’, 9, 2);
INSERT INTO animal (name, type, age, enclosure_id) VALUES (‘Private’, ‘Penguin’, 7, 2);
INSERT INTO animal (name, type, age, enclosure_id) VALUES (‘Kowalski’, ‘Penguin’, 4, 2);

INSERT INTO staff (name, employeeNumber) VALUES (‘some guy’, 42);
INSERT INTO staff (name, employeeNumber) VALUES (‘Steve Person’, 784);

INSERT INTO assignment (employee_id, enclosure_id, day) VALUES (2, 1, ‘Wednesday’);
INSERT INTO assignment (employee_id, enclosure_id, day) VALUES (3, 2, ‘Thursday’);

```

Write queries to find:
1) The names of staff working in enclosures which are closed for maintenance
```sql
SELECT s.name FROM staff as s
INNER JOIN assignment AS a
ON a.employee_id = s.id
INNER JOIN enclosure as e
	ON e.id = a.enclosure_id
WHERE e.closedForMaintenance = true;

```
2) The name of the enclosure where the oldest animal lives. If there are two animals who are the same age choose the first one alphabetically.
```sql
SELECT enclosure.name FROM animal 
INNER JOIN enclosure 
ON animal.enclosure_id = enclosure.id 
ORDER BY animal.age DESC, animal.name
LIMIT 1;

```
3) The number of different animal types a given keeper has been assigned to work with.
```sql
SELECT COUNT(DISTINCT animal.type)
FROM staff as s
INNER JOIN assignment AS a
ON a.employee_id = s.id
INNER JOIN enclosure as e
	ON e.id = a.enclosure_id
INNER JOIN animal 
	ON animal.enclosure_id = e.id
WHERE s.id = 3;

```
4) The number of different keepers who have been assigned to work in a given enclosure
```sql
SELECT COUNT(DISTINCT s)
FROM staff as s
INNER JOIN assignment AS a
ON a.employee_id = s.id
INNER JOIN enclosure as e
	ON e.id = a.enclosure_id
WHERE e.id = 2;

```
5) The names of the other animals sharing an enclosure with a given animal (eg. find the names of all the animals sharing the big cat field with Tony)
```sql
SELECT a.name FROM animal AS a 
INNER JOIN enclosure 
ON a.enclosure_id = enclosure.id
INNER JOIN animal AS b
ON enclosure.id = b.enclosure_id 
WHERE b.name = ‘Tony’ AND a.name != ‘Tony’;

SELECT a.name FROM animal AS a 
INNER JOIN enclosure 
ON a.enclosure_id = enclosure.id
INNER JOIN animal AS b
ON enclosure.id = b.enclosure_id 
WHERE b.name = ‘Skipper’ AND a.name != ‘Skipper’;

```
Alternative:
```sql
SELECT a.name FROM
	(
SELECT e.id FROM enclosure as e
	INNER JOIN animal AS an
		ON an.enclosure_id = e.id
	WHERE an.name = ‘Tony’
) AS ab
INNER JOIN animal a
	ON a.enclosure_id = ab.id
WHERE a.name != ‘Tony’;

```