# Continuous Internal Assessment (CIA) Calculator & SGPA/CGPA Engine
## Project ID: Project-42 | Course Code: 4CTGC2011 (OOP using Java & Kotlin)
### Institution: School of Engineering and Technology, CMR University, Bengaluru
### Academic Year: 2026-2027

---

## 🚀 Authors
* **Udith Preethan** (USN: `25BBTCO125`) - Computer Engineering
* **Druthi K .V** (USN: `25BBTCO118`) - Computer Engineering

## 🎓 Project Governance
* **Project Guide:** Dr. Pandava Sudharshan Babu, Associate Professor, Dept. of CE
* **Head of the Department:** Dr. Kiran Kumari Patil, Professor & HOD, Dept. of CE

---

## 📌 Problem Context & Industrial Relevance
In autonomous academic frameworks, grading systems demand structural mathematical correctness, compliance with institutional evaluation rules, and robust runtime constraints. Manual processing loops or raw spreadsheets are highly vulnerable to typing issues, boundary evaluation mismatches, and data truncation anomalies. 

This production-grade system enforces **Continuous Internal Assessment (CIA) Weighted Grading & SGPA/CGPA Calculation** paradigms. Under autonomous regulations, a student's final course validation requires multi-component aggregate metrics built around weighted internal inputs:
1. **Internal Mid-Term Tests:** Scaled precisely to **50%** of the total CIA score.
2. **Laboratory Practical Logs:** Scaled precisely to **25%** of the total CIA score.
3. **Continuous Assessments (Quizzes/Seminars):** Scaled precisely to **25%** of the total CIA score.

The system prevents data pollution by implementing domain-specific custom checked exceptions (`InvalidMarkException`), blocking out-of-bounds metrics (negative numbers or values exceeding individual fractions) instantly at the point of ingestion before downstream calculation takes place.

---

## 🛠️ Architecture & Core OOP Design
The system architecture uses a decoupled multi-tiered design pattern optimized for academic validation requirements:
* **Strict Encapsulation:** All instance fields inside the `CourseComponent` layer are isolated behind `private` access scopes. Field mutations flow through strict constructor rules and validated setter routines.
* **Checked Exception Layer:** `InvalidMarkException` inherits directly from `java.lang.Exception`, bypassing default runtime generic types to intercept and halt corrupted entry matrices cleanly.
* **Static Evaluation Utilities:** Step-wise UGC/VTU grade mapping limits and fractional scalar equations are handled as `static` structural routines inside `GPACalculator` to keep memory allocations lightweight during continuous loop iterations.
* **Aggregation Modeling:** `AcademicRecord` maintains a clean primitive array array tracker (`CourseEnrollment[]`) to encapsulate student context records cleanly under a consolidated institutional domain anchor.

---

## 💻 Technical Implementation (Source Code)

### 1. InvalidMarkException.java
```java
package org.cmru.grading.exception;

/**
 * Custom checked exception thrown when entered academic marks violate domain invariants.
 */
public class InvalidMarkException extends Exception {
    public InvalidMarkException(String message) {
        super(message);
    }
}
```

### 2. CourseComponent.java
```java
package org.cmru.grading.entity;

import org.cmru.grading.exception.InvalidMarkException;

/**
 * Encapsulates the raw sub-component marks and credit allocation for a specific course.
 */
public class CourseComponent {
    private double testMarks;          // Weight: 50%
    private double labMarks;           // Weight: 25%
    private double assignmentMarks;    // Weight: 25%
    private int courseCredits;
    private double maxMarks;           // Standard maximum boundary

    public CourseComponent(double testMarks, double labMarks, double assignmentMarks, int courseCredits, double maxMarks) 
            throws InvalidMarkException {
        this.maxMarks = maxMarks;
        this.courseCredits = courseCredits;
        validateAndSetMarks(testMarks, labMarks, assignmentMarks);
    }

    public void validateAndSetMarks(double t, double l, double a) throws InvalidMarkException {
        // Enforce sub-component maximum boundary thresholds
        double maxTest = maxMarks * 0.50;
        double maxLab = maxMarks * 0.25;
        double maxAssign = maxMarks * 0.25;

        if (t < 0 || t > maxTest) {
            throw new InvalidMarkException("Violation: Test marks out of bounds [0 - " + maxTest + "]. Got: " + t);
        }
        if (l < 0 || l > maxLab) {
            throw new InvalidMarkException("Violation: Lab marks out of bounds [0 - " + maxLab + "]. Got: " + l);
        }
        if (a < 0 || a > maxAssign) {
            throw new InvalidMarkException("Violation: Assignment marks out of bounds [0 - " + maxAssign + "]. Got: " + a);
        }

        this.testMarks = t;
        this.labMarks = l;
        this.assignmentMarks = a;
    }

    public double getTestMarks() { return testMarks; }
    public double getLabMarks() { return labMarks; }
    public double getAssignmentMarks() { return assignmentMarks; }
    public int getCourseCredits() { return courseCredits; }
    public double getMaxMarks() { return maxMarks; }
}
```

### 3. CourseEnrollment.java
```java
package org.cmru.grading.entity;

/**
 * Represents a student's institutional enrollment context, binding metadata to components.
 */
public class CourseEnrollment {
    private String courseCode;
    private String courseName;
    private CourseComponent component;

    public CourseEnrollment(String courseCode, String courseName, CourseComponent component) {
        this.courseCode = courseCode;
        this.courseName = courseName;
        this.component = component;
    }

    public double calculateTotalPercentage() {
        // Raw scaling implementation based on autonomous university rules
        double totalObtained = component.getTestMarks() + component.getLabMarks() + component.getAssignmentMarks();
        return (totalObtained / component.getMaxMarks()) * 100.0;
    }

    public String getCourseCode() { return courseCode; }
    public String getCourseName() { return courseName; }
    public CourseComponent getComponent() { return component; }
}
```

### 4. GPACalculator.java
```java
package org.cmru.grading.engine;

import org.cmru.grading.entity.CourseEnrollment;

/**
 * Pure computational engine providing deterministic grading operations.
 */
public class GPACalculator {

    public static double convertToGradePoint(double percentage) {
        if (percentage >= 90.0) return 10.0;
        if (percentage >= 80.0) return 9.0;
        if (percentage >= 70.0) return 8.0;
        if (percentage >= 60.0) return 7.0;
        if (percentage >= 50.0) return 6.0;
        if (percentage >= 40.0) return 5.0;
        return 0.0; // Fail state threshold
    }

    public static String convertToLetterGrade(double percentage) {
        if (percentage >= 90.0) return "O";
        if (percentage >= 80.0) return "A+";
        if (percentage >= 70.0) return "A";
        if (percentage >= 60.0) return "B+";
        if (percentage >= 50.0) return "B";
        if (percentage >= 40.0) return "C";
        return "F";
    }

    public static double calculateSGPA(CourseEnrollment[] enrollments, int count) {
        double totalQualityPoints = 0.0;
        int totalCredits = 0;

        for (int i = 0; i < count; i++) {
            double percentage = enrollments[i].calculateTotalPercentage();
            double gradePoint = convertToGradePoint(percentage);
            int credits = enrollments[i].getComponent().getCourseCredits();

            totalQualityPoints += (gradePoint * credits);
            totalCredits += credits;
        }

        if (totalCredits == 0) return 0.0;
        return totalQualityPoints / totalCredits;
    }
}
```

### 5. AcademicRecord.java
```java
package org.cmru.grading.entity;

/**
 * Aggregate base container that anchors student context and records array lists.
 */
public class AcademicRecord {
    private String studentUSN;
    private String studentName;
    private CourseEnrollment[] enrollments;
    private int enrollmentCount;

    public AcademicRecord(String studentUSN, String studentName, int maxCourses) {
        this.studentUSN = studentUSN;
        this.studentName = studentName;
        this.enrollments = new CourseEnrollment[maxCourses];
        this.enrollmentCount = 0;
    }

    public void addEnrollment(CourseEnrollment enrollment) {
        if (enrollmentCount < enrollments.length) {
            enrollments[enrollmentCount++] = enrollment;
        }
    }

    public CourseEnrollment[] getEnrollments() { return enrollments; }
    public int getEnrollmentCount() { return enrollmentCount; }
    public String getStudentUSN() { return studentUSN; }
    public String getStudentName() { return studentName; }
}
```

### 6. AcademicSimulationEngine.java
```java
package org.cmru.grading;

import org.cmru.grading.entity.*;
import org.cmru.grading.engine.GPACalculator;
import org.cmru.grading.exception.InvalidMarkException;

/**
 * Driver runtime class orchestrating both standard validation workflows and exception bounds.
 */
public class AcademicSimulationEngine {
    public static void main(String[] args) {
        System.out.println("=== CMR UNIVERSITY EXAMINATION OPERATIONS ENGINE ===");
        System.out.println("Initializing Student Ledger Evaluation System...\n");

        AcademicRecord studentRecord = new AcademicRecord("25BBTCO125", "Udith Preethan", 5);

        try {
            System.out.println("[WORKFLOW 1: Normal Operation - Processing Valid Data]");
            
            // Course 1: OOPJ (Credits: 4, Max: 100) -> Test: 42/50, Lab: 23/25, Quiz: 24/25 (Total: 89%)
            CourseComponent oopjComp = new CourseComponent(42.0, 23.0, 24.0, 4, 100.0);
            CourseEnrollment oopj = new CourseEnrollment("4CTGC2011", "Object Oriented Programming using Java", oopjComp);
            studentRecord.addEnrollment(oopj);
            System.out.println(">> Registered: " + oopj.getCourseName() + " | Final Percentage: " + oopj.calculateTotalPercentage() + "%");

            // Course 2: Discrete Mathematics (Credits: 3, Max: 100) -> Test: 35/50, Lab: 20/25, Quiz: 18/25 (Total: 73%)
            CourseComponent mathComp = new CourseComponent(35.0, 20.0, 18.0, 3, 100.0);
            CourseEnrollment math = new CourseEnrollment("4CTBS2012", "Discrete Mathematical Structures", mathComp);
            studentRecord.addEnrollment(math);
            System.out.println(">> Registered: " + math.getCourseName() + " | Final Percentage: " + math.calculateTotalPercentage() + "%");

            double calculatedSGPA = GPACalculator.calculateSGPA(studentRecord.getEnrollments(), studentRecord.getEnrollmentCount());
            System.out.println("\n------------------------------------------------");
            System.out.println("Processing Academic Summary Report for: " + studentRecord.getStudentName() + " (" + studentRecord.getStudentUSN() + ")");
            System.out.println("Computed Semester SGPA: " + String.format("%.2f", calculatedSGPA));
            System.out.println("------------------------------------------------\n");

        } catch (InvalidMarkException e) {
            System.out.println("Unexpected Error: " + e.getMessage());
        }

        try {
            System.out.println("[WORKFLOW 2: Invariant Check - Triggering Exception Guard]");
            // Intentional out of bounds entry: Max Test Marks allowed is 50.0 * 0.50 = 25.0. Passing 42.0.
            CourseComponent invalidComp = new CourseComponent(42.0, 10.0, 12.0, 4, 50.0);
            CourseEnrollment errorEnrollment = new CourseEnrollment("4CTGC2013", "Advanced Data Architecture", invalidComp);
            studentRecord.addEnrollment(errorEnrollment);

        } catch (InvalidMarkException e) {
            System.out.println(">> Integrity Intercept Caught: " + e.getMessage());
            System.out.println(">> Pipeline execution halted securely. System state protected.");
        }
        
        System.out.println("\n=== SIMULATION PROCESSING CYCLE COMPLETE ===");
    }
}
```

---

## 📊 Verification Run (Console Output Snapshot)
```text
=== CMR UNIVERSITY EXAMINATION OPERATIONS ENGINE ===
Initializing Student Ledger Evaluation System...

[WORKFLOW 1: Normal Operation - Processing Valid Data]
>> Registered: Object Oriented Programming using Java | Final Percentage: 89.0%
>> Registered: Discrete Mathematical Structures | Final Percentage: 73.0%

------------------------------------------------
Processing Academic Summary Report for: Udith Preethan (25BBTCO125)
Computed Semester SGPA: 8.57
------------------------------------------------

[WORKFLOW 2: Invariant Check - Triggering Exception Guard]
>> Integrity Intercept Caught: Violation: Test marks out of bounds [0 - 25.0]. Got: 42.0
>> Pipeline execution halted securely. System state protected.

=== SIMULATION PROCESSING CYCLE COMPLETE ===
```

---

## ⚙️ How to Compile & Run
1. Ensure you have **Java JDK 17** or higher installed.
2. Clone or download the repository files structure matching the packages.
3. Open your terminal in the root path directory and execute:
   ```bash
   javac -d out src/org/cmru/grading/exception/*.java src/org/cmru/grading/entity/*.java src/org/cmru/grading/engine/*.java src/org/cmru/grading/*.java
   java -cp out org.cmru.grading.AcademicSimulationEngine
   ```