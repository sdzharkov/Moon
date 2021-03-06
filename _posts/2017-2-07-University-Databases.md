---
layout: post
title:  "Creating a Powerful University Database"
date:   2017-2-07
excerpt: "Using Postgres and Python to promote efficiency."
tags: [SQL, Postgres, Python]
comments: false
---

## Introduction

SQL. A behemoth of clustered grids used to repetitively preserve all of our credentials from the variety of services we hold dear to one another. Last quarter, I enrolled in ECS165A, a course focused on the development and management of high-scale databases. The course progressed from relational algebra to database decompositions, used to find losses joins in order to preserve information in case of a misstep. The plot of this course leads toward the final project, the creation of a university database that lacks repetitive information and performs optimal look-ups.

# The Data

We are provided with 93 Excel files of university data, each referencing a specific quarter and task. Each file equates to approximately 6000 lines of information, divided up between three sections: the class, the meetings, and the students. The data was structured like this for one course in a quarter:

<figure>
    <img src="../assets/img/course.png">
</figure>

# The Task

Parse the existing data for all 93 files and moving the provided data towards their specified table in Postgres. The problem: the data consisted of multiple errors. If a class had no students, it would not be considered a class. If a professor did not exist, we did not obtain the needed primary key for the class. On top of that, the lack of random data provided edge cases for which tables would not need to exist, or the rather but without specified information. This was only the start towards the many issues a real life database implementation may hold.

# The Solution

The first step was to structure how the data will be held. My partner designed the following entity-relationship diagram:
<figure>
    <img src="../assets/img/erdplus-diagram.png">
</figure>

The Physical structure of our table looked like this:
courses(*courseID*,subject,crse,sec)

instructors(*iid*, name)

buildings(*bid*,building, room)

offerings(*cid,term*,courseID,totUnits)

meetings(*cid, term*, type, days, t1, t2, *iid, bid*)

student(*sid*, surname, prefname, email)

studentTakes(*sid, cid, term*, seat, level, units, class, major, grade, status)

Our structure was oriented around the table “Offerings”, a central location for repeated courses that only focused on the term and CID of the course. One might think that this table is strange due to it’s connection to courses, but in fact the use of the offering table will not require repetitive information and will provide a way to separate the course from the term it was offered. This is perfect because the actual term during which the course was offered perfectly engages to the time a student takes the course, providing an effective many-to-one relationship between the two tables. Meetings relied upon a secondary key from Offerings to differ the actual meeting types, as there can be multiple meetings per quarter for each offering. The Take table is one of the most important structures to be maintained, as it preserves every student’s record of taking a specified course. This is intuitively smarter than bundling the Student and Takes tables together, as it promotes a lack of repetitive information. Something to note is that our relationships are held together by a large portion of many-to-one relationships. This is done to promote a lack of repetition and to hold tables accountable for their specified course and the meetings for that specific quarter.


## The Code

I cannot actually post source code to our project due to the discretion of the professor, but if this a recruiter/employer that is reading this post I have been granted permission to display the source on GitHub. 