
This project consists of two components that work together to manage and evaluate student grades

•	1. grade_evaluator.py — a Python script that reads a CSV file of course grades and produces an academic evaluation including GPA, pass/fail status, which fromative can be resubmitted.
•	2. organizer.sh — a Bash shell script that archives the current grades.csv with a timestamp, resets the workspace with a new file, and logs every operation to a running organization.log file.

Project Structure
File	Purpose
grade_evaluator.py	Main Python application — validates, calculates, and reports
grades.csv	Input data source containing assignment grades
organizer.sh	Bash script that archives CSV files and logs operations
organizer.log	Cumulative audit log of all archival operations
archive/	Directory holding all timestamped archived CSV files

Prerequisites
•	Python 3.6 or higher
•	Bash shell (Linux, macOS, or Windows WSL)


Data Source: grades.csv
The input CSV file must contain exactly these four columns:

Column	Type	Description
assignment	string	Name of the assignment or assessment
group	string	Must be exactly “Formative” or “Summative”
score	float	Student’s raw score, between 0 and 100 inclusive
weight	float	Assignment’s contribution to the final grade (must total 100)


Python Application: grade_evaluator.py
Run the evaluator from your terminal:
python grade_evaluator.py
When prompted, type the filename:
Enter the name of the CSV file to process (e.g., grades.csv)


Weight Validation
Three rules are enforced before any calculation proceeds:
•	All assignment weights must sum to exactly 100
•	Formative assignments must sum to exactly 60
•	Summative assignments must sum to exactly 40

GPA Calculation
Each assignment contributes a weighted score using the formula:
weighted_score = (score / 100) × weight
The final grade is the sum of all weighted scores (out of 100). GPA is then derived as:
GPA = (Final Grade / 100) × 5.0

Pass / Fail Decision
A student PASSES only if they score at or above 50% in both the Formative and Summative categories independently. Achieving an overall grade above 50% is not sufficient if either category falls below the threshold.

Category averages
Formative average = (formative weighted total / 60) × 100 Summative average = (summative weighted total / 40) × 100

Resubmission Logic
The program identifies which failed Formative assignment(s) the student may resubmit:
•	Collect all Formative assignments where the student scored below 50%
•	From that list, find the highest weight value
•	All failed assignments sharing that highest weight are listed as eligible — this correctly handles ties



--- Final Decision ---
Final Grade: 60.00%
GPA: 3.00
Formative Average: 56.67%
Summative Average: 65.00%
Status: PASSED

Assignment(s) eligible for resubmission:
  - Group Exercise (Score: 40.0, Weight: 20)
  - Functions and Debugging Lab (Score: 45.0, Weight: 20)

Error Handling
Scenario	Behaviour
File not found	Prints a clear error and exits with code 1
Header-only file (after organizer reset)	Prints a warning and exits cleanly with code 0
Score outside 0–100	Names the offending assignment and exits
Weights don’t total 100 / 60 / 40	Prints the actual total and the required total, then exits

Shell Script: organizer.sh
The organizer automates the archiving of grades data and resets the workspace for the next batch. Run it from the same directory as grades.csv:
bash organizer.sh

What It Does
Step	Action	Detail
1	Archive directory	Creates archive/ if it does not already exist
2	Timestamp	Generates a YYYYMMDD-HHMMSS string for unique naming
3	Safety check	Exits with an error if grades.csv is missing
4	Archive file	Moves grades.csv → archive/grades_YYYYMMDD-HHMMSS.csv
5	Workspace reset	Creates a fresh header-only grades.csv for the next batch
6	Logging	Appends timestamp, original name, and archived path to organizer.log

organizer.log Format
Each run appends one block to the log file. The log accumulates across all runs providing a full audit trail:
------------------------------------------------------------
Timestamp      : 20251105-170000
Original file  : grades.csv
Archived as    : archive/grades_20251105-170000.csv
Logged at      : 2025-11-05 17:00:00
------------------------------------------------------------

Grading Logic Summary
Rule	Value
Total weight required	100
Formative weight required	60
Summative weight required	40
Pass threshold (per category)	≥ 50% in BOTH Formative and Summative independently
GPA formula	(Final Grade / 100) × 5.0
Resubmission eligibility	Failed Formative assignment(s) with the highest weight

Typical Workflow
•	1.Add student grade data to grades.csv following the required column format
•	2.Run grade_evaluator.py and enter grades.csv when prompted
•	3.Review the printed report: final grade, GPA, status, and any resubmission recommendations
•	4.When ready to archive, run organizer.sh to timestamp and move the file, reset the workspace, and log the operation
•	5.For the next student or batch, populate the fresh grades.csv created by the organizer and repeat



