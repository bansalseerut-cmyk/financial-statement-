# Financial Statement Dashboard

*A learning project — building a Power BI dashboard on financial data, and exploring how the data pipeline behind it could be automated.*

---

## 🧭 Overview

This project is based on a real-world style case study: a financial analysis team receives around **25 survey files every day** from different locations, and has to combine, clean, and turn them into a client-ready dashboard the same day. The brief asked how this slow, manual process could be made faster and more reliable.

I used this as a chance to learn two things at once — how a data pipeline like this *could* be automated using Python and the Google Drive API, and how to actually build the analytical dashboard in Power BI once the data is ready.

> **Note:** I didn't have access to a paid API setup or a laptop powerful enough to process all 25 files together, so I didn't run the full automation end-to-end. What I *did* do is write and test the automation script so I understand how it works, and build the complete dashboard using a single sample file. The goal here was learning the concept properly, not faking a production system.

---

## ⚡ The Challenge (Case Study Brief)

The manual process this project is based on had a few recurring problems:

- **Slow turnaround** — downloading, combining, and cleaning 25 files by hand took hours every day.
- **Extra cost** — two additional staff were hired just to keep up, adding $12,000/month.
- **Manual errors** — combining and cleaning data by hand led to frequent mistakes.
- **Workload spillover** — the daily crunch started affecting other work too.

---

## 🎯 What I Actually Did

- Wrote a **Python script** that connects to Google Drive using a service account, lists every file in a shared folder, downloads each one, and combines them into a single dataset — this is the automation piece, and I understand and can run it, but I tested it at a smaller scale rather than the full 25-file load.
- Took **one sample file** (6,000 rows × 25 columns) and brought it into Power BI to work with something my laptop could actually handle.
- Did the **full manual data cleaning** process myself inside Power BI.
- Built a **two-page dashboard** answering the exact questions from the case study brief.

So the honest summary is: the automation is written and understood, the dashboard is fully built and cleaned by hand, and the two haven't been merged into one live pipeline yet — that's the next step if I get access to better hardware or a paid processing setup.

---

## 🔩 Tools I Used

| Tool | What I used it for |
|---|---|
| **Python** | Writing the Google Drive automation script |
| **Google Drive API** | Understanding how to securely pull files from a shared folder |
| **Power BI** | Cleaning the data and building the dashboard |

---

## 🗃️ About the Dataset

- Data comes from **25 files**, one per survey agent, each with **6,000 rows and 25 columns**.
- The script is written to handle and combine all 25 — but for the actual dashboard build, I worked with **one file only**, since combining and processing all of them needs more computing power than my current setup has.

---

## 📜 The Automation Script

The script (`load_data_from_drive.py`) does the following:

1. Authenticates with Google Drive using a service account (read-only access).
2. Lists every file inside a given Drive folder.
3. Checks each file's type (Google Sheet, CSV, or Excel) and downloads it the right way.
4. Reads each file into a `pandas` DataFrame.
5. Combines everything into one dataset with `pandas.concat`.

I wrote and tested this to understand the logic end-to-end — it works, but I ran it on a small scale rather than the full daily load.

---

## 🧽 Data Cleaning (done manually in Power BI)

- Fixed incorrect data types
- Renamed unclear column names
- Removed extra/unnecessary columns
- Replaced blank values with **"No Information"**
- Removed duplicate rows
- Standardized inconsistent category labels
- Created new calculated columns, including:
  - **Age Group** (custom age bands, see below)
  - **Numeric Credit Score** (Good = 3, Standard = 2, Bad = 0 — used for LTV calculation)
  - **LTV Score** (calculated per age group)

**Age Group Rule:**

| Age Range | Group |
|---|---|
| 14–19 | Teen |
| 19–25 | Young Adult |
| 25–35 | Old Adult |
| 35–45 | Old1 |
| 45+ | Old2 |

---

## 📑 Questions the Dashboard Needed to Answer

1. Show key metrics: Average Annual Income, Average Monthly Balance, Average Delay in Payment, Average Credit Utilization.
2. Study how age relates to changes in credit limit, and how payment behavior differs across credit mix categories.
3. Show the age distribution of the customer base.
4. Break down the number of customers per age group by credit score category.
5. Look at how often different payment behaviors show up within each credit mix category.
6. Identify which age groups look like good potential customers for loans.
7. Narrow that down further — age groups where the average number of credit inquiries is above 7.5.
8. Calculate an **LTV Score** per age group and match it to a promotion:
   - LTV > 80,000 → 30% off online purchases + home loan at 4% interest
   - LTV 60,000–80,000 → 15% off online purchases + ₹10,000 gift hampers
   - LTV 50,000–60,000 → Any loan at 5% interest
9. Find the average number of loans and credit cards held, broken down by age.
10. Show the count of each loan type disbursed so far.

---

## 🖼️ The Dashboard

### Page 1 — Customer & Credit Overview
- KPI cards: Average Annual Income, Average Monthly Balance, Average Delay from Due Date, Average Credit Utilization Ratio
- Credit mix by age group (bar charts)
- Age vs. change in credit limit (line chart)
- Age distribution across the customer base (column chart)
- Payment behavior by credit mix category (clustered column chart)

### Page 2 — Loans & Lifetime Value
- Average number of loans and credit cards by age (line chart)
- Average credit inquiries by age group, used to flag potential customers (bar chart)
- LTV Score and matched promotion per age group (table)
- Potential customer flags by age (table)
- Loan type distribution across all customers (table)

---

## ✨ What the Data Showed

- Average annual income across customers sits around **164.86K**, with an average monthly balance of **400.51**.
- Customers are, on average, **21.27 days** past their due date, with a **32.21%** average credit utilization ratio.
- Age groups with average credit inquiries **above 7.5** stood out as the strongest potential customers for loan outreach.
- LTV scores differed clearly by age group, which made it possible to assign different promotions to different groups instead of a one-size-fits-all offer.
- Loan types were fairly evenly spread across Student, Personal, Payday, Mortgage, and Home Equity loans — useful for deciding where to focus future offers.

---

## 🧠 What I Learned

This project taught me more about the *gap* between writing an automation script and actually deploying one — connecting to an API is one thing, but running it reliably against a real daily workload takes resources I don't have access to yet. It also gave me solid, hands-on practice in structuring a Power BI dashboard around specific business questions rather than just charting data for its own sake.

---

## 🏗️ How to Try It Yourself

1. Clone this repository.
2. Install the required Python packages:
   ```bash
   pip install google-auth google-auth-oauthlib google-auth-httplib2 google-api-python-client pandas requests
   ```
3. Create a Google Cloud service account with **read-only Drive access** and download its credentials JSON file.
4. In the script, update:
   - `SERVICE_ACCOUNT_FILE` — path to your credentials file
   - `FOLDER_ID` — the ID of your Google Drive folder
5. Run the script:
   ```bash
   python load_data_from_drive.py
   ```
6. Open `Financial_Statement_Dashboard.pbix` in Power BI Desktop and refresh the data source.

---

## 🗂️ Repository Structure

```
financial-statement-dashboard/
├── script/
│   └── load_data_from_drive.py
├── dataset/
│   └── sample_data.csv
├── dashboard/
│   └── Financial_Statement_Dashboard.pbix
└── README.md
```

---

## ✍️ About Me

Built by **Seerat** — as a hands-on way to learn data automation concepts and Power BI dashboarding together.
