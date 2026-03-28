<div align="center">

# 🏏 IPL Data Analytics Dashboard
### *Turning 150,000+ Cricket Deliveries into Powerful Insights*

![Power BI](https://img.shields.io/badge/Power%20BI-F2C811?style=for-the-badge&logo=powerbi&logoColor=black)
![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)
![CSV](https://img.shields.io/badge/Data-CSV-green?style=for-the-badge&logo=files&logoColor=white)
![DAX](https://img.shields.io/badge/DAX-Measures-orange?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen?style=for-the-badge)

<br/>

> **"Cricket is not just a sport — it's 150,000 data points waiting to tell a story."**

</div>

---

## 📌 Table of Contents

- [🎯 Project Overview](#-project-overview)
- [📂 Dataset Description](#-dataset-description)
- [🗂️ Project Structure](#️-project-structure)
- [🔗 Data Model & Relationships](#-data-model--relationships)
- [📊 Dashboard Pages](#-dashboard-pages)
- [🧮 DAX Measures Used](#-dax-measures-used)
- [💡 Key Insights](#-key-insights)
- [⚙️ How to Use This Project](#️-how-to-use-this-project)
- [🛠️ Tech Stack](#️-tech-stack)
- [🚀 Future Enhancements](#-future-enhancements)
- [👤 Author](#-author)

---

## 🎯 Project Overview

This end-to-end **Data Analytics project** explores the Indian Premier League (IPL) cricket dataset using **Power BI**. The goal is to extract meaningful patterns and insights from match-level and ball-by-ball delivery data — covering team performance, player statistics, venue trends, and toss impact.

```
📦 Raw CSV Data
    ↓
🧹 Data Cleaning & Transformation
    ↓
🔗 Data Modeling (Relationships + DAX)
    ↓
📊 Interactive Power BI Dashboard (3 Pages)
    ↓
💡 Insights & Storytelling
```

---

## 📂 Dataset Description

### 📄 File 1 — `cleaned_matches.csv`
> Match-level data with 636 rows and 18 columns

| Column | Description |
|--------|-------------|
| `id` | Unique match identifier |
| `season` | IPL season year |
| `city` | City where match was played |
| `date` | Match date |
| `team1` / `team2` | Competing teams |
| `toss_winner` | Team that won the toss |
| `toss_decision` | Bat or Field |
| `winner` | Match winner |
| `win_by_runs` | Margin of victory (runs) |
| `win_by_wickets` | Margin of victory (wickets) |
| `player_of_match` | Best performer of the match |
| `venue` | Stadium name |

---

### 📄 File 2 — `cleaned_deliveries.csv`
> Ball-by-ball delivery data with ~150,000 rows and 21 columns

| Column | Description |
|--------|-------------|
| `match_id` | Links to matches table |
| `inning` | 1st or 2nd innings |
| `batting_team` / `bowling_team` | Teams involved |
| `over` / `ball` | Ball position in match |
| `batsman` / `bowler` | Player names |
| `batsman_runs` | Runs scored off the bat |
| `total_runs` | Total runs on delivery |
| `player_dismissed` | Wicket (if any) |
| `dismissal_kind` | Type of dismissal |
| `wide_runs` / `noball_runs` | Extras |

---

## 🗂️ Project Structure

```
📁 IPL-PowerBI-Dashboard/
│
├── 📄 cleaned_matches.csv        ← Match-level dataset (636 matches)
├── 📄 cleaned_deliveries.csv     ← Ball-by-ball dataset (~150K rows)
├── 📊 IPL_Dashboard.pbix         ← Power BI Dashboard file
└── 📘 README.md                  ← You are here!
```

---

## 🔗 Data Model & Relationships

```
cleaned_matches               cleaned_deliveries
─────────────────             ──────────────────────
id (PK) ──────────────────►  match_id (FK)
season                        inning
city                          batting_team
team1 / team2                 bowling_team
winner                        batsman / bowler
toss_winner                   batsman_runs
player_of_match               total_runs
venue                         player_dismissed
...                           dismissal_kind
                              ...

Relationship Type: One-to-Many (1 match → many deliveries)
```

---

## 📊 Dashboard Pages

### 📋 Page 1 — Season Overview
> *High-level KPIs and seasonal trends*

- 🔢 **KPI Cards** — Total Matches, Total Runs, Total Wickets, Total Sixes
- 📈 **Line Chart** — Runs scored per season (trend over years)
- 🏅 **Bar Chart** — Most match wins by team across all seasons
- 🍩 **Donut Chart** — Win by Runs vs Win by Wickets split
- 🎛️ **Slicer** — Filter by Season

---

### 🏏 Page 2 — Player Analytics
> *Deep dive into batting and bowling performances*

- 🏆 **Top 10 Run Scorers** — Horizontal bar chart by batsman
- 🎳 **Top 10 Wicket Takers** — Horizontal bar chart by bowler
- 💥 **Most Sixes & Fours** — Clustered bar comparison
- 🎖️ **Player of the Match Leaders** — Who wins it the most?
- 🥧 **Dismissal Types** — Pie chart on `dismissal_kind`
- 🎛️ **Slicer** — Filter by Team & Season

---

### 🧠 Page 3 — Strategic Insights
> *Toss decisions, venue analysis, and winning patterns*

- 🪙 **Toss Impact** — Does winning the toss help win the match?
- 🏟️ **Matches by City** — Bar chart (map alternative)
- ⚡ **Toss Decision Breakdown** — Bat vs Field frequency
- 📊 **Win Margin Analysis** — Avg runs/wickets margin by team
- 🎛️ **Slicer** — Filter by City & Toss Decision

---

## 🧮 DAX Measures Used

```dax
-- Total Matches
Total Matches = COUNTROWS(cleaned_matches)

-- Total Runs Scored
Total Runs Scored = SUM(cleaned_deliveries[total_runs])

-- Total Wickets
Total Wickets =
    COUNTROWS(
        FILTER(cleaned_deliveries, cleaned_deliveries[player_dismissed] <> "None")
    )

-- Total Sixes
Total Sixes =
    COUNTROWS(
        FILTER(cleaned_deliveries, cleaned_deliveries[batsman_runs] = 6)
    )

-- Total Fours
Total Fours =
    COUNTROWS(
        FILTER(cleaned_deliveries, cleaned_deliveries[batsman_runs] = 4)
    )

-- Toss Win Status (Calculated Column)
Toss Win Status =
    IF(cleaned_matches[toss_winner] = cleaned_matches[winner], "Won Match", "Lost Match")

-- Bowler Average
Bowler Average =
    DIVIDE([Total Runs Scored], [Total Wickets])

-- Batting Strike Rate
Batting Strike Rate =
    DIVIDE([Total Runs Scored], COUNTROWS(cleaned_deliveries)) * 100

-- Economy Rate
Economy Rate =
    DIVIDE([Total Runs Scored], DIVIDE(COUNTROWS(cleaned_deliveries), 6))
```

---

## 💡 Key Insights

| # | Insight | Finding |
|---|---------|---------|
| 🥇 | **Most Successful Team** | Mumbai Indians lead in total wins |
| 🪙 | **Toss vs Match Win** | Winning toss ≠ winning match — less correlated than expected |
| 🏟️ | **Highest Scoring Venue** | Wankhede Stadium (Mumbai) consistently high-scoring |
| 🎳 | **Bowling Dominance** | Field-first strategy preferred in most seasons |
| 🏏 | **Top Batsman** | DA Warner and V Kohli among most consistent scorers |
| 💥 | **Six Hitters** | MS Dhoni and AB de Villiers dominate boundary count |
| 📅 | **Best Season for Runs** | Later IPL seasons show significantly higher scoring rates |

---

## ⚙️ How to Use This Project

**Step 1** — Clone or download this repository
```bash
git clone https://github.com/yourusername/ipl-powerbi-dashboard.git
```

**Step 2** — Open Power BI Desktop
```
Download from: https://powerbi.microsoft.com/desktop
```

**Step 3** — Load the CSV files
```
Home → Get Data → Text/CSV → Load both CSV files
```

**Step 4** — Set up the relationship
```
Model View → Drag matches[id] → deliveries[match_id]
Cardinality: One-to-Many
```

**Step 5** — Add DAX measures from the section above and build visuals

**Step 6** — Enable Map Visuals *(if needed)*
```
File → Options & Settings → Options → Security → Enable Map Visuals
```

---

## 🛠️ Tech Stack

| Tool | Purpose |
|------|---------|
| ![Power BI](https://img.shields.io/badge/Power%20BI-F2C811?style=flat&logo=powerbi&logoColor=black) | Dashboard & Visualization |
| ![DAX](https://img.shields.io/badge/DAX-Measures-orange?style=flat) | Custom Calculations & KPIs |
| ![CSV](https://img.shields.io/badge/CSV-Data%20Source-green?style=flat) | Raw Data Input |
| ![Excel](https://img.shields.io/badge/Excel-Data%20Prep-217346?style=flat&logo=microsoft-excel&logoColor=white) | Pre-processing & Cleaning |

---

## 🚀 Future Enhancements

- [ ] 🤖 Add **predictive model** for match winner using ML
- [ ] 🌐 Publish to **Power BI Service** for online sharing
- [ ] 📱 Create **mobile-optimized** dashboard layout
- [ ] 📅 Add **2024 & 2025 IPL season data**
- [ ] 🔄 Connect to **live data source** via API
- [ ] 🎨 Add custom **IPL team color themes** per visual

---

## 👤 Author

<div align="center">

**Soumik**
*Data Analytics Enthusiast*

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-0077B5?style=for-the-badge&logo=linkedin&logoColor=white)](https://linkedin.com/in/soumik-belel)
[![GitHub](https://img.shields.io/badge/GitHub-Follow-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/soumikbelel3-commits)

*If you found this project helpful, please ⭐ star the repository!*

</div>

---

<div align="center">

**#DataAnalytics #PowerBI #IPL #Cricket #DAX #DataVisualization**

*Made with ❤️ and a lot of cricket passion 🏏*

</div>
