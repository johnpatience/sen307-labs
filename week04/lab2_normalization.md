# VUA-SEN307 Week 4, Lab 2: Database Design and Normalization

**Name:** John Patience Godenaan
**Matric No:** VUG/SEN/24/12079
**Scenario:** University course-registration system (Students, Courses, Lecturers, Departments, Enrolments)

---

## Part 1: Entities, Attributes and Relationships

| Entity | Attributes | Primary key |
|---|---|---|
| Department | dept_id, dept_name | dept_id |
| Student | student_id, matric_no, full_name, dept_id | student_id |
| Lecturer | lecturer_id, full_name, dept_id | lecturer_id |
| Course | course_id, course_code, course_title | course_id |
| Enrolment | enrolment_id, student_id, course_id, session | enrolment_id |

**Relationships and cardinalities**

- One student belongs to **one** department; one department has **many** students (1:N).
- One lecturer belongs to **one** department; one department has **many** lecturers (1:N).
- One student registers for **many** courses; one course has **many** students (M:N), resolved with the **Enrolment** junction table.
- One course can have **many** lecturers across sessions; one lecturer can teach **many** courses (M:N), resolved with the **Teaching** junction table.

The design has two M:N relationships, so it needs two junction tables.

---

## Part 2: Normalization

### Unnormalized table

| matric_no | student_name | student_dept | course_codes | course_titles | lecturers | lecturer_depts | session |
|---|---|---|---|---|---|---|---|
| 20231234 | Ada Obi | Software Engineering | SEN307, SEN301 | Database Systems, Software Architecture | Dr A. Okafor, Dr M. Bello | Software Eng., Software Eng. | 2026/2027 |
| 20231235 | John Musa | Computer Science | SEN307, COS301 | Database Systems, Operating Systems | Dr A. Okafor, Mrs F. Eze | Software Eng., Computer Sci. | 2026/2027 |
| 20231236 | Grace Okon | Software Engineering | SEN301 | Software Architecture | Dr M. Bello | Software Eng. | 2026/2027 |

### Step 1: First Normal Form (1NF)

Primary key: **(matric_no, course_code)**

| matric_no | student_name | student_dept | course_code | course_title | lecturer | lecturer_dept | session |
|---|---|---|---|---|---|---|---|
| 20231234 | Ada Obi | Software Engineering | SEN307 | Database Systems | Dr A. Okafor | Software Eng. | 2026/2027 |
| 20231234 | Ada Obi | Software Engineering | SEN301 | Software Architecture | Dr M. Bello | Software Eng. | 2026/2027 |
| 20231235 | John Musa | Computer Science | SEN307 | Database Systems | Dr A. Okafor | Software Eng. | 2026/2027 |
| 20231235 | John Musa | Computer Science | COS301 | Operating Systems | Mrs F. Eze | Computer Sci. | 2026/2027 |
| 20231236 | Grace Okon | Software Engineering | SEN301 | Software Architecture | Dr M. Bello | Software Eng. | 2026/2027 |

**What I fixed:** I split the lists so that every cell holds a single value, and each student–course pair now has its own row.

### Step 2: Second Normal Form (2NF)

**Student**

| matric_no (PK) | student_name | student_dept |
|---|---|---|
| 20231234 | Ada Obi | Software Engineering |
| 20231235 | John Musa | Computer Science |
| 20231236 | Grace Okon | Software Engineering |

**Course**

| course_code (PK) | course_title |
|---|---|
| SEN307 | Database Systems |
| SEN301 | Software Architecture |
| COS301 | Operating Systems |

**Teaching** (which lecturer teaches which course in which session)

| course_code | lecturer | lecturer_dept | session |
|---|---|---|---|
| SEN307 | Dr A. Okafor | Software Eng. | 2026/2027 |
| SEN301 | Dr M. Bello | Software Eng. | 2026/2027 |
| COS301 | Mrs F. Eze | Computer Sci. | 2026/2027 |

**Enrolment**

| matric_no | course_code | session |
|---|---|---|
| 20231234 | SEN307 | 2026/2027 |
| 20231234 | SEN301 | 2026/2027 |
| 20231235 | SEN307 | 2026/2027 |
| 20231235 | COS301 | 2026/2027 |
| 20231236 | SEN301 | 2026/2027 |

**What I fixed:** I moved the columns that depended on only part of the key into their own tables, so student details depend on matric_no alone and course titles depend on course_code alone.

### Step 3: Third Normal Form (3NF)

**Department**

| dept_id (PK) | dept_name |
|---|---|
| 1 | Software Engineering |
| 2 | Computer Science |

**Student**

| student_id (PK) | matric_no | full_name | dept_id (FK) |
|---|---|---|---|
| 1 | 20231234 | Ada Obi | 1 |
| 2 | 20231235 | John Musa | 2 |
| 3 | 20231236 | Grace Okon | 1 |

**Lecturer**

| lecturer_id (PK) | full_name | dept_id (FK) |
|---|---|---|
| 1 | Dr A. Okafor | 1 |
| 2 | Dr M. Bello | 1 |
| 3 | Mrs F. Eze | 2 |

**Course**

| course_id (PK) | course_code | course_title |
|---|---|---|
| 1 | SEN307 | Database Systems |
| 2 | SEN301 | Software Architecture |
| 3 | COS301 | Operating Systems |

**Teaching**

| teaching_id (PK) | course_id (FK) | lecturer_id (FK) | session |
|---|---|---|---|
| 1 | 1 | 1 | 2026/2027 |
| 2 | 2 | 2 | 2026/2027 |
| 3 | 3 | 3 | 2026/2027 |

**Enrolment**

| enrolment_id (PK) | student_id (FK) | course_id (FK) | session |
|---|---|---|---|
| 1 | 1 | 1 | 2026/2027 |
| 2 | 1 | 2 | 2026/2027 |
| 3 | 2 | 1 | 2026/2027 |
| 4 | 2 | 3 | 2026/2027 |
| 5 | 3 | 2 | 2026/2027 |

**What I fixed:** I moved department names into a Department table and replaced the repeated text with foreign keys, so a department is now written once and renaming it means changing a single row.

---

## Step 4: Final Schema

```
Department (dept_id PK, dept_name)
Student    (student_id PK, matric_no, full_name, dept_id FK → Department)
Lecturer   (lecturer_id PK, full_name, dept_id FK → Department)
Course     (course_id PK, course_code, course_title)
Teaching   (teaching_id PK, course_id FK → Course, lecturer_id FK → Lecturer, session)
Enrolment  (enrolment_id PK, student_id FK → Student, course_id FK → Course, session)
```
