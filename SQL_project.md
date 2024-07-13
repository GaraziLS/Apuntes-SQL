## Creating the schema

We'll first create a schema for the project. We must build a database that keeps track of students, courses, grades and teachers.

To create the schema, we'll type this:

```
CREATE SCHEMA `db_project` ;
```
## Creating the tables

Then we'll create the student table. This will be the main table, the table that holds the others. So after applying our values, we get this:

**The students table**

```
CREATE TABLE `db_project`.`project_students` (
  `student_id` INT NOT NULL AUTO_INCREMENT,
  `student_name` VARCHAR(45) NOT NULL,
  PRIMARY KEY (`student_id`),
  UNIQUE INDEX `students_id_UNIQUE` (`student_id` ASC) VISIBLE);
```

**The courses table**

```
CREATE TABLE `db_project`.`project_courses` (
  `course_id` INT NOT NULL AUTO_INCREMENT,
  `course_name` VARCHAR(45) NOT NULL,
  `course_teachers_id` INT NOT NULL,
  PRIMARY KEY (`course_id`),
  UNIQUE INDEX `course_id_UNIQUE` (`course_id` ASC) VISIBLE);

```

**The teachers table**

```
CREATE TABLE `db_project`.`project_teachers` (
  `teacher_id` INT NOT NULL AUTO_INCREMENT,
  `teacher_name` VARCHAR(45) NOT NULL,
  PRIMARY KEY (`teacher_id`),
  UNIQUE INDEX `teacher_id_UNIQUE` (`teacher_id` ASC) VISIBLE);
```

**The grades table**

```
CREATE TABLE `db_project`.`project_grades` (
  `grade_id` INT NOT NULL AUTO_INCREMENT,
  `grade_courses_id` INT NOT NULL,
  `grade_students_id` INT NOT NULL,
  `grade_value` DECIMAL NOT NULL,
  PRIMARY KEY (`grade_id`),
  UNIQUE INDEX `grade_id_UNIQUE` (`grade_id` ASC) VISIBLE);
```

## Creating the foreign keys

We'll now connect the tables. To do so, we'll create foreign keys following this structure (we're in the courses table):

```
ALTER TABLE `db_project`.`project_courses` 
ADD INDEX `course_teachers_id_idx` (`course_teachers_id` ASC) VISIBLE;
;
ALTER TABLE `db_project`.`project_courses` 
ADD CONSTRAINT `course_teachers_id`
  FOREIGN KEY (`course_teachers_id`)
  REFERENCES `db_project`.`project_teachers` (`teacher_id`)
  ON DELETE CASCADE
  ON UPDATE NO ACTION;
```

And to finish this off, the grades table:

```
ALTER TABLE `db_project`.`project_grades` 
ADD INDEX `grade_courses_id_idx` (`grade_courses_id` ASC) VISIBLE,
ADD INDEX `grade_students_id_idx` (`grade_students_id` ASC) VISIBLE;
;
ALTER TABLE `db_project`.`project_grades` 
ADD CONSTRAINT `grade_courses_id`
  FOREIGN KEY (`grade_courses_id`)
  REFERENCES `db_project`.`project_grades` (`course_id`)
  ON DELETE CASCADE
  ON UPDATE NO ACTION,
ADD CONSTRAINT `grade_students_id`
  FOREIGN KEY (`grade_students_id`)
  REFERENCES `db_project`.`project_students` (`student_id`)
  ON DELETE CASCADE
  ON UPDATE NO ACTION;

```

Here's how our database looks like:

![visual database](https://i.postimg.cc/xTcQJFnC/db-project-finished.png)

## Creating a script that populates all the tables

We'll now populate each table.

> Note: Don't create foreign keys before populating data, because if done before, if table A has data but table B and C don't, and table A is linked to the other two, issues will occur, because we're populating data with data that comes from a table that doesn't have data yet, and issues will happen.

> Note 2: CREATE ALL THE FOREIGN KEYS AT ONCE. IF YOU TRY TO ADD SOME LATER, ISSUES WILL HAPPEN.

> Note 3: You can't populate a row with two values. This:

```
INSERT INTO project_courses(course_name, course_teachers_id)
VALUES ("Language", ("2", "4", "6"));
```

Doesn't work. Instead, do this:

```
INSERT INTO project_courses(course_name)
VALUES ("Art");

INSERT INTO project_courses(course_name)
VALUES ("Language");

INSERT INTO project_courses(course_name)
VALUES ("Math");

INSERT INTO project_courses(course_name)
VALUES ("Math");

INSERT INTO project_courses(course_name)
VALUES ("Science");

```

Now we'll populate the students' table:

**Students table**

```
USE db_project;

INSERT INTO project_students(student_name)
VALUES ("Jane Doe");

INSERT INTO project_students(student_name)
VALUES ("John Doe");

INSERT INTO project_students(student_name)
VALUES ("Mary Sue");

INSERT INTO project_students(student_name)
VALUES ("Gary Stu");

INSERT INTO project_students(student_name)
VALUES ("Mister Nobody");

INSERT INTO project_students(student_name)
VALUES ("Random Person");
```

**Teachers table**

```
INSERT INTO project_teachers(teacher_name)
VALUES ("Minerva");

INSERT INTO project_teachers(teacher_name)
VALUES ("Albus");

INSERT INTO project_teachers(teacher_name)
VALUES ("Severus");

INSERT INTO project_teachers(teacher_name)
VALUES ("Remus");
```


**grades table**

```
INSERT INTO project_grades(grade_courses_id, grade_students_id, grade_teachers_id, grade_value)
VALUES ("1", "1", "1", "5.20");

INSERT INTO project_grades(grade_courses_id, grade_students_id, grade_teachers_id, grade_value)
VALUES ("2", "3", "2", "7.00");

INSERT INTO project_grades(grade_courses_id, grade_students_id, grade_teachers_id, grade_value)
VALUES ("3", "5", "3", "10.00");



INSERT INTO project_grades(grade_courses_id, grade_students_id, grade_teachers_id, grade_value)
VALUES ("4", "2", "4", "3.75");

INSERT INTO project_grades(grade_courses_id, grade_students_id, grade_teachers_id, grade_value)
VALUES ("5", "4", "5", "5.00");

INSERT INTO project_grades(grade_courses_id, grade_students_id, grade_teachers_id, grade_value)
VALUES ("6", "6", "6", "6.75");




INSERT INTO project_grades(grade_courses_id, grade_students_id, grade_teachers_id, grade_value)
VALUES ("7", "1", "7", "0.00");

INSERT INTO project_grades(grade_courses_id, grade_students_id, grade_teachers_id, grade_value)
VALUES ("8", "2", "8", "7.50");

INSERT INTO project_grades(grade_courses_id, grade_students_id, grade_teachers_id, grade_value)
VALUES ("9", "3", "9", "0.00");



INSERT INTO project_grades(grade_courses_id, grade_students_id, grade_teachers_id, grade_value)
VALUES ("10", "4", "10", "7.50");

INSERT INTO project_grades(grade_courses_id, grade_students_id, grade_teachers_id, grade_value)
VALUES ("11", "5", "11", "8.00");

INSERT INTO project_grades(grade_courses_id, grade_students_id, grade_teachers_id, grade_value)
VALUES ("12", "6", "12", "10.00");
```

## Generating Foreign Keys

We now need to connect the tables between them. To do so, we'll create Foreign Keys.

> Create the keys in the same table(s), not bidirectionally. Also don't create a FK in a table that doesn't have another point of connection to other tables (if courses have connections to teachers but grades don't, you can't connect grades to teachers, for example).

> You can join a table that doesn't have a FK (teachers) by the FK that points to it (grade_teachers_id points to teacher_id).


**Grades table**

```
ALTER TABLE `db_project`.`project_grades` 
ADD CONSTRAINT `grade_courses_id`
  FOREIGN KEY (`grade_courses_id`)
  REFERENCES `db_project`.`project_courses` (`course_id`)
  ON DELETE NO ACTION
  ON UPDATE CASCADE,
ADD CONSTRAINT `grade_students_id`
  FOREIGN KEY (`grade_students_id`)
  REFERENCES `db_project`.`project_students` (`student_id`)
  ON DELETE NO ACTION
  ON UPDATE CASCADE,
ALTER TABLE `db_project`.`project_grades` 
ADD COLUMN `grade_teachers_id` INT NOT NULL AFTER `grade_students_id`,
ADD INDEX `grade_teachers_id_idx` (`grade_teachers_id` ASC) VISIBLE;
;
ALTER TABLE `db_project`.`project_grades` 
ADD CONSTRAINT `grade_teachers_id`
  FOREIGN KEY (`grade_teachers_id`)
  REFERENCES `db_project`.`project_teachers` (`teacher_id`)
  ON DELETE NO ACTION
  ON UPDATE CASCADE;
```

## Calculating the average grade given by each teacher

Once we have the foreign keys done, we have to calculate some things. An average is equal to the sum of all the grades' value divided by the number of items (grades). We can use the Average function, and filter by each teacher_grade_id, to get the average:

```
SELECT t.teacher_name, avg(g.grade_value) as average_grade
FROM project_grades g
JOIN project_teachers t 
ON g.grade_teachers_id = t.teacher_id
group by g.grade_teachers_id;
```

## Calculating the top grades for each student

To calculate which grades are the best among the students, we can join the grades and the names in a query to see the data better:

```
use db_project;

SELECT *
FROM project_students s 
JOIN project_grades g
ON g.grade_students_id = s.student_id;
```

Now, we only have to query for the highest value for each student, and query them separately to get the top value:

```
SELECT s.student_name, max(g.grade_value)
FROM project_students s
JOIN project_grades g
ON g.grade_students_id = s.student_id
group by s.student_name
```

## Sorting students by the courses they're rolled in

This code below will sort the courses by name and show which students are enrolled in them:

```
use db_project;

SELECT course_name, student_name
FROM project_grades g 
JOIN project_students s
ON g.grade_students_id = s.student_id
JOIN project_courses c 
ON g.grade_courses_id = c.course_id
order by c.course_id asc
```

> If you have to join two tables together and they don't have Foreign Keys, call the table that has the FKs in the FROM clause. Then join the other tables by the Foreign Keys that point to those tables.

## Summary report of courses and average grades

We now want to get the average grade of each course. We'll select the courses and do a calculated field to get the average value, will import it from grades, and then we'll do the course joining. Finally, we'll group by course name and weill order the averages.

```
SELECT 
	c.course_name,
  avg(grade_value) as grade_avg
FROM project_grades g
JOIN project_courses c on g.grade_courses_id = c.course_id
GROUP BY course_name
ORDER BY grade_avg asc
```

## Finding which student and professor have the most courses in common

We'll use a count function to count which teachers and student have most courses in common and then will group/order everything:

```
SELECT s.student_name, t.teacher_name, c.course_name,
COUNT(DISTINCT c.course_id) AS common
FROM project_grades g
JOIN project_students s
ON g.grade_students_id = s.student_id
JOIN project_teachers t
ON g.grade_courses_id = t.teacher_id
JOIN project_courses c
ON g.grade_courses_id = c.course_id
GROUP BY s.student_name, t.teacher_name, course_name
ORDER BY common DESC
```