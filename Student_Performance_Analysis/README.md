🎓 Student Performance, Attendance & Behavior Analytics Dashboard — Power BI
An end-to-end Power BI analytics solution that unifies academic performance, attendance, and behavior data for 1,000+ students into one connected reporting system. Built on a proper star schema, a 25+ measure DAX layer, and three interlinked dashboards with drillthrough navigation down to the individual student — designed the way a school's data team would actually need it, not as a single flat report.
📌 Project Overview
Schools generate three separate streams of data — grades, attendance registers, and behavior logs — that usually live in disconnected spreadsheets. This project merges them into a single model so that academic outcomes can be explained by more than just test scores: attendance gaps and behavior incidents are pulled into the same analytical view.
The three questions this project is built to answer:
Question
Answered by
"How are students performing academically, and where are the weak spots?"
Academic Performance Dashboard
"Are attendance or conduct issues dragging performance down, and where should we intervene first?"
Attendance & Behavior Dashboard
"What does the complete picture look like for one specific student, right now, in this meeting?"
Student Detailed Analysis (Drillthrough)
🗂️ Data Model — Star Schema

![Star Schema](Star_Schema.png)


The model is built as a clean star schema, not a single wide table:
Dim_Students and Dim_Date act as the two universal filters, each connected (1-to-many) to all three fact tables
Fact_Scores, Fact_Attendance, and Fact_Behavior each capture one event type per row
Dim_Subject connects independently to Fact_Scores and Fact_Attendance
Why it matters: because every fact table shares the same student and date dimensions, slicing by "Class 10" or "Term 2" filters scores, attendance, and behavior simultaneously and consistently. This is what makes it possible to ask cross-domain questions like "do students with high absenteeism also score lower?" — something a flat, single-table report structurally cannot do.
🧮 DAX Layer — The Logic Behind Every Number

![Project Tables](Project_Tables.png)

![Dax Formulas](Dax_Formulas.png)


Rather than relying on Power BI's auto-summarization, every KPI is backed by an explicit, named measure — all organized in a dedicated Measures_Table for discoverability as the model grows.
Dynamic calendar: Dim_Date is built with CALENDAR(MIN(...), MAX(...)) instead of a hardcoded date range, so the date table automatically extends if new data is loaded later — no manual maintenance required.
Safe percentage math: every ratio (Attendance %, Overall Percentage) uses DIVIDE() with a zero fallback, preventing the dashboard from breaking when a filter context returns an empty denominator (e.g., a student with zero recorded exams).
Correct per-student averaging: Average Score Per Student uses AVERAGEX(VALUES(Dim_Students[StudentID]), [Average Score]) — this is deliberate, because a plain AVERAGE() over all exam rows would silently bias the result toward students who simply took more exams.
Dynamic high/low performer detection: Highest Average Score and Lowest Average Score use MAXX/MINX over VALUES(...) combined with nested CALCULATE, meaning these always reflect the currently filtered set of students — they recalculate live if you filter to one class or one term.
Consistent business rules at two grains: Performance Category is implemented as a SWITCH(TRUE(), ...) banding rule (High ≥80%, Medium ≥50%, Low below) — once as a measure for aggregate KPIs, and once as a calculated column for row-level exam classification. Keeping the threshold logic identical at both grains prevents the common bug where a summary KPI and a detail table disagree on what counts as "High."
📊 Dashboard 1 — Academic Performance Analysis

![Student Performance Analysis Dashboard](Student_performance_analysis_dashboard.png)

Built for: an academic head or principal doing a performance review.
Answers: "Where is the academic risk, and is it tied to a subject, class, or term?"
The KPI strip (Total Students, Total Exams, Average Score, Highest/Lowest Score, Overall Percentage) gives a complete health check in one glance, before drilling into any chart.
The Subject × Class bar chart shows performance sitting in a tight band (~49–53%) across Math, Science, English, History, and Geography — a useful diagnostic in itself, since it suggests the issue isn't one weak subject but something broader affecting most students similarly.
The Term trend line (Term 1: 50.12% → Term 2: 49.67% → Term 3: 50.10%) isolates when performance dipped, turning a static average into a timeline a school can investigate (was there a disruption in Term 2?).
The conditional-formatted student table converts raw percentages into an instantly scannable red/yellow/green triage list, so a reviewer doesn't have to read every row to find who needs attention.
Useful for: report cards reviews, identifying underperforming cohorts before parent-teacher cycles, and tracking whether interventions actually move the term-over-term trend.
📊 Dashboard 2 — Attendance & Behavior Analysis

![Attendance and Behavior Analysis Dashboard](Attendance_and_behavior_analysis_dashboard.png)


Built for: a discipline coordinator or counselor managing conduct and attendance.
Answers: "Is non-academic behavior creating the academic risk, and which class needs intervention first?"
A 90.05% attendance rate next to 7K behavior incidents across 1K students is the key tension this page is designed to surface — solid attendance alone doesn't guarantee good classroom conduct, so the two need to be tracked separately rather than assumed to move together.
The Behavior Type donut shows incidents split almost evenly across Disruptive, Late, Helpful, Participative, and Absent-without-notice (each ~20%) — this even spread indicates behavior issues are diverse rather than concentrated in one category, pointing toward a broad conduct program rather than a single targeted fix.
The monthly attendance bar chart lets a school check for seasonal patterns (e.g., spikes around exam months or holidays) at a glance.
The Top 10 Students with Behavior Incidents chart is a ready-made counselor worklist — no need to filter a raw table to find who to call in first.
The Class-wise matrix with conditional formatting works like a heatmap, immediately showing which class has the highest "Absent without notice" or "Disruptive" totals, so leadership can prioritize which classroom to visit first.
Useful for: weekly discipline review meetings, identifying at-risk classes before issues escalate, and correlating attendance dips with specific months or events.
📊 Dashboard 3 — Student Detailed Analysis (Drillthrough)

![Drillthrough Setting](Drillthrough_setting.png)

![Student Detailed Analysis Dashboard](Student_detailed_analysis_dashboard.png)

Built for: a teacher or counselor preparing for a one-on-one parent conversation.
Answers: "What does the complete picture look like for this one student — academics, attendance, and behavior together?"
Right-clicking any student name on Dashboard 1 or 2 drills through directly into this page, pre-filtered to that student — no manual searching required.
Drillthrough is deliberately configured with "Allow drillthrough when: Used as category" and "Keep all filters" ON — meaning whatever filters were active on the source page (Class, Term, etc.) carry through automatically, so the drill is contextual rather than resetting to a blank slate.
The page combines Subject-wise Performance, Behavior Type Distribution, and Attendance Status into one screen — replacing what would otherwise require manually cross-referencing three separate exports for the same student.
The featured example (a student with 96% attendance but only 46.5% average score) demonstrates exactly why this page earns its place in the project: it disproves the simple assumption that good attendance guarantees good grades, prompting a more specific, evidence-based conversation instead of a generic one.
Useful for: parent-teacher meetings, individual student case reviews, and counselors who need a 360° view without opening multiple reports.
🧭 Navigation Design
All three pages share a consistent bottom navigation bar, built with bookmarked buttons rather than Power BI's default page tabs — giving the report a polished, app-like feel intended for stakeholder presentation rather than purely internal analyst use.
🛠️ Tech Stack
Power BI Desktop · DAX · Star Schema Data Modeling · Drillthrough Navigation · Conditional Formatting
🚀 Why This Project Stands Out
Modeled as a true star schema with shared dimensions, not a flat table — built to scale as more fact tables get added
DAX written deliberately (CALCULATE, DIVIDE, AVERAGEX, SWITCH/TRUE) to avoid common aggregation mistakes, not just default quick measures
Drillthrough configured with filter-context preservation, turning three separate dashboards into one connected analytical journey
Every visual is tied to a specific real-world stakeholder and a specific real-world question — not added just to fill space.

👩‍💻 Author

Iqra Rangrej

Skills: Power BI | Power Query | DAX | Data Modeling | Star Schema | Data Visualization | Business Intelligence
