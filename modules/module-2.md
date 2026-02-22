# Module 2: Healthcare Database Models with Django ORM
## From Python Classes to Production Databases

**Learning Platform**: You Bet Your Azure AI Learning  
**Course**: Building Healthcare Applications with Django  
**Module Duration**: 4 hours  
**Difficulty**: Beginner to Intermediate  
**Prerequisites**: Module 1 complete, basic Python knowledge  

---

## 🎯 Learning Objectives

By the end of this module, you will:
1. ✅ Understand Django's ORM (Object-Relational Mapper)
2. ✅ Create healthcare-specific models (Hospital, Patient, Appointment)
3. ✅ Choose appropriate field types for medical data
4. ✅ Build relationships between models (ForeignKey, ManyToMany)
5. ✅ Generate and run database migrations
6. ✅ Query data using Django's QuerySet API
7. ✅ Apply HIPAA considerations to database design

---

## 📖 Table of Contents

### Lesson 1: Django ORM Fundamentals
- What is an ORM and why use it?
- Models = Python classes that become database tables
- Writing Python vs writing SQL
- Real example: From model to PostgreSQL table

### Lesson 2: The Hospital Model
- Designing client organization data
- CharField, TextField, EmailField, URLField
- DateField and DateTimeField
- Boolean flags (is_active)
- Real code from Grace & Company

### Lesson 3: The Patient Model
- Sensitive medical data considerations
- Patient demographics (name, DOB, contact)
- Medical Record Number (MRN) - unique identifier
- ForeignKey: Linking patients to hospitals
- HIPAA considerations in model design

### Lesson 4: The Appointment Model
- Scheduling data architecture
- Date, time, duration fields
- Status choices (scheduled, completed, cancelled)
- Multiple relationships (patient, hospital, coordinator)
- Real-world appointment lifecycle

### Lesson 5: Relationships Deep Dive
- ForeignKey (many-to-one): Many patients belong to one hospital
- ManyToManyField: Many hospitals serve many patients (transfers)
- OneToOneField: User profile pattern
- related_name and reverse lookups

### Lesson 6: Migrations - Database Version Control
- What are migrations?
- Creating migrations (makemigrations)
- Applying migrations (migrate)
- Viewing SQL (sqlmigrate)
- Production migration best practices

### Lesson 7: Querying Data
- QuerySet basics (.all(), .filter(), .get())
- Filtering by fields (hospital__name="SSM Health")
- Chaining queries
- select_related() and prefetch_related() (performance)
- Real queries from production dashboards

---

## 📚 Lesson 1: Django ORM Fundamentals

### 🤔 What is an ORM?

**ORM = Object-Relational Mapper**

**Translation Layer**:
- You write: **Python code** (object-oriented)
- Django converts to: **SQL** (database language)
- Database returns: **Results**
- Django converts back to: **Python objects**

**Why This Matters**:
- ✅ Write Python (familiar language) instead of SQL
- ✅ Database-agnostic (switch PostgreSQL → MySQL with 1 line change)
- ✅ Protection against SQL injection (automatic escaping)
- ✅ Less code (ORM handles boilerplate)

---

### 📊 Example: The Translation in Action

#### **Without ORM (Raw SQL)**:
```sql
-- Create table (PostgreSQL syntax)
CREATE TABLE hospital (
    id SERIAL PRIMARY KEY,
    name VARCHAR(200) NOT NULL,
    address TEXT,
    phone VARCHAR(20),
    email VARCHAR(254),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Insert hospital
INSERT INTO hospital (name, address, phone, email) 
VALUES ('Barnes-Jewish Hospital', '1 Barnes Jewish Hospital Plaza', '314-747-3000', 'info@bjc.org');

-- Query hospitals in Missouri
SELECT * FROM hospital WHERE address LIKE '%Missouri%';

-- Update hospital
UPDATE hospital SET phone = '314-555-1234' WHERE id = 1;

-- Delete hospital
DELETE FROM hospital WHERE id = 1;
```

#### **With Django ORM (Python)**:
```python
# Define model (models.py)
class Hospital(models.Model):
    name = models.CharField(max_length=200)
    address = models.TextField()
    phone = models.CharField(max_length=20)
    email = models.EmailField()
    created_at = models.DateTimeField(auto_now_add=True)

# Create hospital
hospital = Hospital.objects.create(
    name='Barnes-Jewish Hospital',
    address='1 Barnes Jewish Hospital Plaza',
    phone='314-747-3000',
    email='info@bjc.org'
)

# Query hospitals in Missouri
missouri_hospitals = Hospital.objects.filter(address__icontains='Missouri')

# Update hospital
hospital.phone = '314-555-1234'
hospital.save()

# Delete hospital
hospital.delete()
```

**Result**: Same functionality, but:
- Python syntax (easier to read/write)
- Type safety (email field validates email format)
- No SQL injection risk (Django escapes automatically)
- Works with PostgreSQL, MySQL, SQLite, Oracle (change 1 setting)

---

### 🏗️ Models = Database Tables

**Mental Model**:
```
Python Class (Model)  →  Database Table
Class Field           →  Table Column
Class Instance        →  Table Row
```

**Example**:
```python
# Python Model
class Hospital(models.Model):
    name = models.CharField(max_length=200)
    address = models.TextField()
    phone = models.CharField(max_length=20)
```

**Becomes PostgreSQL Table**:
```
hospital table:
+----+-------------------------+---------------------------+--------------+
| id | name                    | address                   | phone        |
+----+-------------------------+---------------------------+--------------+
| 1  | Barnes-Jewish Hospital  | 1 Barnes Jewish Plaza...  | 314-747-3000 |
| 2  | SSM Health              | 10101 Woodfield Ln...     | 314-994-7000 |
+----+-------------------------+---------------------------+--------------+
```

**Key Points**:
- Each **model** = one **table**
- Each **field** = one **column**
- Each **instance** = one **row**
- **id** field added automatically (primary key)

---

### 🎓 Knowledge Check

**Question 1**: What are the three main benefits of using Django ORM over raw SQL?

<details>
<summary>Click to reveal answer</summary>

**Answer**:
1. **Database-agnostic**: Write once, works on PostgreSQL, MySQL, SQLite (switch with 1 setting change)
2. **SQL injection protection**: Django automatically escapes user input, preventing security vulnerabilities
3. **Less code**: ORM generates boilerplate SQL, you focus on business logic (Python is more readable than SQL)

**Bonus**: Type safety (EmailField validates emails), automatic primary keys, built-in pagination, caching support.
</details>

---

## 📚 Lesson 2: The Hospital Model (Real Production Code)

### 🏥 Business Requirements

**What is a Hospital in our system?**
- Client organization using Grace & Company services
- Has staff members (nurses, coordinators, administrators)
- Has patients under their care
- Has appointments scheduled
- Needs billing tracking

**Data to Store**:
- Organization details (name, address, contact)
- Contract information (start date, is active?)
- Billing settings (rate, payment terms)
- System configuration (calendar color, default settings)

---

### 💻 Complete Hospital Model

**File**: `wellness_app/models.py`

```python
from django.db import models

class Hospital(models.Model):
    """
    Client hospital organization.
    
    Represents a healthcare facility that uses Grace & Company's
    transitional care coordination services.
    """
    
    # Organization Details
    name = models.CharField(
        max_length=200,
        help_text="Official hospital name (e.g., 'Barnes-Jewish Hospital')"
    )
    
    address = models.TextField(
        help_text="Full mailing address"
    )
    
    city = models.CharField(max_length=100, default='St. Louis')
    state = models.CharField(max_length=2, default='MO')
    zip_code = models.CharField(max_length=10)
    
    # Contact Information
    phone = models.CharField(
        max_length=20,
        help_text="Main contact number"
    )
    
    email = models.EmailField(
        help_text="Primary contact email"
    )
    
    website = models.URLField(blank=True, null=True)
    
    # Contract & Billing
    contract_start_date = models.DateField()
    
    is_active = models.BooleanField(
        default=True,
        help_text="Active contracts can access system"
    )
    
    monthly_rate = models.DecimalField(
        max_digits=10,
        decimal_places=2,
        help_text="Base monthly service fee"
    )
    
    # Calendar Configuration (for master calendar feature)
    calendar_color = models.CharField(
        max_length=7,
        default='#3498db',
        help_text="Hex color for calendar events (e.g., '#3498db')"
    )
    
    calendar_visible_default = models.BooleanField(
        default=True,
        help_text="Show this hospital's events by default"
    )
    
    # System Metadata
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
    
    class Meta:
        ordering = ['name']
        verbose_name = 'Hospital'
        verbose_name_plural = 'Hospitals'
    
    def __str__(self):
        return self.name
    
    def get_active_patients_count(self):
        """Return count of active patients"""
        return self.patient_set.filter(status='active').count()
```

---

### 🔍 Field Type Breakdown

#### **CharField** - Short Text (with max length)
```python
name = models.CharField(max_length=200)
```
- **Use for**: Names, titles, short descriptions
- **Becomes**: VARCHAR(200) in PostgreSQL
- **Validation**: Automatically enforces max length
- **Example values**: "Barnes-Jewish Hospital", "SSM Health"

#### **TextField** - Long Text (unlimited length)
```python
address = models.TextField()
```
- **Use for**: Paragraphs, notes, long descriptions
- **Becomes**: TEXT in PostgreSQL
- **No length limit**: Can store essays
- **Example values**: Full addresses, patient histories

#### **EmailField** - Email Validation
```python
email = models.EmailField()
```
- **Special**: Validates email format (must have @ and domain)
- **Becomes**: VARCHAR(254) in PostgreSQL
- **Automatic validation**: Rejects "notanemail" or "test@"
- **Example values**: "info@bjc.org", "contact@ssmhealth.com"

#### **URLField** - URL Validation
```python
website = models.URLField(blank=True, null=True)
```
- **Special**: Validates URL format (must start with http:// or https://)
- **blank=True**: Form can be submitted empty
- **null=True**: Database allows NULL value
- **Example values**: "https://www.barnesjewish.org"

#### **DateField** - Date Only (no time)
```python
contract_start_date = models.DateField()
```
- **Stores**: Year, month, day (2026-02-21)
- **Becomes**: DATE in PostgreSQL
- **Use for**: Birthdays, contract dates, appointment dates
- **Python type**: datetime.date

#### **DateTimeField** - Date + Time
```python
created_at = models.DateTimeField(auto_now_add=True)
updated_at = models.DateTimeField(auto_now=True)
```
- **Stores**: Year, month, day, hour, minute, second, microsecond
- **auto_now_add=True**: Set once when record created (never changes)
- **auto_now=True**: Updates every time record saved
- **Use for**: Audit trails, timestamps, appointment times
- **Python type**: datetime.datetime

#### **BooleanField** - True/False
```python
is_active = models.BooleanField(default=True)
```
- **Stores**: True or False (1 or 0)
- **Becomes**: BOOLEAN in PostgreSQL
- **default**: Value when not specified
- **Use for**: Flags, toggles, yes/no questions

#### **DecimalField** - Precise Numbers (money, measurements)
```python
monthly_rate = models.DecimalField(max_digits=10, decimal_places=2)
```
- **max_digits**: Total digits (before + after decimal)
- **decimal_places**: Digits after decimal point
- **Example**: max_digits=10, decimal_places=2 → max value 99999999.99
- **Use for**: Money (no floating point errors), medical measurements
- **DON'T use FloatField for money!** (0.1 + 0.2 ≠ 0.3 in floating point)

---

### 🎨 Class Meta Options

```python
class Meta:
    ordering = ['name']  # Default sort order (alphabetical by name)
    verbose_name = 'Hospital'  # Singular name in admin
    verbose_name_plural = 'Hospitals'  # Plural name in admin
```

**ordering**:
- Controls default sort when querying
- `Hospital.objects.all()` returns hospitals alphabetically
- Can use multiple fields: `ordering = ['state', 'name']` (sort by state, then name)
- Reverse order: `ordering = ['-created_at']` (newest first)

---

### 🔧 Special Methods

#### **__str__()** - String Representation
```python
def __str__(self):
    return self.name
```
- **Purpose**: What displays when you print the object
- **Without __str__**: `<Hospital object (1)>` (not useful)
- **With __str__**: `"Barnes-Jewish Hospital"` (readable!)
- **Used in**: Admin panel, debugging, forms (dropdown shows name not "object 1")

#### **Custom Methods** - Business Logic
```python
def get_active_patients_count(self):
    return self.patient_set.filter(status='active').count()
```
- **Purpose**: Encapsulate queries/calculations in model
- **Usage**: `hospital.get_active_patients_count()` → returns integer
- **Best Practice**: Keep business logic in models, not views

---

### 🎓 Knowledge Check

**Question 2**: What's the difference between `CharField` and `TextField`?

<details>
<summary>Click to reveal answer</summary>

**Answer**:
- **CharField**: Fixed maximum length (max_length required), becomes VARCHAR in database. Use for short text like names, titles, codes (200 characters or less).
- **TextField**: Unlimited length, becomes TEXT in database. Use for long content like descriptions, notes, patient histories (paragraphs).

**Example**:
- Hospital name (≤200 chars) → CharField
- Patient medical history (unlimited) → TextField
</details>

---

**Question 3**: Why use `DecimalField` instead of `FloatField` for money?

<details>
<summary>Click to reveal answer</summary>

**Answer**: **Floating point precision errors**.

**Problem with FloatField**:
```python
>>> 0.1 + 0.2
0.30000000000000004  # ❌ Not exactly 0.3!
```

Financial transactions with floating point can compound errors:
- Patient bill: $100.10
- After 1000 transactions: $100.09999847 or $100.10000153
- Off by cents → HIPAA audit failure

**DecimalField Solution**:
```python
>>> Decimal('0.1') + Decimal('0.2')
Decimal('0.3')  # ✅ Exactly 0.3!
```

**Rule**: Always use `DecimalField(max_digits=10, decimal_places=2)` for currency.
</details>

---

## 📚 Lesson 3: The Patient Model

### 🏥 Business Requirements

**Patient Data in Transitional Care**:
- Demographics (name, DOB, contact)
- Medical identifier (MRN - Medical Record Number)
- Current hospital providing care
- Contact information (phone, emergency contact)
- Status (active, discharged, transferred)
- HIPAA considerations (sensitive data)

---

### 💻 Complete Patient Model

```python
class Patient(models.Model):
    """
    Patient receiving transitional care services.
    
    HIPAA NOTICE: This model stores Protected Health Information (PHI).
    Access is restricted to authorized hospital staff only.
    """
    
    # Relationships
    hospital = models.ForeignKey(
        Hospital,
        on_delete=models.PROTECT,
        related_name='patients',
        help_text="Hospital currently providing care"
    )
    
    # Medical Identifier
    mrn = models.CharField(
        max_length=50,
        unique=True,
        help_text="Medical Record Number (unique across all hospitals)"
    )
    
    # Demographics (PHI - Protected Health Information)
    first_name = models.CharField(max_length=100)
    last_name = models.CharField(max_length=100)
    
    date_of_birth = models.DateField(
        help_text="Format: YYYY-MM-DD"
    )
    
    GENDER_CHOICES = [
        ('M', 'Male'),
        ('F', 'Female'),
        ('O', 'Other'),
        ('U', 'Prefer not to say'),
    ]
    gender = models.CharField(
        max_length=1,
        choices=GENDER_CHOICES,
        default='U'
    )
    
    # Contact Information (PHI)
    phone = models.CharField(max_length=20, blank=True)
    email = models.EmailField(blank=True)
    address = models.TextField(blank=True)
    
    # Emergency Contact
    emergency_contact_name = models.CharField(max_length=200, blank=True)
    emergency_contact_phone = models.CharField(max_length=20, blank=True)
    emergency_contact_relationship = models.CharField(max_length=100, blank=True)
    
    # Clinical Status
    STATUS_CHOICES = [
        ('active', 'Active - In Care'),
        ('discharged', 'Discharged'),
        ('transferred', 'Transferred to Another Facility'),
        ('deceased', 'Deceased'),
    ]
    status = models.CharField(
        max_length=20,
        choices=STATUS_CHOICES,
        default='active'
    )
    
    admission_date = models.DateField(
        help_text="Date admitted to hospital"
    )
    
    discharge_date = models.DateField(
        null=True,
        blank=True,
        help_text="Date discharged (null if still admitted)"
    )
    
    # Medical Notes
    diagnosis = models.TextField(
        blank=True,
        help_text="Primary diagnosis or reason for admission"
    )
    
    care_plan_notes = models.TextField(
        blank=True,
        help_text="Transitional care plan details"
    )
    
    # System Metadata
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
    created_by = models.ForeignKey(
        'auth.User',
        on_delete=models.SET_NULL,
        null=True,
        related_name='created_patients'
    )
    
    class Meta:
        ordering = ['-admission_date']  # Newest admissions first
        verbose_name = 'Patient'
        verbose_name_plural = 'Patients'
        indexes = [
            models.Index(fields=['hospital', 'status']),  # Fast queries by hospital+status
            models.Index(fields=['mrn']),  # Fast MRN lookups
        ]
    
    def __str__(self):
        return f\"{self.last_name}, {self.first_name} (MRN: {self.mrn})\"
    
    def get_full_name(self):
        return f\"{self.first_name} {self.last_name}\"
    
    def get_age(self):
        \"\"\"Calculate patient age from date of birth\"\"\"
        from datetime import date
        today = date.today()
        born = self.date_of_birth
        return today.year - born.year - ((today.month, today.day) < (born.month, born.day))
    
    def is_discharged(self):
        return self.status == 'discharged'
```

---

### 🔗 Understanding ForeignKey (Many-to-One Relationship)

**Concept**: Many patients belong to one hospital

```
Hospital (One)          Patient (Many)
┌──────────────┐        ┌─────────────────────┐
│ id: 1        │        │ id: 101             │
│ name: BJH    │◄───────│ hospital_id: 1      │ ─┐
└──────────────┘        │ name: John Doe      │  │
                        └─────────────────────┘  │  Multiple patients
                                                 │  point to same hospital
                        ┌─────────────────────┐  │
                        │ id: 102             │  │
                        │ hospital_id: 1      │ ─┘
                        │ name: Jane Smith    │
                        └─────────────────────┘
```

**Code**:
```python
hospital = models.ForeignKey(
    Hospital,                    # What model we're linking to
    on_delete=models.PROTECT,    # What happens if hospital deleted?
    related_name='patients',     # Reverse lookup name
)
```

**on_delete Options**:
- `PROTECT`: **Prevent deletion** if patients exist (our choice - safety first!)
  - Can't delete hospital if it has patients
  - Raises error: "Cannot delete hospital, 15 patients still assigned"
  
- `CASCADE`: **Delete patients** when hospital deleted (DANGEROUS for healthcare!)
  - Deleting hospital → deletes all patients → data loss!
  
- `SET_NULL`: **Set to NULL** when hospital deleted (if null=True)
  - Patient remains but hospital_id becomes NULL
  
- `SET_DEFAULT`: **Set to default** value (if default specified)

**Why PROTECT for healthcare?**
- Patient data is precious (HIPAA requires retention)
- Accidentally deleting hospital shouldn't wipe patient records
- Force manual reassignment before hospital deletion

---

### 🔄 Reverse Relationships (related_name)

**Forward Lookup** (easy - just access field):
```python
patient = Patient.objects.get(mrn='12345')
hospital = patient.hospital  # Access hospital via ForeignKey field
print(hospital.name)  # "Barnes-Jewish Hospital"
```

**Reverse Lookup** (hospital → patients):
```python
hospital = Hospital.objects.get(name='Barnes-Jewish Hospital')

# Without related_name: hospital.patient_set.all()  (Django default)
# With related_name='patients': hospital.patients.all()  (cleaner!)

patients = hospital.patients.all()  # Get all patients at this hospital
active_patients = hospital.patients.filter(status='active')  # Filter patients
count = hospital.patients.count()  # Count patients
```

**related_name Benefits**:
- `hospital.patients.all()` is more readable than `hospital.patient_set.all()`
- Cleaner API for your model
- Industry standard naming convention

---

### 🎨 Choices - Limited Options Fields

**Problem**: Gender should only be M, F, O, or U (not free text)

**Solution**: `choices` parameter

```python
GENDER_CHOICES = [
    ('M', 'Male'),          # ('database_value', 'human_readable_label')
    ('F', 'Female'),
    ('O', 'Other'),
    ('U', 'Prefer not to say'),
]
gender = models.CharField(max_length=1, choices=GENDER_CHOICES, default='U')
```

**In Database**: Stores 'M', 'F', 'O', or 'U' (single character)  
**In Forms**: Dropdown shows "Male", "Female", "Other", "Prefer not to say"

**Accessing Values**:
```python
patient = Patient.objects.get(mrn='12345')

# Database value (what's stored)
print(patient.gender)  # 'M'

# Human-readable label
print(patient.get_gender_display())  # 'Male'
```

**Best Practice**: Define choices at module level (not inside class)
- Reusable across multiple models
- Easy to update (change in one place)

---

### 🏷️ Unique Fields

```python
mrn = models.CharField(max_length=50, unique=True)
```

**unique=True** means:
- No two patients can have same MRN
- Database creates UNIQUE INDEX automatically
- Attempting to create duplicate raises `IntegrityError`

**Real-World MRN Example**:
- Hospital A assigns: MRN-BJH-0001234
- Hospital B assigns: MRN-SSM-0005678
- Both unique across system (no collisions)

---

### 📅 Date Fields and Null Values

```python
admission_date = models.DateField()  # Required (no null/blank)
discharge_date = models.DateField(null=True, blank=True)  # Optional
```

**null=True**: Database allows NULL value  
**blank=True**: Form can be submitted empty

**Why separate null and blank?**
- **null**: Database constraint (PostgreSQL level)
- **blank**: Validation constraint (Django form level)

**Common Combinations**:
- `null=False, blank=False`: Required field (must have value)
- `null=True, blank=True`: Optional field (can be empty)
- `null=False, blank=True`: Has default value (blank form uses default)

**Date Logic**:
```python
patient.admission_date  # Always has value: 2026-02-21
patient.discharge_date  # Might be None (if patient still admitted)

if patient.discharge_date:
    print(f\"Discharged on {patient.discharge_date}\")
else:
    print(\"Still admitted\")
```

---

### 🎓 Knowledge Check

**Question 4**: What does `on_delete=models.PROTECT` do in a ForeignKey?

<details>
<summary>Click to reveal answer</summary>

**Answer**: Prevents deletion of the referenced object (Hospital) if any related objects (Patients) exist.

**Example**:
```python
# Try to delete hospital with active patients
hospital = Hospital.objects.get(name='Barnes-Jewish')
hospital.delete()  # ❌ Raises ProtectedError: \"Cannot delete hospital, 25 patients still assigned\"

# Must first reassign or delete all patients before deleting hospital
```

**Why PROTECT for healthcare?**
- Prevents accidental data loss
- Patient records are legally required (HIPAA retention)
- Forces explicit intent (must reassign patients before hospital removal)
- Safety over convenience
</details>

---

**Question 5**: What's the difference between `null=True` and `blank=True`?

<details>
<summary>Click to reveal answer</summary>

**Answer**:
- **null=True**: Database-level constraint. Allows NULL value in PostgreSQL/MySQL table.
- **blank=True**: Django form-level validation. Allows empty form submission.

**When to use**:
```python
# Optional text field
notes = models.TextField(null=True, blank=True)  # ✅ Both (can be empty in form and DB)

# Optional foreign key
hospital = models.ForeignKey(Hospital, null=True, blank=True, on_delete=SET_NULL)  # ✅ Both

# Required field with default
status = models.CharField(max_length=20, default='active', blank=True)  # blank=True, null=False (uses default if empty)
```

**Common Mistake**:
```python
# ❌ WRONG: CharField should NOT use null=True (use blank=True + empty string '')
name = models.CharField(max_length=100, null=True)  # Two ways to represent empty (NULL and '')

# ✅ CORRECT: CharFields use empty string '' for \"no value\"
name = models.CharField(max_length=100, blank=True)  # Empty = '' not NULL
```
</details>

---

---

## Lesson 4: The Appointment Model - Scheduling Architecture

### 📖 What You'll Learn

Healthcare applications need robust scheduling systems. The Appointment model connects:
- **Patients** (who needs care)
- **Providers** (doctors, nurses)
- **Services** (what type of appointment)
- **Resources** (exam rooms, equipment)

This lesson teaches **multiple ForeignKey relationships** and **status workflow management**.

---

### 🔧 Appointment Model Architecture

```python
# wellness_app/models.py

from django.db import models
from django.contrib.auth.models import User
from django.utils import timezone
from datetime import timedelta

class Appointment(models.Model):
    """
    Manages patient appointments with providers
    
    Key Features:
    - Multiple ForeignKeys (patient, provider, hospital)
    - Status workflow (scheduled → confirmed → completed → cancelled)
    - Time slot management (start/end times)
    - Appointment types (initial, follow-up, emergency)
    """
    
    # Appointment Status Choices
    SCHEDULED = 'scheduled'
    CONFIRMED = 'confirmed'
    IN_PROGRESS = 'in_progress'
    COMPLETED = 'completed'
    NO_SHOW = 'no_show'
    CANCELLED = 'cancelled'
    
    STATUS_CHOICES = [
        (SCHEDULED, 'Scheduled'),
        (CONFIRMED, 'Confirmed'),
        (IN_PROGRESS, 'In Progress'),
        (COMPLETED, 'Completed'),
        (NO_SHOW, 'No Show'),
        (CANCELLED, 'Cancelled'),
    ]
    
    # Appointment Type Choices
    INITIAL_VISIT = 'initial'
    FOLLOW_UP = 'follow_up'
    EMERGENCY = 'emergency'
    TELEMEDICINE = 'telemedicine'
    
    TYPE_CHOICES = [
        (INITIAL_VISIT, 'Initial Visit'),
        (FOLLOW_UP, 'Follow-up'),
        (EMERGENCY, 'Emergency'),
        (TELEMEDICINE, 'Telemedicine'),
    ]
    
    # Foreign Keys - Multiple relationships!
    patient = models.ForeignKey(
        'Patient',
        on_delete=models.PROTECT,  # Can't delete patient with appointments
        related_name='appointments',
        help_text="Patient attending appointment"
    )
    
    provider = models.ForeignKey(
        User,
        on_delete=models.PROTECT,  # Can't delete provider with appointments
        related_name='provider_appointments',
        limit_choices_to={'groups__name': 'Healthcare Provider'},  # Only users in Provider group
        help_text="Healthcare provider conducting appointment"
    )
    
    hospital = models.ForeignKey(
        'Hospital',
        on_delete=models.PROTECT,
        related_name='appointments',
        help_text="Hospital/clinic where appointment occurs"
    )
    
    # Scheduling fields
    scheduled_start = models.DateTimeField(
        help_text="Appointment start time"
    )
    
    scheduled_end = models.DateTimeField(
        help_text="Appointment end time"
    )
    
    actual_start = models.DateTimeField(
        null=True,
        blank=True,
        help_text="Actual start time (if different from scheduled)"
    )
    
    actual_end = models.DateTimeField(
        null=True,
        blank=True,
        help_text="Actual end time"
    )
    
    # Appointment metadata
    appointment_type = models.CharField(
        max_length=20,
        choices=TYPE_CHOICES,
        default=INITIAL_VISIT
    )
    
    status = models.CharField(
        max_length=20,
        choices=STATUS_CHOICES,
        default=SCHEDULED
    )
    
    reason_for_visit = models.TextField(
        help_text="Chief complaint or reason for appointment"
    )
    
    notes = models.TextField(
        blank=True,
        help_text="Provider notes during/after appointment"
    )
    
    cancellation_reason = models.TextField(
        blank=True,
        help_text="Reason if cancelled"
    )
    
    # Timestamps
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
    
    class Meta:
        ordering = ['scheduled_start']
        indexes = [
            models.Index(fields=['scheduled_start', 'status']),
            models.Index(fields=['provider', 'scheduled_start']),
            models.Index(fields=['patient', 'scheduled_start']),
        ]
    
    def __str__(self):
        return f"{self.patient.get_full_name()} with {self.provider.get_full_name()} on {self.scheduled_start.strftime('%Y-%m-%d %H:%M')}"
    
    def duration_minutes(self):
        """Calculate scheduled appointment duration"""
        delta = self.scheduled_end - self.scheduled_start
        return int(delta.total_seconds() / 60)
    
    def actual_duration_minutes(self):
        """Calculate actual appointment duration"""
        if self.actual_start and self.actual_end:
            delta = self.actual_end - self.actual_start
            return int(delta.total_seconds() / 60)
        return None
    
    def is_upcoming(self):
        """Check if appointment is in the future"""
        return self.scheduled_start > timezone.now() and self.status not in [self.CANCELLED, self.COMPLETED]
    
    def is_overdue(self):
        """Check if appointment should have started but hasn't"""
        return (
            timezone.now() > self.scheduled_start 
            and self.status == self.SCHEDULED
        )
    
    def can_cancel(self):
        """Business rule: Can only cancel if not completed/in-progress"""
        return self.status in [self.SCHEDULED, self.CONFIRMED]
    
    def mark_confirmed(self):
        """Transition from scheduled → confirmed"""
        if self.status == self.SCHEDULED:
            self.status = self.CONFIRMED
            self.save()
    
    def start_appointment(self):
        """Provider clicks 'Start' - begins appointment"""
        if self.status in [self.SCHEDULED, self.CONFIRMED]:
            self.status = self.IN_PROGRESS
            self.actual_start = timezone.now()
            self.save()
    
    def complete_appointment(self, notes=""):
        """Provider clicks 'Complete' - finishes appointment"""
        if self.status == self.IN_PROGRESS:
            self.status = self.COMPLETED
            self.actual_end = timezone.now()
            if notes:
                self.notes = notes
            self.save()
    
    def cancel_appointment(self, reason=""):
        """Cancel appointment with reason"""
        if self.can_cancel():
            self.status = self.CANCELLED
            self.cancellation_reason = reason
            self.save()
```

---

### 💻 Using the Appointment Model

#### Example 1: Create Appointment

```python
from datetime import timedelta
from django.utils import timezone

# Get patient, provider, hospital
patient = Patient.objects.get(user__username='john_smith')
provider = User.objects.get(username='dr_johnson', groups__name='Healthcare Provider')
hospital = Hospital.objects.get(name='Barnes-Jewish Hospital')

# Schedule appointment for tomorrow at 2 PM, 30-minute slot
start_time = timezone.now() + timedelta(days=1)
start_time = start_time.replace(hour=14, minute=0, second=0)
end_time = start_time + timedelta(minutes=30)

appointment = Appointment.objects.create(
    patient=patient,
    provider=provider,
    hospital=hospital,
    scheduled_start=start_time,
    scheduled_end=end_time,
    appointment_type=Appointment.FOLLOW_UP,
    reason_for_visit="Follow-up for diabetes management"
)

print(f"✅ Appointment scheduled: {appointment}")
# Output: ✅ Appointment scheduled: John Smith with Dr. Johnson on 2026-01-16 14:00
```

#### Example 2: Provider Day Schedule

```python
from datetime import date

# Get all appointments for Dr. Johnson today
today = date.today()
todays_appointments = Appointment.objects.filter(
    provider__username='dr_johnson',
    scheduled_start__date=today
).exclude(
    status__in=[Appointment.CANCELLED, Appointment.NO_SHOW]
).select_related('patient__user', 'hospital')

print(f"Dr. Johnson's Schedule ({today}):")
print("="*50)

for appt in todays_appointments:
    status_emoji = {
        'scheduled': '📅',
        'confirmed': '✅',
        'in_progress': '🏥',
        'completed': '✔️'
    }.get(appt.status, '❓')
    
    print(f"{status_emoji} {appt.scheduled_start.strftime('%I:%M %p')} - {appt.patient.get_full_name()}")
    print(f"   Duration: {appt.duration_minutes()} min | Type: {appt.get_appointment_type_display()}")
    print(f"   Reason: {appt.reason_for_visit}")
    print()
```

#### Example 3: Appointment Workflow

```python
# Patient books appointment
appt = Appointment.objects.create(
    patient=patient,
    provider=provider,
    hospital=hospital,
    scheduled_start=start_time,
    scheduled_end=end_time,
    appointment_type=Appointment.INITIAL_VISIT,
    reason_for_visit="New patient intake - CHF management"
)
print(f"Status: {appt.status}")  # scheduled

# Clinic calls to confirm
appt.mark_confirmed()
print(f"Status: {appt.status}")  # confirmed

# Provider starts appointment
appt.start_appointment()
print(f"Status: {appt.status}")  # in_progress
print(f"Started at: {appt.actual_start}")

# Provider finishes appointment
appt.complete_appointment(notes="Patient showing improvement. Continue current medications.")
print(f"Status: {appt.status}")  # completed
print(f"Duration: {appt.actual_duration_minutes()} minutes")
```

#### Example 4: Find Conflicting Appointments

```python
def check_provider_availability(provider, start_time, end_time):
    """
    Check if provider has conflicting appointments
    
    Business Rule: Provider can only have ONE appointment at a time
    """
    conflicts = Appointment.objects.filter(
        provider=provider,
        scheduled_start__lt=end_time,  # Starts before new appointment ends
        scheduled_end__gt=start_time,  # Ends after new appointment starts
        status__in=[Appointment.SCHEDULED, Appointment.CONFIRMED, Appointment.IN_PROGRESS]
    )
    
    return conflicts.exists(), conflicts

# Try to book appointment
has_conflict, conflicting_appts = check_provider_availability(
    provider=provider,
    start_time=start_time,
    end_time=end_time
)

if has_conflict:
    print("❌ Cannot book: Provider has conflicting appointment")
    for conflict in conflicting_appts:
        print(f"   Conflict: {conflict.scheduled_start} - {conflict.scheduled_end}")
else:
    print("✅ Time slot available!")
    # Create appointment...
```

---

### 🎯 Hands-On Exercise: Appointment Analytics Dashboard

**Task**: Build queries for appointment analytics.

```python
from django.db.models import Count, Avg, Q
from datetime import date, timedelta

# 1. Appointments by status (last 30 days)
last_30_days = timezone.now() - timedelta(days=30)

status_breakdown = Appointment.objects.filter(
    created_at__gte=last_30_days
).values('status').annotate(
    count=Count('id')
).order_by('-count')

print("Appointment Status (Last 30 Days):")
for row in status_breakdown:
    print(f"  {row['status']}: {row['count']}")

# 2. No-show rate by patient
no_show_rates = Patient.objects.annotate(
    total_appointments=Count('appointments'),
    no_shows=Count('appointments', filter=Q(appointments__status=Appointment.NO_SHOW))
).filter(total_appointments__gt=0).annotate(
    no_show_rate=Avg('no_shows') * 100 / Avg('total_appointments')
).order_by('-no_show_rate')

print("\nPatients with High No-Show Rates:")
for patient in no_show_rates[:5]:
    print(f"  {patient.get_full_name()}: {patient.no_show_rate:.1f}% ({patient.no_shows}/{patient.total_appointments})")

# 3. Provider utilization (appointments per day)
provider_utilization = User.objects.filter(
    groups__name='Healthcare Provider'
).annotate(
    appts_last_30=Count(
        'provider_appointments',
        filter=Q(provider_appointments__scheduled_start__gte=last_30_days)
    ),
    avg_per_day=Count('provider_appointments') / 30.0
).order_by('-avg_per_day')

print("\nProvider Utilization (Appointments/Day):")
for provider in provider_utilization:
    print(f"  {provider.get_full_name()}: {provider.avg_per_day:.1f} appts/day")

# 4. Find overdue appointments (should have started but status still 'scheduled')
overdue = Appointment.objects.filter(
    scheduled_start__lt=timezone.now(),
    status=Appointment.SCHEDULED
).select_related('patient__user', 'provider')

print(f"\n⚠️ {overdue.count()} Overdue Appointments:")
for appt in overdue[:10]:
    print(f"  {appt.patient.get_full_name()} with {appt.provider.get_full_name()}")
    print(f"  Scheduled: {appt.scheduled_start}")
```

---

## Lesson 5: ManyToMany Relationships - Complex Connections

### 📖 What You'll Learn

So far we've used **ForeignKey** (one-to-many). But what if:
- A **patient has multiple conditions** AND each **condition affects multiple patients**?
- A **provider has multiple specialties** AND each **specialty has multiple providers**?
- A **medication treats multiple conditions** AND each **condition has multiple medications**?

This is a **many-to-many** relationship.

---

### 🔧 ManyToManyField Basics

```python
class Patient(models.Model):
    # ... existing fields ...
    
    # Many-to-many: Patient can have multiple conditions
    conditions = models.ManyToManyField(
        'MedicalCondition',
        related_name='patients',
        blank=True,
        help_text="All conditions diagnosed for this patient"
    )
    
    # Many-to-many: Patient takes multiple medications
    medications = models.ManyToManyField(
        'Medication',
        through='PatientMedication',  # Use custom through table for extra data
        related_name='patients',
        blank=True
    )
```

**How it works**:
- Django creates a **junction table** (e.g., `wellness_app_patient_conditions`)
- Junction table has two ForeignKeys: `patient_id` and `medicalcondition_id`
- Simple many-to-many = automatic junction table
- Complex many-to-many (with extra data) = custom `through` model

---

### 💡 Through Model - ManyToMany with Extra Data

**Scenario**: Track WHEN medication was prescribed and dosage.

```python
class PatientMedication(models.Model):
    """
    Through model: Connects Patient ↔ Medication with extra data
    
    Why use 'through'?
    - Store prescription date
    - Store dosage
    - Store prescribing provider
    - Track active/inactive status
    """
    patient = models.ForeignKey('Patient', on_delete=models.CASCADE)
    medication = models.ForeignKey('Medication', on_delete=models.PROTECT)
    
    # Extra data (why we need a through model)
    dosage = models.CharField(
        max_length=100,
        help_text="e.g., '10mg twice daily'"
    )
    
    prescribed_date = models.DateField(
        help_text="When medication was prescribed"
    )
    
    prescribed_by = models.ForeignKey(
        User,
        on_delete=models.PROTECT,
        limit_choices_to={'groups__name': 'Healthcare Provider'}
    )
    
    is_active = models.BooleanField(
        default=True,
        help_text="Is patient still taking this medication?"
    )
    
    discontinue_date = models.DateField(
        null=True,
        blank=True,
        help_text="When medication was stopped"
    )
    
    discontinue_reason = models.TextField(
        blank=True,
        help_text="Why medication was discontinued"
    )
    
    notes = models.TextField(
        blank=True,
        help_text="Special instructions"
    )
    
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
    
    class Meta:
        unique_together = ['patient', 'medication', 'prescribed_date']
        ordering = ['-prescribed_date']
    
    def __str__(self):
        return f"{self.patient.get_full_name()} - {self.medication.name} ({self.dosage})"
    
    def days_active(self):
        """Calculate how long patient has been on medication"""
        if self.is_active:
            return (date.today() - self.prescribed_date).days
        elif self.discontinue_date:
            return (self.discontinue_date - self.prescribed_date).days
        return 0
```

---

### 💻 Using ManyToMany Relationships

#### Example 1: Add conditions to patient

```python
# Simple ManyToMany (no through model)
patient = Patient.objects.get(user__username='john_smith')

# Get conditions
chf = MedicalCondition.objects.get(name='Congestive Heart Failure')
diabetes = MedicalCondition.objects.get(name='Type 2 Diabetes')

# Add conditions
patient.conditions.add(chf, diabetes)

# Check patient's conditions
print(f"{patient.get_full_name()}'s Conditions:")
for condition in patient.conditions.all():
    print(f"  - {condition.name} (ICD-10: {condition.icd10_code})")

# Reverse lookup: Find all patients with CHF
chf_patients = chf.patients.all()
print(f"\nPatients with CHF: {chf_patients.count()}")
```

#### Example 2: Prescribe medication (with through model)

```python
from datetime import date

# Get patient and medication
patient = Patient.objects.get(user__username='john_smith')
medication = Medication.objects.get(name='Lisinopril')
provider = User.objects.get(username='dr_johnson')

# Create prescription (through PatientMedication model)
prescription = PatientMedication.objects.create(
    patient=patient,
    medication=medication,
    dosage='10mg once daily',
    prescribed_date=date.today(),
    prescribed_by=provider,
    is_active=True,
    notes='Take in morning with food'
)

print(f"✅ Prescribed: {prescription}")

# Get all active medications for patient
active_meds = PatientMedication.objects.filter(
    patient=patient,
    is_active=True
).select_related('medication', 'prescribed_by')

print(f"\n{patient.get_full_name()}'s Active Medications:")
for pm in active_meds:
    print(f"  • {pm.medication.name}")
    print(f"    Dosage: {pm.dosage}")
    print(f"    Prescribed: {pm.prescribed_date} by Dr. {pm.prescribed_by.last_name}")
    print(f"    Days active: {pm.days_active()}")
```

#### Example 3: Discontinue medication

```python
# Patient stops taking medication
prescription = PatientMedication.objects.get(
    patient=patient,
    medication__name='Lisinopril',
    is_active=True
)

prescription.is_active = False
prescription.discontinue_date = date.today()
prescription.discontinue_reason = 'Patient developed persistent cough (common side effect)'
prescription.save()

print(f"✅ Discontinued: {prescription.medication.name}")
print(f"   Reason: {prescription.discontinue_reason}")
print(f"   Duration: {prescription.days_active()} days")
```

#### Example 4: Medication history report

```python
def generate_medication_history(patient):
    """
    Generate complete medication history for patient
    """
    all_prescriptions = PatientMedication.objects.filter(
        patient=patient
    ).select_related('medication', 'prescribed_by').order_by('-prescribed_date')
    
    print(f"Medication History: {patient.get_full_name()}")
    print("="*60)
    
    # Active medications
    active = all_prescriptions.filter(is_active=True)
    print(f"\n✅ ACTIVE MEDICATIONS ({active.count()}):")
    for pm in active:
        print(f"  • {pm.medication.name} - {pm.dosage}")
        print(f"    Prescribed: {pm.prescribed_date} ({pm.days_active()} days ago)")
    
    # Discontinued medications
    discontinued = all_prescriptions.filter(is_active=False)
    print(f"\n❌ DISCONTINUED MEDICATIONS ({discontinued.count()}):")
    for pm in discontinued:
        print(f"  • {pm.medication.name} - {pm.dosage}")
        print(f"    Prescribed: {pm.prescribed_date}")
        print(f"    Discontinued: {pm.discontinue_date}")
        print(f"    Reason: {pm.discontinue_reason}")

# Generate report
generate_medication_history(patient)
```

---

### 🎯 Hands-On Exercise: Find Drug Interactions

```python
# Business logic: Check if patient's medications have known interactions
def check_drug_interactions(patient):
    """
    Check for potential drug interactions in patient's active medications
    
    (Simplified example - real system would use drug interaction database)
    """
    # Known interactions (simplified)
    INTERACTIONS = {
        ('Lisinopril', 'Spironolactone'): 'High risk of hyperkalemia',
        ('Warfarin', 'Aspirin'): 'Increased bleeding risk',
        ('Metformin', 'Alcohol'): 'Risk of lactic acidosis',
    }
    
    active_meds = patient.medications.filter(
        patientmedication__is_active=True
    ).values_list('name', flat=True)
    
    active_med_list = list(active_meds)
    warnings = []
    
    # Check all pairs
    for i, med1 in enumerate(active_med_list):
        for med2 in active_med_list[i+1:]:
            interaction = INTERACTIONS.get((med1, med2)) or INTERACTIONS.get((med2, med1))
            if interaction:
                warnings.append(f"⚠️ {med1} + {med2}: {interaction}")
    
    return warnings

# Check interactions
interactions = check_drug_interactions(patient)
if interactions:
    print("⚠️ DRUG INTERACTION WARNINGS:")
    for warning in interactions:
        print(f"  {warning}")
else:
    print("✅ No known drug interactions")
```

---

## Lesson 6: Advanced QuerySets & Database Migrations

### 📖 What You'll Learn

Now that you've built complex models, learn to:
- Write efficient database queries
- Use annotations and aggregations
- Create and run migrations
- Optimize with select_related / prefetch_related

---

### 🔧 QuerySet Optimization

#### Problem: N+1 Queries

```python
# ❌ BAD: For 100 appointments, this runs 201 queries!
appointments = Appointment.objects.all()  # 1 query

for appt in appointments:
    print(appt.patient.get_full_name())  # 1 query per appointment (100 queries)
    print(appt.provider.get_full_name())  # 1 query per appointment (100 queries)

# Total: 1 + 100 + 100 = 201 queries 😱
```

#### Solution: select_related (for ForeignKey)

```python
# ✅ GOOD: Only 1 query using SQL JOIN
appointments = Appointment.objects.select_related(
    'patient__user',   # Follow patient → user relationship
    'provider',        # Include provider
    'hospital'         # Include hospital
).all()

for appt in appointments:
    print(appt.patient.get_full_name())  # No extra query!
    print(appt.provider.get_full_name())  # No extra query!
    print(appt.hospital.name)            # No extra query!

# Total: 1 query (with JOINs) 🚀
```

#### Solution: prefetch_related (for ManyToMany)

```python
# ❌ BAD: Loading patient medications
patients = Patient.objects.all()  # 1 query

for patient in patients:
    for pm in patient.patientmedication_set.all():  # 1 query per patient!
        print(pm.medication.name)

# ✅ GOOD: Prefetch all medications
patients = Patient.objects.prefetch_related(
    'patientmedication_set__medication'
).all()

for patient in patients:
    for pm in patient.patientmedication_set.all():  # Already loaded!
        print(pm.medication.name)  # Already loaded!

# Total: 2 queries (one for patients, one for all medications)
```

---

### 💻 Advanced Filtering

#### Complex Q Objects

```python
from django.db.models import Q

# Find appointments: (scheduled OR confirmed) AND (this week)
from datetime import datetime, timedelta

this_week_start = datetime.now()
this_week_end = this_week_start + timedelta(days=7)

appointments = Appointment.objects.filter(
    Q(status=Appointment.SCHEDULED) | Q(status=Appointment.CONFIRMED),  # OR
    scheduled_start__gte=this_week_start,   # AND
    scheduled_start__lt=this_week_end       # AND
)

# Complex: Find patients with (CHF OR Diabetes) AND on Metformin
patients = Patient.objects.filter(
    Q(conditions__name='Congestive Heart Failure') | Q(conditions__name='Type 2 Diabetes'),
    medications__name='Metformin',
    patientmedication__is_active=True
).distinct()  # Important: remove duplicates!
```

#### Annotations & Aggregations

```python
from django.db.models import Count, Avg, Sum, Max, Min

# Count appointments per provider
providers = User.objects.filter(
    groups__name='Healthcare Provider'
).annotate(
    total_appointments=Count('provider_appointments'),
    completed_appointments=Count('provider_appointments', filter=Q(provider_appointments__status=Appointment.COMPLETED))
).order_by('-total_appointments')

print("Provider Workload:")
for provider in providers:
    print(f"  {provider.get_full_name()}: {provider.total_appointments} total, {provider.completed_appointments} completed")

# Average appointment duration by provider
providers_with_avg = User.objects.filter(
    groups__name='Healthcare Provider'
).annotate(
    avg_duration=Avg('provider_appointments__actual_end' - 'provider_appointments__actual_start')
)

# Patient risk score: Count of conditions + active medications
high_risk_patients = Patient.objects.annotate(
    condition_count=Count('conditions'),
    active_med_count=Count('medications', filter=Q(patientmedication__is_active=True)),
    risk_score=Count('conditions') + Count('medications', filter=Q(patientmedication__is_active=True))
).filter(risk_score__gte=5).order_by('-risk_score')

print("\nHigh-Risk Patients (5+ conditions/medications):")
for patient in high_risk_patients:
    print(f"  {patient.get_full_name()}: Risk Score {patient.risk_score} ({patient.condition_count} conditions, {patient.active_med_count} medications)")
```

---

### 🔧 Database Migrations

#### Creating Migrations

```bash
# After modifying models.py, create migration file
python manage.py makemigrations

# Output:
# Migrations for 'wellness_app':
#   wellness_app/migrations/0004_appointment_patientmedication.py
#     - Create model Appointment
#     - Create model PatientMedication
#     - Add field medications to patient
```

#### Viewing Migration SQL

```bash
# See the actual SQL Django will run
python manage.py sqlmigrate wellness_app 0004

# Output (PostgreSQL):
# BEGIN;
# CREATE TABLE "wellness_app_appointment" (
#     "id" serial NOT NULL PRIMARY KEY,
#     "scheduled_start" timestamp with time zone NOT NULL,
#     "scheduled_end" timestamp with time zone NOT NULL,
#     ...
# );
# COMMIT;
```

#### Running Migrations

```bash
# Apply all pending migrations
python manage.py migrate

# Output:
# Operations to perform:
#   Apply all migrations: wellness_app
# Running migrations:
#   Applying wellness_app.0004_appointment_patientmedication... OK
```

#### Checking Migration Status

```bash
# See which migrations have been applied
python manage.py showmigrations

# Output:
# wellness_app
#  [X] 0001_initial
#  [X] 0002_patient_medicalcondition
#  [X] 0003_medication
#  [X] 0004_appointment_patientmedication
#  [ ] 0005_add_hospital_specialties  ← Not yet applied
```

---

### 💡 Custom Migration Example

**Scenario**: Add default medical conditions to database during migration.

```python
# wellness_app/migrations/0005_add_default_conditions.py

from django.db import migrations

def create_default_conditions(apps, schema_editor):
    """
    Data migration: Add common medical conditions
    """
    MedicalCondition = apps.get_model('wellness_app', 'MedicalCondition')
    
    default_conditions = [
        {'name': 'Congestive Heart Failure', 'icd10_code': 'I50.9'},
        {'name': 'Type 2 Diabetes', 'icd10_code': 'E11.9'},
        {'name': 'Hypertension', 'icd10_code': 'I10'},
        {'name': 'COPD', 'icd10_code': 'J44.9'},
        {'name': 'Chronic Kidney Disease', 'icd10_code': 'N18.9'},
    ]
    
    for condition_data in default_conditions:
        MedicalCondition.objects.get_or_create(
            icd10_code=condition_data['icd10_code'],
            defaults={'name': condition_data['name']}
        )

def reverse_default_conditions(apps, schema_editor):
    """
    Reverse migration: Remove default conditions
    """
    MedicalCondition = apps.get_model('wellness_app', 'MedicalCondition')
    MedicalCondition.objects.filter(
        icd10_code__in=['I50.9', 'E11.9', 'I10', 'J44.9', 'N18.9']
    ).delete()

class Migration(migrations.Migration):
    dependencies = [
        ('wellness_app', '0004_appointment_patientmedication'),
    ]
    
    operations = [
        migrations.RunPython(create_default_conditions, reverse_default_conditions),
    ]
```

```bash
# Run the custom migration
python manage.py migrate

# Output:
# Running migrations:
#   Applying wellness_app.0005_add_default_conditions... OK

# Verify
python manage.py shell
>>> from wellness_app.models import MedicalCondition
>>> MedicalCondition.objects.count()
5
>>> MedicalCondition.objects.values_list('name', flat=True)
<QuerySet ['Congestive Heart Failure', 'Type 2 Diabetes', 'Hypertension', 'COPD', 'Chronic Kidney Disease']>
```

---

### 🎯 Final Exercise: Patient Dashboard Query

**Task**: Build a single query to get ALL data for patient dashboard:
- Patient info
- All conditions
- All active medications (with prescriber)
- Upcoming appointments (with provider and hospital)

```python
def get_patient_dashboard_data(patient_id):
    """
    Optimized query for patient dashboard - fetches everything in minimal queries
    """
    patient = Patient.objects.select_related(
        'user'  # Patient info
    ).prefetch_related(
        'conditions',  # All conditions
        Prefetch(
            'patientmedication_set',
            queryset=PatientMedication.objects.filter(is_active=True).select_related('medication', 'prescribed_by'),
            to_attr='active_medications'
        ),
        Prefetch(
            'appointments',
            queryset=Appointment.objects.filter(
                scheduled_start__gte=timezone.now(),
                status__in=[Appointment.SCHEDULED, Appointment.CONFIRMED]
            ).select_related('provider', 'hospital').order_by('scheduled_start'),
            to_attr='upcoming_appointments'
        )
    ).get(id=patient_id)
    
    return patient

# Use it
patient = get_patient_dashboard_data(1)

# Display dashboard
print(f"Patient: {patient.get_full_name()}")
print(f"DOB: {patient.date_of_birth}")
print(f"Contact: {patient.phone_number}, {patient.email}")

print(f"\n📋 Conditions ({patient.conditions.count()}):")
for condition in patient.conditions.all():
    print(f"  • {condition.name}")

print(f"\n💊 Active Medications ({len(patient.active_medications)}):")
for pm in patient.active_medications:
    print(f"  • {pm.medication.name} - {pm.dosage}")
    print(f"    Prescribed by: Dr. {pm.prescribed_by.last_name}")

print(f"\n📅 Upcoming Appointments ({len(patient.upcoming_appointments)}):")
for appt in patient.upcoming_appointments:
    print(f"  • {appt.scheduled_start.strftime('%b %d, %Y at %I:%M %p')}")
    print(f"    Provider: Dr. {appt.provider.last_name}")
    print(f"    Location: {appt.hospital.name}")

# Total queries: ~4 (patient + conditions + medications + appointments)
# Without optimization: ~50+ queries!
```

---

## 🎓 Module 2 Complete!

### What You've Mastered

✅ Patient, MedicalCondition, Medication models (Lessons 1-3)  
✅ Appointment model with multiple ForeignKeys (Lesson 4)  
✅ ManyToMany relationships with through models (Lesson 5)  
✅ Advanced QuerySets, optimization, migrations (Lesson 6)  

### Real-World Skills

- Design healthcare database schemas
- Implement business logic in model methods
- Write efficient queries (avoid N+1 problem)
- Use Django ORM like a pro
- Create and manage database migrations

---

## 📚 Next Steps

**Module 3**: Census API & Geographic Healthcare Analytics  
*Learn to integrate U.S. Census data, calculate hospital catchment areas, and build Power BI visualizations showing "Know Your Patients, Know Your Neighborhood" analytics*

**Module 4**: Django Views & Templates  
*Build the web interface - forms, dashboards, patient portals*

**Module 5**: Authentication & Authorization  
*Secure your application - HIPAA compliance, role-based access*

---

**Module Progress**: 100% complete (Lessons 1-6 of 6) ✅

---

*© 2026 YouBetYourAzure.com - Healthcare Data Analytics Education*
