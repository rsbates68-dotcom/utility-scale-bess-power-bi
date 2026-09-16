# Utility-Scale BESS Screening Model

## Power BI analysis of battery operation, peak reduction, and avoided system cost

This repository contains a Power BI screening model that evaluates how a utility-scale battery energy storage system, or BESS, could affect hourly net load, peak demand, and marginal energy costs in a synthetic Atlantic Canadian electricity system.

The project demonstrates dimensional data modelling, Power Query transformation, DAX development, hourly electricity-system analysis, analytical validation, drill-through reporting, and decision-focused visualization.

> **Key result:** Under the illustrative dispatch assumptions, a 100 MW / 400 MWh BESS reduces the modeled annual net-load peak by approximately 85 MW and produces approximately $0.8 million in gross avoided marginal energy cost. Most modeled value is concentrated in winter.

---

## Repository contents

```text
.
├── README.md
├── report/
│   └── BESS_Analysis_Report.pdf
├── power-bi/
│   └── Atlantic_Electricity_BESS_Model.pbix
├── data/
│   └── Atlantic_Electricity_Synthetic_Data_2026.csv

```
### Suggested starting points

- **Quick review:** Open `report/BESS_Analysis_Report.pdf`.
- **Interactive technical review:** Open `power-bi/Atlantic_Electricity_BESS_Model.pbix` in Power BI Desktop.
- **Source data:** Review the synthetic hourly CSV in the `data` folder.

---

## Project objective

The model addresses four practical questions:

1. When would the battery charge and discharge?
2. How would battery operation affect hourly and peak net load?
3. What gross marginal energy-cost value could selective dispatch provide?
4. What additional information would be required for a complete investment decision?

The report is designed as a transparent **screening model**, not as a complete project feasibility study or investment-grade financial model.

---

## Demonstration system

| Assumption | Model value |
|---|---:|
| Rated battery power | 100 MW |
| Energy capacity | 400 MWh |
| Duration | 4 hours |
| Round-trip efficiency | 85% |
| Charging rate | 100 MW |
| Delivered discharge rate | 85 MW |
| Maximum candidate cycling | 1 full cycle per day |
| Dispatch basis | Daily hourly marginal-cost spread |
| Operating rule | Dispatch only when positive after efficiency losses |
| Study period | Calendar year 2026 |
| Time resolution | Hourly, 8,760 observations |

These assumptions define an illustrative analytical scenario and do not represent a recommended project specification.

---

## Dispatch methodology

For each day, the model:

1. Ranks the four lowest-cost hours as candidate charging periods.
2. Ranks the four highest-cost hours as candidate discharging periods.
3. Pairs the lowest charging-cost hour with the highest discharge-cost hour, then repeats for the remaining ranked hours.
4. Tests whether each pair remains economic after accounting for 85% round-trip efficiency.
5. Charges at 100 MW and delivers 85 MW during qualifying one-hour intervals.
6. Recalculates net load and gross avoided marginal energy cost after dispatch.

A candidate pair operates only when:

```text
0.85 × discharge-hour marginal cost > charge-hour marginal cost
```

This rule prevents the model from assuming daily cycling when the cost spread is insufficient to overcome storage losses.

---

## Headline modeled results

| Metric | Result |
|---|---:|
| Annual energy charged | 40,000 MWh |
| Annual energy delivered | 34,000 MWh |
| Annual storage losses | 6,000 MWh |
| Observed round-trip efficiency | 85% |
| Equivalent full cycles | 100 |
| Days with at least some operation | 147 |
| Gross avoided marginal energy cost | Approximately $0.80 million |
| Peak net load before BESS | 2,398.48 MW |
| Peak net load after BESS | 2,313.48 MW |
| Maximum modeled peak reduction | 85 MW |
| Share of gross value in December to February | Approximately 86% |

### Principal finding

The modeled opportunity is primarily a **winter peak-management and selective cost-avoidance opportunity**, rather than a continuous year-round arbitrage opportunity.

The gross avoided-cost result does not include battery capital cost, financing, fixed operating cost, replacement cost, or all potential system-value streams.

---

## Power BI model design

The semantic model uses a star schema with shared dimensions filtering the hourly source and BESS simulation tables.

```text
                         dim_Date
                       /          \
                      /            \
fact_HourlyElectricity              fact_BESSHourlySimulation
                      \            /
                       \          /
                         dim_Time
```

### Principal tables

- `fact_HourlyElectricity`: Hourly load, renewable generation, net load, fuel-price, carbon-price, and marginal-cost observations.
- `fact_BESSHourlySimulation`: Hourly charging, discharging, energy movement, net-load effect, and gross avoided-cost calculations.
- `dim_Date`: Calendar fields used for annual, seasonal, monthly, and daily analysis.
- `dim_Time`: Hour-of-day fields and formatted time labels.
- `BESS_DailySummary`: Daily peak, energy, cost, and operating metrics used for detailed analysis and drill-through.
- `LoadRank`: Annual before-and-after net-load duration-curve values.
- `BESS_Assumptions`: Display table containing the principal scenario assumptions.
- `BESS_VariableCostSensitivity`: Disconnected table used to test assumed variable battery cost.

### Modelling principles

- Dimensions filter facts through one-to-many, single-direction relationships.
- The hourly fact tables are not related directly to one another.
- Measures are used for filter-responsive totals and ratios.
- Hourly MW and accumulated MWh are kept conceptually distinct.
- Disconnected tables are used only where intentional, such as sensitivity and annual rank analysis.

---

## Report pages

### 1. Executive Summary

Presents the annual findings, including gross avoided cost, peak reduction, energy throughput, operating frequency, and seasonal concentration of value.

### 2. How the Battery Works

Explains hourly charging and discharging, net-load effects, operating frequency by hour, monthly energy balance, storage losses, and peak-load impacts.

### 3. Peak Management and System Value

Examines monthly avoided cost, the highest-peak days before and after BESS, and the annual high-net-load duration curve.

### 4. Decision Support and Model Boundaries

Shows sensitivity to variable battery cost, distinguishes quantified from unquantified value streams, states the core assumptions, and identifies the additional analysis required for a complete business case.

### Daily BESS Detail drill-through

A hidden drill-through page provides an hourly view for a selected date, including:

- Charging and discharging dispatch
- Net load before and after BESS
- Hourly marginal cost
- Daily energy charged and delivered
- Storage losses
- Daily gross avoided cost
- Daily peak reduction

---

## Quantified value streams

The model quantifies:

- Hourly battery charging and discharging
- Energy charged and delivered
- Storage losses
- Change in hourly net load
- Annual and monthly peak reduction
- Gross avoided marginal energy cost
- Operating days and hours
- Equivalent full cycles
- Seasonal operating patterns
- Sensitivity to assumed variable operating cost

## Value streams not quantified

The model does not quantify:

- Battery capital cost
- Fixed operating and maintenance cost
- Financing, replacement, or residual value
- Dependable capacity value
- Operating-reserve value
- Ancillary-service revenue
- Transmission investment deferral
- Distribution investment deferral
- Renewable-curtailment recovery
- Outage resilience
- Marginal emissions reduction

An unquantified value should not be interpreted as having no value. It indicates that the available dataset does not contain enough market, engineering, cost, or emissions information to estimate that value credibly.

---

## Important interpretation notes

### Gross value is not project profitability

The reported avoided cost is a gross system-operating value. It is not net income, project revenue, or an investment return.

### Carbon benefits are not estimated

The dataset does not identify the marginal generator or provide hourly marginal-emissions rates. Carbon benefits are therefore not estimated, avoiding unsupported assumptions or double counting.

### MW and MWh serve different purposes

- **MW** represents power at a specific point in time and is used in hourly dispatch and load-profile visuals.
- **MWh** represents energy accumulated over an interval and is used for charging, delivery, losses, and annual throughput.

Because the source observations are hourly, 100 MW sustained for one observation equals 100 MWh.

### The annual duration curve is static

The load-duration table is calculated during model refresh and represents the full annual distribution. It is intentionally disconnected and does not change with month or date slicers.

---

## Validation checks

The model was reviewed against the following reconciliation conditions:

```text
Energy delivered = Energy charged × 85%
Storage losses = Energy charged - Energy delivered
Annual energy balance variance = 0 MWh
Maximum daily charging energy <= 400 MWh
Maximum daily delivered energy <= 340 MWh
Simultaneous charging and discharging hours = 0
Hourly records in the annual dataset = 8,760
Hourly records on a daily drill-through page = 24
```

Expected annual reconciliation:

```text
40,000 MWh charged
34,000 MWh delivered
 6,000 MWh lost
    85% observed efficiency
   100 equivalent full cycles
```

---

## Tools and techniques demonstrated

- Power BI Desktop
- Power Query data transformation
- DAX calculated tables, columns, and measures
- Star-schema semantic modelling
- Disconnected parameter and rank tables
- Hourly dispatch simulation
- Load-duration analysis
- Sensitivity analysis
- Dynamic titles and filter context
- Report-page drill-through
- Interactive tooltips and slicers
- Analytical reconciliation and validation
- Decision-focused data visualization

---

## Running the project

### Requirements

- A current version of Power BI Desktop
- The PBIX file from the `power-bi` folder
- The source CSV from the `data` folder if a refresh is required

### Open without refreshing

The PBIX should contain the imported model and report visuals. Open the file in Power BI Desktop to review the saved report state.

### Refresh the data

If the CSV path differs from the original development environment:

1. Open the PBIX in Power BI Desktop.
2. Select **Transform data**.
3. Open **Data source settings**.
4. Update the source path to the CSV in this repository.
5. Apply changes and refresh the model.
6. Confirm that the hourly fact table contains 8,760 rows.
7. Recheck the annual BESS energy-balance measures.

---

## Suggested review sequence

1. Review the four-page PDF for the principal findings and report narrative.
2. Open the PBIX and test the month slicer on Page 2.
3. Review Page 3 and drill through from one of the highest-peak days.
4. Review Page 4 for assumptions, sensitivities, and model limitations.
5. Inspect the model view, Power Query transformations, calculated tables, and DAX measures if technical detail is required.

---

## Potential extensions

Future development could include:

1. Multiple BESS power and duration scenarios
2. State-of-charge tracking and chronological dispatch constraints
3. Charge-before-discharge chronology within each daily cycle
4. Battery degradation by throughput and cycle depth
5. Capital, fixed operating, financing, and replacement costs
6. Capacity and ancillary-service value
7. Renewable-curtailment recovery
8. Transmission and distribution deferral value
9. Hourly marginal-emissions analysis
10. Multiple weather, demand-growth, and renewable-development years
11. Optimization-based dispatch rather than deterministic daily ranking
12. Probabilistic reliability and capacity-contribution analysis

---

## Data notice

The electricity-system dataset used in this project is synthetic and was prepared for analytical and portfolio-development purposes. Results should not be interpreted as a forecast of an actual utility system or as a recommendation to construct a specific storage project.

---

## Author

**Robert Bates**  
Energy policy, electricity-sector analysis, and data analytics

---

## Contact

E-mail: rsbates68@gmail.com
LinkedIn: www.linkedin.com/in/robertsbates

