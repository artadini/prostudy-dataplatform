# Analytics Engineer Application Task (m/f/d) – Prostudy

Great to have you on board! This task gives you a realistic impression of what you would be working on with us, and gives us an impression of how you approach data problems. It is **not** about perfection, but about understandable decisions, clean craftsmanship, and clear communication.

**Timeframe:** Plan for about 4–6 hours. You have **7 calendar days** from receipt to submit. If you realize you need significantly longer, prioritize and document what you left out – that is perfectly fine.

***

## 1. The Scenario

Prostudy trains people in data-related fields (Data Analyst, Data Scientist, Data Engineer, …) – full-time, part-time, and alongside work, often funded through education vouchers. Our data is distributed across:

* the **CRM** (participants, bookings/enrollments, status),
* the **learning platform (LXP)** (learning progress per module, feedback surveys),
* an **Excel list** that our coaches maintain manually on a weekly basis (who is supporting whom, traffic-light status, last contact).

The *Training Operations* leadership team wants weekly KPI reporting: How many participants are active, who is not making progress, how satisfied are graduates, and how heavily utilized are the coaches? Today, these figures are laboriously compiled by hand in Excel – and different numbers come out every week.

**Your role:** Build the first version of a data pipeline that brings these sources together, cleans them, transforms them into an analysis-friendly model, and answers the most important KPIs.

> The data is synthetic but, like real systems, contains inconsistencies. Part of the task is to find them and deal with them deliberately.

***

## 2. What You Get

| What | Where | Notes |
|---|---|---|
| Local API with CRM and LXP data | `api/` | Start: `python api/server.py` (Python standard library only). Docs at <http://127.0.0.1:8000/docs>. Details in [`api/README.md`](api/README.md). |
| Coach support list | `data/coach_betreuungsliste.xlsx` | Manually maintained Excel, as of 01.09.2026 |

API credentials: username `prostudy`, password `learn-data-2026`.

The data snapshot is **01.09.2026**. All analyses refer to the **reference date of 31.08.2026**.

***

## 3. Your Task (Mandatory Part)

### 3.1 Extract – Retrieve Data from the Sources

* Retrieve all relevant data from the API (trainings including modules, participants, enrollments, learning-progress events, feedback) and from the Excel list.
* The API is paginated, authenticated, and not one hundred percent reliable – your client should handle this.
* Think about what a **daily run** would look like: How would you load only new or changed records? Implement this as far as time allows, or at least describe concretely how you would implement it.

### 3.2 Transform – Clean and Model

* Clean and normalize the data (data types, time zones, spelling, duplicates, dangling references, …). Document **what** you found and **how** you decided.
* Join the coach list with the system data. There is no shared key – you need to come up with an approach and state its limitations.
* Transform the data into an **analysis-friendly model** (e.g., a star schema with fact and dimension tables) from which the questions in section 3.4 can be answered easily. A brief ERD or sketch is sufficient for documentation.

### 3.3 Load – Write to a Data Sink

* Load the model into a **locally runnable** database of your choice (e.g., DuckDB, SQLite, PostgreSQL via Docker). Briefly justify your choice.
* The pipeline must be **reproducible**: With one command (e.g., `make run`, `python pipeline.py`, `docker compose up`), the populated target database is created from the sources. A second run must not create duplicates.

### 3.4 Analysis – Answer the Business Questions

Answer the following questions using SQL (or equivalent) on your model and include the queries and results (table or screenshot). Where a definition is unclear, make a decision and document it.

1. **Active participants:** How many enrollments are active on the reference date – per training?
2. **Completion and dropout rate:** For enrollments whose planned end date is before the reference date: What proportion is completed and what proportion dropped out – per training and funding type (`funding_type`)?
3. **Learning progress vs. time progress:** For active enrollments: How far along are participants in the curriculum (proportion of completed modules) compared with how much of the planned training duration has already elapsed? What proportion is at least 15 percentage points behind schedule (“behind schedule”)?
4. **NPS:** What is the Net Promoter Score (promoters 9–10 minus detractors 0–6, in percent) from the completion feedback – per training and quarter?
5. **Coach utilization:** How many active or paused enrollments is each coach supporting according to the support list, and how many of those are marked **red**? How many enrollments could you not assign to a coach?

***

## 4. Bonus: Dashboard

If you have time and interest: Build a small dashboard on your target database that visualizes the KPIs from 3.4 for the *Training Operations* leadership team. Tool of your choice (Streamlit, Evidence, Metabase, Power BI, Tableau Public, a Jupyter notebook with plots, …). We are less interested in the visuals than in whether the presentation helps an executive make decisions – e.g., filters by training/variant, meaningful timelines, and highlighting areas requiring action.

The bonus is truly optional. We would rather see a clean mandatory part without a dashboard than a dashboard on a shaky pipeline.

***

## 5. Ground Rules

**Tooling:** Your choice. Python is our standard, as is SQL; dbt, DuckDB, pandas/Polars, Airflow/Dagster/Prefect, Docker – all welcome, none required. Choose what you can handle confidently within the time.

**AI support is explicitly allowed and encouraged.** We use ChatGPT, Claude, Copilot & Co. in our daily work and expect you to do the same. Three conditions:

1. You must be able to explain **every line** of your submission and modify it live during the interview.
2. Briefly and honestly describe in your README **what** you used AI for and where it did *not* help you or led you astray.
3. You are responsible for correctness. A plausible-sounding but incorrect KPI is worse than a missing one.

**Language:** German or English, whichever is easier for you. Code and comments are welcome in English.

***

## 6. Submission

A Git repository (link on GitHub/GitLab, public or with access for us) or a ZIP archive by email. It should contain:

* **Code** for the pipeline (Extract, Transform, Load) and the **queries** from 3.4.
* **README** with:
  * Setup and start command – we need to be able to follow it on a fresh machine;
  * a sketch of the architecture and data model (ERD, image, or text);
  * your **assumptions** and definition decisions;
  * a list of **data quality findings** and how you handled each one;
  * your **AI usage** (see above);
  * a section on **“If this were to run in production”**: What would you do differently, what is missing (tests, monitoring, orchestration, data privacy, …)?
* The **results** of the five questions (tables or screenshots).
* Optional: Dashboard (code, link, or screenshots).

Please do **not** include a populated database or loaded raw data in the repo – the pipeline should generate them.

***

## 7. What We Look For

We evaluate transparently based on these criteria – in this weighting:

| Criterion | What we ask ourselves |
|---|---|
| **Pipeline craftsmanship** | Does it run reproducibly? Is the API client robust (auth, pagination, retries)? Is a repeated/incremental run considered? |
| **Data modeling** | Is the target model suitable for the questions, clearly named, and extensible? Are keys and relationships clean? |
| **Handling data quality** | Did you find the inconsistencies? Did you make and document deliberate decisions instead of silently filtering or ignoring? |
| **Analytical correctness** | Are the KPIs correct within your documented definitions? Are the definitions business-sensible? |
| **Communication** | Can we understand the README and code in 15 minutes? Are trade-offs named? |
| **Bonus: Dashboard** | Does the presentation help an executive make decisions? |

Not evaluated: lines of code, framework variety, design polish.

***

## 8. The Follow-up Interview (approx. 60 minutes)

You present your solution for 10–15 minutes (screen sharing, no slides needed). Afterwards, we will discuss your decisions, look at the code together, and change it live in one or two places – for example, a new requirement from the business team or another data problem. We are also interested in how you used AI and where you saw its limitations.

***

## Questions?

If anything is unclear or technically does not work, feel free to contact your recruiting contact at any time. Asking questions is not a negative – quite the opposite.

Enjoy!