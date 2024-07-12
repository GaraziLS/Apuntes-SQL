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

The students will have courses, that in turn will have grades. Courses also have teachers, and teachers have both courses and students, and also give grades that belong to students. Knowing this, we'll create the other three tables, that will have several foreign keys to connect between themselves:

**The courses table**

```
CREATE TABLE `db_project`.`project_courses` (
  `course_id` INT NOT NULL AUTO_INCREMENT,
  `course_name` VARCHAR(45) NOT NULL,
  `course_students_id` INT NOT NULL,
  `course_teachers_id` INT NOT NULL,
  PRIMARY KEY (`course_id`),
  UNIQUE INDEX `course_id_UNIQUE` (`course_id` ASC) VISIBLE);

```

**The teachers table**

```
CREATE TABLE `db_project`.`project_teachers` (
  `teacher_id` INT NOT NULL AUTO_INCREMENT,
  `teacher_name` VARCHAR(45) NOT NULL,
  `teacher_courses_id` INT NOT NULL,
  `teacher_students_id` INT NOT NULL,
  `teacher_grades_id` INT NOT NULL,
  PRIMARY KEY (`teacher_id`),
  UNIQUE INDEX `teacher_id_UNIQUE` (`teacher_id` ASC) VISIBLE);
```

**The grades table**

```
CREATE TABLE `db_project`.`project_grades` (
  `grade_id` INT NOT NULL AUTO_INCREMENT,
  `grade_courses_id` INT NOT NULL,
  `grade_teachers_id` INT NOT NULL,
  `grade_students_id` INT NOT NULL,
  `grade_value` DECIMAL NOT NULL,
  PRIMARY KEY (`grade_id`),
  UNIQUE INDEX `grade_id_UNIQUE` (`grade_id` ASC) VISIBLE);
```

## Creating the foreign keys

We'll now connect the tables. To do so, we'll create foreign keys following this structure (we're in the courses table):

```
ALTER TABLE `db_project`.`project_courses` 
ADD INDEX `course_students_id_idx` (`course_students_id` ASC) VISIBLE;
;
ALTER TABLE `db_project`.`project_courses` 
ADD CONSTRAINT `course_students_id`
  FOREIGN KEY (`course_students_id`)
  REFERENCES `db_project`.`project_students` (`student_id`)
  ON DELETE NO ACTION
  ON UPDATE NO ACTION;
ALTER TABLE `db_project`.`project_courses` 
ADD INDEX `course_teachers_id_idx` (`course_teachers_id` ASC) VISIBLE,
ADD INDEX `course_grades_id_idx` (`course_grades_id` ASC) VISIBLE;
;
ALTER TABLE `db_project`.`project_courses` 
ADD CONSTRAINT `course_teachers_id`
  FOREIGN KEY (`course_teachers_id`)
  REFERENCES `db_project`.`project_teachers` (`teacher_id`)
  ON DELETE NO ACTION
  ON UPDATE NO ACTION,
ADD CONSTRAINT `course_grades_id`
  FOREIGN KEY (`course_grades_id`)
  REFERENCES `db_project`.`project_grades` (`grade_id`)
  ON DELETE NO ACTION
  ON UPDATE NO ACTION;
```

Another example, with the teachers table:

```
ALTER TABLE `db_project`.`project_teachers` 
ADD INDEX `teacher_courses_id_idx` (`teacher_courses_id` ASC) VISIBLE,
ADD INDEX `teacher_students_id_idx` (`teacher_students_id` ASC) VISIBLE,
ADD INDEX `teacher_grades_id_idx` (`teacher_grades_id` ASC) VISIBLE;
;
ALTER TABLE `db_project`.`project_teachers` 
ADD CONSTRAINT `teacher_courses_id`
  FOREIGN KEY (`teacher_courses_id`)
  REFERENCES `db_project`.`project_courses` (`course_id`)
  ON DELETE NO ACTION
  ON UPDATE NO ACTION,
ADD CONSTRAINT `teacher_students_id`
  FOREIGN KEY (`teacher_students_id`)
  REFERENCES `db_project`.`project_students` (`student_id`)
  ON DELETE NO ACTION
  ON UPDATE NO ACTION,
ADD CONSTRAINT `teacher_grades_id`
  FOREIGN KEY (`teacher_grades_id`)
  REFERENCES `db_project`.`project_grades` (`grade_id`)
  ON DELETE NO ACTION
  ON UPDATE NO ACTION;
```

And to finish this off, the grades table:

```
ALTER TABLE `db_project`.`project_grades` 
ADD INDEX `grade_courses_id_idx` (`grade_courses_id` ASC) VISIBLE,
ADD INDEX `grade_students_id_idx` (`grade_students_id` ASC) VISIBLE,
ADD INDEX `grade_teachers_id_idx` (`grade_teachers_id` ASC) VISIBLE;
;
ALTER TABLE `db_project`.`project_grades` 
ADD CONSTRAINT `grade_courses_id`
  FOREIGN KEY (`grade_courses_id`)
  REFERENCES `db_project`.`project_courses` (`course_id`)
  ON DELETE NO ACTION
  ON UPDATE NO ACTION,
ADD CONSTRAINT `grade_students_id`
  FOREIGN KEY (`grade_students_id`)
  REFERENCES `db_project`.`project_students` (`student_id`)
  ON DELETE NO ACTION
  ON UPDATE NO ACTION,
ADD CONSTRAINT `grade_teachers_id`
  FOREIGN KEY (`grade_teachers_id`)
  REFERENCES `db_project`.`project_teachers` (`teacher_id`)
  ON DELETE NO ACTION
  ON UPDATE NO ACTION;

```

Here's how our database looks like:

![visual database](https://i.postimg.cc/JnqcDHK1/Captura-de-pantalla-2024-07-11-105535.png)

## Creating a script that populates all the tables

We'll now populate each table.

> Note: Don't create foreign keys before populating data, because if done before, if table A has data but table B and C don't, and table A is linked to the other two, issues will occur, because we're populating data with data that comes from a table that doesn't have data yet, and issues will happen.

> Note 2: CREATE ALL THE FOREIGN KEYS AT ONCE. IF YOU TRY TO ADD SOME LATER, ISSUES WILL HAPPEN.

> Note 3: You can't populate a row with two values. This:

```
INSERT INTO project_courses(course_name, course_students_id, course_teachers_id, course_grades_id)
VALUES ("Language", ("2", "4", "6"), "2");
```

Doesn't work. Instead, do this:

```
INSERT INTO project_courses(course_name, course_students_id, course_teachers_id, course_grades_id)
VALUES ("Art", "1", "1", "1");

INSERT INTO project_courses(course_name, course_students_id, course_teachers_id, course_grades_id)
VALUES ("Art", "3", "1", "2");

INSERT INTO project_courses(course_name, course_students_id, course_teachers_id, course_grades_id)
VALUES ("Art", "5", "1", "3");



INSERT INTO project_courses(course_name, course_students_id, course_teachers_id, course_grades_id)
VALUES ("Language", "2", "2", "4");

INSERT INTO project_courses(course_name, course_students_id, course_teachers_id, course_grades_id)
VALUES ("Language", "4", "2", "5");

INSERT INTO project_courses(course_name, course_students_id, course_teachers_id, course_grades_id)
VALUES ("Language", "6", "2", "6");



INSERT INTO project_courses(course_name, course_students_id, course_teachers_id, course_grades_id)
VALUES ("Math", "1", "3", "7");

INSERT INTO project_courses(course_name, course_students_id, course_teachers_id, course_grades_id)
VALUES ("Math", "2", "3", "8");

INSERT INTO project_courses(course_name, course_students_id, course_teachers_id, course_grades_id)
VALUES ("Math", "3", "3", "9");



INSERT INTO project_courses(course_name, course_students_id, course_teachers_id, course_grades_id)
VALUES ("Science", "4", "4", "10");

INSERT INTO project_courses(course_name, course_students_id, course_teachers_id, course_grades_id)
VALUES ("Science", "5", "4", "11");

INSERT INTO project_courses(course_name, course_students_id, course_teachers_id, course_grades_id)
VALUES ("Science", "6", "4", "12");
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
INSERT INTO project_teachers(teacher_name, teacher_courses_id, teacher_students_id, teacher_grades_id)
VALUES ("Minerva", "1", "1", "1");

INSERT INTO project_teachers(teacher_name, teacher_courses_id, teacher_students_id, teacher_grades_id)
VALUES ("Minerva", "2", "3", "2");

INSERT INTO project_teachers(teacher_name, teacher_courses_id, teacher_students_id, teacher_grades_id)
VALUES ("Minerva", "3", "5", "3");



INSERT INTO project_teachers(teacher_name, teacher_courses_id, teacher_students_id, teacher_grades_id)
VALUES ("Albus", "4", "2", "4");

INSERT INTO project_teachers(teacher_name, teacher_courses_id, teacher_students_id, teacher_grades_id)
VALUES ("Albus", "5", "4", "5");

INSERT INTO project_teachers(teacher_name, teacher_courses_id, teacher_students_id, teacher_grades_id)
VALUES ("Albus", "6", "6", "6");



INSERT INTO project_teachers(teacher_name, teacher_courses_id, teacher_students_id, teacher_grades_id)
VALUES ("Severus", "7", "1", "7");

INSERT INTO project_teachers(teacher_name, teacher_courses_id, teacher_students_id, teacher_grades_id)
VALUES ("Severus", "8", "2", "8");

INSERT INTO project_teachers(teacher_name, teacher_courses_id, teacher_students_id, teacher_grades_id)
VALUES ("Severus", "9", "3", "9");



INSERT INTO project_teachers(teacher_name, teacher_courses_id, teacher_students_id, teacher_grades_id)
VALUES ("Remus", "10", "4", "10");

INSERT INTO project_teachers(teacher_name, teacher_courses_id, teacher_students_id, teacher_grades_id)
VALUES ("Remus", "11", "5", "11");

INSERT INTO project_teachers(teacher_name, teacher_courses_id, teacher_students_id, teacher_grades_id)
VALUES ("Remus", "12", "6", "12");
```


**grades table**

```
INSERT INTO project_grades(grade_courses_id, grade_teachers_id, grade_students_id, grade_value)
VALUES ("1", "1", "1", "5.20");

INSERT INTO project_grades(grade_courses_id, grade_teachers_id, grade_students_id, grade_value)
VALUES ("2", "1", "3", "7.00");

INSERT INTO project_grades(grade_courses_id, grade_teachers_id, grade_students_id, grade_value)
VALUES ("3", "1", "5", "10.00");



INSERT INTO project_grades(grade_courses_id, grade_teachers_id, grade_students_id, grade_value)
VALUES ("4", "2", "2", "3.75");

INSERT INTO project_grades(grade_courses_id, grade_teachers_id, grade_students_id, grade_value)
VALUES ("5", "2", "4", "5.00");

INSERT INTO project_grades(grade_courses_id, grade_teachers_id, grade_students_id, grade_value)
VALUES ("6", "2", "6", "6.75");




INSERT INTO project_grades(grade_courses_id, grade_teachers_id, grade_students_id, grade_value)
VALUES ("7", "3", "1", "0.00");

INSERT INTO project_grades(grade_courses_id, grade_teachers_id, grade_students_id, grade_value)
VALUES ("8", "3", "2", "7.50");

INSERT INTO project_grades(grade_courses_id, grade_teachers_id, grade_students_id, grade_value)
VALUES ("9", "3", "3", "0.00");



INSERT INTO project_grades(grade_courses_id, grade_teachers_id, grade_students_id, grade_value)
VALUES ("10", "4", "4", "7.50");

INSERT INTO project_grades(grade_courses_id, grade_teachers_id, grade_students_id, grade_value)
VALUES ("11", "4", "5", "8.00");

INSERT INTO project_grades(grade_courses_id, grade_teachers_id, grade_students_id, grade_value)
VALUES ("12", "4", "6", "10.00");
```

## Generating Foreign Keys

We now need to connect the tables between them. To do so, we'll create Foreign Keys.

**Courses table**

```
ALTER TABLE `db_project`.`project_courses` 
ADD INDEX `course_students_id_idx` (`course_students_id` ASC) VISIBLE,
ADD INDEX `course_teachers_id_idx` (`course_teachers_id` ASC) VISIBLE;
;
ALTER TABLE `db_project`.`project_courses` 
ADD CONSTRAINT `course_students_id`
  FOREIGN KEY (`course_students_id`)
  REFERENCES `db_project`.`project_students` (`student_id`)
  ON DELETE CASCADE
  ON UPDATE NO ACTION,
ADD CONSTRAINT `course_teachers_id`
  FOREIGN KEY (`course_teachers_id`)
  REFERENCES `db_project`.`project_teachers` (`teacher_id`)
  ON DELETE NO ACTION
  ON UPDATE NO ACTION;
ALTER TABLE `db_project`.`project_courses` 
DROP FOREIGN KEY `courses_teacher_id`;
ALTER TABLE `db_project`.`project_courses` 
ADD CONSTRAINT `courses_teacher_id`
  FOREIGN KEY (`course_teachers_id`)
  REFERENCES `db_project`.`project_teachers` (`teacher_id`)
  ON DELETE NO ACTION
  ON UPDATE NO ACTION;
```

**Teachers table**

```
ALTER TABLE `db_project`.`project_teachers` 
ADD INDEX `teacher_courses_id_idx` (`teacher_courses_id` ASC) VISIBLE,
ADD INDEX `teacher_students_id_idx` (`teacher_students_id` ASC) VISIBLE,
ADD INDEX `teacher_grades_id_idx` (`teacher_grades_id` ASC) VISIBLE;
;
ALTER TABLE `db_project`.`project_teachers` 
ADD CONSTRAINT `teacher_courses_id`
  FOREIGN KEY (`teacher_courses_id`)
  REFERENCES `db_project`.`project_courses` (`course_id`)
  ON DELETE NO ACTION
  ON UPDATE NO ACTION,
ADD CONSTRAINT `teacher_students_id`
  FOREIGN KEY (`teacher_students_id`)
  REFERENCES `db_project`.`project_students` (`student_id`)
  ON DELETE NO ACTION
  ON UPDATE NO ACTION,
ADD CONSTRAINT `teacher_grades_id`
  FOREIGN KEY (`teacher_grades_id`)
  REFERENCES `db_project`.`project_grades` (`grade_id`)
  ON DELETE NO ACTION
  ON UPDATE NO ACTION;
```

**Grades table**

```
ALTER TABLE `db_project`.`project_grades` 
DROP FOREIGN KEY `grade_courses_id`,
DROP FOREIGN KEY `grade_students_id`,
DROP FOREIGN KEY `grade_teachers_id`;
ALTER TABLE `db_project`.`project_grades` 
ADD CONSTRAINT `grade_courses_id`
  FOREIGN KEY (`grade_courses_id`)
  REFERENCES `db_project`.`project_courses` (`course_id`)
  ON DELETE NO ACTION
  ON UPDATE NO ACTION,
ADD CONSTRAINT `grade_students_id`
  FOREIGN KEY (`grade_students_id`)
  REFERENCES `db_project`.`project_students` (`student_id`)
  ON DELETE NO ACTION
  ON UPDATE NO ACTION,
ADD CONSTRAINT `grade_teachers_id`
  FOREIGN KEY (`grade_teachers_id`)
  REFERENCES `db_project`.`project_teachers` (`teacher_id`)
  ON DELETE NO ACTION
  ON UPDATE NO ACTION;
```

## Calculating the average grade given by each teacher

Once we have the foreign keys done, we have to calculate some things. An average is equal to the sum of all the grades' value divided by the number of items (grades). We can use the Average function, and filter by each teacher_grade_id, to get the average:

```
SELECT avg(grade_value)
FROM project_grades
WHERE grade_teachers_id = 1;

SELECT avg(grade_value)
FROM project_grades
WHERE grade_teachers_id = 2;

SELECT avg(grade_value)
FROM project_grades
WHERE grade_teachers_id = 3;

SELECT avg(grade_value)
FROM project_grades
WHERE grade_teachers_id = 4;
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
SELECT max(grade_value) from project_grades
where grade_students_id = 1;

SELECT max(grade_value) from project_grades
where grade_students_id = 2;

SELECT max(grade_value) from project_grades
where grade_students_id = 3;

SELECT max(grade_value) from project_grades
where grade_students_id = 4;

SELECT max(grade_value) from project_grades
where grade_students_id = 5;

SELECT max(grade_value) from project_grades
where grade_students_id = 6;
```

## Sorting students by the courses they're rolled in

This code below will sort the courses by name and show which students are enrolled in them:

```
use db_project;

SELECT student_name, course_name
FROM project_students s 
JOIN project_courses c
ON c.course_students_id = s.student_id
order by course_name asc
```

## Summary report of courses and average grades

```
SELECT 
	coalesce(c.course_name) as "Course",
  coalesce(g.grade_value) as "Grade"
FROM project_courses c
 JOIN (
	SELECT  grade_value, grade_courses_id
	FROM project_grades
 ) as g
 ON g.grade_courses_id = c.course_id
 ```

 This code lists the courses and the grades. Now that's half of the work because we want the average for each course and to avoid duplicates as well.

To do so, we'll type this:

```
SELECT 
	c.course_name,
    avg(grade_value) as grade_avg
FROM project_grades g
JOIN project_courses c on c.course_grades_id = g.grade_id
GROUP BY course_name
ORDER BY grade_avg asc
```

## Finding which student and professor have the most courses in common

We'll use a count function to count which teachers and student have most courses in common, but we'll first join courses, students and teachers.

```
SELECT student_name, course_name, teacher_name
FROM project_students s
JOIN project_courses c
ON c.course_students_id = s.student_id
JOIN project_teachers t
ON t.teacher_courses_id = c.course_id
```

It's important to note that we first join the courses to the students, and then the teachers to the courses, because that's the order we're following. Otherwise, issues will happen if we tied teachers to students, for example.

From there, we'll use a count function to count the courses, and will then group/order everything:

```
SELECT s.student_name, t.teacher_name,
COUNT(DISTINCT c.course_id) AS common
FROM project_students s
JOIN project_courses c
ON c.course_students_id = s.student_id
JOIN project_teachers t
ON t.teacher_courses_id = c.course_id
GROUP BY s.student_name, t.teacher_name
ORDER BY common DESC
```