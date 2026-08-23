---
share_cop4331c: "true"
site-folder: docs/Lecture Slides
theme: ucf-knights.css
height: "1080"
width: "1920"
---

# COP 4331C
## Processes of Object Oriented Software Development
### Fall 2026

---

## Instructor:  Dr. John Aedo
## Office Location:  HEC 328
- Office Hours:  
	- Monday, Wednesday 4:45pm - 5:45pm
	- Tuesday, Thursday 3:00pm - 5:00pm

---
## Welcome and Course Overview

- We're going to build cool stuff and learn how the "pros" build software
- The chief objective is to prepare you for both Senior Design and the professional software development world
- This is your on-ramp to Senior Design.  Many of the course elements are designed specifically to prepare you for the 2-semester trial of tears that is your capstone project.
- Prerequisites:  CS2 (COP 3503C) and for CS majors, Foundation Exam (COT 3960)

---
## Course Materials

- All required materials will be provided via Canvas
- Most of these materials will be available concurrently on the course website:
	- https://teaching.johnaedo.com/cop4331c-site

- You will be required to purchase a domain and a virtual server for our assignments.
	- Costs can vary, but should be inexpensive.
	- Domain registration can be as cheap as a buck of two for a year.
	- A virtual server should run you under $10\/month, usually cheaper.

- Other software or technology requirements:
	- A device capable of running Visual Studio Code or equivalent software, possibly also some server services such as NodeJS, mySQL, Docker etc. if you wish to code locally.
	- A phone that is capable of successfully registering your attendance via UCF Here.

---
## Class Schedule and Format

- Section 1: Tuesdays and Thursdays, 9:00am - 10:15am
- Section 2:  Mondays and Wednesdays, 6:00pm - 7:15pm
- Location:  HEC 125
- **In Person**
- Sessions will be a mix of lectures and live-coding demos
- No Class on Monday 9/  in observance of Labor Day
- No Class on Wednesday 11\/11 in observance of Veteran's Day
- No Class on the week of 11\/23 for Thanksgiving
- Labor Day and Veteran's Day will have recorded lectures available since Section 1 will hold classes as normal that week.

---
## Attendance Policy

- Attendance comprises 10% of your final grade
- Attendance will be taken randomly throughout the semesters
	- It may be at the beginning of class, in the middle of class, or at the end.
- Use UCF Here to register your attendance
	- Make sure your phone works with UCF Here.
	- If you have issues, contact UCF IT
- Excused absences must be accompanied by documentation such a doctor's note.

---
## Punctuality and Class Participation

- If attendance is taken at the start of class, the QR code will remain available for 5 minutes after the start.
- I do not allow for tardiness.  If you miss the QR code, you are considered absent.
- I ask that if you do not wish to stay for class, please do not come at all.  Unless you have a pressing need to be somewhere in the middle of class, it is fundamentally disrespectful to up and walk.  I ask kindly that you not do this.
- If you choose to leave in the middle of class, please be considerate and respectful of others.  Do so quietly.  Note that the doors to this classroom are LOUD.  DO NOT LET THEM SLAM.

---
## Assignment Overview

| Activity            | Contribution |
| ------------------- | ------------ |
| Mid-Term Exam       | 10%          |
| LAMP Tutorial       | 5%           |
| LAMP Project        | 15%          |
| MERN Tutorial       | 10%          |
| MERN Project        | 25%          |
| Quizzes             | 5%           |
| Labs                | 10%          |
| Attendance          | 10%          |
| Instructor Check-In | 10%          |

---
## Grading Scale

| Letter Grade | Range        |
| ------------ | ------------ |
| A            | 100% - 94%   |
| A-           | \< 94% - 90% |
| B+           | \< 90% - 87% |
| B            | \< 87% - 84% |
| B-           | \< 84% - 80% |
| C+           | \< 80% - 77% |
| C            | \< 77% - 74% |
| C-           | \< 74% - 70% |
| D+           | \< 70% - 67% |
| D            | \< 67% - 64% |
| D-           | \< 64% - 60% |
| F            | \< 60% - 0%  |

---
## Get Your Head in the Game!
- This course is very different from any you've had so far.
- You cannot expect to succeed if you put off the projects until the last minute.
- Success requires time management and being proactive, not just with your own tasks, but your teammates!
- The best strategy is to spend some time every week working on the projects.
- You do not have time to dawdle.  This course is packed and will require a lot of self-learning and discipline to stay on task!

---
## Learning
- What if nobody on your team knows about databases, or APIs, or front ends?
- You will learn the basics in class, but these are just "getting started" lectures
- You will need to spend time learning on your own with YouTube videos and online courses.
- You can also visit me during office hours (see above)
- I am also willing to schedule extra zoom sessions.
- In this class, you will learn a lot more than I have time to teach you.

---
# Project Descriptions
---
## LAMP Stack Project
- A contact manager web app built on a LAMP stack (Linux, Apache, MySQL, PHP) — no Python
- Users can log in or sign up (register); once authenticated, they perform standard CRUD operations on their contacts:
	- **C** – Create
	- **R** – Read
	- **U** – Update
	- **D** – Delete

---
## Expected Features

- Includes a search function for contact records (case-insensitive, partial match — e.g. searching "Jo" should match John, Jones, Jobs)
- Minimum contact fields: First Name, Last Name, Email, Phone
- Must implement at least one one:many relation in the database schema
- Must communicate from front end to backend through an API; at least one endpoint must be demonstrated with Bruno.
- Application must be secured with TLS and passwords hashed and salted.
- Must reside on a remote server (not a local machine) — e.g. GoDaddy, Heroku, Digital Ocean, AWS, or Azure.  Class demos will be on Digital Ocean.
- Deliverables: professional PowerPoint slides.  All team members must participate meaningfully in the presentation.
- For the current rubric, check Canvas

---
## MERN Project (Large Project)

- The larger, capstone-style project of the semester (MongoDB, Express, React, Node)
- Unlike the small project's assigned teams, you may form your own team for this project
- Detailed requirements and rubric to be provided separately — check Webcourses for updates

---
## Team Formation and Group Work Policies

- For the small project, you will be assigned to a team; for the large project, you may form your own team
- In most cases, everyone on a team receives the same grade for a project — exceptions apply for members who do not contribute their fair share
- You are expected to hold regular team meetings (in person and/or via a tool such as Discord) in addition to class time
- Take personal responsibility for your part of the work — "Do what you say you will do" (DWYSYWD) is a philosophy that will follow you into Senior Design

---
## Suggested Roles
- **Database** – creates the entity relationship diagram (ERD) for the team, which becomes part of the presentation**Project manager** – may also develop, to provide overlap
- **API developers** – build and test endpoints, typically via SwaggerHub, so front-end developers know how to consume them
- **Front-end developers** – build the UI (Bootstrap/jQuery recommended), following API documentation; avoid alert/dialog boxes except for delete confirmations

---
## Expectations

- Do not ghost your team — social loafing is not fair to your teammates and will affect your individual grade
- If you hold a full-time job, plan for strong time management; this is a 4000-level class that prepares you for Senior Design and requires real time and dedication

---
## Assignment Submission

- Only one team member needs to submit the presentation slides.
- All project code must be kept in a public GitHub repository so the instructor and TA can access it and check progress; add the GitHub link to the project spreadsheet
- APIs must be documented and testable via Bruno 
- Keep documentation current so the team knows how to use each endpoint and any changes made
---
## Late Assignment Policy

### No late work will be accepted without prior approval or documentation (i.e., doctor's note)

---
## Assignment Expectations and Quality Standards

- Project presentations must use professional PowerPoint slides
- Follow good UX practices in your web apps: the only alert/dialog that should appear is a delete confirmation; test on multiple devices, including full-screen desktop and phone
- Do not load entire recordsets into memory — design search and data access to scale (e.g., a search for "Jo" should efficiently match John, Jones, Jobs without pulling every record)

---
## AI and Technology Use Policy

### AI is allowed in the course, but must be fully disclosed during your presentations:
- Which services/models were used (e.g. Claude, Gemini, ChatGPT, local model)
- In which capacity were they used (e.g. code generation, debugging help, learning assistance)
- If an agent was used, provide the harness used (e.g. Antigravity, Claude Code, Codex etc.), system prompt (if approrpriate) and any other parameters used.

### This is a no-shame course!
- You will not be penalized or in any way shamed for using AI in this course.  (Future courses may in fact include a module on agentic coding!)

---
## Communication Expectations

- **E-Mail Only!**  I am available exclusively via e-mail.  
- ***DO NOT use Wecourses/Canvas Messaging*** 
- I try my best to respond in a timely manner, but stuff happens.  You are well within your right to bug me after class or office hoursif I haven't responded within 3 business days.
- I do, however, try to maintain a healthy work-life balance.  I love my job, but I also love my family.  I am unavailable after 5pm (lectures and office hours not withstanding) and during weekends.

---
