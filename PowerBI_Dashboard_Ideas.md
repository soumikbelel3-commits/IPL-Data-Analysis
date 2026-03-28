# Complete Power BI Workflow: IPL Data Analysis

This guide provides a comprehensive, step-by-step workflow for building your IPL dashboard. It covers everything from data ingestion to advanced DAX formulas and visualization design.

---

## Phase 1: Data Import & Power Query (Transformation)

**1. Import Data**
* Open Power BI Desktop.
* Go to **Home > Get Data > Text/CSV**.
* Import `matches.csv` and `deliveries.csv`.

**2. Clean Data in Power Query**
* Click **Transform Data** to open the Power Query Editor.

* **For the `matches` table:**
  * **Promote Headers:** Ensure the first row is recognized as column headers.
  * **Replace Nulls:**
    * Right-click `city` column > Replace Values (Null -> "Unknown").
    * Replace nulls in `winner` with "No Result/Tie".
    * Replace nulls in `player_of_match` with "No Player".
    * Replace nulls in `umpire1`, `umpire2`, `umpire3` with "Unknown".
  * **Standardize Team Names:** (e.g., Replace "Delhi Daredevils" with "Delhi Capitals", "Deccan Chargers" with "Sunrisers Hyderabad"). Select team-related columns (`team1`, `team2`, `toss_winner`, `winner`) and use Replace Values.
  * **Data Types:** Ensure `date` is set to Type > Date.

* **For the `deliveries` table:**
  * **Replace Nulls:** 
    * `player_dismissed`, `dismissal_kind`, `fielder` -> Replace Nulls with "None".
  * **Standardize Team Names:** Do the same replacements for `batting_team` and `bowling_team`.

* Click **Close & Apply**.

---

## Phase 2: Data Modeling

**1. Create Relationships**
* Go to the **Model View** (the relationship icon on the left panel).
* Drag the `id` column from the `matches` table and drop it onto the `match_id` column in the `deliveries` table.
* Ensure the relationship is **1-to-Many** (`matches` -> `deliveries`) and the cross-filter direction is **Single**.

---

## Phase 3: Creating DAX Measures

To keep things organized, create a separate "Measures Table" (Home > Enter Data > Name it "Key Measures"). Then, create the following DAX measures:

### Basic KPI Measures
```dax
Total Matches = COUNT(matches[id])

Total Runs Scored = SUM(deliveries[total_runs])

Total Wickets = 
CALCULATE(
    COUNT(deliveries[dismissal_kind]), 
    deliveries[dismissal_kind] IN {"caught", "bowled", "lbw", "caught and bowled", "stumped", "hit wicket"}
)

Total Sixes = 
CALCULATE(
    COUNT(deliveries[batsman_runs]), 
    deliveries[batsman_runs] = 6
)

Total Fours = 
CALCULATE(
    COUNT(deliveries[batsman_runs]), 
    deliveries[batsman_runs] = 4
)
```

### Advanced Performance Measures
```dax
Economy Rate = 
VAR TotalRuns = SUM(deliveries[total_runs])
VAR TotalBalls = COUNT(deliveries[ball])
RETURN DIVIDE(TotalRuns, TotalBalls / 6, 0)

Batting Strike Rate = 
VAR RunsScored = SUM(deliveries[batsman_runs])
VAR BallsFaced = COUNT(deliveries[ball])
RETURN DIVIDE(RunsScored, BallsFaced, 0) * 100

Bowler Average = 
DIVIDE(SUM(deliveries[total_runs]), [Total Wickets], 0)
```

---

## Phase 4: Designing the Dashboard Pages

### Page 1: Overview & Team Performance
* **Top KPIs:** Use Card visuals for `Total Matches`, `Total Runs Scored`, `Total Wickets`, and `Total Sixes`.
* **Most Successful Teams:** 
  * Visual: **Clustered Bar Chart**
  * Y-Axis: `matches[winner]`
  * X-Axis: `Total Matches`
* **Matches Won After Winning Toss:**
  * Visual: **Donut Chart**
  * Values: Count of `matches[id]`
  * Legend: Create a calculated column in `matches` table: 
    ```dax
    Toss Win = IF(matches[toss_winner] = matches[winner], "Won Match", "Lost Match")
    ```
* **Matches per Season:**
  * Visual: **Line Chart**
  * X-Axis: `matches[season]`
  * Y-Axis: `Total Matches`

### Page 2: Player Analytics (Batting & Bowling)
* **Top 10 Scorer:**
  * Visual: **Clustered Column Chart**
  * X-Axis: `deliveries[batsman]`
  * Y-Axis: Sum of `deliveries[batsman_runs]`
  * Filter: Top N (10) based on Sum of `batsman_runs`.
* **Top 10 Bowlers:**
  * Visual: **Clustered Column Chart**
  * X-Axis: `deliveries[bowler]`
  * Y-Axis: `[Total Wickets]` measure.
* **Top 5 Bowler Stats (Matrix):**
  * Visual: **Matrix**
  * Rows: `deliveries[bowler]`
  * Values: `[Total Wickets]`, `Economy Rate`, `Bowler Average`
  * Filter: Top N (5) based on `Total Wickets`.

### Page 3: Strategic Insights (Venues & Fielding)
* **Matches Hosted by City:**
  * Visual: **Filled Map** or **Bubble Map**
  * Location: `matches[city]`
  * Size: `Total Matches`
* **Best Fielders:**
  * Visual: **Stacked Bar Chart**
  * Y-Axis: `deliveries[fielder]`
  * X-Axis: Count of `deliveries[dismissal_kind]`
  * Legend: `deliveries[dismissal_kind]` (filter to only show 'caught' and 'run out')
* **Overall Toss Decisions:**
  * Visual: **Pie Chart**
  * Legend: `matches[toss_decision]`
  * Values: `Total Matches`

---

## Phase 5: Formatting and Polish (UI/UX)
1. **Theming:** Go to View > Themes > Customize Current Theme. Use dark mode (Background: `#1E1E1E`, Text: `#FFFFFF`) or vibrant team colors to make it look premium.
2. **Filters & Slicers:** Add a Slicer visual for `matches[season]` on all pages (Sync Slicers) so users can view data for specific years.
3. **Tooltips:** Create a custom Report Page Tooltip. When a user hovers over a team on Page 1, it pops up showing their top batsman and bowler.
4. **Navigation:** Insert Buttons (Insert > Buttons > Blank) or use the built-in Page Navigator to create a sleek menu on the left side of every page.
