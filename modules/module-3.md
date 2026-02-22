# Module 3: Census API & Geographic Healthcare Analytics

**Platform:** youbetyourazure.com  
**Course**: Healthcare Data Analytics with Census API & Power BI  
**Module Duration**: 3 hours  
**Prerequisites**: Basic Python, understanding of APIs  
**Level**: Advanced

## 🎯 Module Objectives

By the end of this module, you will:
1. Understand U.S. Census API structure and healthcare applications
2. Calculate hospital catchment areas using geographic coordinates
3. Create Power BI maps with demographic overlays
4. Build Venn diagram analytics for multi-hospital networks
5. Perform gap analysis comparing Census data vs. actual patients served
6. Implement patient routing optimization algorithms

---

## 📚 Lesson Overview

| # | Lesson | Duration | Key Concepts |
|---|--------|----------|--------------|
| 1 | Census API Fundamentals | 30 min | API structure, variables, geography levels |
| 2 | Hospital Catchment Areas | 30 min | GPS coordinates, radius calculations, ZIP aggregation |
| 3 | Power BI Geographic Viz | 30 min | Map visualizations, bubble maps, choropleth |
| 4 | Multi-Hospital Venn Diagrams | 30 min | Overlapping service areas, capacity analysis |
| 5 | Gap Analysis & Market Intelligence | 30 min | Census vs. actual patients, opportunity sizing |
| 6 | Patient Routing Optimization | 30 min | Distance calculations, specialty matching, algorithms |

---

## Lesson 1: Census API Fundamentals for Healthcare

### 📖 What You'll Learn

The U.S. Census Bureau provides **30,000+ demographic variables** through their free API. For healthcare applications, this data reveals:

- Population characteristics (age, disability status, poverty level)
- Catchment area demographics around hospitals
- Underserved populations
- Market opportunities

### 🔧 Census API Structure

**Endpoint Pattern:**
```
https://api.census.gov/data/{YEAR}/{DATASET}
  ?get={VARIABLES}
  &for={GEOGRAPHY}:{CODE}
```

**Example - Get demographics for ZIP 63144:**
```python
import requests

CENSUS_API = "https://api.census.gov/data/2020/acs/acs5"

params = {
    "get": "B01003_001E,B19013_001E,B18101_001E",  # Population, Income, Disability
    "for": "zip code tabulation area:63144"
}

response = requests.get(CENSUS_API, params=params)
data = response.json()

# Returns: [
#   ["B01003_001E", "B19013_001E", "B18101_001E", "zip code tabulation area"],
#   ["8759", "65432", "1245", "63144"]
# ]
```

### Key Healthcare Variables

| Variable | Description | Healthcare Use Case |
|----------|-------------|---------------------|
| `B01003_001E` | Total population | Catchment area size |
| `B18101_*` | Disability by age/sex | High-need patient identification |
| `B19013_001E` | Median household income | Financial assistance targeting |
| `B01002_001E` | Median age | Service line planning (pediatrics vs. geriatrics) |
| `B17001_*` | Poverty status | Community health needs |
| `B25077_001E` | Median home value | Market segmentation |

### 💻 Hands-On Exercise 1.1: Fetch Hospital Neighborhood Demographics

**Task:** Create a Python function to fetch demographics for any ZIP code.

```python
def get_zip_demographics(zip_code):
    """
    Fetch key healthcare demographics for a ZIP code
    
    Args:
        zip_code (str): 5-digit ZIP code
        
    Returns:
        dict: Demographics with keys: population, income, disabled, age_65_plus
    """
    CENSUS_API = "https://api.census.gov/data/2020/acs/acs5"
    
    # Variables we want
    variables = [
        "B01003_001E",  # Total population
        "B19013_001E",  # Median household income
        "B18101_001E",  # Total for disability status
        "B18101_004E",  # Male disabled 18-34
        "B18101_007E",  # Male disabled 35-64
        "B18101_010E",  # Male disabled 65-74
        "B18101_013E",  # Male disabled 75+
        "B18101_023E",  # Female disabled 18-34
        "B18101_026E",  # Female disabled 35-64
        "B18101_029E",  # Female disabled 65-74
        "B18101_032E",  # Female disabled 75+
        "B01001_020E",  # Male 65-66
        "B01001_021E",  # Male 67-69
        "B01001_022E",  # Male 70-74
        "B01001_023E",  # Male 75-79
        "B01001_024E",  # Male 80-84
        "B01001_025E",  # Male 85+
        "B01001_044E",  # Female 65-66
        "B01001_045E",  # Female 67-69
        "B01001_046E",  # Female 70-74
        "B01001_047E",  # Female 75-79
        "B01001_048E",  # Female 80-84
        "B01001_049E",  # Female 85+
    ]
    
    params = {
        "get": ",".join(variables),
        "for": f"zip code tabulation area:{zip_code}"
    }
    
    try:
        response = requests.get(CENSUS_API, params=params, timeout=10)
        response.raise_for_status()
        data = response.json()
        
        # Parse response (skip header row)
        values = data[1]
        
        # Calculate totals
        disabled_total = sum([
            int(values[3]),   # Male 18-34
            int(values[4]),   # Male 35-64
            int(values[5]),   # Male 65-74
            int(values[6]),   # Male 75+
            int(values[7]),   # Female 18-34
            int(values[8]),   # Female 35-64
            int(values[9]),   # Female 65-74
            int(values[10]),  # Female 75+
        ])
        
        age_65_plus = sum([
            int(values[11]),  # Male 65-66
            int(values[12]),  # Male 67-69
            int(values[13]),  # Male 70-74
            int(values[14]),  # Male 75-79
            int(values[15]),  # Male 80-84
            int(values[16]),  # Male 85+
            int(values[17]),  # Female 65-66
            int(values[18]),  # Female 67-69
            int(values[19]),  # Female 70-74
            int(values[20]),  # Female 75-79
            int(values[21]),  # Female 80-84
            int(values[22]),  # Female 85+
        ])
        
        return {
            "zip_code": zip_code,
            "population": int(values[0]),
            "median_income": int(values[1]) if values[1] != "-666666666" else 0,
            "disabled_residents": disabled_total,
            "age_65_plus": age_65_plus,
            "disabled_percentage": round((disabled_total / int(values[0])) * 100, 1),
            "elderly_percentage": round((age_65_plus / int(values[0])) * 100, 1)
        }
        
    except Exception as e:
        print(f"Error fetching Census data: {e}")
        return None

# Test it
demographics = get_zip_demographics("63144")
print(demographics)

# Output:
# {
#   'zip_code': '63144',
#   'population': 8759,
#   'median_income': 65432,
#   'disabled_residents': 1245,
#   'age_65_plus': 1876,
#   'disabled_percentage': 14.2,
#   'elderly_percentage': 21.4
# }
```

### 🎯 Exercise 1.2: Multi-ZIP Aggregation

**Real-World Scenario:** St. Joseph's Hospital serves 5 ZIP codes. Calculate total catchment area demographics.

```python
def get_hospital_catchment_demographics(zip_codes):
    """
    Aggregate demographics across multiple ZIP codes
    
    Args:
        zip_codes (list): List of ZIP code strings
        
    Returns:
        dict: Aggregated demographics
    """
    total_population = 0
    total_disabled = 0
    total_elderly = 0
    incomes = []
    
    for zip_code in zip_codes:
        demo = get_zip_demographics(zip_code)
        if demo:
            total_population += demo['population']
            total_disabled += demo['disabled_residents']
            total_elderly += demo['age_65_plus']
            if demo['median_income'] > 0:
                incomes.append(demo['median_income'])
    
    return {
        "total_zips": len(zip_codes),
        "total_population": total_population,
        "disabled_residents": total_disabled,
        "age_65_plus": total_elderly,
        "disabled_percentage": round((total_disabled / total_population) * 100, 1),
        "elderly_percentage": round((total_elderly / total_population) * 100, 1),
        "avg_income": round(sum(incomes) / len(incomes)) if incomes else 0
    }

# Test with St. Joseph's Hospital catchment area
st_josephs_zips = ["63301", "63302", "63303", "63304", "63366"]
catchment = get_hospital_catchment_demographics(st_josephs_zips)

print(f"St. Joseph's Hospital Catchment Area:")
print(f"  Total Population: {catchment['total_population']:,}")
print(f"  Disabled Residents: {catchment['disabled_residents']:,} ({catchment['disabled_percentage']}%)")
print(f"  Age 65+: {catchment['age_65_plus']:,} ({catchment['elderly_percentage']}%)")
print(f"  Avg. Income: ${catchment['avg_income']:,}")
```

---

## Lesson 2: Hospital Catchment Areas with GPS Coordinates

### 📖 What You'll Learn

Hospitals serve patients within a geographic radius, typically 5-10 miles. By combining:
- Hospital GPS coordinates
- ZIP code geographic centers
- Distance calculations

We can identify which ZIP codes fall within a hospital's catchment area.

### 🔧 Geographic Distance Calculation

**Haversine Formula** calculates distance between two GPS coordinates:

```python
import math

def calculate_distance_miles(lat1, lon1, lat2, lon2):
    """
    Calculate distance between two GPS coordinates using Haversine formula
    
    Args:
        lat1, lon1: First coordinate (degrees)
        lat2, lon2: Second coordinate (degrees)
        
    Returns:
        float: Distance in miles
    """
    # Earth radius in miles
    R = 3959
    
    # Convert to radians
    lat1_rad = math.radians(lat1)
    lat2_rad = math.radians(lat2)
    delta_lat = math.radians(lat2 - lat1)
    delta_lon = math.radians(lon2 - lon1)
    
    # Haversine formula
    a = (math.sin(delta_lat / 2) ** 2 +
         math.cos(lat1_rad) * math.cos(lat2_rad) *
         math.sin(delta_lon / 2) ** 2)
    c = 2 * math.atan2(math.sqrt(a), math.sqrt(1 - a))
    
    distance = R * c
    return round(distance, 2)

# Example: Distance from St. Joseph's Hospital to ZIP 63301
hospital_coords = (38.7764, -90.5092)  # St. Joseph's Hospital, Lake Saint Louis
zip_coords = (38.7947, -90.7852)        # ZIP 63301 center

distance = calculate_distance_miles(
    hospital_coords[0], hospital_coords[1],
    zip_coords[0], zip_coords[1]
)
print(f"Distance: {distance} miles")  # Output: 15.3 miles
```

### 💻 Exercise 2.1: Find ZIPs Within Radius

```python
# ZIP code centers for St. Charles County (sample data)
ZIP_COORDINATES = {
    "63301": (38.7947, -90.7852),
    "63302": (38.7538, -90.5231),
    "63303": (38.8126, -90.4789),
    "63304": (38.7211, -90.6543),
    "63366": (38.8592, -90.6011),
    "63367": (38.7089, -90.7123),
    "63368": (38.9123, -90.5234),
}

def find_zips_within_radius(hospital_lat, hospital_lon, radius_miles):
    """
    Find all ZIP codes within a specified radius of hospital
    
    Args:
        hospital_lat, hospital_lon: Hospital GPS coordinates
        radius_miles: Catchment area radius
        
    Returns:
        list: ZIP codes within radius with distances
    """
    nearby_zips = []
    
    for zip_code, (zip_lat, zip_lon) in ZIP_COORDINATES.items():
        distance = calculate_distance_miles(
            hospital_lat, hospital_lon,
            zip_lat, zip_lon
        )
        
        if distance <= radius_miles:
            nearby_zips.append({
                "zip_code": zip_code,
                "distance_miles": distance,
                "coordinates": (zip_lat, zip_lon)
            })
    
    # Sort by distance
    return sorted(nearby_zips, key=lambda x: x['distance_miles'])

# Find catchment area for 5-mile radius
st_josephs_coords = (38.7764, -90.5092)
catchment_zips = find_zips_within_radius(
    st_josephs_coords[0], 
    st_josephs_coords[1], 
    radius_miles=5
)

print("St. Joseph's Hospital 5-Mile Catchment Area:")
for zip_info in catchment_zips:
    print(f"  {zip_info['zip_code']}: {zip_info['distance_miles']} miles")
```

### 🎯 Exercise 2.2: Complete Catchment Analysis

**Combine GPS + Census API:**

```python
def analyze_hospital_catchment(hospital_name, hospital_lat, hospital_lon, radius_miles=5):
    """
    Complete catchment area analysis for a hospital
    
    Returns:
        dict: Full demographic profile of catchment area
    """
    # Step 1: Find ZIPs within radius
    catchment_zips = find_zips_within_radius(hospital_lat, hospital_lon, radius_miles)
    zip_codes = [z['zip_code'] for z in catchment_zips]
    
    # Step 2: Get Census demographics for each ZIP
    demographics = get_hospital_catchment_demographics(zip_codes)
    
    # Step 3: Combine results
    return {
        "hospital_name": hospital_name,
        "catchment_radius_miles": radius_miles,
        "zip_codes_served": len(zip_codes),
        "zip_codes": zip_codes,
        "total_population": demographics['total_population'],
        "potential_disabled_patients": demographics['disabled_residents'],
        "potential_elderly_patients": demographics['age_65_plus'],
        "market_insights": {
            "disabled_percentage": demographics['disabled_percentage'],
            "elderly_percentage": demographics['elderly_percentage'],
            "avg_household_income": demographics['avg_income']
        }
    }

# Run complete analysis
analysis = analyze_hospital_catchment(
    "St. Joseph's Hospital - Lake Saint Louis",
    38.7764, -90.5092,
    radius_miles=5
)

print(f"\n{analysis['hospital_name']} - Catchment Area Analysis")
print(f"{'='*60}")
print(f"Radius: {analysis['catchment_radius_miles']} miles")
print(f"ZIP Codes: {analysis['zip_codes_served']}")
print(f"Total Population: {analysis['total_population']:,}")
print(f"\nTarget Populations:")
print(f"  Disabled Residents: {analysis['potential_disabled_patients']:,} ({analysis['market_insights']['disabled_percentage']}%)")
print(f"  Age 65+: {analysis['potential_elderly_patients']:,} ({analysis['market_insights']['elderly_percentage']}%)")
print(f"\nMarket Characteristics:")
print(f"  Avg. Household Income: ${analysis['market_insights']['avg_household_income']:,}")
```

---

## Lesson 3: Power BI Geographic Visualizations

### 📖 What You'll Learn

Power BI excels at geographic visualizations. You'll learn to:
- Create bubble maps showing hospital locations
- Add demographic data layers
- Build choropleth maps (color-coded by metrics)
- Interactive filtering by region

### 🔧 Connecting Power BI to Healthcare Database

**Step 1: Get Data from PostgreSQL**

1. Open Power BI Desktop
2. Get Data → PostgreSQL database
3. Server: `your-database-server.postgres.database.azure.com`
4. Database: `wellness`
5. Enter credentials

**Step 2: Load Hospital Data**

```sql
-- Query to fetch hospital locations with demographics
SELECT 
    h.id,
    h.organization_name,
    h.city,
    h.state,
    h.zip_code,
    h.latitude,
    h.longitude,
    h.licensed_bed_count,
    -- Calculate catchment demographics (would come from Census table)
    COUNT(DISTINCT p.id) as patients_served
FROM wellness_app_hospital h
LEFT JOIN wellness_app_patientcareepisode p 
    ON p.referring_hospital_id = h.id
GROUP BY h.id;
```

### 💻 Exercise 3.1: Create Bubble Map

**Power BI Steps:**

1. **Add Map Visual:**
   - Click "Map" visualization
   - Drag `latitude` to Location → Latitude
   - Drag `longitude` to Location → Longitude

2. **Size Bubbles by Bed Count:**
   - Drag `licensed_bed_count` to Size

3. **Color by Patient Volume:**
   - Drag `patients_served` to Legend
   - Format → Data colors → Choose gradient

4. **Add Tooltips:**
   - Drag `organization_name`, `city`, `licensed_bed_count` to Tooltips

**Result:** Interactive map showing all hospitals, sized by beds, colored by utilization

### 🎯 Exercise 3.2: Add Census Demographics Layer

**Create a ZIP-level demographic dataset:**

```python
# Python script to generate Power BI data source
import pandas as pd

# List of all ZIPs in service area
all_zips = ["63301", "63302", "63303", "63304", "63366", "63367"]

demographics_data = []
for zip_code in all_zips:
    demo = get_zip_demographics(zip_code)
    if demo:
        demographics_data.append({
            "ZIP": zip_code,
            "Population": demo['population'],
            "Disabled": demo['disabled_residents'],
            "Age65Plus": demo['age_65_plus'],
            "MedianIncome": demo['median_income'],
            "DisabledPct": demo['disabled_percentage']
        })

# Save to CSV for Power BI import
df = pd.DataFrame(demographics_data)
df.to_csv("census_demographics.csv", index=False)
print("✅ Census data exported for Power BI")
```

**In Power BI:**
1. Get Data → Text/CSV → Load `census_demographics.csv`
2. Add "Filled Map" visualization
3. Location: `ZIP`
4. Saturation: `DisabledPct`
5. Result: Choropleth map color-coded by disability rate

---

## Lesson 4: Multi-Hospital Venn Diagram Analytics

### 📖 What You'll Learn

When multiple hospitals serve overlapping geographic areas, Venn diagrams reveal:
- Overlap zones (patients could go to either hospital)
- Coverage gaps
- Capacity imbalances
- Optimal patient routing

### 🔧 Identifying Overlapping Catchment Areas

```python
def find_catchment_overlap(hospital_a, hospital_b, radius_miles=5):
    """
    Find ZIP codes in overlapping catchment areas of two hospitals
    
    Args:
        hospital_a: (name, lat, lon)
        hospital_b: (name, lat, lon)
        radius_miles: Catchment radius
        
    Returns:
        dict: Overlap analysis
    """
    # Get catchment areas
    zips_a = set([z['zip_code'] for z in find_zips_within_radius(
        hospital_a[1], hospital_a[2], radius_miles
    )])
    
    zips_b = set([z['zip_code'] for z in find_zips_within_radius(
        hospital_b[1], hospital_b[2], radius_miles
    )])
    
    # Calculate overlaps
    overlap = zips_a & zips_b  # Intersection
    only_a = zips_a - zips_b
    only_b = zips_b - zips_a
    
    # Get demographics for overlap zone
    overlap_demographics = get_hospital_catchment_demographics(list(overlap))
    
    return {
        "hospital_a": hospital_a[0],
        "hospital_b": hospital_b[0],
        "zips_only_a": len(only_a),
        "zips_only_b": len(only_b),
        "zips_overlap": len(overlap),
        "overlap_population": overlap_demographics['total_population'],
        "overlap_zips": list(overlap)
    }

# Example: Analyze overlap between two St. Joseph hospitals
hospital_lake_stlouis = ("St. Joseph - Lake St. Louis", 38.7764, -90.5092)
hospital_wentzville = ("St. Joseph - Wentzville", 38.8114, -90.8529)

overlap_analysis = find_catchment_overlap(hospital_lake_stlouis, hospital_wentzville)

print(f"\nCatchment Area Overlap Analysis:")
print(f"  {overlap_analysis['hospital_a']}: {overlap_analysis['zips_only_a']} exclusive ZIPs")
print(f"  {overlap_analysis['hospital_b']}: {overlap_analysis['zips_only_b']} exclusive ZIPs")
print(f"  Overlap: {overlap_analysis['zips_overlap']} shared ZIPs ({overlap_analysis['overlap_population']:,} people)")
print(f"  Overlap ZIPs: {', '.join(overlap_analysis['overlap_zips'])}")
```

### 💻 Exercise 4.1: 29-Hospital Network Venn Analysis

**For SSM Health's 29 hospitals:**

```python
def analyze_full_network_overlaps(hospitals, radius_miles=5):
    """
    Analyze ALL pairwise catchment overlaps in hospital network
    
    Args:
        hospitals: List of (name, lat, lon) tuples
        radius_miles: Catchment radius
        
    Returns:
        list: All significant overlaps
    """
    overlaps = []
    
    for i in range(len(hospitals)):
        for j in range(i + 1, len(hospitals)):
            overlap = find_catchment_overlap(
                hospitals[i], 
                hospitals[j], 
                radius_miles
            )
            
            # Only include significant overlaps (2+ shared ZIPs)
            if overlap['zips_overlap'] >= 2:
                overlaps.append(overlap)
    
    # Sort by overlap size
    return sorted(overlaps, key=lambda x: x['overlap_population'], reverse=True)

# SSM Health hospitals (sample)
ssm_hospitals = [
    ("St. Joseph - Lake St. Louis", 38.7764, -90.5092),
    ("St. Joseph - St. Charles", 38.7836, -90.4898),
    ("St. Joseph - Wentzville", 38.8114, -90.8529),
    ("St. Mary's - St. Louis", 38.6436, -90.2453),
    # ... add all 29 hospitals
]

network_overlaps = analyze_full_network_overlaps(ssm_hospitals)

print(f"\nSSM Health Network: Top 10 Catchment Overlaps")
print(f"{'='*80}")
for idx, overlap in enumerate(network_overlaps[:10], 1):
    print(f"{idx}. {overlap['hospital_a']} ↔ {overlap['hospital_b']}")
    print(f"   Shared ZIPs: {overlap['zips_overlap']} | Population: {overlap['overlap_population']:,}")
```

---

## Lesson 5: Gap Analysis & Market Intelligence

### 📖 What You'll Learn

**Gap Analysis** compares:
- Census demographics (potential patients) 
- Actual patients served by hospital

Reveals underserved populations and market opportunities.

### 🔧 Gap Analysis Model

```python
def perform_gap_analysis(hospital_id, hospital_catchment_zips):
    """
    Compare Census potential vs. actual patients served
    
    Args:
        hospital_id: Database ID of hospital
        hospital_catchment_zips: List of ZIP codes in catchment
        
    Returns:
        dict: Gap analysis results
    """
    # Get Census demographics (potential)
    census_data = get_hospital_catchment_demographics(hospital_catchment_zips)
    
    # Get actual patients from database (pseudo-code - would be Django ORM)
    patients_served = PatientCareEpisode.objects.filter(
        referring_hospital_id=hospital_id
    )
    
    disabled_patients = patients_served.filter(has_disability=True).count()
    elderly_patients = patients_served.filter(
        patient_profile__user__date_of_birth__lt=date.today() - timedelta(days=65*365)
    ).count()
    
    # Calculate gaps
    return {
        "census_potential": {
            "total_population": census_data['total_population'],
            "disabled_residents": census_data['disabled_residents'],
            "age_65_plus": census_data['age_65_plus']
        },
        "actual_served": {
            "total_patients": patients_served.count(),
            "disabled_patients": disabled_patients,
            "elderly_patients": elderly_patients
        },
        "gap_analysis": {
            "disabled_gap": census_data['disabled_residents'] - disabled_patients,
            "disabled_penetration_pct": round((disabled_patients / census_data['disabled_residents']) * 100, 1),
            "elderly_gap": census_data['age_65_plus'] - elderly_patients,
            "elderly_penetration_pct": round((elderly_patients / census_data['age_65_plus']) * 100, 1)
        },
        "opportunities": {
            "additional_disabled_patients": census_data['disabled_residents'] - disabled_patients,
            "additional_elderly_patients": census_data['age_65_plus'] - elderly_patients,
            "estimated_annual_revenue": (census_data['disabled_residents'] - disabled_patients) * 5500
        }
    }

# Example
gap_results = perform_gap_analysis(
    hospital_id=1,
    hospital_catchment_zips=["63301", "63302", "63303"]
)

print("\n📊 Gap Analysis Report")
print(f"{'='*60}")
print(f"\n Census Potential:")
print(f"   Disabled residents: {gap_results['census_potential']['disabled_residents']:,}")
print(f"   Age 65+: {gap_results['census_potential']['age_65_plus']:,}")

print(f"\n✅ Actually Serving:")
print(f"   Disabled patients: {gap_results['actual_served']['disabled_patients']}")
print(f"   Elderly patients: {gap_results['actual_served']['elderly_patients']}")

print(f"\n⚠️  Service Gaps:")
print(f"   Disabled penetration: {gap_results['gap_analysis']['disabled_penetration_pct']}%")
print(f"   Missing {gap_results['opportunities']['additional_disabled_patients']:,} potential disabled patients")

print(f"\n💰 Market Opportunity:")
print(f"   Estimated Annual Revenue: ${gap_results['opportunities']['estimated_annual_revenue']:,}")
```

---

## Lesson 6: Patient Routing Optimization

### 📖 What You'll Learn

When a patient lives in an overlap zone (served by multiple hospitals), route them to the OPTIMAL hospital based on:
- Distance
- Specialty match (cardiac center for CHF patient)
- Current capacity / wait times
- Historical outcomes

### 🔧 Hospital Scoring Algorithm

```python
def calculate_hospital_score(patient, hospital, max_score=100):
    """
    Score hospital suitability for patient
    
    Args:
        patient: dict with address, conditions, insurance
        hospital: dict with location, specialties, capacity, outcomes
        max_score: Maximum possible score
        
    Returns:
        float: Suitability score (0-100)
    """
    score = 0
    
    # Factor 1: Distance (40 points max)
    # Closer = better, penalty after 10 miles
    distance = calculate_distance_miles(
        patient['latitude'], patient['longitude'],
        hospital['latitude'], hospital['longitude']
    )
    if distance <= 5:
        distance_score = 40
    elif distance <= 10:
        distance_score = 40 - ((distance - 5) * 4)  # -4 pts per mile over 5
    else:
        distance_score = max(0, 20 - ((distance - 10) * 2))  # -2 pts per mile over 10
    
    score += distance_score
    
    # Factor 2: Specialty match (30 points max)
    patient_conditions = set(patient.get('conditions', []))
    hospital_specialties = set(hospital.get('specialties', []))
    
    if patient_conditions & hospital_specialties:  # Intersection
        specialty_score = 30
    elif 'General Medicine' in hospital_specialties:
        specialty_score = 15
    else:
        specialty_score = 0
    
    score += specialty_score
    
    # Factor 3: Capacity (20 points max)
    utilization = hospital.get('current_utilization_pct', 100)
    if utilization < 70:
        capacity_score = 20
    elif utilization < 85:
        capacity_score = 15
    elif utilization < 95:
        capacity_score = 10
    else:
        capacity_score = 0  # At capacity
    
    score += capacity_score
    
    # Factor 4: Outcomes (10 points max)
    readmission_rate = hospital.get('readmission_rate_pct', 20)
    if readmission_rate < 10:
        outcomes_score = 10
    elif readmission_rate < 15:
        outcomes_score = 7
    elif readmission_rate < 20:
        outcomes_score = 4
    else:
        outcomes_score = 0
    
    score += outcomes_score
    
    return round(score, 1)


def recommend_optimal_hospital(patient, nearby_hospitals):
    """
    Recommend best hospital for patient from available options
    
    Args:
        patient: Patient details
        nearby_hospitals: List of hospitals within acceptable range
        
    Returns:
        list: Ranked hospitals with scores
    """
    recommendations = []
    
    for hospital in nearby_hospitals:
        score = calculate_hospital_score(patient, hospital)
        recommendations.append({
            "hospital_name": hospital['name'],
            "score": score,
            "distance_miles": calculate_distance_miles(
                patient['latitude'], patient['longitude'],
                hospital['latitude'], hospital['longitude']
            ),
            "specialties": hospital.get('specialties', []),
            "capacity": f"{hospital.get('current_utilization_pct', 0)}%"
        })
    
    # Sort by score
    return sorted(recommendations, key=lambda x: x['score'], reverse=True)


# Example usage
patient = {
    "name": "John Smith",
    "latitude": 38.7947,
    "longitude": -90.7852,
    "conditions": ["CHF", "Diabetes"],
    "insurance": "Medicare"
}

nearby_hospitals = [
    {
        "name": "St. Joseph - Lake St. Louis",
        "latitude": 38.7764,
        "longitude": -90.5092,
        "specialties": ["Cardiac", "General Medicine"],
        "current_utilization_pct": 72,
        "readmission_rate_pct": 12
    },
    {
        "name": "St. Joseph - Wentzville",
        "latitude": 38.8114,
        "longitude": -90.8529,
        "specialties": ["Orthopedic", "General Medicine"],
        "current_utilization_pct": 85,
        "readmission_rate_pct": 15
    },
    {
        "name": "St. Mary's - St. Louis",
        "latitude": 38.6436,
        "longitude": -90.2453,
        "specialties": ["Cardiac", "Oncology", "General Medicine"],
        "current_utilization_pct": 94,
        "readmission_rate_pct": 10
    }
]

recommendations = recommend_optimal_hospital(patient, nearby_hospitals)

print(f"\n🏥 Hospital Recommendations for {patient['name']}")
print(f"{'='*70}")
for idx, rec in enumerate(recommendations, 1):
    print(f"\n{idx}. {rec['hospital_name']} - Score: {rec['score']}/100")
    print(f"   Distance: {rec['distance_miles']} miles")
    print(f"   Specialties: {', '.join(rec['specialties'])}")
    print(f"   Current Capacity: {rec['capacity']}")
```

---

## 🎓 Module 3 Summary

You've learned to:

✅ Fetch Census demographics via API  
✅ Calculate hospital catchment areas with GPS  
✅ Create Power BI geographic visualizations  
✅ Analyze multi-hospital network overlaps  
✅ Perform gap analysis (potential vs. actual)  
✅ Build patient routing optimization algorithms  

### Real-World Applications

- **Hospital CFOs:** "Show me untapped market opportunity"
- **Care Coordinators:** "Route patients to optimal facility"
- **Network Planners:** "Should we open new location?"
- **Quality Teams:** "Are we serving our community equitably?"

### Your Portfolio Project

**Build:** Interactive Power BI dashboard showing:
1. Map of all 29 SSM Health hospitals
2. Color-coded catchment areas
3. Overlap zones (Venn diagrams)
4. Gap analysis by hospital
5. Patient routing recommendations

---

## 📚 Additional Resources

- [Census API Documentation](https://www.census.gov/data/developers/data-sets.html)
- [Haversine Formula Explained](https://en.wikipedia.org/wiki/Haversine_formula)
- [Power BI Geographic Visualizations](https://docs.microsoft.com/en-us/power-bi/visuals/power-bi-map-tips-and-tricks)
- [Healthcare Analytics Best Practices](https://www.healthcatalyst.com/insights/healthcare-analytics-best-practices)

---

## ✅ Module 3 Complete!

**Next Module:** Module 4 - Predictive Analytics & Machine Learning for Healthcare

---

**Need Help?**  
📧 Email: learn@youbetyourazure.com  
💬 Community Forum: [youbetyourazure.com/community](https://youbetyourazure.com/community)

---

*© 2026 YouBetYourAzure.com - Healthcare Data Analytics Education*
