# Instructor Run Sheet
## Using LLMs to Programmatically Extract and Curate Research Data
**Instructor copy**

---

## Setup — display on slide

1. Create folder: `data curation workshop resources 30 June 2026`
2. Download `plants_site_A.csv`, `plants_site_B.csv`, `plants_sites_AB_large.csv` from Zoom chat
3. Open Copilot at [copilot.microsoft.com](https://copilot.microsoft.com)
4. Open Word — title: `Prompt log — data curation workshop — 30 June 2026`
5. Open data in Excel

---

## Segment 1 — Intro + framing

### 1.1 Welcome and logistics
### 1.2 The data curation problem
### 1.3 Scenario walkthrough

---

## Segment 2 — Block 1: Data cleaning

### 2.0 Stage 1 — Data exploration

*Drag and drop `plants_site_A.csv` into Copilot.*

**Prompt →**
```
I am a researcher studying the effect of weed management on native plant
restoration across two sites in West Auckland. I want to understand whether
weeding alongside native planting (Site A) produces better plant outcomes
than native planting alone (Site B).

Attached is my data set (plants_site_A)

Tell me what you understand about this data — the columns, and what they seem
to measure.
```

---

### Stage 2 — Numeric: `Height_cm`

**Steps 1–2: Identify**

**Prompt →**
```
Look at `Height_cm` only. Return a table with the mean, min, max, range.
```

**Prompt →**
```
Look at `Height_cm` only. Return a two-column table with the original column (`Hieght_cm`), and a new column (`Height_cm_cleaned`) with potential outlier values marked with "check".
```

**Step 3: Fix**

**Prompt →**
```
Modify value 340 to 34 and return the two-column table, do not include any notes in the new cell, jsut "34".
```

**Step 4: Validate** — paste into `plants_site_A`; check first, last, changed rows

**Step 5: Log** — copy prompt to Word table `| column | prompt | summary of changes |`

**Step 6: Apply**

**Prompt →**
```
Provide me the same summary stats and outlier checks for `Num_Leaves` and `Num_Flowers`.
```

**Prompt →**
```
Modify values and return four column table (original column + _cleaned):
`Num_Leaves`: 847 -> 87. 
`Num_Flowers`: -3 -> 3. Keep 22 as 22.
```

---

### 2.3 Categorical: `Site`, `Location`, `Species`

**Steps 1–2: Identify** — Excel: Data → Filter, check `Species` dropdown

**Step 3: Fix**

**Prompt →**
```
Clean categorical columns `Site`, `Location`, `Quadrat`, `Species` and `Flowering`.
I want them to be consistently spelt, all values lower-case, and values separated by underscores "_". 

Return pairs of columns for each variable: the original and a new column `[column_name]_cleaned` for
each. In the cleaned column have the new, corrected value and use "check" if you are unsure.
```

**Step 3b (optional): Condition**

**Prompt →**
```
For `Condition`, return the original column and a new column `Condition_cleaned` with suggested categories representing a consistent five-point scale from poor to excellent.
```

**Step 5: Log** — copy prompts to Word

---

### 2.4 Block 1 close

---

## Segment 3 — Block 2: Feature extraction

### 3.1 Plant_Age_Months — guided

**Prompt →**
```
Look at `Date_Planted` and `Date_Measured`. These columns contain dates
in inconsistent formats — some DD/MM/YYYY, some D/M/YY, some DD-Mon
with no year, and possibly some US-format (M/D/YY).

Without standardising the formats first, calculate the age of each plant
in whole months at the time of measurement. Add this as a new column
called `Plant_Age_Months`. Return these three columns as a table.
Flag any row where you cannot confidently interpret the dates with "check".
```

**Validate** — paste into spreadsheet; check row count, spot-check 3 rows, flag negatives

**Step 5: Log**

---

### 3.2 Plant_ID — independent

**Prompt →** *(hint if needed)*
```
Using `Site_cleaned`, `Location_cleaned`, `Quadrat_cleaned`, and `Species_cleaned`,
create a new column `Plant_ID`. Format example: Sa_L1_Q1_LS (Site = A, Location = 1, quadrat = 1, Leptospermum scoparium)
Return the only the new column.
```

**Validate** — sort by `Plant_ID` in Excel; check all values unique

**Step 5: Log**

---

### 3.3 Notes — independent

**Prompt →**
```
I am about to analyse `Plant_Age_Months`, `Height_cm`, and `Condition` for these
plants. For each row, I want to know whether the `Notes` field gives any reason this row's
measurements might be unreliable or need special handling.

Return two new columns: `Data_Quality_Flag`: Y or N; and `Data_Quality_Notes`: a short reason for any Y.
```

**Prompt →**
```
Provide me a row-by-row thematic analysis of the `Notes` field, limit your analysis of each comment into one of three, short distinct themes from the perspective of a botanist that you will decide. Include the analysis as column `Notes_themes`.
```

**Step 5: Log**

---

### 3.5 Apply to Site B

*Fresh Copilot conversation. Participants work from their Word log.*

**Save combined output as** `plants_combined.csv`

---

## Segment 4 — Wrap-up

### 4.1 Scale test

*New Copilot conversation. Drag and drop `plants_sites_AB_large.csv`.*

**Prompt →**
```
Here is a dataset of 1,520 plant survey records from two sites.
> What I want you to do is to complete all of the same steps you completed for site_A and _B. Return a table of all original columns (even those not modified) and `[column_name]_cleaned' for any new, cleaned ones.

> For numerical values, flag any potential outliers with "check"; for categorical variables, change spelling, separation errors, and flag any values you are uncertain about with "check".

> Perform these steps:

> Cleaned or new columns	Modification prompt
Height_cm	Look at `Height_cm` only. Return a two-column table with the original column (`Hieght_cm`), and a new column (`Height_cm_cleaned`) with potential outlier values marked with "check".
Num_Leaves
Num_Flowers	Provide me the same summary stats and outlier checks for `Num_Leaves` and `Num_Flowers`.
	
> Notes_themes	Provide me a row-by-row thematic analysis of the `Notes` field, limit your analysis of each comment into on of three, short distinct themes from the perspective of a botanist that you will decide. Include the analysis as column `Notes_themes`.

> Site`Location`, `Quadrat`, `Species` and `Flowering`.	Clean categorical columns `Site`, `Location`, `Quadrat`, `Species` and `Flowering`.
I want them to be consistently spelt, all values lower-case, and values separated by underscores "_". 
Return pairs of columns for each variable: the original and a new column `[column_name]_cleaned` for
each. In the cleaned column have the new, corrected value and use "check"" if you are unsure.

> Using `Site_cleaned`, `Location_cleaned`, `Quadrat_cleaned`, and `Species_cleaned`,
create a new column `Plant_ID`. Format example: Sa_L1_Q1_LS (Site = A, Location = 1, quadrat = 1, Leptospermum scoparium)
Return the only the new column.

> Plant_Age_Months	Look at `Date_Planted` and `Date_Measured`. These columns contain dates
in inconsistent formats — some DD/MM/YYYY, some D/M/YY, some DD-Mon
with no year, and possibly some US-format (M/D/YY).
Without standardising the formats first, calculate the age of each plant
in whole months at the time of measurement. Add this as a new column
called `Plant_Age_Months`. Return the these three columns as a table.
Flag any row where you cannot confidently interpret the dates with "check".

> Condition	For `Condition`, return the original column and a new column `Condition_cleaned` with suggested categories representing a consistent five-point scale from poor to excellent.

> Data_Quality_Flag
Data_Quality_Notes	I am about to analyse 'Age_Months`, `Height_cm`, and `Condition` for these
plants. For each row, I want to know whether the `Notes` field give any reason this row's
measurements might be unreliable or need special handling.
Add two new columns: `Data_Quality_Flag`: Y or N; and `Data_Quality_Notes`: a short reason for any Y."
```

---

### 4.2 AI R Code

**Prompt →**
```
Produce the R code to run these steps myself in one block of code (one script) I can copy and paste across in one go. Put some thought into this. 
```

---

### 4.3 Decision Matrix
### 4.4 Next Steps

---

### 4.4 Considerations

### 4.5 Resources + close
