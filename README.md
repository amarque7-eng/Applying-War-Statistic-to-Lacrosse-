# ACC Lacrosse PDF Scraper

## Overview

This project addresses a practical problem in sports analytics: ACC men’s lacrosse statistics are published primarily as web-based PDFs that are not machine-readable.

The goal of this project was to convert those PDFs into a clean, consistent dataset of player-level cumulative season statistics for the following teams:

- Notre Dame  
- Syracuse  
- North Carolina  
- Virginia  
- Duke  

The final output, `ACC_all_players_FINAL.csv`, contains one row per skater with standardized season statistics and is ready for advanced analysis such as WAR modeling, efficiency metrics, and lineup evaluation.

---

## Motivation

Most college lacrosse data is locked in formats that are difficult to analyze programmatically, including:

- PDF box scores  
- Team-specific statistical summaries  
- Inconsistent layouts across schools  

As a result, meaningful analysis often requires manual data entry. This project demonstrates a reproducible workflow for transforming these PDFs into structured data.

Specifically, the project focuses on:

1. Extracting statistics from PDFs using Python and `pdfplumber`  
2. Cleaning and standardizing fields across five ACC teams  
3. Producing a unified ACC dataset that can be reused for future analytics projects  

---

## Data Sources

All statistics were scraped from official athletic department PDFs for the 2025 season.  
Each team publishes cumulative statistics using a different layout, requiring team-specific scraping logic.

- **Notre Dame**: cumulative team statistics PDF (combined season stats)  
- **Syracuse**: cumulative skater stats on page 2  
- **North Carolina**: cumulative skater stats on page 2  
- **Virginia**: cumulative skater stats on page 1  
- **Duke**: cumulative skater stats on page 2  

Processed CSV files are stored in the `data/` directory.

---

## Methods

### 1. PDF Text Extraction

For each team, `pdfplumber` was used to open the PDF and extract text from the page containing cumulative skater statistics. Pages containing game-by-game logs were excluded to avoid duplicating individual performances.

### 2. Regex-Based Row Extraction

Once the relevant text was extracted, regular expressions were used to identify player-level rows.

Although each team’s PDF used slightly different headers and column ordering, the underlying structure of a player row was consistent:

**jersey number – player name – games played – goals – assists – points – shots – additional statistics – faceoff record**

This approach allowed player statistics to be isolated while ignoring headers, footers, and team summary rows.

For example, Notre Dame’s cumulative player rows were extracted using the following regular expression:

```python
pattern = re.compile(
    r"(\d+)\s+"                          # jersey number
    r"([A-Za-z'.\- ]+?)\s+"              # player name
    r"(\d+)\s+"                          # games played
    r"(\d+)\s+"                          # goals
    r"(\d+)\s+"                          # assists
    r"(\d+)\s+"                          # points
    r"(\d+)\s+"                          # shots
    r"(\d+)\s+"                          # man-up goals
    r"(\d+)\s+"                          # man-down goals
    r"(\d+)\s+"                          # ground balls
    r"(\d+-\d+)",                        # faceoff record
    flags=re.MULTILINE
)

