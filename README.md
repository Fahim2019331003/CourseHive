# CourseHive

> A role-based course management system for bringing administrators, teachers, and students into one streamlined academic workspace.

CourseHive is a Java web application that supports the everyday flow of course administration: creating accounts, publishing courses, assigning instructors, enrolling students, and viewing class rosters. It is built with Java Servlets and JSP, with MySQL providing persistent storage.

## Highlights

- **Role-based experience** - dedicated dashboards and session-aware access control for Admin, Teacher, and Student users.
- **Course administration** - administrators can add courses and assign an available course to a teacher.
- **Self-service enrollment** - students can browse courses they have not yet taken and enroll directly.
- **Teaching overview** - teachers can view their assigned courses and the students enrolled in each one.
- **Account registration and authentication** - student and teacher registration, login, logout, and duplicate-username checks.
- **Responsive UI** - JSP pages are styled with custom CSS and include role-specific profile views.

## Roles and capabilities

| Role | What they can do |
| --- | --- |
| **Admin** | Add courses, review unassigned courses, and assign teachers. |
| **Teacher** | View a profile, see assigned courses, and inspect each course roster. |
| **Student** | View a profile, review registered courses, and enroll in available courses. |

## Technology

- Java and Java Servlets (`javax.servlet`)
- JSP, HTML, and CSS
- MySQL 8
- MySQL Connector/J 8.0.29 (included under `WEB-INF/lib`)
- Apache Tomcat 9 or another Servlet 3.1-compatible container

## Project structure

```text
CourseManagement/
+-- src/main/java/com/cms/       # Servlets and JDBC data-access helpers
|   +-- validate.java            # Login handling
|   +-- validateNew.java         # Registration handling
|   +-- addCourse.java           # Course creation
|   +-- enrollment.java          # Student enrollment
|   +-- updateTeacher.java       # Teacher-course assignment
|   +-- LoginDao.java            # Authentication and query helper
|   +-- InsertDB.java            # Database write operations
+-- src/main/webapp/
    +-- Admin/                   # Administrator dashboard and pages
    +-- Student/                 # Student dashboard and pages
    +-- Teacher/                 # Teacher dashboard and pages
    +-- index.jsp                # Login page
    +-- signup.jsp               # Registration page
    +-- WEB-INF/                 # Deployment descriptor and JDBC driver
```

## Getting started

### Prerequisites

- JDK 8 or later
- Apache Tomcat 9
- MySQL 8 or compatible MySQL server
- An IDE with Dynamic Web Project support, such as Eclipse IDE for Enterprise Java and Web Developers

### 1. Create the database

Run the following in MySQL to create the database and the tables used by the application:

```sql
CREATE DATABASE course_management;
USE course_management;

CREATE TABLE users (
  type VARCHAR(20) NOT NULL,
  username VARCHAR(100) PRIMARY KEY,
  password VARCHAR(255) NOT NULL
);

CREATE TABLE students (
  username VARCHAR(100) PRIMARY KEY,
  name VARCHAR(150) NOT NULL,
  studentID INT NOT NULL,
  FOREIGN KEY (username) REFERENCES users(username)
);

CREATE TABLE teachers (
  username VARCHAR(100) PRIMARY KEY,
  name VARCHAR(150) NOT NULL,
  teacherID INT NOT NULL,
  FOREIGN KEY (username) REFERENCES users(username)
);

CREATE TABLE courses (
  coursename VARCHAR(150) NOT NULL,
  courseID VARCHAR(30) PRIMARY KEY,
  credit FLOAT NOT NULL
);

CREATE TABLE takes (
  username VARCHAR(100) NOT NULL,
  coursename VARCHAR(150) NOT NULL,
  courseID VARCHAR(30) NOT NULL,
  FOREIGN KEY (username) REFERENCES students(username),
  FOREIGN KEY (courseID) REFERENCES courses(courseID)
);

CREATE TABLE teaches (
  username VARCHAR(100) NOT NULL,
  coursename VARCHAR(150) NOT NULL,
  courseID VARCHAR(30) NOT NULL,
  FOREIGN KEY (username) REFERENCES teachers(username),
  FOREIGN KEY (courseID) REFERENCES courses(courseID)
);
```

Create an administrator account (registration is intentionally limited to students and teachers):

```sql
INSERT INTO users (type, username, password)
VALUES ('admin', 'admin', 'change-this-password');
```

### 2. Configure the database connection

Update the connection settings in both files to match your local MySQL account:

- `CourseManagement/src/main/java/com/cms/LoginDao.java`
- `CourseManagement/src/main/java/com/cms/InsertDB.java`

The current development defaults point to `jdbc:mysql://localhost:3306/course_management` with the username `root` and password `1234`.

### 3. Deploy

1. Import `CourseManagement` into your IDE as an existing Dynamic Web Project.
2. Associate it with a Tomcat 9 runtime.
3. Ensure `mysql-connector-java-8.0.29.jar` remains available in `src/main/webapp/WEB-INF/lib`.
4. Add the project to the server and start Tomcat.
5. Open `http://localhost:8080/CourseManagement/` (adjust the context path if your server uses a different one).

## Application flow

```text
Admin creates courses --> Admin assigns teachers --> Students enroll
       |                         |                         |
       +-------------------------+-------------------------+
                                 |
                     Teachers view enrolled students
```

## Servlet routes

| Route | Purpose |
| --- | --- |
| `/validate` | Authenticates a user and opens the appropriate dashboard. |
| `/validateNew` | Registers a student or teacher account. |
| `/addCourse` | Creates a course. |
| `/updateTeacher` | Assigns a teacher to a course. |
| `/enrollment` | Enrolls the signed-in student in a course. |
| `/Logout` | Clears the current session and returns to login. |

## Notes for production use

This project is an academic/demo application. Before deploying it publicly, move database credentials out of source code, hash passwords with a modern password-hashing algorithm, add database constraints for duplicate enrollments/assignments, and replace string-built SQL queries with prepared statements throughout.

## License

No license file is currently included. Add a license before redistributing or accepting external contributions.
