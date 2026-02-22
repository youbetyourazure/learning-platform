# Module 1: Django Fundamentals - YouTube Video Script
## 90-Minute Complete Walkthrough

---

## Video Metadata
- **Title:** "Building Healthcare Apps with Django - Module 1: Django Fundamentals (Complete Tutorial)"
- **Description:** 
  ```
  Complete 90-minute tutorial on Django fundamentals for healthcare development.
  Learn why we chose Django over MAUI, set up your first project, and understand
  the decision-making process behind enterprise technology choices.
  
  🔗 Written Module: [Link to MODULE_1_Django_Fundamentals.md]
  🔗 GitHub Code: https://github.com/graceandcompanyllc/transitionalcareapp
  🔗 Learning Platform: https://youbetyourazure.com
  
  ⏱️ Timestamps:
  00:00 - Introduction & Who This Is For
  05:00 - Lesson 1: Technology Decision Journey
  25:00 - Lesson 2: Django Setup & First Project
  50:00 - Lesson 3: Understanding Django ORM
  70:00 - Lesson 4: Your First Model & Admin Panel
  85:00 - Knowledge Checks & Next Steps
  
  📚 What You'll Learn:
  - Why Django beat MAUI 95/100 vs 52/100
  - Real decision matrix used by Grace & Company
  - Virtual environment setup (Python/Windows/Mac)
  - Creating your first Django project
  - Understanding Django's architecture
  - Building your first model (Hospital)
  - Django admin panel magic
  
  🎯 Prerequisites: Basic Python knowledge (if statements, functions, classes)
  
  #Django #Python #Healthcare #Azure #WebDevelopment #Tutorial
  ```
- **Tags:** Django, Python, Healthcare, Web Development, Tutorial, Azure, OpenAI, Enterprise Development, MAUI vs Django, PostgreSQL

---

## Technical Setup
- **Resolution:** 1920×1080 (1080p)
- **Frame Rate:** 60fps
- **Video Editor:** DaVinci Resolve or Adobe Premiere
- **Screen Recording:** OBS Studio with display capture
- **Audio:** Blue Yeti microphone or equivalent
- **VS Code Theme:** Dracula (high contrast for visibility)
- **Terminal Font:** 16pt (readable in recordings)
- **Browser:** Chrome with clean tab bar (no bookmarks visible)

---

## Script with Timestamps

### [00:00-05:00] INTRO - Who This Is For & What You'll Learn (5 minutes)

**[SCREEN: Show youbetyourazure.com landing page]**

**NARRATION:**
> "Hey everyone, welcome to Module 1 of 'Building Healthcare Apps with Django' - part of the You Bet Your Azure AI Learning platform. I'm excited to walk you through our complete journey from researching technologies to deploying a production healthcare system serving 5,000+ users."
>
> **[PAUSE 2 seconds]**
>
> "This isn't your typical tutorial using toy examples with Person and Employee models. Everything you're about to see is *real production code* from Grace & Company Healthcare Systems - a platform managing patient care across multiple hospitals."
>
> **[SCREEN: Show screenshot of actual hospital dashboard]**
>
> "Who is this course for? If you already know basic Python - if statements, functions, classes - you're ready. We'll assume you understand Python fundamentals, but we'll teach Django from absolute zero."
>
> **[SCREEN: Show module outline]**
>
> "In this 90-minute module, we'll cover four major lessons:"
> - "Lesson 1: The Technology Decision Journey - why we chose Django over .NET MAUI with a real decision matrix scoring"
> - "Lesson 2: Django Setup - virtual environments, first project, understanding Django's structure"
> - "Lesson 3: Django ORM - what is an ORM and why it's game-changing"
> - "Lesson 4: Your First Model - building a Hospital model with the auto-generated admin panel"
>
> **[PAUSE 2 seconds]**
>
> "By the end, you'll understand enterprise technology decisions, have Django running locally, and have created your first database model. Let's get started!"

---

### [05:00-25:00] LESSON 1 - Technology Decision Journey (20 minutes)

**[SCREEN: Show MAUI research screenshot]**

**NARRATION:**
> "Let's talk about one of the most important decisions in any project: choosing your technology stack."
>
> **[PAUSE]**
>
> "When we started Grace & Company, we didn't just randomly pick Django. We spent weeks researching .NET MAUI - Microsoft's cross-platform framework. And honestly, it looked *amazing* at first."
>
> **[SCREEN: Show Visual Studio screenshot]**
>
> "MAUI promised 'build once, run everywhere' - Windows, macOS, iOS, Android from a single C# codebase. As a Microsoft shop using Azure and Microsoft 365, this seemed perfect."

**[SCREEN: Show MAUI dealbreakers slide]**

> "But then we hit the dealbreakers. Let me show you the three issues that killed MAUI for us:"
>
> **[CLICK to highlight #1]**
>
> "First: Multiple platform builds. You're not really 'building once' - you're building *four separate apps*. Each platform needs testing. Each platform has quirks. Android behaves differently than iOS. Windows has different UI patterns than macOS. You're essentially maintaining four apps."
>
> **[CLICK to highlight #2]**
>
> "Second: App store gatekeepers. Apple takes 3-7 days to review every update. Google takes 2-5 days. So you fix a critical bug on Monday... and your users don't get the fix until next *week*. In healthcare, that's unacceptable."
>
> **[SCREEN: Show fake app store screenshot "App review in progress"]**
>
> "Imagine telling a hospital: 'Yes, we found the bug making appointments disappear, but Apple is reviewing our update, so you'll have the fix... sometime next week?' That's a nightmare."
>
> **[CLICK to highlight #3]**
>
> "Third: Version fragmentation. Users don't update apps consistently. Three months after launch, you might have users on v1.0, v1.1, v1.2, and v1.3 - all with different bugs. You can't force updates. You're supporting multiple versions forever."

**[SCREEN: Show Django advantages slide]**

> "Now let's compare that to Django - a web framework where users access your app through a browser."
>
> **[PAUSE for emphasis]**
>
> "When you deploy to Django, what happens? You push your code to Railway or Heroku or Azure App Service, and within 30 seconds, *every single user* has the latest version. Everyone sees the same thing. No platform differences. No app store delays. No version fragmentation."
>
> **[SCREEN: Show terminal with git push command]**
>
> "Bug fix on Monday morning? Fixed for everyone by lunch. That's the power of web-based deployment."

**[SCREEN: Show decision matrix table]**

> "So we built a decision matrix to score both options objectively. Let me walk you through the categories:"
>
> **[CLICK through each row, explaining]**
>
> - "**Deployment**: MAUI scores 2/10 (four separate builds), Django scores 10/10 (single web deployment)"
> - "**Update Speed**: MAUI 3/10 (app store delays), Django 10/10 (instant updates)"
> - "**User Fragmentation**: MAUI 3/10 (multiple versions), Django 10/10 (everyone on latest)"
> - "**Cross-Platform**: MAUI 8/10 (works but testing nightmare), Django 10/10 (browser is universal)"
> - "**Development Speed**: MAUI 6/10 (C# and XAML), Django 9/10 (Python is fast)"
> - "**Third-party Libraries**: MAUI 7/10 (strong .NET ecosystem), Django 10/10 (PyPI is massive)"
>
> **[SCREEN: Show final scores: MAUI 52/100, Django 95/100]**
>
> "Final scores: MAUI 52 out of 100, Django 95 out of 100. Django wasn't just better - it was *overwhelmingly* better for our use case."

**[SCREEN: Show success stories - Instagram, NASA, Spotify logos]**

> "And Django has *proven* scale. Instagram serves 2.35 billion users on Django. NASA uses Django for mission-critical systems. Spotify, Pinterest, Washington Post - all Django."
>
> **[PAUSE]**
>
> "If it's good enough for 2.35 billion Instagram users, it's good enough for our 5,000 hospital users."

**[SCREEN: Show bug fix comparison timeline graphic]**

> "Let me give you a concrete example. Last month, we found a bug in the appointment scheduling logic."
>
> "**Django path:** I fixed the code, ran tests, pushed to GitHub, Railway auto-deployed in 31 minutes from bug report to fix live. *Every user* got the fix instantly."
>
> "**MAUI path:** Fix code, build for iOS, submit to Apple (3-7 days), build for Android, submit to Google (2-5 days), build for Windows (Microsoft Store 1-3 days). *Best case:* 3 days. *Realistic:* 10-14 days. And users who don't update apps? They never get the fix."
>
> **[PAUSE for emphasis]**
>
> "31 minutes versus 10-14 days. That's why we chose Django."

---

### [25:00-50:00] LESSON 2 - Django Setup & First Project (25 minutes)

**[SCREEN: Switch to live coding - blank VS Code]**

**NARRATION:**
> "Alright, enough theory. Let's build something. I'm going to assume you have Python installed - if not, pause the video and grab Python 3.11 or later from python.org."
>
> **[PAUSE]**
>
> "First, let's verify Python is installed. Open your terminal."

**[TYPE in terminal]**
```bash
python --version
```

**[SHOW output: Python 3.11.x]**

> "Great, Python 3.11 confirmed. Now, we're going to use a *virtual environment*. This is crucial."

**[SCREEN: Show simple diagram of virtual environments]**

> "A virtual environment is like a separate Python installation for each project. Why? Imagine Project A needs Django 4.2 and Project B needs Django 5.0. Without virtual environments, you'd have conflicts. With virtual environments, each project has its own isolated packages."
>
> **[PAUSE]**
>
> "Creating a virtual environment is simple:"

**[TYPE in terminal]**
```bash
python -m venv .venv
```

**[WAIT for .venv folder to appear in VS Code]**

> "That created a `.venv` folder containing our isolated Python. Now we *activate* it."

**[TYPE in terminal - Windows]**
```powershell
.venv\Scripts\activate
```

**[OR for Mac/Linux - show both]**
```bash
source .venv/bin/activate
```

**[SHOW terminal prompt change: (venv) appears]**

> "See that `(venv)` prefix? That means we're inside our virtual environment. Everything we install now stays in this project."
>
> **[PAUSE]**
>
> "Now, let's install Django:"

**[TYPE in terminal]**
```bash
pip install Django==5.2.7
```

**[WAIT for installation output]**

**[SCREEN: Show pip output scrolling]**

> "Django is downloading... and done. Let's verify:"

**[TYPE in terminal]**
```bash
python -m django --version
```

**[SHOW output: 5.2.7]**

> "Perfect. Django 5.2.7 installed."

**[PAUSE]**

> "Now let's create our first Django project. I'm going to call it `hospital_demo`:"

**[TYPE in terminal]**
```bash
django-admin startproject hospital_demo
```

**[WAIT for folder structure to appear in VS Code]**

> "Look at what Django created for us:"

**[CLICK through folder structure in VS Code sidebar]**

```
hospital_demo/
    manage.py
    hospital_demo/
        __init__.py
        settings.py
        urls.py
        asgi.py
        wsgi.py
```

> "Let me explain each file:"
> - "`manage.py` - Your command-line tool for everything: running servers, creating databases, managing users"
> - "`settings.py` - Configuration: database, installed apps, security settings"
> - "`urls.py` - URL routing: maps URLs like `/patients/` to code"
> - "`wsgi.py` and `asgi.py` - Deployment files for production servers"
>
> **[PAUSE]**
>
> "Now, let's run the development server:"

**[TYPE in terminal]**
```bash
cd hospital_demo
python manage.py runserver
```

**[WAIT for server output]**

**[SHOW terminal output:]**
```
Watching for file changes with StatReloader
Performing system checks...

System check identified no issues (0 silenced).

You have 18 unapplied migration(s). Your project may not work properly until you apply the migrations for app(s): admin, auth, contenttypes, sessions.
Run 'python manage.py migrate' to apply them.

February 21, 2026 - 23:45:00
Django version 5.2.7, using settings 'hospital_demo.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CTRL-BREAK.
```

> "Don't worry about those migration warnings - we'll handle that in a minute. Notice the URL: `http://127.0.0.1:8000/`. Let's visit it."

**[SWITCH to browser, navigate to http://127.0.0.1:8000/]**

**[SHOW: Django rocket ship "The install worked successfully! Congratulations!"]**

> "There it is! The Django rocket ship. This means Django is running successfully."
>
> **[PAUSE for emphasis]**
>
> "This is huge. You just created a web server in two commands. No Apache configuration, no nginx setup, no IIS install. Django includes a development server out of the box."

**[SWITCH back to terminal, press Ctrl+C to stop server]**

> "Now, let's handle those migrations that Django warned us about. Migrations are how Django sets up its database."

**[TYPE in terminal]**
```bash
python manage.py migrate
```

**[SHOW migration output]**
```
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, sessions
Running migrations:
  Applying contenttypes.0001_initial... OK
  Applying auth.0001_initial... OK
  Applying admin.0001_initial... OK
  [... more migrations ...]
```

> "Django just created a SQLite database with tables for users, sessions, and admin. We'll switch to PostgreSQL later, but SQLite is perfect for development."

**[PAUSE]**

> "Now let's create an admin user so we can access Django's admin panel:"

**[TYPE in terminal]**
```bash
python manage.py createsuperuser
```

**[SHOW prompts and type responses]**
```
Username: admin
Email: admin@example.com
Password: ****
Password (again): ****
Superuser created successfully.
```

> "Good. Now restart the server:"

**[TYPE]**
```bash
python manage.py runserver
```

**[SWITCH to browser, navigate to http://127.0.0.1:8000/admin/]**

**[SHOW: Django admin login screen]**

> "Here's Django's admin panel. This is automatically generated - you didn't write any HTML, CSS, or JavaScript. Django built this for you."

**[LOGIN with admin/password credentials]**

**[SHOW: Django admin dashboard with Users and Groups]**

> "And we're in. Two tables show up: Users and Groups. These are Django's built-in authentication tables. We can manage users without writing any code."
>
> **[CLICK on Users, show the admin user we just created]**
>
> "There's our admin user. This is fully functional - we can create users, set permissions, everything."
>
> **[PAUSE]**
>
> "This is why people love Django. The admin panel alone saves *weeks* of development time. Instead of building a user management UI, Django gives it to you free."

---

### [50:00-70:00] LESSON 3 - Understanding Django ORM (20 minutes)

**[SCREEN: Back to VS Code, create new file `notes.md`]**

**NARRATION:**
> "Before we build our first model, let's talk about the ORM - Object-Relational Mapper. This is one of Django's most powerful features."

**[SCREEN: Show simple diagram: Python ↔ ORM ↔ SQL ↔ Database]**

> "Here's what an ORM does: It translates between Python code and SQL. You write Python, Django generates SQL, the database executes it, and Django converts results back to Python objects."
>
> **[PAUSE]**
>
> "Let me show you a comparison. Without an ORM, creating a patient record looks like this:"

**[SCREEN: Show SQL code]**
```sql
INSERT INTO patients (first_name, last_name, date_of_birth, hospital_id)
VALUES ('John', 'Doe', '1980-05-15', 1);
```

> "With Django's ORM, the same operation looks like this:"

**[SCREEN: Show Python code]**
```python
Patient.objects.create(
    first_name='John',
    last_name='Doe',
    date_of_birth='1980-05-15',
    hospital_id=1
)
```

> "Much more readable, right? But the benefits go way beyond syntax."

**[SCREEN: Show three benefits slide]**

> "**Benefit 1: Database-agnostic**. That Python code works with PostgreSQL, MySQL, Oracle, SQL Server - you just change one setting. The SQL version? You'd need different SQL for each database."
>
> **[PAUSE]**
>
> "**Benefit 2: SQL injection protection**. The ORM automatically escapes dangerous input. If someone tries to hack you with `'; DROP TABLE patients;--`, Django escapes it safely. With raw SQL, you'd need to manually sanitize inputs."
>
> **[PAUSE]**
>
> "**Benefit 3: Less code**. Compare fetching all active patients:"

**[SCREEN: Show comparison]**

**Raw SQL:**
```python
cursor.execute("SELECT * FROM patients WHERE status = 'active'")
rows = cursor.fetchall()
patients = []
for row in rows:
    patient = Patient(
        id=row[0],
        first_name=row[1],
        last_name=row[2],
        # ... map all columns manually
    )
    patients.append(patient)
```

**Django ORM:**
```python
patients = Patient.objects.filter(status='active')
```

> "Same result, one line instead of ten. The ORM handles all the conversion."

---

### [70:00-85:00] LESSON 4 - Your First Model & Admin Panel (15 minutes)

**[SCREEN: Back to VS Code, terminal]**

**NARRATION:**
> "Alright, let's build our first Django model. We're creating a Hospital model."
>
> "First, we need to create a Django *app*. In Django terminology, a *project* is your entire website, and an *app* is a module within it. Our project is `hospital_demo`, and we'll create an app called `core`:"

**[TYPE in terminal]**
```bash
python manage.py startapp core
```

**[SHOW: Django creates core/ folder]**

> "Django created a `core` folder with several files. The important one right now is `models.py`."

**[OPEN core/models.py in VS Code]**

**[SHOW: Empty file with just imports]**
```python
from django.db import models

# Create your models here.
```

> "Let's build our Hospital model:"

**[START typing, explaining each line]**

```python
from django.db import models

class Hospital(models.Model):
    name = models.CharField(max_length=200)
    address = models.TextField()
    phone = models.CharField(max_length=20)
    email = models.EmailField()
    is_active = models.BooleanField(default=True)
    created_at = models.DateTimeField(auto_now_add=True)
    
    def __str__(self):
        return self.name
```

> "Let me explain each field:"
> - "`name = models.CharField(max_length=200)` - A text field for the hospital name, maximum 200 characters"
> - "`address = models.TextField()` - Unlimited text for the full address"
> - "`phone = models.CharField(max_length=20)` - Phone number as text (not integer, because of formatting)"
> - "`email = models.EmailField()` - Email with automatic validation"
> - "`is_active = models.BooleanField(default=True)` - Active/inactive flag, defaults to True"
> - "`created_at = models.DateTimeField(auto_now_add=True)` - Automatically sets timestamp when created"
>
> **[PAUSE]**
>
> "And the `__str__` method returns a readable name for the admin panel."

> "Now we need to tell Django about this app. Open `settings.py`:"

**[OPEN hospital_demo/settings.py]**

**[SCROLL to INSTALLED_APPS]**

**[ADD 'core' to the list]**
```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'core',  # <-- Our new app
]
```

> "Saved. Now Django knows about our `core` app. Next, we create a migration:"

**[SWITCH to terminal]**
```bash
python manage.py makemigrations
```

**[SHOW output]**
```
Migrations for 'core':
  core/migrations/0001_initial.py
    - Create model Hospital
```

> "Django created a migration file describing how to build the Hospital table. Now we apply it:"

**[TYPE]**
```bash
python manage.py migrate
```

**[SHOW output]**
```
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, core, sessions
Running migrations:
  Applying core.0001_initial... OK
```

> "Done! The Hospital table now exists in the database."

> "But here's the magic: let's register this model with the admin panel. Create `admin.py` in the core folder:"

**[CREATE core/admin.py]**
```python
from django.contrib import admin
from .models import Hospital

admin.site.register(Hospital)
```

> "Three lines. That's it. Now let's see what happens:"

**[SWITCH to browser, refresh http://127.0.0.1:8000/admin/]**

**[SHOW: Hospital appears in the admin panel!]**

> "There it is! Django automatically generated a full CRUD interface - Create, Read, Update, Delete - for hospitals. Let's create one:"

**[CLICK "Add Hospital"]**

**[FILL IN form with Barnes-Jewish Hospital example]**

**[CLICK Save]**

**[SHOW: Hospital list with "Barnes-Jewish Hospital"]**

> "And there's our first hospital. We can edit it, delete it, search it - all without writing any HTML or JavaScript. Django generated this entire interface from our model."

---

### [85:00-90:00] CLOSING - Knowledge Checks & Next Steps (5 minutes)

**[SCREEN: Show knowledge checks slide]**

**NARRATION:**
> "Let's recap what we learned today with five quick questions:"
>
> "**Question 1:** Why did we choose Django over MAUI?"
> **Answer:** Single web deployment (everyone gets updates instantly), no app store delays (push to production in minutes), no version fragmentation (everyone on same version), browser is universal platform.
>
> **Question 2:** What is a virtual environment and why do we use it?"
> **Answer:** Isolated Python installation for each project. Prevents package conflicts between projects needing different versions.
>
> **Question 3:** What does Django's ORM do?"
> **Answer:** Translates Python code to SQL and back. Provides database-agnostic code, SQL injection protection, and simpler syntax.
>
> **Question 4:** What's the difference between `CharField` and `TextField`?"
> **Answer:** CharField has max_length limit for short text like names and titles. TextField is unlimited for long text like addresses and notes.
>
> **Question 5:** What did Django's admin panel give us for free?"
> **Answer:** Complete CRUD interface with forms, validation, search, and filtering - no frontend code required.

**[PAUSE]**

> "Excellent work making it through Module 1. You now understand enterprise technology decisions, have Django running, and built your first model."

**[SCREEN: Show Module 2 preview]**

> "In Module 2, we'll dive deep into healthcare database models. We'll build Patient records with HIPAA considerations, Appointments with complex relationships, and learn about ForeignKeys connecting hospitals to patients."
>
> **[SCREEN: Show call-to-action]**
>
> "If you found this helpful, please like and subscribe - we're releasing new modules every two weeks. The written version of this module is linked below, along with our GitHub repo where you can see the full Grace & Company source code."
>
> "Thanks for watching, and I'll see you in Module 2!"

**[END SCREEN: Show youbetyourazure.com with social links]**

---

## Post-Production Checklist

### Editing Tasks:
- [ ] Add animated timestamp markers every 5 minutes
- [ ] Insert code callouts highlighting specific lines during explanations
- [ ] Add decision matrix animation (rows highlighting as explained)
- [ ] Include error example showing what NOT to do (common mistakes)
- [ ] Add subscribe reminder overlay at 45-minute mark
- [ ] Add end card overlay last 20 seconds with Module 2 thumbnail
- [ ] Generate auto-captions and manually correct technical terms
- [ ] Export 1080p60fps H.264 for YouTube upload

### Thumbnail Design:
- Title: "Django Fundamentals - Module 1"
- Subtitle: "Healthcare App Tutorial"
- Visual: Split screen (left: MAUI logo red X, right: Django logo green checkmark)
- Branding: "You Bet Your Azure" logo corner

### YouTube Upload Settings:
- Category: Education
- Tags: [all from metadata section]
- Premiere vs Immediate: Immediate publish
- Comments: Enabled (engage with learners)
- Age Restriction: No
- Licensing: Standard YouTube

---

## Notes for Presenter

### Pacing:
- Speak at ~150 words per minute (conversational)
- Pause 2-3 seconds between major sections for editing cuts
- Emphasize key terms: "ORM", "migrations", "ForeignKey" with slight stress
- Be enthusiastic but not hyper - professional energy

### Common Mistakes to Avoid:
- Don't say "um" or "uh" - pause silently instead (easier to edit)
- Don't rush terminal commands - viewers need to read them
- Don't skip error messages - explain what went wrong
- Don't assume knowledge - define every acronym first use

### Re-record Segments If:
- Stumbled over words multiple times
- Background noise (phone ringing, dogs barking)
- Wrong information stated (don't try to edit around this)
- Energy level dropped (coffee break then re-record)

### Equipment Check Before Recording:
- [ ] Microphone positioned correctly (6 inches away, pop filter)
- [ ] VS Code Dracula theme enabled (high contrast)
- [ ] Terminal font 16pt (readable in 1080p)
- [ ] Browser clean (no personal bookmarks visible)
- [ ] Notifications disabled (Do Not Disturb mode)
- [ ] Second monitor for script (don't read on-screen)
- [ ] Water nearby (vocal hydration)
- [ ] Full battery / plugged in (90 minutes runtime)

---

## Script Version History
- **v1.0 (2026-02-21):** Initial draft with 90-minute structure
- Future updates will be tracked here

---

**END OF SCRIPT**
