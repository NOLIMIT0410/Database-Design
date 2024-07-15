# Data Normalization and Entity-Relationship Diagramming

## Original Form:

| assignment_id | student_id | due_date | professor | assignment_topic                | classroom | grade | relevant_reading    | professor_email   |
| :------------ | :--------- | :------- | :-------- | :------------------------------ | :-------- | :---- | :------------------ | :---------------- |
| 1             | 1          | 23.02.21 | Melvin    | Data normalization              | WWH 101   | 80    | Deumlich Chapter 3  | l.melvin@foo.edu  |
| 2             | 7          | 18.11.21 | Logston   | Single table queries            | 60FA 314  | 25    | Dümmlers Chapter 11 | e.logston@foo.edu |
| 1             | 4          | 23.02.21 | Melvin    | Data normalization              | WWH 101   | 75    | Deumlich Chapter 3  | l.melvin@foo.edu  |
| 5             | 2          | 05.05.21 | Logston   | Python and pandas               | 60FA 314  | 92    | Dümmlers Chapter 14 | e.logston@foo.edu |
| 4             | 2          | 04.07.21 | Nevarez   | Spreadsheet aggregate functions | WWH 201   | 65    | Zehnder Page 87     | i.nevarez@foo.edu |
| ...           | ...        | ...      | ...       | ...                             | ...       | ...   | ...                 | ...               |

## Why the above form does not satisfy 4NF:
By definition, 4NF should satisfy the requirement of 3NF, which should satisfy the requirement of 2NF. So we should starts with 2NF first.

Also by definition, each non-key field in 2NF must provide a fact about the entity uniquely identifies by the primary key. And the fact could only be one-to-many relationship or one-to-one relationship(many-to-many)is not allowed.

So no matter which key we pick as the primary key, assignment id or student id from the original form, 2NF is not satisfied as all kinds of information are mixed together. 

Then, by definition, a 3NF must satisfy 2NF in the first place, and shoult not have a non-key field as a fact about another non-key field, which is clearly violated. For example, if we choose assignment as the primary key, we have professor email that is a fact of professors, but has nothing to do with assignment. 

Lastly, by definition the 4NF should satisfy 3NF, and should not contain more than one independent multi-values fact about an entity.

**So, in short, what we want here as 4NF is that we want to talk about one thing at a time, one fact at a time.**

## How to make it a 4NF:
**What I do is going over all the assumptions, figuring out what entities we are talking about, and trying to relatate one to another without violating 4NF:**
- each course can be taught by multiple professors in different sections
- each professor might teach multiple sections of the same course
- each section meets in a specific classroom with a specific professor
- different sections of the same course may meet in different classrooms, even if the professor is the same
- professors give assignments, with due dates that are specific to the section of the course
- professors give readings that are relevant and helpful to a given assignment.
- a professor might (or might not) give the same assignment to different sections of the same course, but with different due dates
- professors give readings to help with the assignments
- students complete assignments and receive a grade

So from the assumptions, we can at least find 8 entities: 
- courses
- sections
- professors
- classrooms
- assignments
- readings
- grades
- students

There might be more needed, but those are the basic eight entities we are trying to illustrate.

**Step two is figuring out their relationship:**
- one course could have multiple sections (**one-to-many**)
- each section can be taught by only one professor, but one professor can teach multiple sections(**one-to-many**)
- each section takes place in a specific classroom, but one classroom might have multiple sections.(**one-to-many**)
- tricky part is the relationship between assignment and sections. Each sections have multiple assignments, and one assignment can also be assigned to a several sections. So this is a **many-to-many** relationship, which we should avoid. What I do to address that is adding another entity as the intermediate station called section_assignment. One section could only have one section assignment, and all the assignments that are assigned to a specific section is stored in that section assignment. So now we have a **one-to-one** and a **one-to-many** relationship. Which satisfies 4NF
- One assignment can have multiple helpful readings(**one-to-many**)
- Another tricky part is the relationship between assignment and students, because one students have multiple assignments, and one assignment can be assigned to multiple students, so it is another **many-to-many** relationship. But here I don't need to create a new entity but simply use grades as the intermediate station. Be careful that I link grades to section_assignment instead of assignment, because assignment in different sections have different due days; thus, different grades.

## Creating 4NF table:

With all the information reagrading entities and their relationships, we are now able to make 4NF tables: 

**Tips: the first column is always the primary key, all the other id stuff are foreign key, relating one entity with another**

### _Table courses:_
| course_id | course_name|
| :-------- | :----------|

### _Table sections:_
| section_id | time | course_id | classroom_id | professor_id |
| :---| :---|:---|:---| :---|

### _Table professors:_
| professor_id | professor_name | email |
| :--| :--| :--|

### _Table classrooms:_
|classroom_id | location |
| :--| :--|

### _Table section_assignments_
|section_assignment_id | due_date | section_id | assignment_id |
| :--| :--| :--| :--|

### _Table assignments:_
|assignment_id | topic |
| :--| :--|

### _Table readings:_
| reading_id | name | chapter | assignment_id |
| :--| :--| :--| :--|

### _Table grades:_
| grade_id | section_assignment_id | student_id |
| :--| :--| :--|

### _Table students:_
| student_id | student_name | email |
| :--| :--| :--|

## Lastly, the ER diagram that illustrates everything above:


![ERM](https://github.com/dbdesign-students-spring2024/5-database-design-NOLIMIT0410/blob/master/images/ERM.svg)

