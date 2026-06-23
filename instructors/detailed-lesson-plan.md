# Detailed Lesson Plan
## Using LLMs to Programmatically Extract and Curate Research Data
**ResBaz NZ | Instructor copy**

---

**How to read this document:**
- `> **Instructor:**` — note for the instructor only; not read aloud to participants
- `**Prompt →**` followed by a code block — copy or paste directly into Copilot
- `> "text in quotes"` — say this aloud to participants

---

## At a glance

- Opening: setup checklist
- Intro + framing
- Block 1: Data cleaning
- Block 2: Feature extraction
- Wrap-up

> **Instructor:** Buffer for questions or expanding 3.1 if needed. Cut 3.3 (Notes) or 3.4 (Plant_Health_Score, optional) if Block 2 runs long.

**Tool:** Microsoft Copilot (browser — participants' own account, institutional or free tier)
**Datasets:** `plants_site_A.csv` and `plants_site_B.csv` and `plants_sites_AB_large.csv`

---

## Before you start

Ask who is in the audience? 
What field are they in? 
How much data cleaning have they had to do? 
Use a word to describe your experiences of it.

> **Instructor:** Share `plants_site_A.csv` via the Zoom chat before the session opens. Have your own Copilot tab, Excel file, and Word log open and ready to demo. Slides open alongside — flip between them throughout.

### Participant setup checklist (opening activity — display on slide)

Ask participants to complete these steps as the session opens:

**1. Create your workshop folder**
Open Finder or File Explorer. Create a new folder somewhere easy to find (Desktop or Documents). Name it exactly: `data curation workshop resources 30 June 2026`

**2. Get the data**
Download `plants_site_A.csv` and `plants_site_B.csv` and `plants_sites_AB_large.csv` from the Zoom chat. Save it into your workshop folder.

**3. Open Copilot**
Go to [copilot.microsoft.com](https://copilot.microsoft.com) in your browser. Log in with your university Microsoft account (or a personal Microsoft account if you don't have one).

**4. Open Word**
Open a new Word document. Title it: `Prompt log — data curation workshop — 30 June 2026`. Save it to your workshop folder.

> **Instructor:** Two things go in the Word log: table with column(s) + prompts that worked + a one-line summary of what each changed. Not a minute-by-minute record.

**5. Check out data**
Open data in Excel. Have a look: what does this look like its measuring. What looks like it could be challenging?

---

## Dataset quick reference — plants_site_A.csv

> **Instructor:** Keep this visible from 1.3 onward. If a participant names an issue, it will be here.

| Column | Error type | Values / rows | Notes |
|---|---|---|---|
| `Percentage_Cover` | Out-of-range | 344 (likely 34.4), −5 (impossible), 111 (ambiguous) | 344 and −5 are clear errors; 111 could be genuine dense cover |
| `Height_cm` | Implausible outlier | 340 cm (1 row) | Notes column flags this row explicitly |
| `Num_Leaves` | Outlier + duplicate row | 847 (appears twice) | Notes say "may be inflated"; same row pair signals the dataset's duplicate |
| `Num_Flowers` | Impossible value | −3 (row 20) | Also: some rows have `Flowering = No` with non-zero `Num_Flowers` |
| `Site` | Capitalisation variants | `Site_A`, `site_a`, `Site_a` | 3 variants of the same value |
| `Location` | Capitalisation + typo | `Loc_1`, `Loc_2`, `LOC_1`, `LOC_2`, `Loc_l` | `Loc_l` = letter l, not number 1 |
| `Quadrat` | Case + possible entry error | `Q1`, `Q2`, `Q3`, `Q11`, `q2`, `q3` | `Q11` may be `Q1` with an extra digit |
| `Flowering` | Multiple variants | `Yes`, `yes`, `YES`, `Y`, `No`, `NO`, `no`, `N`, `0` | `0` requires a judgment call |
| `Species` | Misspellings + unknown | `Leptospermun scoparium`, `Coprosma robust`, `Phormiun tenax`, `[check]` | `[check]` = unresolved from prior survey; `Pseudopanax crassifolius` is valid (decoy — don't flag it) |
| Duplicate row | Identical records | Both rows with `Num_Leaves = 847` | Same Site, Quadrat, Plant_Number, Species — visible once `Plant_ID` is built in 3.2 |

---

## Segment 1 — Intro + framing

### 1.1 Welcome and logistics

- Housekeeping: breaks, materials access, Zoom chat

---

### 1.2 The data curation problem

Frame the problem before touching any tools.

> "When people think about research they think of analyses, results."
> "Like a marathon — most of the time doing the marathon is spent prepping."
> "Doing your analysis is relatively straight forward. (Stay on track!). Getting the data there? That's where the time goes."
> "Today is about speeding up that process, reliably."

Key points to land:
- Raw data is almost never analysis-ready
- Manual cleaning is slow, error-prone, and hard to audit
- AI doesn't fix this — but it changes the pace and scale at which you can work

> **Instructor:** Avoid promising AI will "solve" data cleaning. It won't. That's the point of the session.

---

### 1.3 Scenario walkthrough

Read or paraphrase:

> "You're a field ecologist studying plant regeneration across two sites in West Auckland."
> "You're asking: how bad are weeds? Specifically, does weeding make a difference?"
> "Site A had weeding plus native planting. Site B had native planting only."
> "The data was digitised from paper forms filled in the rain and typed up six months later. You're dubious of the accuracy, but you need it analysis-ready for the last chapter of your PhD."

Show `plants_site_A` on screen. 

Ask: *"What did you notice? What could cause problems?"* Take 2–3 responses. Don't confirm or correct yet — you'll return to these observations throughout.

---

## Segment 2 — Block 1: Data cleaning

### Key learning objectives

By the end of Block 1, you will be able to:

1. Use a chatbot to clean and transform research data.
2. Identify the key benefits and limits of AI for research tasks.
3. Apply strategies to mitigate those limits.

---

### Research task completion framework

These steps apply to every activity in this workshop. They appear at the start of each exercise — you do not need to memorise them.

| Step | | What you do |
|---|---|---|
| 1 | **Identify task** | Understand what is required |
| 2 | **Identify potential issues** | Look at the data, run a prompt, note what's wrong in Word |
| 3 | **Copilot: fix** | Run a prompt to fix the issue |
| 4 | **Validate outputs** | Check the issue is fixed and the task is complete |
| 5 | **Log it in Word** | Note the prompt and what changed |
| 6 *(if time allows)* | **Apply** | Use the same prompt on other columns of the same type |

> **Instructor:** 2.0 is setup only — the framework begins at 2.1.

---

### 2.0 Setup — Copilot context and data exploration

#### Stage 1 — Data exploration

Before any cleaning, give Copilot context and ask it what it sees. Do not ask it to fix anything yet.

> "We'll give Copilot some context first, then ask it what it sees. Don't ask it to clean anything — just give it the scenario and the data."

> "Drag and Drop data into Copilot by finding the file in the folder, clicking on it, holding down, and dropping it in the the conversation."

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

Run this live. Show the output on screen.

Ask the room:
- *"What did Copilot notice?"*
- *"Does its description match what you'd say about the dataset?"*

Write both in the Word log — what Copilot flagged, and what it didn't.

**Save your prompt:** Copy the context prompt into Word now. Label it: *"Stage 1 — data exploration."*

> **Instructor:** Close this discussion when responses slow — Stage 2 must run before any cleaning begins.

### Stage 2- Data Cleaning Steps

We are starting with Numeric data: outliers and range errors

Numeric columns can have values that are impossible (outside a known bound) or implausible (likely a data-entry error — a misplaced decimal, an extra digit). We identify the problem, fix it, check it, log it, then apply to similar columns.

> **Instructor:** Models the full six-step cycle on `Height_cm`. Each step is labeled below.

#### Steps 1–2: Identify task + potential issues

Ask: *"Before we run anything — what are we actually trying to do here? What's the task?"*

Take one answer. Confirm: checking whether numeric values fall within a valid range, and fixing any that don't.

**Prompt →**

```
Look at `Height_cm` only. Return a table with the mean, min, max, range.
```

```
Look at `Height_cm` only. Return a two-column table with the original column (`Hieght_cm`), and a new column (`Height_cm_cleaned`) with potential outlier values marked with "check".
```

#### Step 3: Copilot fix

**Prompt →**
```
Modify value 340 to 34 and return the two-column table, do not include any notes in the new cell, jsut "34".
```

#### Step 4: Validate outputs

Paste the corrected column into `plants_site_A`.

Validate by looking at first, last and middle rows. As well as changed rows.

#### Step 5: Log it in Word

**Save your prompt:** Copy the fix prompt into Word table:

| column | prompt | summary of changes |


#### Step 6: Apply — complete steps 1–5 for `Num_Leaves` and `Num_Flowers`

**Prompt →**
```
Provide me the same summary stats and outlier checks for `Num_Leaves` and `Num_Flowers`.
```
Do no propose changes to values yet, just "check" for potential outliers.

```
Modify values and return four column table (original column + _cleaned):
`Num_Leaves`: 847 -> 87. 
`Num_Flowers`: -3 -> 3. Keep 22 as 22.
```
---

### 2.3 Categorical data: Species, Site, Location

Categorical columns should have a fixed set of valid values. Problems are misspellings, inconsistent capitalisation and spacing. The fix is: provide a canonical list, ask Copilot to match to it.

#### Steps 1–2: Identify task + potential issues

Ask: *"Before we run anything — what are we trying to do here? What's the task?"*

> "Before prompting Copilot, check the categories yourself. In Excel: Data → Filter. Click the dropdown on `Species` — how many unique values appear?"

#### Step 3: Copilot fix

**Prompt →**
```
Clean categorical columns `Site`, `Location`, `Quadrat`, `Species` and `Flowering`.
I want them to be consistently spelt, all values lower-case, and values separated by underscores "_". 

Return pairs of columns for each variable: the original and a new column `[column_name]_cleaned` for
each. In the cleaned column have the new, corrected value and use "check"" if you are unsure.
```

Show output. Ask: *"What decisions did Copilot make that you didn't specify? Do you agree with them?"*

> **Instructor:** Watch for: `Q11` — Copilot will likely change it to `Q1` without flagging. Is that right? It could be a legitimate quadrat 11, or an entry error. `Loc_l` (letter l) → `Loc_1` is usually caught. `Flowering = 0` may or may not be resolved to `No`. These silent decisions are the teaching point — Copilot doesn't ask, it just decides.

#### Step 3b (optional): Condition

**Prompt →**
```
For `Condition`, return the original column and a new column `Condition_cleaned` with suggested categories representing a consistent five-point scale from poor to excellent.
```

#### Step 5: Log it in Word

**Save your prompts** 

---

### 2.4 Block 1 close

Ask: *"Open your Word log. What's in it?"*

Take a couple of responses. Then:

> "This is a prompt log and a change record. If someone asked what you did to the data — and why — you now have an answer. And you built it as you went, without extra effort."

Summarise on slide or verbally: **"You've just worked through the six-step framework twice — once on numeric columns, once on categorical. Same steps, different data problem."**

> **Instructor:** Note which issues are still `[check]` — unresolved. These carry forward into Block 2. The Word log is also a checklist: what's resolved and what isn't.

---

## Segment 3 — Block 2: Feature extraction

### Overview

Block 2 moves from *fixing what's there* to *creating something new* — deriving variables from existing columns. Same cycle, now applied to create information rather than correct it. This includes calculations across columns, harmonising two columns into one, extracting structured flags from free text, and building composite identifiers.

> **Instructor:** `Aesthetic_quality` and `Condition` were not touched in Block 1. The extraction task in 3.4 works directly on the messy originals — which is realistic. You don't always clean a column before you extract from it.

---

### 3.1 Guided walkthrough — Plant_Age_Months

Run this step by step. This is the most structured exercise in Block 2.

Ask first:

> "Before prompting — what information do you need to calculate how old each plant is at the time of measurement? What columns?"

Confirm: `Date_Planted` and `Date_Measured` — the raw columns. We are not standardising dates first.

> "You might think we should clean the dates before doing arithmetic on them. Here's the problem: Excel aggressively reformats date values the moment they land in a cell — it will decide a format for you, often silently converting something like `20-Sep` to a completely different representation. So we skip standardisation and go straight to calculation. We will see what Copilot does with the inconsistency."

#### Calculate

**Prompt →**
```
Look at `Date_Planted` and `Date_Measured`. These columns contain dates
in inconsistent formats — some DD/MM/YYYY, some D/M/YY, some DD-Mon
with no year, and possibly some US-format (M/D/YY).

Without standardising the formats first, calculate the age of each plant
in whole months at the time of measurement. Add this as a new column
called `Plant_Age_Months`. Return the these three columns as a table.
Flag any row where you cannot confidently interpret the dates with "check".
```

Show output. Point to the new column and to any `[check]` flags.

> **Instructor:** This is where the limits surface naturally. Discuss as they come up:
> - `DD-Mon` with no year (e.g. `20-Sep`, `21-Mar`) — what year does Copilot assume? Is it right?
> - Ambiguous US/NZ format (`8/1/24` — August 1 or January 8?) — did Copilot flag it, or silently pick one?
> - Rows with blank `Date_Measured` — these will be blank or `[check]` in `Plant_Age_Months`
> These are not Copilot failures — they are genuine ambiguities in the data that no tool can resolve without more information.

#### Verify

Paste into the spreadsheet. Download. Check:

1. **Row count** — still 30 rows?
2. Column is right-aligned — numbers, not text like "6 months"
3. **Spot-check** 3 rows: start, middle, end of a sort by `Plant_Age_Months` — verify the arithmetic by hand using the original date values
4. Any negative values? A plant cannot have negative age — this flags a date that Copilot interpreted in the wrong order

Paste `Plant_Age_Months` into `plants_site_A_cleaned`.

**Save your prompt**

---

### 3.2 Independent practice — Plant_ID

Participants run this themselves.

> "Your task: create a `Plant_ID` column as a composite of Site, Location, Quadrat and species initials"

Give participants time to try. Then facilitate a share-back: what prompt did they write? What did Copilot do with it?

> **Instructor:** Common issue: Copilot may use the messy original columns instead of the cleaned versions. Prompt participants to specify `cleaned_Site`, `cleaned_Quadrat`, `cleaned_Species`. Share the prompt below as a hint if people are stuck.

**Prompt →** *(share as a hint if needed)*
```
Using `Site_cleaned`, `Location_cleaned`, `Quadrat_cleaned`, and `Species_cleaned`,
create a new column `Plant_ID`. Format example: Sa_L1_Q1_LS (Site = A, Location = 1, quadrat = 1, Leptospermum scoparium)
Return the only the new column.
```

Paste `Plant_ID` into `plants_site_A_cleaned`. Check in Excel: sort by `Plant_ID`. Are all values unique? A `Plant_ID` appearing twice means there is a duplicate row or a data problem.

**Save your prompt:** Copy your Plant_ID prompt into Word (whatever version worked for you).

---

### 3.3 Independent practice — Notes: what does "summarise" mean?

A different kind of column: free text. Not a format error, not a range problem — unstructured language. This exercise surfaces a core prompting principle.

**Prompt →**
```
I am about to analyse 'Age_Months`, `Height_cm`, and `Condition` for these
plants. For each row, I want to know whether the `Notes` field give any reason this row's
measurements might be unreliable or need special handling.

Return two new columns: `Data_Quality_Flag`: Y or N; and `Data_Quality_Notes`: a short reason for any Y.
```

Paste `Data_Quality_Flag` into `plants_site_A_cleaned`.

**Save your prompt:** Copy the research-framed Data_Quality_Flag prompt into Word. This is the one worth keeping — the generic summary prompt is an example of what not to use.

**Prompt →**
```
Provide me a row-by-row thematic analysis of the `Notes` field, limit your analysis of each comment into on of three, short distinct themes from the perspective of a botanist that you will decide. Include the analysis as column `Notes_themes`.
```

---

### 3.5 Apply to Site B — and find out if your log is good enough

> "You have spent the session building a prompt log — every prompt that worked, with a one-line note on what it changed. Now we put it to use. Site B needs the same treatment as Site A."

> **Instructor:** Distribute `plants_site_B.csv` now (or confirm participants have it). Give them time to work through their Word log independently. Fast participants will have a clear log and move quickly. Slow participants can ask Copilot to repeat steps or work in pairs. Watch for participants whose logs are incomplete — steps they did verbally but didn't write down. This is the payoff for the Word log framing.

> "Open `plants_site_B.csv` in Excel. Open a fresh Copilot conversation and paste in your Stage 1 and Stage 2 prompts from Word to restore context. Then work through your saved prompts — apply each one to Site B."

> **Instructor:** 

Log example:

> "Uploaded is measurements from site_B that I want cleaned, transformed to be appended to site_A's cleaned data. 

> What I want you to do is to complete all of the same steps you completed for site_A. Return a table of all original columns (even those not modified) and `[column_name]_cleaned' for any new, cleaned ones.

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

Save as `plants_combined.csv`.

> **Instructor:** Reference the analysis extension appendix for what to do with this dataset next.

---

## Segment 4 — Apply to more realistic data set

### 4.1 What happens when you scale up

> "You have just cleaned 30 rows. Let's see what happens when we throw something bigger at Copilot."

Open a new Copilot conversation. Paste `plants_sites_AB_large.csv` (1,520 rows, mixed Sites A and B).

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

> **Instructor:** Copilot will likely accept the data and return something. That is the surprising part — it doesn't just refuse. Show the output. Then zoom in.

Point to the `cleaned_` columns. Ask: *"Did it do what we asked?"*

Show the failure pattern on screen — something like:

| Original | Copilot's "cleaned" version |
|---|---|
| `Loc-1` | `loc-1` |
| `LOC1` | `loc1` |
| `site_b` | `site_b` |
| `SITE_B` | `site_b` |

> "It applied a mechanical transformation — mostly lowercasing — rather than actually standardising to canonical values. `Loc-1` became `loc-1` instead of `Loc_1`. It did *something* that looked like cleaning, without doing what we actually needed."

Name the failure mode: **plausible but wrong, at scale**. This is harder to catch than an outright refusal, because the output looks reasonable at a glance.

> **Instructor:** This moment is the pivot of the wrap-up. The failure isn't a context window crash — it's a quality problem. Copilot made shallow pattern-matching decisions across 1,500 rows that none of us would have time to check manually. That is exactly when you need a different approach.

---

### 4.2 Debrief — what else let you down today?

Ask: *"What else did you notice today where Copilot fell short?"*

Collect responses. You should hear:
- Outputs differed between participants or between runs
- Silent decisions — it changed something without flagging it
- Judgment calls made without being asked (Condition mapping, ambiguous dates)
- The Word log was manual effort — no automatic record of what Copilot actually did

> "None of these are bugs. They are properties of how chatbot AI works. And they point to exactly when you would want something different."

---

### 4.3 Slide: Three exits from the Copilot wall

Reveal the **2×2 decision matrix** progressively as you describe each exit.

> **Instructor:** The matrix axes are **technical savvy** (x) and **project scale / complexity** (y). Reveal one box at a time as described below.

---

**Reveal: Green box** (low tech × low complexity)

> "You spent today in the green box. Thirty rows, one-off task, Copilot did most of it. That is the right tool for that job. Take note."

---

**Reveal: Red box** (low tech × high complexity)

> "The 1,520-row demo just put us here. More data than Copilot can clean reliably, and you do not have the technical skills to write a cleaning script from scratch. What do you do?"

> "You get help. CeR, a collaborator, or a service built for this. That is not a failure — that is knowing when to ask."

---

**Exit 1 — Get Copilot to write the code** *(reveal: Orange box, high tech × low complexity)*

> "But what if you have some coding confidence? There is a middle path."

**Prompt →**
```
Write R code to clean this dataset. Standardise these columns:
- Site: canonical value is "Site_A" or "Site_B"
- Location: canonical values are "Loc_1", "Loc_2", "Loc_3", "Loc_4"
- Quadrat: canonical values are "Q1", "Q2", "Q3", "Q4", "Q5", "Q6"
- Flowering: canonical values are "Yes" or "No"
- Species: [paste canonical species list]

Use fuzzy matching where appropriate. Output a cleaned CSV with
cleaned_ columns alongside originals.
```

> "Copilot will generate code. It probably won't run perfectly first time — expect roughly 50% to work out of the box, with troubleshootable errors for the rest. But the errors are readable, and fixing them is doable even without deep programming experience."

> **Instructor:** This is the orange box — higher technical skill applied to a task that Copilot-as-chatbot can't do reliably. The code is reproducible, documentable, and re-runnable. The barrier is lower than most participants expect. If anyone has tried this and gotten code that mostly worked, invite them to share.

---

**Exit 2 — Local LLM in Colab** *(reveal: Blue box, high tech × high complexity)*

> "Exit 2 is what this notebook does."

Show pre-run Colab output: `plants_sites_AB_large.csv` processed by a local LLM — cleaned columns, JSON-structured output, uncertainty flags. Do not run live.

> "A local model running in Colab: no context window limit, fully deterministic (`do_sample=False` — same output every run), and your code is the audit trail. Every decision is documented. This is the blue box — more setup, more power, right tool for large or repeatable pipelines."

> **Instructor:** The key contrast with Exit 1: the Colab approach uses an LLM to do the interpretation (not just rule-based fuzzy matching). It handles the ambiguous cases — the ones code struggles with — while still being deterministic and scalable. That is what makes it worth the extra setup for complex data.

---

**Full matrix reveal**

Show the complete 2×2:

| | **Low complexity** | **High complexity** |
|---|---|---|
| **Low technical savvy** | **Green** — Copilot works. You were here today. | **Red** — Get help: CeR, collaborator, or a purpose-built tool. |
| **High technical savvy** | **Orange** — Copilot-generated code works, but it is overkill for simple tasks. | **Blue** — Programmatic LLM. Reproducible, scalable, auditable. |

> "Where does your own research sit? Take a moment to think about the messiest dataset you are currently working with. Which box is it in?"

---

### 4.4 Slide: Considerations

Tell participants: *"Screenshot this."*

**Using AI with research data:**

- **Sensitive data:** Do not paste patient records, student data, or identifiable participant data into Copilot or any public chatbot. Check your institution's guidelines before using any AI tool with research data.
- **Validation:** You are responsible for the outputs. Copilot does not know when it is wrong. Build checking into your workflow — not as an afterthought.
- **Documentation:** Record the prompts you used, the tool, and the date. "I used AI" is not a sufficient methods statement.
- **Attribution:** Some journals and thesis guidelines require disclosure of AI tool use. Check before you submit.

---

### 4.5 Resources + close

- Link to Colab notebook (self-directed follow-up)
- Link to workshop materials and all datasets
- CeR contact / drop-in hours if applicable
- **Analysis extension** — appendix below: questions, graphs, and statistical tests to try with `plants_combined.csv`

> "You have done real data curation work today. The messy data you started with is genuinely the kind of thing that sits in a folder for six months. You now have a method, a tool, an analysis-ready dataset, and a map for what to do when the task gets bigger."

Questions.

---

## Appendix: Analysis extension exercise

**For participants who want to keep going — or as a self-directed follow-up.**

Your combined dataset (`plants_combined.csv`) is analysis-ready. The grouping variable is `Site` (Site_A = weeding + native planting; Site_B = native planting only). The research question is: *does weeding make a difference?*

This extension uses the same principles from the workshop — noting your steps, saving new outputs, and applying the same logic each time you add a new variable or test.

---

### Variables available

| Column | Type | Notes |
|---|---|---|
| `Site` | Categorical (2 levels) | Your grouping variable |
| `Species` | Categorical (6 levels) | Cleaned in Block 1 |
| `Height_cm` | Numeric (continuous) | Cleaned in Block 1 |
| `Num_Leaves` | Numeric (continuous) | Cleaned in Block 1 |
| `Plant_Age_Months` | Numeric (derived) | From Block 2 |
| `Data_Quality_Flag` | Categorical (Y/N) | From Block 2 |

---

### Questions to work through

**Start here — get to know your data:**
- How many plants are in Site A vs Site B? Are the sample sizes roughly equal?
- Which species appear in each site? Are they the same, or do the sites differ in species composition?
- How many rows have `Data_Quality_Flag = Y`? Is this evenly distributed across sites, or concentrated in one? If Site B has more flagged rows, that could bias any comparison.

**Then ask these comparison questions:**
- Is the average `Height_cm` different between Site A and Site B?
- Is the average `Plant_Health_Score` different between sites?
- Is there a difference in `Percentage_Cover`?
- After accounting for `Plant_Age_Months`, is there still a site effect on height? Plants planted earlier will naturally be taller — controlling for age matters.
- Does the site effect differ by species? Some species might respond more to weeding than others.

---

### Graphs to produce

Run these in order — each one informs whether the next statistical test is appropriate.

**1. Histogram — test the normality assumption**
For each numeric variable you plan to compare (e.g. `Height_cm`, `Plant_Health_Score`): does the distribution look approximately normal? A strongly skewed histogram means a standard t-test may not be appropriate.

**Prompt →**
```
Generate a histogram of `Height_cm` for each Site using Python
and matplotlib. Show them side by side.
```

**2. Boxplot — visual comparison between sites**
Side-by-side boxplots of `Height_cm` by `Site`. This shows median, spread, and outliers at a glance — before running any statistics.

**Prompt →**
```
Generate a boxplot comparing `Height_cm` between Site_A and Site_B.
```

**3. Scatter plot — check for the age confound**
Plot `Height_cm` (y-axis) vs `Plant_Age_Months` (x-axis), coloured by `Site`. If Site A points are systematically older, height differences between sites might be explained by age, not weeding.

**Prompt →**
```
Generate a scatter plot of `Plant_Age_Months` vs `Height_cm`,
coloured by `Site`.
```

**4. Bar chart — species counts by site**
Are the same species present in both sites and in similar proportions? A big imbalance in species composition could confound any site-level comparison.

---

### Statistical tests

**Before running any test, ask yourself:**
- Is my outcome variable approximately normally distributed? (Check histogram)
- Are the two groups roughly equal in size?
- Are the observations independent? Plants in the same quadrat may not be — they share soil, canopy, disturbance history
- Is my sample large enough to detect a meaningful difference? With ~30 plants per site, power is limited

| Question | Test | Check first |
|---|---|---|
| Is mean `Height_cm` different between sites? | Independent samples t-test | Normality (histogram), equal variances |
| Same question, if normality fails | Mann-Whitney U test | No normality required |
| Does site effect differ by species? | Two-way ANOVA | Normality, roughly equal group sizes |
| Does height relate to age, controlling for site? | Linear regression (`Height ~ Age + Site`) | Linearity, residual normality |

**Prompts to try:**
```
Given these two groups (Site_A and Site_B) and this variable (Height_cm),
which statistical test should I use to compare them? State any assumptions
I need to check first.
```
```
Generate Python code to run an independent samples t-test comparing
`Height_cm` between Site_A and Site_B in this dataset.
```
```
Run a linear regression with `Height_cm` as the outcome and
`Plant_Age_Months` and `Site` as predictors. Interpret the coefficient
for `Site`.
```

---

### The harder validation line

Statistical assumptions are validation steps — just further downstream. The same principle applies: check before you trust the output.

- **Normality:** histogram looks roughly bell-shaped? Run a Shapiro-Wilk test to be more rigorous
- **Equal variance:** boxplots have similar spread? Run a Levene's test
- **Independence:** are plants clustered by quadrat? If so, standard tests underestimate uncertainty — consider a mixed model
- **Data quality:** exclude `Data_Quality_Flag = Y` rows from analysis, or run the analysis both with and without them and report whether results change

**Prompt →**
```
What does it mean for a t-test if the assumption of normality is violated?
What should I do instead?
```

---

### Applying the same workshop principles

- **Note your steps:** same Word log approach — what tests you ran, what decisions you made (e.g. why you excluded flagged rows, why you chose Mann-Whitney over t-test)
- **Save new outputs:** save plots, save the summary statistics table, save the combined dataset with any additional columns added during analysis
- **Apply to new data:** if a third site or a follow-up visit comes in, the same pipeline applies — and now you know what that pipeline looks like in practice
