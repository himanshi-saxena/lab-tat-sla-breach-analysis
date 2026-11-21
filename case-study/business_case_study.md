# Case Study: Testing Turnaround Time & SLA Breach Analysis

**Author:** Himanshi Saxena  
**Tools:** Excel, SQL Server, Power BI  
**Domain:** Testing laboratory operations (ISO/IEC 17025 environment)

> **Data note:** All data in this project is synthetic (AI-generated) and designed to mirror a real testing-lab workflow. No confidential client or employer data is used.

---

## 1. Executive Summary

A testing laboratory promises clients a result within an agreed time, called the **SLA (Service Level Agreement)**. A late result puts client trust and audit performance at risk.

This project analyses **5,916 completed samples** and **62 open samples** to answer three questions:

1. How often does the lab breach its SLA, and how does that change month to month?
2. At which stage of the workflow (Assign, Queue, Analysis, Review) is time being lost?
3. Which open samples need action today to prevent new breaches?

**Headline result:** the overall SLA breach rate is **25.5%**, so roughly **one in four samples is delivered late**. In the current open backlog, **24 of 62 samples (38.71%) are either overdue or due within 2 days**, which means the next wave of breaches is already forming.

The work was delivered in three phases: an Excel prototype, a SQL Server star schema, and a 3-page Power BI dashboard.

---

## 2. Business Problem

In day-to-day quality work, the question "are we delivering on time?" is usually answered from scattered registers and manual counts. Three gaps stand out:

- There is no single view of breach rate by month, lab section and priority.
- There is no visibility into **which workflow stage** causes the delay.
- There is no early warning for open samples that are close to breaching.

**Objective:** build a reporting pipeline that helps the lab move from *reacting* to breaches to *preventing* them.

---

## 3. Dataset

| Item | Detail |
|---|---|
| Completed samples | 5,916 |
| Open samples | 62 |
| Lab sections | Chemical, Instrumental, Microbiology |
| Priority classes | Normal, Urgent |
| Workflow stages | Assign, Queue, Analysis, Review |
| Key fields | Sample ID, test, client, analyst, re-test flag, received date, SLA days, delay reason |

**Key definitions**

- **TAT (Turnaround Time):** days from sample receipt to result release.
- **SLA breach:** TAT greater than the SLA days allowed for that sample.
- **Breach %:** breached samples divided by completed samples.
- **At risk (open samples):** samples that are already overdue or due within 2 days.

**Data cleaning:** TAT was standardised to days, text typos were corrected, and blank delay reasons were labelled **"Not recorded"** so they stay visible in the analysis instead of silently disappearing.

---

## 4. Approach

### Phase 1: Excel (prototype and first dashboard)

- Cleaned the data and calculated TAT and breach flags.
- Built pivot tables and an **Executive Summary Dashboard** with KPI cards (5,916 completed samples, 25.5% breach, 62 open samples).
- Added charts for monthly breach trend, breach % by lab section, stacked stage days, and delay reasons.
- Added slicers for month, lab section and priority class.

**Why Excel first:** it allowed quick validation of the business logic before investing in a database design.

### Phase 2: SQL Server (single source of truth)

- Loaded raw data into a **staging table**, then produced a cleaned table, `clean_samples`.
- Designed a **star schema**: one fact table connected to dimensions for test, client, analyst and date.
- Created reusable views: `vw_SampleBase`, `vw_FactCompleted` and `vw_OpenRisk`.
- Wrote eight KPI queries (Q1 to Q8) covering breach rate, monthly trend, section and stage analysis, and open-sample risk.

**Why SQL:** the business logic is written once and every report reads the same numbers, so Excel and Power BI cannot disagree.

### Phase 3: Power BI (interactive reporting)

A data model was built on the star schema, with DAX measures for breach %, average stage days and risk. The report has three pages:

**Page 1, Executive Summary:** KPI cards, monthly breach trend and breach % by lab section, so leadership can see the overall position in seconds.

**Page 2, Bottleneck Analysis:** the "why" behind the breaches.
- Stacked bar of average days per stage (Assign, Queue, Analysis, Review) by lab section
- Pareto chart of breached samples by delay reason
- Test-by-month breach % heat map
- Decomposition tree to drill into breached samples
- Tooltip showing the month-over-month change in breach %

**Page 3, Risk Watchlist:** built on open samples. Cards show **62 open, 3 overdue, 21 due within 2 days and 38.71% at risk**. Each sample has a risk score and a risk category, so the team can sort the list and act on the most urgent items first.

---

## 5. Key Findings

1. **One in four samples breaches its SLA.** The overall breach rate is 25.5% across 5,916 completed samples. At this level, lateness is a regular pattern and not a rare exception.
2. **The open backlog already carries risk.** Of 62 open samples, 3 are overdue and 21 are due within 2 days. Together that is 24 samples, or 38.71% of the backlog.
3. **Delay is traceable to specific stages.** Splitting TAT into Assign, Queue, Analysis and Review shows where time is spent, so improvement effort can target one stage instead of the whole process.
4. **Delay reasons are not recorded consistently.** A visible "Not recorded" category appears in the delay-reason analysis. Where the reason is missing, nobody can say *why* a sample was late, which weakens root-cause work.
5. **Breaches vary across sections, priority and tests.** The slicers and heat map make it possible to compare lab sections, Normal versus Urgent samples, and individual tests month by month, instead of relying on one blended average.

---

## 6. Recommendations

1. **Start with the slowest stage.** Use the stage-days chart on the Bottleneck page to identify the stage with the highest average days, then set a target and an owner for that stage first.
2. **Make delay reason mandatory at release.** This closes the "Not recorded" gap and turns every future breach into usable root-cause data.
3. **Run a daily Risk Watchlist review.** Spend ten minutes each morning on overdue and due-in-2-days samples, so late results are prevented and not explained afterwards.
4. **Track breach % monthly by lab section and priority,** with an agreed alert threshold, so deterioration is noticed early.
5. **Review high-breach tests.** Use the heat map to find tests that breach repeatedly and check their capacity, method time and SLA definition.

---

## 7. Limitations

- The data is synthetic, so the project demonstrates **method and process**, not real-world lab performance.
- The analysis shows **association, not proven cause**. A stage with high delay is a strong lead to investigate, not a final verdict.
- An Analyst and Workload page was scoped out of this version and is a natural next phase.
- Delay reasons are incomplete, so root-cause conclusions are limited to the records where a reason was captured.

---

## 8. What I Learned

- Define metrics such as TAT, breach and risk precisely *before* building, to avoid rework.
- A star schema with reusable views keeps every report consistent.
- A good dashboard answers a decision question ("what should we do today?"), not only "what happened?".
- Missing data is itself a finding and should be shown, not hidden.

---

## 9. Repository Contents

```
/excel      Executive Summary dashboard (.xlsx)
/sql        Staging, clean_samples, star schema, views, KPI queries Q1-Q8
/powerbi    Dashboard file (.pbix) and screenshots
/data       Synthetic dataset
README.md   Project overview
CASE_STUDY.md
```

---

## 10. Contact

**Himanshi Saxena**  
[LinkedIn](https://www.linkedin.com/in/himanshi-saxena-b2960a20b) | himanshisaxena.ind@gmail.com
