# Marketing Analytics Dashboard --- Power BI

## Project Overview

A complete **Marketing Analytics solution built with Power BI** to
analyze marketing performance, customer acquisition, campaign
effectiveness, funnel conversion, and merchant/customer value.

The goal was not only to build a dashboard, but to create a reliable
analytical model where the **data structure, relationships, KPI
definitions, and business logic** work together correctly.

## Business Objectives

The analysis was designed to answer:

-   How is overall marketing performance changing over time?
-   How much GMV is generated compared with marketing cost?
-   Which channels and campaigns perform best?
-   What is the return on marketing spend?
-   How efficiently are leads converted?
-   Where are the biggest funnel drop-offs?
-   Which personas, segments, and markets generate the strongest
    results?
-   What is the average LTV?
-   How does current performance compare with the previous month?
-   Are KPIs based on consistent definitions and reliable data?

# Project Workflow

1.  Data Understanding
2.  Data Quality Assessment
3.  Data Cleaning and Transformation
4.  Data Modeling
5.  Relationship Design
6.  KPI Definition
7.  DAX Measure Development
8.  Time Intelligence
9.  Funnel Analysis
10. Dashboard Development
11. Conditional Formatting and KPI Variance
12. Validation and Testing
13. Business Insights

# 1. Data Understanding

The first step was understanding what each table represented and, more
importantly, the **grain of each table**.

Main tables included:

-   `MarketingActivity`
-   `CRM_Leads`
-   `GA4`
-   `EmailMarketing`
-   `DimCampaigns`
-   `ChannelDim`
-   `Markets`
-   `Calendar`

Different tables represent different business processes, so they should
not automatically be treated as if they have the same level of detail.

A key question throughout the project was:

> **What does one row represent?**

One row may represent a marketing activity, a lead, a campaign record,
or a date. Understanding this prevented incorrect relationships and
potential double counting.

# 2. Data Quality

Several data quality issues were identified and handled.

### Missing Campaigns

Some `MarketingActivity` records had no campaign attribution. These
records were treated as **Unattributed** so they remained visible in
overall KPIs.

### Missing Market

Some transaction records had missing market information. Where a
reliable merchant relationship existed, market information could be
backfilled.

### Country Inconsistency

Different raw country labels required standardization so geographical
analysis remained consistent.

### Data Freshness

Marketing data and transaction data did not always end on the same date,
so freshness needed to be checked before interpreting the report.

### Invalid Transaction Values

Zero or negative transaction amounts were identified because they can
distort GMV and financial KPIs.

The main lesson: **data quality is part of analytics, not a step to
ignore until the dashboard is finished.**

# 3. Data Modeling

One of the biggest learning areas was Data Modeling.

The model was designed around business processes, dimensions, and
relationships rather than simply connecting every table together.

## Data Modeling Schemas

Three common approaches were considered:

-   **Star Schema:** Fact tables in the center with dimensions around
    them. Usually the clearest and most practical structure for Power BI
    analytics.
-   **Snowflake Schema:** Some dimensions are further normalized into
    related tables. This can reduce duplication but may increase model
    complexity.
-   **Galaxy Schema:** Multiple fact tables share common dimensions and
    is useful when several business processes need to be analyzed
    together.

The important lesson was not memorizing schema names, but understanding
**when and why each structure should be used**.

## Normalization & Denormalization

**Normalization** organizes data and reduces unnecessary duplication.

**Denormalization** can combine related information when doing so makes
analytical consumption simpler.

In BI, the goal is to balance data integrity, simplicity, performance,
usability, and analytical requirements.

## Role-Playing Dimension

A single dimension can represent different roles within the same
business process.

For example, an `Employees` dimension could represent both
**Salesperson** and **Manager**. The entity is the same, but its role
changes depending on the analytical question.

## Bridge Table

A Bridge Table can manage complex **Many-to-Many relationships**.

Instead of connecting two tables directly when that could create
ambiguous filtering or duplicate results, the bridge acts as an
intermediate layer and gives better control over relationships and
filter propagation.

## Grain of the Fact Table

The grain defines exactly what **one row represents**.

For example:

-   One row = Order
-   One row = Order Line
-   One row = Transaction
-   One row = Lead
-   One row = Marketing Activity

If every row represents an Order Line, an order containing five products
appears in five rows.

If the grain is not understood, joining that table with another table
can multiply records and cause:

-   Double counting
-   Incorrect revenue
-   Incorrect quantities
-   Incorrect customer counts
-   Incorrect KPIs

Therefore, before writing DAX, one of the most important questions is:

> **What does one row represent?**

The grain determines how data can safely be aggregated and related.

# 4. Relationships

Relationships were designed based on business meaning and filter
behavior.

## Active vs Inactive Relationships

Sometimes the same dimension connects to a fact table through multiple
dates.

Activating all relationships can create ambiguous filter paths. One
relationship can remain active while another remains inactive and can be
activated inside a specific measure using:

`USERELATIONSHIP()`

An inactive relationship is not necessarily a problem; it can be part of
the correct model design.

# 5. Single Source of Truth

## SSOT

The **Single Source of Truth** principle means reports should rely on a
consistent, trusted data model and business logic.

Instead of every report creating its own relationships, calculations,
and definitions, reports should use the same approved model.

> **Same data → Same model → Same definitions → Same truth**

This helps keep KPIs consistent across dashboards.

# 6. Metric Fragmentation

Metric Fragmentation happens when the same KPI has multiple definitions,
calculations, or data sources.

For example, one report calculates Revenue from one table while another
uses a different source or business rule.

The result can be multiple numbers for what appears to be the same KPI.

The problem is not always DAX. It can come from different:

-   Data sources
-   Business definitions
-   Filters
-   Relationships
-   Calculation logic

This is why KPI governance and a consistent data model are important.

# 7. KPI Development

Main marketing KPIs included:

### GMV

Total Gross Merchandise Value generated during the selected period.

### Total Cost

Total marketing cost, including the relevant cost components.

### ROAS

`ROAS = GMV / Marketing Cost`

### CAC

`CAC = Marketing Cost / New Customers`

### ROI

`ROI = (GMV - Marketing Cost) / Marketing Cost`

### Average LTV

Average Lifetime Value within the selected analytical context.

# 8. Month-over-Month Analysis

Time intelligence was used to compare current performance with the
previous month.

Examples included:

-   GMV vs Last Month
-   Marketing Cost vs Last Month
-   ROAS vs Last Month
-   CAC vs Last Month
-   ROI vs Last Month

KPI variance labels were designed to show:

-   Direction
-   Absolute change
-   Percentage change
-   Comparison period

Example:

`▲ $898,789 | 59% Vs LM`

This makes KPI cards more useful because they show both the current
value and what changed.

# 9. Funnel Analysis

The customer journey was analyzed through:

**Awareness → Engagement → Website → Leads → Revenue → Retention**

The purpose was to understand how users move through the marketing
journey and identify the largest drop-offs.

The analysis focuses on:

-   Volume at each stage
-   Conversion between stages
-   Drop-off
-   Final conversion
-   Retention

# 10. Dashboard Pages

## Page 1 --- Marketing Performance

Executive overview containing:

-   Total GMV
-   Total Cost
-   ROAS
-   CAC
-   ROI
-   Average LTV
-   GMV Trend
-   Cost & GMV by Channel
-   Spend & Conversions by Campaign Objective
-   ROAS by Channel

Main question:

> **How is marketing performing overall?**

## Page 2 --- Marketing Performance Analysis

Deeper performance analysis including:

-   Organic Traffic
-   Organic Clicks
-   CTR
-   Conversion
-   Campaign performance
-   Channel trends
-   Marketing activity

A Ribbon Chart was also used where the goal is to understand **rank
changes over time**, rather than only showing a trend.

## Page 3 --- Channel & Campaign Performance

Focuses on:

-   Marketing Cost
-   Clicks
-   CTR
-   CVR
-   Conversions
-   GMV
-   ROAS
-   Campaign-level performance

Main question:

> **Which channels and campaigns are actually delivering results?**

## Page 4 --- Funnel & Customer Journey

Analyzes:

**Awareness → Engagement → Website → Leads → Revenue → Retention**

The objective is to identify conversion bottlenecks and understand where
users are lost.

## Page 5 --- Personas, Segments & Markets

Focuses on:

-   Top persona by GMV
-   Best market by ROAS
-   Top segment by GMV
-   Average LTV
-   B2B merchant share
-   Enterprise GMV share

# 11. Supporting Marketing Metrics

### CTR

Click-Through Rate: how often users click after seeing an advertisement.

### CVR

Conversion Rate: the percentage of users who complete the defined
conversion action.

### CPA

Cost Per Acquisition: the marketing cost required to generate an
acquisition.

### Impressions

The total number of times an advertisement was displayed.

### Reach

The number of unique users who saw the advertisement.

One person can generate multiple impressions while still counting as one
reached user.

# 12. Dashboard Design

The dashboard follows a clear hierarchy:

**Executive KPIs → Trends → Channel/Campaign Performance → Detailed
Analysis**

Design principles included:

-   KPI cards for high-level performance
-   Previous-month comparisons
-   Conditional colors for movement
-   Clear chart titles
-   Consistent spacing and alignment
-   Filters for date, market, and channel
-   Visual hierarchy between summary and detailed analysis

# 13. Validation & Testing

The report was tested by changing filters and comparing results across
visuals.

Checks included:

-   Do filters affect the correct visuals?
-   Do monthly comparisons return the correct previous month?
-   Are KPIs consistent across pages?
-   Are relationships producing expected filter propagation?
-   Are totals affected by duplicates?
-   Are percentages using the correct denominator?
-   Are funnel stages logically connected?
-   Do KPI definitions remain consistent?

A dashboard can look correct while still producing incorrect
calculations, so validation was an essential part of the project.

# 14. Key Lessons

The biggest lesson was that **Data Modeling is part of the analysis
itself**.

The project covered:

-   Star Schema
-   Snowflake Schema
-   Galaxy Schema
-   Normalization
-   Denormalization
-   Role-Playing Dimensions
-   Bridge Tables
-   Fact Table Grain
-   Active and Inactive Relationships
-   Filter Propagation
-   Ambiguous Paths
-   Row-Level Security
-   Single Source of Truth
-   Metric Fragmentation
-   KPI Definition and Governance

Many of these concepts became clearer through practical problems rather
than theory alone.

The most important question I learned to ask before writing DAX was:

> **What exactly does this row represent, and how should it interact
> with the rest of the model?**

# Tools & Technologies

-   Power BI
-   DAX
-   Power Query
-   Data Modeling
-   Time Intelligence
-   KPI Development
-   Marketing Analytics
-   Funnel Analysis
-   Data Quality Analysis
-   Business Intelligence

# Final Takeaway

This project was not only about creating a Power BI dashboard.

It was about building a reliable analytical environment where:

-   Data has a clear structure
-   Relationships have business meaning
-   KPIs have consistent definitions
-   Filters behave predictably
-   DAX is built on a sound model
-   The report can scale without becoming difficult to maintain