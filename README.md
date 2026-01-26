# NYC Airbnb Market Analysis: Data-Driven Investment Dashboard

![Tableau](https://img.shields.io/badge/Tableau-E97627?style=for-the-badge&logo=Tableau&logoColor=white)
![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)
![Pandas](https://img.shields.io/badge/Pandas-150458?style=for-the-badge&logo=pandas&logoColor=white)

A comprehensive data analysis project that transforms raw Airbnb listing data into actionable investment insights through data cleaning, feature engineering, and interactive Tableau dashboards.

---

## 📊 Project Overview

This project analyzes **101,348 NYC Airbnb listings** to provide data-driven insights for real estate investors. The analysis pipeline includes:

1. **Data Cleaning & Preparation** (Python)
2. **Feature Engineering** (Python)
3. **Interactive Dashboard Creation** (Tableau)
4. **Investment Insights Generation**

**Target Audience:** Real estate investors, property managers, and market analysts seeking to understand the NYC short-term rental market.

---

## 🎯 Key Insights Discovered

### Market Overview
- **Total Revenue Potential:** $10.59 billion across all NYC listings
- **Average Listing Price:** $622/night (after outlier removal)
- **Market Concentration:** Manhattan (42.5%) and Brooklyn (40.6%) dominate with 83% of listings

### Competition Analysis
- **62% of hosts are individuals** (single property owners)
- **Only 12% are professional operators** (6+ properties)
- **Professional hosts earn 2X more per listing** ($178K vs $85K annually)

### Property Performance
- **Building age has NO impact** on pricing or ratings
- **Year-round availability = 17X higher revenue** ($272K vs $16K for part-time hosts)
- **Top neighborhood:** Bedford-Stuyvesant, Brooklyn ($675M revenue potential)

---

## 🛠️ Technologies Used

### Data Processing
- **Python 3.x**
- **Pandas** - Data manipulation and cleaning
- **NumPy** - Numerical computations

### Visualization
- **Tableau Desktop Public Edition** - Interactive dashboard creation

### Development Environment
- **Jupyter Notebook** - Data exploration and cleaning

---


---

## 🔧 Data Cleaning Process

### 1. Initial Data Assessment
- **Dataset Size:** 101,348 rows × 22 columns
- **Data Types:** Mixed (numeric, categorical, geographic)
- **Quality Issues:** Duplicates, nulls, outliers, inconsistent formatting

### 2. Data Cleaning Steps

#### Duplicate Removal
```python
# Remove duplicate listings based on ID
df = df.drop_duplicates(subset=['id'], keep='first')
```

#### Null Value Handling
- **Strategy:** Analyzed null patterns by column
- **Actions:**
  - Dropped rows with nulls in critical columns (price, location)
  - Imputed nulls in non-critical columns (reviews, ratings)

#### Outlier Treatment
```python
# Cap extreme prices at $1,200 to remove unrealistic values
df['price'] = df['price'].clip(upper=1200)

# Remove listings with 0 availability (inactive)
df = df[df['availability_365'] > 0]
```

#### Data Type Corrections
```python
# Convert data types for proper analysis
df['price'] = df['price'].astype(float)
df['Construction year'] = df['Construction year'].astype(int)
df['availability_365'] = df['availability_365'].astype(int)
```

---

## ⚙️ Feature Engineering

Created **8 new calculated fields** to enable deeper analysis:

### 1. Revenue Potential
```python
df['revenue_potential'] = df['price'] * df['availability_365']
```
**Purpose:** Estimates maximum annual revenue per listing

### 2. Price Category
```python
df['price_category'] = pd.cut(df['price'], 
                               bins=[0, 100, 200, 1200],
                               labels=['Budget', 'Mid-Range', 'Premium'])
```
**Purpose:** Segments market by pricing tier

### 3. Host Type
```python
def categorize_host(count):
    if count == 1:
        return 'Single'
    elif count <= 5:
        return 'Small (2-5)'
    elif count <= 20:
        return 'Medium (6-20)'
    else:
        return 'Large (20+)'

host_counts = df.groupby('host_id')['id'].count()
df['host_type'] = df['host_id'].map(host_counts).apply(categorize_host)
```
**Purpose:** Identifies individual vs professional operators

### 4. Listing Quality
```python
df['listing_quality'] = pd.cut(df['review_rate_number'],
                                bins=[0, 2, 3, 4, 5],
                                labels=['Poor', 'Good', 'Excellent', 'Unrated'])
```
**Purpose:** Categorizes properties by guest satisfaction

### 5. Property Age
```python
df['property_age'] = 2025 - df['Construction year']
```
**Purpose:** Analyzes impact of building age on performance

### 6. Property Age Group
```python
df['property_age_group'] = pd.cut(df['property_age'],
                                   bins=[0, 5, 10, 15, 20, 100],
                                   labels=['0-5 years (New)', '6-10 years', 
                                          '11-15 years', '16-20 years', '20+ years (Old)'])
```
**Purpose:** Groups properties by age categories

### 7. Availability Category
```python
df['availability_category'] = pd.cut(df['availability_365'],
                                      bins=[-1, 89, 179, 364, 365],
                                      labels=['Low (<90)', 'Medium (90-179)', 
                                             'High (180-364)', 'Always (365)'])
```
**Purpose:** Segments by host commitment level

### 8. Total Price
```python
df['total_price'] = df['price'] + df['service_fee']
```
**Purpose:** Calculates all-in cost for guests

---

## 📈 Tableau Dashboards

### Dashboard 1: Market Overview
**Purpose:** High-level market analysis for initial investment assessment

**Components:**
- **KPIs:** Total Listings (101,348) | Total Revenue Potential ($10.59B)
- **Charts:**
  - Listing count by borough (Manhattan & Brooklyn dominate)
  - Listing quality distribution by room type
  - Average price by room type and borough

**Key Insight:** Manhattan and Brooklyn account for 83% of the market, with nearly identical median pricing across boroughs.

---

### Dashboard 2: Revenue Potential Analysis
**Purpose:** Identify highest-revenue opportunities

**Components:**
- **KPI:** Total Revenue Potential ($10.59B)
- **Charts:**
  - Revenue potential by borough and room type
  - Top 10 most profitable neighborhoods
  - Revenue breakdown visualization

**Key Insights:**
- **Top Neighborhood:** Bedford-Stuyvesant, Brooklyn ($675M)
- **Best Property Type:** Entire Home/Apartment in Brooklyn or Manhattan
- **Brooklyn dominates:** 6 of top 10 neighborhoods

---

### Dashboard 3: Host Competition & Market Dynamics
**Purpose:** Understand competitive landscape

**Components:**
- **KPIs:** 
  - Total Hosts: 101,347
  - Professional Market Share: 11.93%
  - Avg Listings per Host: 7.9
- **Charts:**
  - Market share by host size (pie chart)
  - Revenue per listing by host type
  - Host type revenue breakdown
  - Professional host concentration by borough

**Key Insights:**
- **Low professional competition:** 88% of hosts are amateurs
- **Professional advantage:** Large operators make $178K per listing vs $85K for individuals
- **Market opportunity:** Bronx has highest professional concentration (7.2%)

---

### Dashboard 4: Property Analysis
**Purpose:** Evaluate property characteristics and availability strategies

**Components:**
- **KPIs:**
  - Average Property Age: 12.5 years
  - Newest Property: 2022
  - Avg Availability: 139 days/year
- **Charts:**
  - Construction timeline (area chart showing market growth)
  - Availability distribution by category
  - Revenue per listing by availability level

**Key Insights:**
- **Property age doesn't matter:** Prices and ratings identical across all age groups
- **Availability is everything:** Year-round hosts earn 17X more than part-timers ($272K vs $16K)
- **Market composition:** 49% are part-time hosts (<90 days available)

---

## 💡 Business Recommendations

Based on the analysis, we recommend investors:

1. **Target Brooklyn neighborhoods** (Bedford-Stuyvesant, Williamsburg, Bushwick) for highest revenue potential
2. **Commit to year-round availability** (180+ days) to maximize returns
3. **Focus on Entire Home/Apartment** listings over private rooms
4. **Enter markets with low professional competition** (Manhattan, Brooklyn still 63-65% individual hosts)
5. **Don't overpay for new construction** - building age has no impact on pricing or ratings

---

## 🚀 How to Use This Project

### For Data Analysts

1. **Clone the repository:**
```bash
git clone https://github.com/yourusername/nyc-airbnb-analysis.git
cd nyc-airbnb-analysis
```

2. **Install dependencies:**
```bash
pip install pandas numpy jupyter
```

3. **Run the Jupyter notebook:**
```bash
jupyter notebook notebooks/data_cleaning_and_feature_engineering.ipynb
```

4. **Open Tableau dashboards:**
- Download Tableau Desktop Public (free)
- Open `.twbx` files in `dashboards/` folder

### For Business Users

1. **Download Tableau Reader** (free) from [Tableau's website](https://www.tableau.com/products/reader)
2. **Open dashboard files** (`.twbx`) to explore interactive visualizations
3. **Filter by:** Borough, room type, price range, host type
4. **Export insights** as images or PDFs for presentations

---

## 📊 Data Sources

- **Original Dataset:** NYC Airbnb Open Data (via [Kaggle](https://www.kaggle.com/) or similar public source)
- **Time Period:** 2019-2022 listings
- **Geographic Scope:** All 5 NYC boroughs
- **Record Count:** 101,348 active listings

---

## 📝 Key Learnings

### Technical Skills Developed
- Data cleaning and validation techniques in Pandas
- Feature engineering for business intelligence
- Dashboard design principles for non-technical audiences
- Data storytelling and visualization best practices

### Domain Knowledge Gained
- NYC real estate market dynamics
- Short-term rental business economics
- Competitive analysis frameworks
- Investment decision-making criteria

---

---

*Last Updated: January 2025*
