# Listeria Outbreak Analysis (2009-2021)

## Project Overview

Following the February 2024 investigation into **Listeria monocytogenes** contamination linked to Queso Fresco and Cotija cheese 🧀, this project dives into an in-depth analysis of Listeria outbreaks over a 12-year period. The focus is on identifying key trends and high-risk environments that contribute to the spread of Listeria.

**Listeria monocytogenes** is a bacteria that thrives in diverse environments like soil, water, and damp areas. It can contaminate food during harvesting, processing, or transportation, and it can even survive in refrigeration. When consumed, it may lead to a severe illness called listeriosis.

This project was part of a hands-on learning experience at the **Broadstreet Institute**, where I used **SAS programming** for data cleaning, analysis, and visualization to uncover patterns in outbreak-related fatalities.

> **Note**: The data used for this project was obtained from the CDC's National Outbreak Reporting System (NORS), and the results are based on public data available from 2009 to 2021.

## Objectives

- **Identify High-Risk Settings**: Determine which environments (e.g., homes, restaurants, healthcare facilities) were most frequently linked to Listeria outbreaks, illnesses, hospitalizations, and deaths.
- **Spot Significant Trends**: Investigate trends to determine if any particular year showed a surge in outbreak frequency or severity.

## Key Findings

- The most significant number of illnesses, hospitalizations, and deaths were linked to **private residences**.
- **2014** experienced the highest number of Listeria outbreaks during the study period.
- These insights can guide public health measures to reduce the impact of future outbreaks.

## Project Workflow

The analysis was performed using **SAS** with the following steps:

1. **Data Import**: Importing the National Outbreak Public Data Tool worksheet into SAS and reviewing the contents.
2. **Data Cleaning**: Renaming and recategorizing settings to simplify the data.
3. **Frequency Analysis**: Creating frequency tables to identify outbreak patterns based on settings and outcomes.
4. **Visualization**: Developing bar charts to display trends, such as the frequency of outbreaks by year.

### Sample SAS Code

```
/* Step 1: Set library reference if needed */

/* Step 2: Import the data */
PROC IMPORT DATAFILE='NationalOutbreakPublicDataTool.xlsx' DBMS=XLSX OUT=WORK.IMPORT;
    GETNAMES=YES;
RUN;

PROC CONTENTS DATA=WORK.IMPORT;
RUN;

/* Step 3: Rename dataset to make further manipulation easier */
data listeria;
    set Import;
run;

/* Step 4: Frequency of different settings in descending order */
proc freq data=WORK.listeria order=freq;
    tables Setting / plots=(freqplot);
run;

/* Step 5: Recategorize settings to simplify categories */
proc sql;
    create table NewListeria as
    select *,
        case 
            when Setting like "Private%" then "Private_Residence"
            when Setting like "Restaurant%" then "Restaurant"
            when Setting like "Long-term%" or Setting like "Hospital%" then "Healthcare_Facility"
            when Setting like "Grocer%" then "Grocery_Store"
            when Setting like "Banque%" then "Banquet_Facility"
            else Setting
        end as New_Setting
    from work.Listeria;
quit;

/* Step 6: Frequency of newly categorized settings */
proc freq data=WORK.NewListeria order=freq;
    tables New_Setting / plots=(freqplot);
run;

/* Step 7: Frequency tables for Illnesses, Hospitalization, and Deaths */

/* Illnesses */
ODS noproctitle;
Title1 "Outbreak Settings Associated with Highest Number of Illnesses";
title2 "Unknown and Other settings excluded";
proc freq data= NewListeria order=freq;
    table New_Setting/nocum;
    weight illnesses;
    where New_Setting <> "Other" and New_Setting <> "Unknown";
RUN;

/* Hospitalizations */
ODS noproctitle;
Title1 "Outbreak Settings Associated with Highest Number of Hospitalizations";
title2 "Unknown and Other settings excluded";
proc freq data= NewListeria order=freq;
    table New_Setting/nocum;
    weight hospitalizations;
    where New_Setting <> "Other" and New_Setting <> "Unknown";
RUN;

/* Deaths */
ODS noproctitle;
Title1 "Outbreak Settings Associated with Highest Number of Deaths";
title2 "Unknown and Other settings excluded";
proc freq data= NewListeria order=freq;
    table New_Setting/nocum;
    weight deaths;
    where New_Setting <> "Other" and New_Setting <> "Unknown";
RUN;
ODS reset;
Title;

/* Step 8: Bar chart to visualize frequency of Listeria outbreaks by year */
ods graphics / reset width=6.4in height=4.8in imagemap;
proc sgplot data=WORK.NewListeria;
    title height=14pt "Frequency of Listeria Outbreaks by Year";
    vbar Year / fillattrs=(color=CX9a51d5) datalabel;
    yaxis grid;
run;
ods graphics / reset;
title;
```
