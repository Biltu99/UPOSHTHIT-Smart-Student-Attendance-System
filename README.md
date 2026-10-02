# UPOSHTHIT-Smart-Student-Attendance-System
You are helping me build a complete college project called **UPOSHTHIT — Smart Student Attendance System**.  The name "Uposhthit" comes from the Bengali word "উপস্থিত", meaning "Present".  Build this project from the beginning. Do NOT use Django, Laravel, React, Node.js, or other complicated frameworks.

# UPOSHTHIT — Smart Student Attendance System

> **উপস্থিত** (Uposhthit) means "Present" in Bengali.

A multi-factor attendance system for colleges that verifies each student's presence using **GPS location**, **bench QR codes**, **face recognition**, and a **5-second live video** — all under teacher-controlled attendance sessions.

---

## 📖 Project Overview

Traditional attendance systems rely on teachers calling names or students signing sheets. Both are easy to abuse. UPOSHTHIT makes proxy attendance difficult by requiring four independent verifications before recording a student as present.

### Core Workflow

**Teacher side:**
1. Login with Teacher ID
2. Select assigned subject
3. Click **START ATTENDANCE**
4. Monitor live check-ins
5. Click **END ATTENDANCE** when done

**Student side (during an open session):**
1. Login with Student ID
2. Click **Mark Attendance**
3. ✅ **GPS** — must be physically inside college premises
4. ✅ **Bench QR** — must scan the QR on their seat
5. ✅ **Face** — live camera frame compared against their registered face
6. ✅ **Video** — 5-second live recording with face visible throughout
7. Attendance saved only if all four pass

If any verification fails, the attendance is not recorded. The teacher can then manually mark the student after physically confirming presence — and every manual change is logged with a reason.

---

## 🛠 Technology Stack

| Layer | Technology |
|-------|-----------|
| Frontend | HTML5, CSS3, Vanilla JavaScript |
| Backend | PHP 8.0+ |
| Database | MySQL 8.0 (via phpMyAdmin) |
| Computer Vision | Python 3.10+ (face_recognition, OpenCV, Flask) |
| Auth | PHP Sessions |
| Password | `password_hash()` / `password_verify()` (bcrypt) |
| QR | html5-qrcode (browser) + qrserver.com (image generation) |
| GPS | Browser Geolocation API + server-side Haversine |
| Environment | VS Code + XAMPP |

### Architecture
Browser (HTML/CSS/JS)
│
▼
PHP Backend ──────► MySQL
│
▼
Python Verification Service (localhost:5001)
├─ Face encoding / comparison
└─ Video frame analysis

PHP is the primary backend. Python is a local microservice used only for computer vision.

---

## 📁 Folder Structure

uposhthit/
├── admin/ Admin panel pages
├── teacher/ Teacher panel pages
├── student/ Student panel pages
├── api/ JSON endpoints (GPS, QR, face, video)
├── auth/ Login/logout handlers
├── config/ Configuration + PDO connection
├── database/ SQL schema
├── includes/ Shared auth, helpers, layout
├── assets/
│ ├── css/
│ ├── js/
│ └── uploads/ Face photos + verification videos (protected)
├── python/
│ └── face/ Face + video verification service
├── index.php Landing page
└── README.md

---

## 🚀 Installation

### Prerequisites

- XAMPP (Apache + MySQL)
- PHP 8.0 or newer
- Python 3.10 or newer
- Modern browser (Chrome, Edge, Firefox)

### 1. Copy the project

Place the `uposhthit` folder inside:

<!-- C:\xampp\htdocs\ -->

### 2. Start XAMPP services

Open XAMPP Control Panel → **Start** Apache → **Start** MySQL.

### 3. Create the database

Browser → `http://localhost/phpmyadmin/` → **Import** → select:

<!-- uposhthit/database/uposhthit_db.sql -->


Click **Go**. It creates `uposhthit_db` with all 17 tables.

### 4. Configure the connection

Open `config/config.php`. Verify:

```php
define('DB_HOST', 'localhost');
define('DB_NAME', 'uposhthit_db');
define('DB_USER', 'root');
define('DB_PASSWORD', '');

// Create the first admin ///////////////////////////////////////////////////////

cd C:\xampp\htdocs\uposhthit
C:\xampp\php\php.exe auth\create_admin.php


//  Install Python dependencies

cd C:\xampp\htdocs\uposhthit\python
python -m pip install -r requirements.txt

// python -m pip install dlib-20.0.99-cp314-cp314-win_amd64.whl--
python -m pip install dlib-20.0.99-cp314-cp314-win_amd64.whl


// Start the Python service
python\face\start.bat

cd C:\xampp\htdocs\uposhthit\python\face
python app.py

// Leave the window open. Test:

// text
http://127.0.0.1:5001/health

// Access the system //////////////////////////////////////////////

http://localhost/uposhthit/



🔑 First-Time Setup Order
After logging in as admin:

Create a Department (e.g., BCA)

Create a Subject linked to that department

Create a Classroom (e.g., Room 101)

Create Benches with QR codes (B01, B02, ...)

Create a Teacher and assign them subjects (Assignments)

Create Students in the same department

Configure GPS Settings — set college location and radius

Print/download bench QRs and place on benches

Now:

Teacher can Start Attendance

Students can Mark Attendance with all four verifications

🧪 Verification Pipeline
Step	What's Checked	Where
GPS	Student within allowed radius	Server-side Haversine
Bench QR	Valid, active, in session's classroom	Server lookup by unique payload
Face	Live frame matches stored encoding (distance ≤ 0.55)	Python face_recognition
Video	~5s, face visible ≥70% of frames, single face	Python OpenCV frame sampling


🔒 Security Features
Bcrypt password hashing

PDO prepared statements (no string concatenation)

CSRF tokens on every POST

Session fixation prevention (session_regenerate_id)

HttpOnly + SameSite=Strict cookies

Rate limiting (5 attempts → 15-minute lockout)

Role-based access (admin / teacher / student)

Teacher queries scoped by teacher_id at SQL level

Uploads protected via .htaccess

Server-side verification — client cannot force success

Documented Limitations
Liveness detection is heuristic, not anti-deepfake. A pre-recorded video could potentially fool the check. Real liveness requires active challenges (blink/turn), depth sensors, or 3D structured light — out of scope for a college project.

GPS can be spoofed by a rooted device. The server uses distance thresholds and accuracy checks, but determined attackers with developer tools can fake coordinates.

CSV export is not formula-injection protected. Trusted admin use only.

🧑‍💻 Development
Testing
Database diagnostics: http://localhost/uposhthit/database_test.php (delete before submission)

Python health: http://127.0.0.1:5001/health

Common Issues
Face service unavailable:

Python service isn't running → start python app.py

Port 5001 in use → kill old Python process

GPS signal too weak:

Desktop without GPS → use a phone or relax the accuracy threshold in config/config.php (dev only)

Camera not working:

Browser permission denied → allow camera

Another app is using the camera → close it

dlib install fails:

Python version mismatch → use Python 3.10–3.12 or install pre-built wheel

📊 Database Schema
17 tables:

users, students, teachers, departments, subjects, teacher_subjects, classrooms, benches, attendance_sessions, attendance, attendance_videos, face_data, verification_logs, attendance_audit_logs, login_attempts, settings, plus implicit.

Key constraints:

attendance(session_id, student_id) unique → prevents duplicates

All foreign keys with ON DELETE CASCADE or SET NULL as appropriate

Bench QR payload unique

📝 License
College project — for academic use.


.

👤 Author
BILTU KUNDU
SVIMS
01.10.2026


---

## 📄 File 2: `SETUP_GUIDE.md` — quick reference for examiners

Create `C:\xampp\htdocs\uposhthit\SETUP_GUIDE.md`:

```markdown
# UPOSHTHIT — Quick Setup for Evaluation

## 5-Minute Startup

1. **XAMPP Control Panel** → Start **Apache** and **MySQL**

2. **Import database**
   - Open `http://localhost/phpmyadmin/`
   - Click **Import**
   - Select `database/uposhthit_db.sql`
   - Click **Go**

3. **Start Python service**
   - Double-click `python\face\start.bat`
   - Keep the window open

4. **Open the app**
   - `http://localhost/uposhthit/`

## Default Accounts

After running `auth/create_admin.php`:

| Role | ID | Password |
|------|-----|----------|
| Admin | (whatever you set) | (whatever you set) |

Sample teacher and student accounts must be created via the Admin panel.

## Demo Flow (5 minutes)

1. Login as **admin** → show dashboard statistics
2. Show **Students** list, **Teachers**, **Departments**
3. Show **Benches & QR** — display a bench QR image
4. Show **GPS Settings** — configured college location
5. Logout → login as **teacher**
6. Click **Start Attendance** on a subject
7. Open **session view** — live attendance list
8. In a second browser, login as **student**
9. Click **Mark Attendance**
10. Walk through: GPS → QR scan → face capture → 5-second video
11. Show **Attendance Successful**
12. Return to teacher view — student appears in present list
13. Show **End Attendance**
14. Show **Audit Trail** for the session
15. Show **Reports** with CSV export

## Checkpoints for Evaluators

- Multiple verification layers (GPS, QR, Face, Video)
- Role-based access control
- Audit trail for manual changes
- Reports with CSV export
- Session-based attendance (not time-based auto-expiry)
- Secure password handling (bcrypt)
- Prepared SQL statements



Project Report Content
Use this outline for your written report. Copy into Word/Google Docs and expand each section.

# UPOSHTHIT: Smart Student Attendance System
## Project Report

### 1. Introduction
- Problem: proxy attendance in colleges
- Solution: multi-factor verification under teacher-controlled sessions
- Name meaning: উপস্থিত = "Present" in Bengali

### 2. Objectives
- Eliminate proxy attendance
- Provide verifiable evidence of physical presence
- Give teachers full control over session timing
- Generate accurate attendance analytics

### 3. Literature / Existing Systems
- Traditional roll call
- Biometric fingerprint systems
- RFID cards
- Mobile-based apps
- Why multi-factor works better

### 4. System Requirements
**Hardware:** Laptop/PC with webcam, smartphone with camera (for GPS accuracy), printer (for QR codes)
**Software:** Windows 10+, XAMPP, PHP 8.0+, MySQL 8.0, Python 3.10+
**Browser:** Chrome/Edge/Firefox with camera and geolocation permissions

### 5. System Architecture
(Browser → PHP → MySQL + PHP → Python → Response)

Include an architecture diagram.

### 6. Database Design
- ER diagram
- 17 tables
- Key constraints
- Sample data

### 7. Module Descriptions
- Admin panel (student/teacher/subject/classroom/bench CRUD, GPS config, reports)
- Teacher panel (sessions, live monitoring, manual marking, audit)
- Student panel (dashboard, verification flow, history)
- Python service (face encoding, face comparison, video frame analysis)

### 8. Verification Pipeline
- GPS: Haversine distance, server-side validation
- QR: unique bench payload, classroom matching
- Face: 128-dimensional encoding, Euclidean distance ≤ 0.55
- Video: 5-second duration, face ratio ≥ 70%, single face

### 9. Implementation Highlights
- Session-based attendance (no auto-expiry)
- Append-only audit log
- Role-based scoping at SQL level
- Freshness windows per verification stage

### 10. Security Considerations
(Copy from README's Security section)

### 11. Testing
- Manual test matrix
- Security test results
- Edge cases

### 12. Limitations
- Liveness is heuristic, not anti-deepfake
- GPS spoofable by rooted device
- CSV formula injection not protected
- Single-classroom attendance (not multi-room simultaneous)

### 13. Future Enhancements
- Active liveness challenges (blink detection)
- Face encoding on-device for privacy
- Push notifications to teacher when student fails
- Mobile native app
- Integration with college ERP

### 14. Conclusion

### 15. References
- face_recognition library documentation
- OWASP Top 10 for PHP security
- Haversine formula source
- html5-qrcode documentation

📄 File 4: Demo Video Script
Record a 3–5 minute demo. Follow this script.

Scene 1 (0:00–0:20) — Intro
Show the homepage: UPOSHTHIT — Smart Student Attendance System

Speak: "UPOSHTHIT is a multi-factor student attendance system that verifies presence using GPS, bench QR codes, face recognition, and live video."

Scene 2 (0:20–1:00) — Admin panel
Log in as admin

Show dashboard with stat cards

Open Students → show a student

Open Benches & QR → show a bench QR image

Open GPS Settings → show the configured radius

Scene 3 (1:00–1:30) — Teacher starts session
Log out → log in as teacher

Open dashboard → click Start Attendance on a subject

Show session page with live "Present Students" table

Scene 4 (1:30–3:00) — Student verification flow
In another browser tab: log in as student

Click Mark Attendance

GPS step: Allow location → show ✓ Location verified

QR step: Scan the bench QR (or paste payload) → show ✓ Bench verified

Face step: Look at camera → click Capture → show ✓ Face verified

Video step: 5-second countdown → show ✓ Video verified

Return to dashboard → show success message

Scene 5 (3:00–3:30) — Teacher sees result
Switch back to teacher session page

Show the student now appears in the Present table

Click View Audit Trail → show log entry

Scene 6 (3:30–4:00) — Reports
Click End Attendance

Go to Reports

Show subject-wise and student-wise summaries

Click Export CSV → show downloaded file

Scene 7 (4:00–4:30) — Wrap up
Return to homepage

Speak: "UPOSHTHIT reduces proxy attendance through four independent verification layers, gives teachers full control over sessions, and provides an audit trail for every manual change. Thank you."

🧹 Cleanup Checklist (before submission)
Delete these files from the project:

□ database_test.php
□ auth/create_admin.php (if not already deleted)
□ Any scratch files: .ps1, .sh, test.php, tmp.php
□ Browser cache — not needed
Set production mode:

□ config/config.php → define('APP_ENV', 'production');
Restore DB defaults (or leave as-is):

□ SESSION_TIMEOUT back to 1800
□ GPS accuracy threshold back to 200
💾 Backup Instructions
Before submission, back up:

Project folder

text
Right-click C:\xampp\htdocs\uposhthit → Send to → Compressed (zipped) folder
Database

phpMyAdmin → uposhthit_db → Export

Method: Quick

Format: SQL

Click Go → save uposhthit_db_backup.sql

Python environment (optional)

Not needed — examiner can pip install from requirements.txt

Demo video

Save as .mp4 in a docs/ folder inside the project

📦 Submission Package Contents
Arrange your final submission folder like this:

text
UPOSHTHIT_Project/
├── uposhthit/                 ← full project folder
├── docs/
│   ├── Project_Report.docx
│   ├── Architecture_Diagram.png
│   ├── ER_Diagram.png
│   ├── Demo_Video.mp4
│   └── Screenshots/
│       ├── 01_homepage.png
│       ├── 02_admin_dashboard.png
│       ├── 03_teacher_session.png
│       ├── 04_gps_verified.png
│       ├── 05_qr_scan.png
│       ├── 06_face_verified.png
│       ├── 07_video_verified.png
│       ├── 08_attendance_success.png
│       └── 09_reports.png
├── database_backup.sql
└── README.md
🎤 Presentation Talking Points
When examiners ask questions, be ready with these answers.

Q: What makes this different from a regular attendance app?
A: Four independent verification layers. A student cannot mark attendance by simply clicking a button — they must physically be inside the college, at a specific bench, and pass a face + video check.

Q: How does the GPS verification work?
A: The browser reports coordinates. PHP calculates the Haversine distance to the college's configured location. If the student is outside the allowed radius (or accuracy is too poor), the request is rejected.

Q: What stops a student from sending a fake GPS location?
A: The server doesn't trust the client. It recalculates the distance independently and checks the reported accuracy. Developer tools can change the frontend, but the server-side check still runs.

Q: How does face recognition work?
A: A Python microservice uses the face_recognition library (128-dimensional encoding via dlib). Each student's encoding is stored securely on first registration. During attendance, a live frame is compared and accepted if the Euclidean distance is below the threshold.

Q: Can a student use a photo of someone else?
A: The face check requires a live frame from the camera. But note: our video check is heuristic — it's not full liveness detection. It would take active challenges (blink, turn head) or depth-sensing hardware to defeat a determined deepfake attack. That's documented as a limitation.

Q: What if the GPS or internet fails?
A: The teacher can manually mark attendance. Every manual change is logged with a reason and stored in the audit table.

Q: How is proxy attendance prevented?
A: (a) GPS — must be physically present. (b) Bench QR — must be at the exact seat. (c) Face — must match the registered student. (d) Video — face must remain visible for 5 seconds. Any single failure blocks automatic attendance.

Q: What about the audit log?
A: Every manual change to attendance is stored append-only with old status, new status, reason, and the user who made the change. The admin can review all changes system-wide.

Q: How does the Python service communicate with PHP?
A: PHP sends base64-encoded images/videos via HTTP POST to 127.0.0.1:5001. Python processes them and returns JSON. Both sides authenticate with a shared API key. The Python service binds to localhost only and is never exposed to the network.

Q: What database is used and why?
A: MySQL via PDO. It's widely supported, works with XAMPP, and PDO prepared statements give us protection against SQL injection.

Q: How do you handle concurrency (multiple students marking at once)?
A: Attendance insert uses a unique constraint on (session_id, student_id) at the DB level. Even if two requests arrive simultaneously, only one succeeds.

Q: What is the biggest limitation?
A: Liveness detection. Our 5-second video check verifies duration and face visibility, but cannot distinguish a real person from a high-quality video of that person. Real liveness detection would require active challenges or depth-sensing hardware. We've documented this honestly in the report.

✅ Final Verification Before Submitting
Run through this checklist one last time:

□ http://localhost/uposhthit/ loads the homepage
□ Admin can log in and see the dashboard
□ Teacher can log in and start a session
□ Student can log in and see the active session
□ Full verification flow works end-to-end
□ Attendance saves in the database
□ attendance_videos row appears
□ Reports page loads with data
□ CSV export downloads
□ Audit trail shows manual changes
□ No PHP warnings in error.log
□ Python service starts cleanly
□ database_test.php deleted
□ APP_ENV set to production
□ Database backup saved
□ Project folder zipped
□ Demo video recorded
□ Report document finalized
🎓 You're Done
You have built a complete multi-factor attendance system with:

17 normalized MySQL tables

3 role-based dashboards (admin, teacher, student)

Full CRUD for users, subjects, classrooms, benches

4-stage verification pipeline (GPS, QR, face, video)

Python microservice for computer vision

Complete audit trail

Reports with CSV export

Comprehensive security hardening

This is a genuinely impressive BCA project — it combines web development, computer vision, geolocation, and security in a single coherent system.

📋 Reply Back
Tell me:

Did you finish all 16 steps? Y/N

What's still broken or unclear? (list)

Do you want help with anything specific:

Project report writing?

Diagram creation (architecture, ER)?

Debugging a specific failure?

Practice Q&A for the viva?

Whatever's next, reply with it and I'll help. Good work getting this far.
