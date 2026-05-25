# 🗺️ Tejal Bhole — Complete Data Analyst Growth Roadmap

> *Personalized roadmap for Tejal Bhole | Data Analyst @ Apiphany | AI & DS Background | Smart Manufacturing + Green Finance Niche*

---

## 📌 Overview

| Phase | Timeline | Focus |
|---|---|---|
| Phase 1 | Months 1–6 | Strengthen Foundations |
| Phase 2 | Months 6–12 | Level Up Technically |
| Phase 3 | Months 12–18 | Specialization & Niche |
| Phase 4 | Months 18–24 | Leadership & Brand Building |

---

## 🟢 PHASE 1 — Strengthen Foundations (Months 1–6)

### 1.1 SQL — The Language of Data

SQL is the single most important skill for any data analyst. Master it before anything else.

#### Week 1–2: Basics
- `SELECT`, `FROM`, `WHERE`, `ORDER BY`, `LIMIT`
- Filtering with `AND`, `OR`, `NOT`, `IN`, `BETWEEN`, `LIKE`
- `NULL` handling — `IS NULL`, `COALESCE`, `ISNULL`
- String functions — `CONCAT`, `UPPER`, `LOWER`, `TRIM`, `SUBSTRING`, `LEN`
- Date functions — `GETDATE()`, `DATEADD`, `DATEDIFF`, `FORMAT`, `YEAR`, `MONTH`, `DAY`

#### Week 3–4: Intermediate SQL
- `GROUP BY` with `HAVING`
- Aggregate functions — `COUNT`, `SUM`, `AVG`, `MIN`, `MAX`
- All JOINs — `INNER JOIN`, `LEFT JOIN`, `RIGHT JOIN`, `FULL OUTER JOIN`, `CROSS JOIN`, `SELF JOIN`
- Subqueries — correlated vs non-correlated
- `CASE WHEN THEN ELSE END` statements
- `UNION` vs `UNION ALL`

#### Week 5–8: Advanced SQL
- **Window Functions** — this is where most analysts plateau; master these:
  - `ROW_NUMBER()`, `RANK()`, `DENSE_RANK()`
  - `LEAD()` and `LAG()` for time-series comparisons
  - `FIRST_VALUE()`, `LAST_VALUE()`
  - `PARTITION BY` and `ORDER BY` within windows
  - Running totals with `SUM() OVER (PARTITION BY ... ORDER BY ...)`
- **CTEs (Common Table Expressions)**
  - Basic CTEs for readability
  - Recursive CTEs for hierarchical data
  - Chaining multiple CTEs
- **Temp Tables vs CTEs vs Subqueries** — when to use which
- Query optimization:
  - Understanding indexes (clustered vs non-clustered)
  - Reading query execution plans
  - Avoiding `SELECT *`
  - Using `EXISTS` vs `IN`

#### Databases to Practice On
- **PostgreSQL** (free, industry-standard)
- **MySQL** (common in web-based companies)
- **BigQuery** (Google's cloud SQL — very relevant for modern analytics)

#### Practice Platforms
- [Mode Analytics SQL Tutorial](https://mode.com/sql-tutorial/)
- [SQLZoo](https://sqlzoo.net/)
- [LeetCode SQL problems](https://leetcode.com/problemset/database/) — start Easy, target Medium
- [StrataScratch](https://www.stratascratch.com/) — real interview questions from FAANG companies

#### Milestone Check ✅
> Can you write a query that finds the top 3 products by revenue for each month over the last year, showing month-over-month growth percentage? If yes — move to the next module.

---

### 1.2 Python for Data Analysis

Python is your power tool. The goal here is not to become a software engineer — it's to use Python to analyze data faster than Excel ever could.

#### Week 1–2: Python Fundamentals (if not already solid)
- Data types — int, float, string, boolean, list, tuple, dict, set
- Control flow — `if/elif/else`, `for` loops, `while` loops
- Functions — defining, calling, `*args`, `**kwargs`, default arguments
- List comprehensions
- File I/O — reading/writing `.csv`, `.txt`, `.json`
- Error handling — `try/except/finally`
- Virtual environments — `venv`, `pip`, `requirements.txt`

#### Week 3–5: NumPy
- Arrays vs Python lists (why arrays are faster)
- Array creation — `np.array`, `np.zeros`, `np.ones`, `np.arange`, `np.linspace`
- Array operations — broadcasting, slicing, reshaping
- Math operations — `np.mean`, `np.std`, `np.median`, `np.percentile`
- Boolean indexing and masking
- `np.where` for conditional logic

#### Week 5–9: Pandas — The Core Skill
- **Series and DataFrame** — creation, indexing, slicing
- Reading data — `pd.read_csv()`, `pd.read_excel()`, `pd.read_json()`, `pd.read_sql()`
- **Data Inspection** — `.head()`, `.tail()`, `.info()`, `.describe()`, `.shape`, `.dtypes`
- **Data Cleaning** — the most important skill:
  - Handling missing values — `.isnull()`, `.fillna()`, `.dropna()`
  - Removing duplicates — `.duplicated()`, `.drop_duplicates()`
  - Changing data types — `.astype()`
  - Renaming and reordering columns
  - String cleaning — `.str.strip()`, `.str.lower()`, `.str.replace()`, regex in pandas
- **Filtering and Selection**
  - `.loc[]` vs `.iloc[]` — know the exact difference
  - Boolean indexing
  - `.query()` method
- **Grouping and Aggregation**
  - `.groupby()` with multiple aggregations
  - `.agg({'col': ['mean', 'sum', 'count']})`
  - `.pivot_table()` — like Excel pivot but far more powerful
  - `.crosstab()`
- **Merging and Joining**
  - `pd.merge()` — all join types
  - `pd.concat()` — stacking DataFrames vertically/horizontally
  - `.join()` on index
- **Apply and Lambda Functions**
  - `.apply()` on rows and columns
  - `.map()` for element-wise operations
  - `.transform()` for group-level transformations
- **Time Series with Pandas**
  - `pd.to_datetime()` — parsing date strings
  - DatetimeIndex — `.resample()`, `.rolling()`
  - Timezone handling
- **Reshaping Data**
  - `.melt()` — wide to long format
  - `.pivot()` — long to wide format
  - `.stack()` and `.unstack()`

#### Week 9–12: Matplotlib & Seaborn
- **Matplotlib basics**
  - `plt.figure()`, `plt.subplot()`, `plt.subplots()`
  - Line plots, bar charts, scatter plots, histograms, pie charts
  - Customization — labels, titles, legends, colors, grid, figure size
  - Saving figures — `plt.savefig()` with DPI settings
- **Seaborn for statistical visualization**
  - `sns.histplot()`, `sns.kdeplot()` — distributions
  - `sns.boxplot()`, `sns.violinplot()` — spread and outliers
  - `sns.scatterplot()`, `sns.regplot()` — relationships
  - `sns.heatmap()` — correlations (very common in EDA)
  - `sns.pairplot()` — multi-variable relationships
  - `sns.countplot()`, `sns.barplot()` — categorical data
  - `sns.FacetGrid()` — conditional plots

#### Practice Projects for Python
1. Analyze a 1-year sales dataset — find top products, regions, seasonal trends
2. Clean a messy real-world dataset from Kaggle (e.g., Netflix or Airbnb data)
3. Time-series analysis — analyze stock price or weather data with rolling averages

---

### 1.3 Excel & Google Sheets — Advanced

Even as a data analyst with Python skills, you will use Excel and Sheets daily in business settings.

#### Must-Know Excel Skills
- **Formulas**
  - `VLOOKUP`, `HLOOKUP`, `INDEX-MATCH` (prefer Index-Match)
  - `XLOOKUP` (modern Excel)
  - `SUMIF`, `SUMIFS`, `COUNTIF`, `COUNTIFS`, `AVERAGEIF`
  - `IF`, `IFS`, `AND`, `OR`, `NOT`, `IFERROR`
  - `TEXT`, `LEFT`, `RIGHT`, `MID`, `FIND`, `LEN`, `TRIM`
  - `DATE`, `TODAY`, `NOW`, `EDATE`, `EOMONTH`, `NETWORKDAYS`
  - `UNIQUE`, `SORT`, `FILTER`, `SEQUENCE` — dynamic array formulas
- **Pivot Tables** — the most underused tool in most analysts' kits:
  - Creating from scratch
  - Multiple value fields with different aggregations
  - Grouping dates by month/quarter/year
  - Calculated fields
  - Slicers and timelines for interactive dashboards
- **Charts and Dashboards**
  - Combo charts (bar + line)
  - Dynamic charts linked to pivot tables
  - Conditional formatting — color scales, data bars, icon sets
  - Drop-down lists with `Data Validation`
  - Dashboard layout best practices
- **Power Query** (this is a game-changer)
  - Connecting to multiple data sources
  - Merging and appending queries
  - Transforming data without formulas
  - Refreshing data with one click
- **Named Ranges and Dynamic Arrays**
- **Keyboard shortcuts** — learn at least 30; they save hours per week

#### Google Sheets Extras
- `IMPORTRANGE()` — pull data from another sheet
- `QUERY()` function — SQL-like queries inside Sheets
- `ARRAYFORMULA()` — apply formula to entire column
- Google Apps Script basics for automation

---

### 1.4 Statistics — The Foundation of Insight

Without statistics, you're just making colorful charts. With statistics, you're finding truth in data.

#### Descriptive Statistics
- Measures of central tendency — mean, median, mode (and when each is appropriate)
- Measures of spread — variance, standard deviation, IQR, range
- Skewness and kurtosis — understanding data shape
- Percentiles and quartiles — Q1, Q3, IQR
- Outlier detection — IQR method, Z-score method

#### Probability Basics
- Basic probability rules — addition, multiplication
- Conditional probability — P(A|B)
- Bayes' Theorem — understanding and application
- Probability distributions:
  - Normal distribution — 68-95-99.7 rule, Z-scores, standard normal
  - Binomial distribution — success/failure experiments
  - Poisson distribution — event count in fixed time
  - Uniform distribution
  - Exponential distribution — time between events

#### Inferential Statistics
- **Sampling**
  - Population vs sample
  - Sampling methods — random, stratified, cluster, systematic
  - Sampling bias — how to spot and avoid it
- **Confidence Intervals**
  - What a 95% CI actually means (most people get this wrong)
  - Calculating CI for mean
- **Hypothesis Testing**
  - Null hypothesis (H₀) vs alternative hypothesis (H₁)
  - Type I error (false positive) vs Type II error (false negative)
  - p-value — what it means and common misconceptions
  - Significance level (α) — typically 0.05
  - **t-test** — one-sample, two-sample, paired
  - **Chi-square test** — for categorical data
  - **ANOVA** — comparing means across 3+ groups
  - **Mann-Whitney U test** — non-parametric alternative to t-test
- **Correlation**
  - Pearson correlation — linear relationships
  - Spearman correlation — rank-based, for non-linear
  - Correlation is NOT causation — be able to explain this clearly
- **A/B Testing**
  - Designing an A/B test
  - Sample size calculation
  - Statistical significance vs practical significance
  - Common pitfalls — peeking, multiple comparisons

#### Resources
- *Statistics for Data Science* by James D. Miller
- Khan Academy Statistics (free, excellent)
- StatQuest with Josh Starmer on YouTube — HIGHLY recommended

---

## 🔵 PHASE 2 — Level Up Technically (Months 6–12)

### 2.1 Data Visualization — Power BI (Recommended)

Given her corporate data analyst role, Power BI is the most practical choice.

#### Power BI Learning Path

**Month 1: Fundamentals**
- Installing Power BI Desktop (free)
- Connecting to data sources — Excel, CSV, SQL, SharePoint
- Power Query Editor — data transformation inside Power BI
- Data types and data profiling
- Creating basic visuals — bar, line, pie, card, table

**Month 2: Data Modeling**
- Star schema vs snowflake schema — understand this deeply
- Fact tables vs dimension tables
- Relationships — one-to-many, many-to-many
- Cardinality and cross-filter direction
- Calculated columns vs measures — know the exact difference and when to use each

**Month 3: DAX (Data Analysis Expressions)**
This is what separates mediocre Power BI users from experts.
- Basic measures — `SUM`, `AVERAGE`, `COUNT`, `DISTINCTCOUNT`
- `CALCULATE()` — the most important DAX function
- Filter functions — `FILTER()`, `ALL()`, `ALLEXCEPT()`, `KEEPFILTERS()`
- Time intelligence — `TOTALYTD()`, `SAMEPERIODLASTYEAR()`, `DATEADD()`, `DATESYTD()`
- `RELATED()` and `RELATEDTABLE()` for cross-table calculations
- Iterators — `SUMX()`, `AVERAGEX()`, `RANKX()`
- Variables in DAX — `VAR` and `RETURN`
- Context — row context vs filter context (this is the hardest concept in DAX)

**Month 4: Advanced Dashboards**
- Drill-through pages
- Tooltips — custom tooltip pages
- Bookmarks and buttons for interactivity
- Row-level security (RLS) — control who sees what
- Custom visuals from AppSource
- Performance optimization — reducing model size, optimizing DAX
- Publishing to Power BI Service
- Scheduled refresh setup
- Sharing and workspace management

#### Certification
- **PL-300: Microsoft Power BI Data Analyst** — this is the industry-standard cert for Power BI

---

### 2.2 Machine Learning Basics

As a data analyst with an AI & DS degree, adding ML skills creates a clear path toward data science roles.

#### Supervised Learning
- **Linear Regression**
  - Simple and multiple linear regression
  - Assumptions — linearity, homoscedasticity, normality, independence
  - Coefficients, R², adjusted R²
  - Residual analysis
- **Logistic Regression**
  - Binary classification
  - Odds ratio and log-odds
  - Decision boundary
  - Confusion matrix — TP, TN, FP, FN
  - Metrics — Accuracy, Precision, Recall, F1-score, ROC-AUC
- **Decision Trees**
  - Gini impurity vs entropy
  - Overfitting and pruning
  - Feature importance
- **Random Forest**
  - Ensemble learning — bagging concept
  - Why Random Forest > single Decision Tree
  - Hyperparameter tuning — n_estimators, max_depth, min_samples_split
- **Gradient Boosting — XGBoost / LightGBM**
  - Boosting concept
  - XGBoost for tabular data (industry favorite)
  - Basic hyperparameter tuning

#### Unsupervised Learning
- **K-Means Clustering**
  - The algorithm step by step
  - Choosing K — elbow method, silhouette score
  - Limitations of K-Means
- **Hierarchical Clustering**
  - Dendrogram reading
  - Agglomerative vs divisive
- **PCA (Principal Component Analysis)**
  - Dimensionality reduction
  - Explained variance ratio
  - When and why to use PCA

#### The scikit-learn Workflow (learn this pipeline cold)
```
1. Load and explore data
2. Split — train_test_split()
3. Preprocess — StandardScaler, MinMaxScaler, OneHotEncoder, LabelEncoder
4. Build pipeline — Pipeline()
5. Train — model.fit(X_train, y_train)
6. Predict — model.predict(X_test)
7. Evaluate — metrics
8. Tune — GridSearchCV or RandomizedSearchCV
9. Cross-validate — cross_val_score()
```

#### ML Project Ideas for Portfolio
1. Customer churn prediction (classification)
2. Sales forecasting (regression + time series)
3. Customer segmentation (clustering)
4. Anomaly detection in manufacturing sensor data (ties to her niche)

---

### 2.3 Cloud Basics — Google Cloud Platform (GCP)

Given she's at Apiphany and her background, GCP is most aligned.

#### Core GCP Services for Data Analysts
- **BigQuery** — serverless data warehouse, SQL-based
  - Running SQL queries on massive datasets
  - Creating datasets and tables
  - Partitioned and clustered tables (for cost optimization)
  - Scheduled queries
  - Connecting BigQuery to Looker Studio (formerly Data Studio)
- **Google Cloud Storage (GCS)** — object storage for data files
- **Looker Studio** — free dashboarding tool, connect to BigQuery, Sheets, etc.
- **Vertex AI** — basic awareness of managed ML platform
- **Cloud IAM** — understanding permissions and roles

#### Certification
- **Google Associate Cloud Engineer** — foundational (optional for analysts)
- **Google Professional Data Engineer** — advanced, worth targeting in Phase 3-4

#### Alternative: AWS
If her company uses AWS:
- S3 — object storage
- Redshift — cloud data warehouse
- Athena — serverless SQL on S3 data
- QuickSight — dashboarding
- **AWS Cloud Practitioner** — entry-level cert

---

### 2.4 Git & Version Control

Every professional data analyst should know Git. No exceptions.

#### What to Learn
- What is Git and why analysts need it (not just developers)
- **Core commands** — know these by heart:
  - `git init`, `git clone`
  - `git status`, `git add`, `git commit -m "message"`
  - `git push`, `git pull`, `git fetch`
  - `git branch`, `git checkout`, `git switch`
  - `git merge`, `git rebase` (basics)
  - `git log`, `git diff`
  - `git stash`
  - `.gitignore` — what to exclude (data files, secrets, virtual envs)
- **GitHub workflows**
  - Forking and Pull Requests
  - Issues and project boards
  - README files — write good ones with Markdown
  - GitHub Pages for portfolio hosting

#### Git for Data Projects Best Practices
- Never commit raw data files or credentials
- Use `.gitignore` for notebooks with output (or use `nbstripout`)
- Write meaningful commit messages — "Added churn analysis notebook with ROC curve" not "updated file"
- Use branches for experiments

---

## 🟣 PHASE 3 — Specialization (Months 12–18)

### 3.1 Tejal's Unique Niche: Smart Manufacturing + Sustainability Analytics

This is her biggest competitive advantage. Very few analysts sit at this intersection.

#### Smart Manufacturing Analytics
- **OEE (Overall Equipment Effectiveness)** — the gold metric in manufacturing
  - Availability × Performance × Quality
  - How to calculate and visualize OEE from sensor data
- **Predictive Maintenance**
  - Time-series anomaly detection
  - Survival analysis basics
  - Sensor data analysis — vibration, temperature, pressure
- **Statistical Process Control (SPC)**
  - Control charts — X-bar, R-chart, P-chart
  - Control limits — UCL, LCL
  - Western Electric rules for detecting process shifts
- **Supply Chain Analytics**
  - Inventory optimization metrics
  - Lead time analysis
  - Demand forecasting

#### ESG / Sustainability Analytics
Building on her Green Finance Hub Fellowship:
- **ESG Metrics to Know**
  - Scope 1, 2, 3 emissions — calculation methods
  - Carbon footprint analysis
  - Water usage, energy intensity metrics
  - Social metrics — diversity ratios, employee turnover
  - Governance metrics
- **Reporting Standards**
  - GRI (Global Reporting Initiative)
  - TCFD (Task Force on Climate-related Financial Disclosures)
  - SASB Standards
  - EU CSRD — new mandatory ESG reporting directive
- **Green Finance Data Sources**
  - MSCI ESG Ratings data
  - Bloomberg ESG data
  - CDP (Carbon Disclosure Project) data

#### Tools for this Niche
- Python libraries: `Prophet` (Meta's forecasting library), `statsmodels` for time series, `pyod` for anomaly detection
- Industry platforms: SAP Analytics Cloud, Siemens MindSphere awareness

---

### 3.2 Advanced Analytics Techniques

- **Time Series Analysis** (critical for manufacturing and finance)
  - Decomposition — trend, seasonality, residuals
  - ARIMA / SARIMA models
  - Facebook Prophet for business forecasting
  - Anomaly detection in time series
- **Cohort Analysis** — tracking user/customer groups over time
- **Funnel Analysis** — conversion tracking
- **RFM Analysis** — Recency, Frequency, Monetary for customer segmentation
- **Monte Carlo Simulation** — for risk and scenario analysis in finance/manufacturing

---

### 3.3 SQL Advanced — Level Master

By Phase 3, SQL should be almost reflexive:
- **Performance tuning** — query plans, index optimization
- **Stored procedures and views**
- **Analytical functions mastery** — every window function without hesitation
- **dbt (data build tool)** — the modern way to transform data in warehouses
  - dbt models, tests, documentation
  - dbt Cloud vs dbt Core
  - This is becoming a must-have skill for data analysts

---

## 🟡 PHASE 4 — Brand & Leadership (Months 18–24)

### 4.1 LinkedIn Personal Brand

Tejal already has 1,437 followers and is posting — now make it strategic.

#### Content Strategy
- **Post 3x per week minimum**
- Content mix:
  - 40% — Learnings and insights ("Here's what I learned analyzing manufacturing data this week...")
  - 30% — Project showcases (screenshots of dashboards, with key insights)
  - 20% — Industry news commentary (Green Finance, AI in Manufacturing)
  - 10% — Personal story (fellowship experience, career journey)
- **Long-form articles** — write 1 article per month on LinkedIn on her niche topics
- **Engage** — comment meaningfully on posts in her niche (5-10 comments/day)
- **Use data in posts** — "I analyzed X and found Y — here's what surprised me" performs best for data professionals

#### Profile Optimization
- Headline: Make it more specific — "Data Analyst | Manufacturing & Sustainability Analytics | Turning Industrial Data into Decisions"
- Featured section: Add 2-3 best project screenshots or articles
- Skills section: Add and get endorsements for SQL, Python, Power BI, Data Analysis
- Recommendations: Ask manager/colleagues at Apiphany for recommendations

---

### 4.2 Portfolio on GitHub

Create a polished GitHub portfolio with these projects:

| # | Project | Skills Shown | Niche Relevance |
|---|---------|-------------|-----------------|
| 1 | Manufacturing OEE Dashboard | SQL, Python, Power BI | Smart Manufacturing |
| 2 | ESG Carbon Footprint Analyzer | Python, Data viz | Green Finance |
| 3 | Predictive Maintenance Model | ML, Time Series | Smart Manufacturing |
| 4 | Supply Chain Demand Forecast | Prophet, Python | Manufacturing |
| 5 | Green Finance Opportunity Analysis | EDA, Statistics | Green Finance |

Each project repo should have:
- Clear `README.md` with business problem, approach, findings
- Clean, commented code
- Visualizations and insights (not just code)
- Dataset source and any data cleaning steps

---

### 4.3 Certifications Priority List

| Priority | Certification | Cost | Timeline |
|----------|--------------|------|----------|
| 🔴 Must | Google Data Analytics (Coursera) | ~₹2,000/mo | Month 1-3 |
| 🔴 Must | Microsoft PL-300 Power BI | ~₹3,500 | Month 6-9 |
| 🟡 High | AWS Cloud Practitioner OR Google ACE | ~₹7,000 | Month 9-12 |
| 🟡 High | Tableau Desktop Specialist | ~₹15,000 | Month 12 |
| 🟢 Nice | dbt Analytics Engineering Cert | Free | Month 15 |
| 🟢 Nice | Professional Data Engineer (GCP) | ~₹20,000 | Month 18+ |

---

### 4.4 Career Trajectory Options

After executing this roadmap, Tejal will have multiple paths:

**Path A: Senior Data Analyst → Analytics Lead**
- Stay in data analytics, grow into a team lead role
- Manage a team of analysts, own analytics strategy
- Typical timeline: 3-4 years

**Path B: Data Analyst → Data Scientist**
- Leverage AI & DS degree + ML skills
- Move into a data science role with prediction/modeling focus
- Typical timeline: 2-3 years with strong ML projects

**Path C: Niche Expert — Sustainability Analytics**
- Become a specialist in ESG/Green Finance data
- Target companies: ESG rating agencies, green finance firms, sustainability consulting
- High demand, low competition — her best differentiation

**Path D: Product Analytics**
- Move into product analytics at a tech company
- Focus on user behavior, A/B testing, metrics
- Typical timeline: 2 years with right project experience

---

## 📚 Recommended Resources (Free First!)

---

### 🎥 YouTube Channels — Phase-by-Phase Guide

#### SQL
| Channel | What to Watch | Link |
|---------|--------------|------|
| **Alex The Analyst** | Full SQL Beginner to Advanced playlist | [youtube.com/@AlexTheAnalyst](https://www.youtube.com/@AlexTheAnalyst) |
| **Luke Barousse** | SQL for Data Analysts series | [youtube.com/@LukeBarousse](https://www.youtube.com/@LukeBarousse) |
| **Socratica** | SQL tutorials — clean and concise | [youtube.com/@Socratica](https://www.youtube.com/@Socratica) |
| **techTFQ** | Advanced SQL — window functions, CTEs | [youtube.com/@techTFQ](https://www.youtube.com/@techTFQ) |

#### Python & Pandas
| Channel | What to Watch | Link |
|---------|--------------|------|
| **Keith Galli** | Pandas complete tutorial, Python projects | [youtube.com/@KeithGalli](https://www.youtube.com/@KeithGalli) |
| **Corey Schafer** | Python OOP, pandas, matplotlib — best structured content | [youtube.com/@coreyms](https://www.youtube.com/@coreyms) |
| **Sentdex** | Python for data analysis and ML | [youtube.com/@sentdex](https://www.youtube.com/@sentdex) |
| **Rob Mulla** | EDA walkthroughs and Kaggle notebooks | [youtube.com/@robmulla](https://www.youtube.com/@robmulla) |

#### Statistics & Math
| Channel | What to Watch | Link |
|---------|--------------|------|
| **StatQuest with Josh Starmer** | Statistics, ML, hypothesis testing — best channel ever | [youtube.com/@statquest](https://www.youtube.com/@statquest) |
| **Khan Academy** | Statistics & Probability full course (free) | [youtube.com/@khanacademy](https://www.youtube.com/@khanacademy) |
| **3Blue1Brown** | Linear algebra and math intuition visually | [youtube.com/@3blue1brown](https://www.youtube.com/@3blue1brown) |

#### Power BI
| Channel | What to Watch | Link |
|---------|--------------|------|
| **Guy in a Cube** | Power BI tutorials, DAX deep dives | [youtube.com/@GuyInACube](https://www.youtube.com/@GuyInACube) |
| **Pragmatic Works** | Power BI fundamentals and advanced | [youtube.com/@PragmaticWorks](https://www.youtube.com/@PragmaticWorks) |
| **SQLBI** | DAX — the deepest DAX content on YouTube | [youtube.com/@SQLBI](https://www.youtube.com/@SQLBI) |
| **Avi Singh - PowerBIPro** | Power BI tips and tricks | [youtube.com/@PowerBIPro](https://www.youtube.com/@PowerBIPro) |

#### Machine Learning
| Channel | What to Watch | Link |
|---------|--------------|------|
| **StatQuest with Josh Starmer** | ML algorithms explained from scratch | [youtube.com/@statquest](https://www.youtube.com/@statquest) |
| **Sentdex** | ML with scikit-learn hands-on | [youtube.com/@sentdex](https://www.youtube.com/@sentdex) |
| **Krish Naik** | ML, DL, end-to-end projects (great for Indian context) | [youtube.com/@krishnaik06](https://www.youtube.com/@krishnaik06) |
| **CampusX** | Hindi/English ML and data science — very detailed | [youtube.com/@campusx-official](https://www.youtube.com/@campusx-official) |
| **Andrej Karpathy** | Deep learning fundamentals (advanced, Phase 3+) | [youtube.com/@AndrejKarpathy](https://www.youtube.com/@AndrejKarpathy) |

#### Data Analytics Career & Projects
| Channel | What to Watch | Link |
|---------|--------------|------|
| **Alex The Analyst** | Data analyst portfolio projects, resume tips | [youtube.com/@AlexTheAnalyst](https://www.youtube.com/@AlexTheAnalyst) |
| **Luke Barousse** | Day in the life, tools, career advice | [youtube.com/@LukeBarousse](https://www.youtube.com/@LukeBarousse) |
| **Thu Vu data analytics** | Real projects, career journey | [youtube.com/@Thuvu5](https://www.youtube.com/@Thuvu5) |
| **Tina Huang** | Data science career, Kaggle tips | [youtube.com/@TinaHuang1](https://www.youtube.com/@TinaHuang1) |

#### Cloud & GCP
| Channel | What to Watch | Link |
|---------|--------------|------|
| **Google Cloud Tech** | Official GCP tutorials and BigQuery | [youtube.com/@googlecloudtech](https://www.youtube.com/@googlecloudtech) |
| **Cloud With Raj** | GCP for beginners — Indian creator | [youtube.com/@CloudWithRaj](https://www.youtube.com/@CloudWithRaj) |
| **freeCodeCamp** | AWS, GCP full courses — completely free | [youtube.com/@freecodecamp](https://www.youtube.com/@freecodecamp) |

#### Git & GitHub
| Channel | What to Watch | Link |
|---------|--------------|------|
| **Corey Schafer** | Git tutorials for beginners | [youtube.com/@coreyms](https://www.youtube.com/@coreyms) |
| **The Coding Train** | Git and GitHub explained simply | [youtube.com/@TheCodingTrain](https://www.youtube.com/@TheCodingTrain) |

---

### 🌐 Free Websites & Platforms

#### SQL Practice (All Free)
| Platform | Best For | Link |
|----------|---------|------|
| **SQLZoo** | Interactive SQL exercises, beginner to advanced | [sqlzoo.net](https://sqlzoo.net) |
| **Mode SQL Tutorial** | Real-world SQL with practice environment | [mode.com/sql-tutorial](https://mode.com/sql-tutorial/) |
| **W3Schools SQL** | Quick SQL reference and try-it editor | [w3schools.com/sql](https://www.w3schools.com/sql/) |
| **LeetCode (Database)** | SQL interview problems (filter by Easy/Medium) | [leetcode.com/problemset/database](https://leetcode.com/problemset/database/) |
| **StrataScratch** | Real interview SQL questions from top companies | [stratascratch.com](https://www.stratascratch.com) |
| **HackerRank SQL** | Structured SQL challenges with levels | [hackerrank.com/domains/sql](https://www.hackerrank.com/domains/sql) |
| **pgexercises.com** | PostgreSQL-specific practice problems | [pgexercises.com](https://pgexercises.com) |

#### Python Learning (Free)
| Platform | Best For | Link |
|----------|---------|------|
| **freeCodeCamp** | Python for beginners full course | [freecodecamp.org](https://www.freecodecamp.org) |
| **Python Official Docs** | Reference and tutorials | [docs.python.org/3/tutorial](https://docs.python.org/3/tutorial/) |
| **Real Python** | Tutorials, articles, projects — very practical | [realpython.com](https://realpython.com) |
| **Pandas Docs** | Official pandas documentation with examples | [pandas.pydata.org/docs](https://pandas.pydata.org/docs/) |
| **W3Schools Python** | Quick reference for syntax | [w3schools.com/python](https://www.w3schools.com/python/) |

#### Statistics (Free)
| Platform | Best For | Link |
|----------|---------|------|
| **Khan Academy Statistics** | Full probability & statistics course, free | [khanacademy.org/math/statistics-probability](https://www.khanacademy.org/math/statistics-probability) |
| **Seeing Theory** | Visual introduction to statistics — beautiful | [seeing-theory.brown.edu](https://seeing-theory.brown.edu) |
| **OpenIntro Statistics** | Free textbook PDF + exercises | [openintro.org/book/os](https://www.openintro.org/book/os/) |

#### Machine Learning (Free)
| Platform | Best For | Link |
|----------|---------|------|
| **Kaggle Learn** | Free micro-courses: Python, ML, pandas, SQL | [kaggle.com/learn](https://www.kaggle.com/learn) |
| **Google ML Crash Course** | Intro to ML by Google, completely free | [developers.google.com/machine-learning/crash-course](https://developers.google.com/machine-learning/crash-course) |
| **Fast.ai** | Practical deep learning for coders (free) | [fast.ai](https://www.fast.ai) |
| **scikit-learn Docs** | Best ML library documentation with examples | [scikit-learn.org/stable/user_guide](https://scikit-learn.org/stable/user_guide.html) |
| **Distill.pub** | Research-quality ML explanations, visual | [distill.pub](https://distill.pub) |

#### Power BI (Free)
| Platform | Best For | Link |
|----------|---------|------|
| **Microsoft Learn — Power BI** | Official free learning path for PL-300 | [learn.microsoft.com/en-us/training/powerplatform/power-bi](https://learn.microsoft.com/en-us/training/browse/?products=power-bi) |
| **SQLBI DAX Guide** | Complete DAX function reference, free | [dax.guide](https://dax.guide) |
| **Power BI Community** | Forums, sample reports, ideas | [community.powerbi.com](https://community.powerbi.com) |

#### Cloud & GCP (Free)
| Platform | Best For | Link |
|----------|---------|------|
| **Google Cloud Skills Boost** | Free learning paths, some with free credits | [cloudskillsboost.google](https://cloudskillsboost.google) |
| **BigQuery Sandbox** | Free tier — run queries on public datasets | [console.cloud.google.com/bigquery](https://console.cloud.google.com/bigquery) |
| **AWS Skill Builder** | Free AWS training courses | [skillbuilder.aws](https://skillbuilder.aws) |

#### Git & GitHub (Free)
| Platform | Best For | Link |
|----------|---------|------|
| **GitHub Docs** | Official Git and GitHub guides | [docs.github.com](https://docs.github.com) |
| **Learn Git Branching** | Interactive visual Git tutorial — very fun | [learngitbranching.js.org](https://learngitbranching.js.org) |
| **Oh My Git!** | Card-based Git learning game | [ohmygit.org](https://ohmygit.org) |

#### Reading & Articles (Free)
| Platform | Best For | Link |
|----------|---------|------|
| **Towards Data Science** | Data science articles, tutorials, case studies | [towardsdatascience.com](https://towardsdatascience.com) |
| **Medium (Data)** | Broad data + analytics articles | [medium.com/tag/data-science](https://medium.com/tag/data-science) |
| **The Pudding** | Data journalism — inspiring visual storytelling | [pudding.cool](https://pudding.cool) |
| **FlowingData** | Data visualization inspiration | [flowingdata.com](https://flowingdata.com) |
| **Substack — Data newsletters** | Subscribe to: *Data Elixir*, *The Analytics Engineering Roundup* | [substack.com](https://substack.com) |

#### Datasets to Practice On (All Free)
| Source | What's Available | Link |
|--------|----------------|------|
| **Kaggle Datasets** | Thousands of curated datasets on every topic | [kaggle.com/datasets](https://www.kaggle.com/datasets) |
| **data.gov.in** | Indian government open data — manufacturing, economy | [data.gov.in](https://data.gov.in) |
| **Our World in Data** | Sustainability, climate, ESG datasets | [ourworldindata.org](https://ourworldindata.org) |
| **UCI ML Repository** | Classic ML datasets | [archive.ics.uci.edu/ml](https://archive.ics.uci.edu/ml/index.php) |
| **Google Dataset Search** | Search engine for datasets across the web | [datasetsearch.research.google.com](https://datasetsearch.research.google.com) |
| **World Bank Open Data** | Economic and development data globally | [data.worldbank.org](https://data.worldbank.org) |
| **BigQuery Public Datasets** | Real large-scale datasets in the cloud, free to query | [cloud.google.com/bigquery/public-data](https://cloud.google.com/bigquery/public-data) |

---

### 📖 Books (Free PDFs or Very Low Cost)
| Book | Why Read It | Where to Get |
|------|-------------|-------------|
| *Storytelling with Data* — Cole Nussbaumer Knaflic | Best book on data visualization for analysts | Buy or borrow |
| *Naked Statistics* — Charles Wheelan | Statistics made human and accessible | Buy or borrow |
| *Python for Data Analysis* — Wes McKinney | Pandas bible by its creator | Buy or borrow |
| *OpenIntro Statistics* | Free statistics textbook (PDF) | [openintro.org](https://www.openintro.org/book/os/) — **Free PDF** |
| *The Elements of Statistical Learning* | Advanced stats/ML — classic reference | [web.stanford.edu/~hastie/ElemStatLearn](https://web.stanford.edu/~hastie/ElemStatLearn/) — **Free PDF** |
| *Calling Bullshit* — Carl T. Bergstrom | Critical thinking with data | Buy or borrow |

---

### 🗓️ Suggested Weekly Learning Schedule by Phase

#### Phase 1 Weekly Plan
| Day | Platform | Activity |
|-----|----------|----------|
| Monday | SQLZoo / LeetCode | 2–3 SQL problems |
| Tuesday | YouTube (Corey Schafer) | 1 Python tutorial video + practice |
| Wednesday | Kaggle Learn | 1 Python/Pandas lesson |
| Thursday | Khan Academy | 1 Statistics topic |
| Friday | Mode Analytics | 1 SQL tutorial + exercise |
| Saturday | Personal project | Apply week's skills on a dataset |
| Sunday | LinkedIn | Write a post about what you learned |

#### Phase 2 Weekly Plan
| Day | Platform | Activity |
|-----|----------|----------|
| Monday | Microsoft Learn | 1 Power BI module |
| Tuesday | YouTube (SQLBI) | 1 DAX video + replicate |
| Wednesday | Kaggle Learn | ML micro-course lesson |
| Thursday | Google Cloud Skills Boost | 1 BigQuery lab |
| Friday | GitHub | Push code from the week |
| Saturday | Personal project | Build on portfolio project |
| Sunday | LinkedIn | Post a dashboard screenshot + insight |

---

## 🎯 Weekly Routine Template

| Time | Activity |
|------|----------|
| Mon–Fri: 1 hr/day | Skill learning (SQL/Python/Power BI — rotate per phase) |
| Sat: 3 hrs | Project work (build portfolio) |
| Sun: 1 hr | LinkedIn — write post, engage with community |
| Any day: 20 min | Read 1 article from Towards Data Science or LinkedIn feed |
| Monthly | Complete 1 mini certification module |

---

## 📊 Progress Tracker

### Phase 1 Checklist
- [ ] Write a complex SQL query with window functions and CTEs
- [ ] Clean and analyze a messy dataset with pandas
- [ ] Build an advanced Excel dashboard with pivot tables and slicers
- [ ] Run a hypothesis test on real data and explain the result in plain English
- [ ] Complete Google Data Analytics Certificate

### Phase 2 Checklist
- [ ] Build and publish a 3-page Power BI report
- [ ] Write DAX measures using CALCULATE and time intelligence
- [ ] Train and evaluate 3 different ML models on the same dataset
- [ ] Run first BigQuery query on a public dataset
- [ ] Push first data project to GitHub

### Phase 3 Checklist
- [ ] Complete one Manufacturing OEE analysis project end-to-end
- [ ] Complete one ESG/sustainability analysis project
- [ ] Learn and implement dbt on a project
- [ ] Earn PL-300 certification
- [ ] Get 5,000 LinkedIn followers

### Phase 4 Checklist
- [ ] 5 polished projects on GitHub
- [ ] Published 3+ LinkedIn articles in niche
- [ ] Applied for Senior Data Analyst or Data Scientist roles
- [ ] 2 LinkedIn recommendations from colleagues

---

*Last updated: May 2026 | Crafted with 💙 for Tejal's data journey*