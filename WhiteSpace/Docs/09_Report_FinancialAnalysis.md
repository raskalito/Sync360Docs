# Report: Report_FinancialAnalysis.xml

## Purpose

Report_FinancialAnalysis provides revenue-based insights into service combinations, showing the financial value associated with different service groupings. This enables value-based prioritization of cross-sell opportunities and service bundling strategies.

**Key Characteristics**:
- **Revenue-weighted analysis**: Incorporates financial data into service analysis
- **Service combination focus**: Analyzes companies with multiple services
- **Financial metrics**: Total revenue, percentages, and distributions
- **CSV export**: Ready for financial modeling and analysis

**Note**: This report requires financial data to be available in your source tables (referenced as `fol_fb5ytotal` in the example script).

---

## When to Use This Report

Run Report_FinancialAnalysis when:
- **Value-based prioritization**: Need to focus on high-revenue opportunities
- **Financial modeling**: Building revenue forecasts for service adoption
- **ROI analysis**: Understanding revenue impact of cross-sell efforts
- **Executive reporting**: Presenting financially-focused insights
- **Account prioritization**: Identifying high-value accounts for service expansion

**Typical usage**: Initial analysis and quarterly financial reviews

---

## What You Need Before Running

### Prerequisites
1. **RevenueServiceProcessing completed**: Must have matched company-service data
2. **Financial data available**: Source tables must contain revenue/financial fields
3. **Practice area mapping**: Revenue Services must include practice area IDs in their JSON
4. **Write access to output directory**: Default C:\temp\

### Data Requirements

The script expects:
- **Revenue Services** with practice area IDs in their JSON configuration
- **Source table** (e.g., vs360_job) with financial data (e.g., `fol_fb5ytotal`)
- **Companies** with multiple matched services for meaningful analysis

---

## What the Script Does

### Process Flow

```
1. Load all active Revenue Services
2. Parse JSON to extract practice area IDs
3. Load matched company-service relationships
4. Identify service combinations (companies with 2+ services)
5. Query financial data for each company/service combination
6. Aggregate revenue by:
   - Service combination (group)
   - Individual company
   - Individual service within combination
7. Calculate percentages and totals
8. Export to CSV
```

### Detailed Operations

#### Phase 1: Load Revenue Services
- Retrieves active Revenue Services
- Parses `vs360_selectedcombinations` JSON
- Extracts practice area ID from conditions
- Maps Revenue Service → Practice Area
- Maps Practice Area → Revenue Service

#### Phase 2: Load Matched Relationships
- Loads matched company-service data
- Groups companies by their service combinations
- Filters to combinations with 2+ services

#### Phase 3: Query Financial Data
For each service combination:
- Queries source table (e.g., vs360_job)
- Filters by:
  - Company ID
  - Practice Area IDs in the combination
- Retrieves financial field (e.g., `fol_fb5ytotal`)
- Aggregates revenue per company per service

#### Phase 4: Calculate Financial Metrics
- **Group Total Financial**: Sum of all revenue in this service combination
- **Group Service Financial**: Sum of revenue for one service across all companies
- **Company Total Financial**: Total revenue for one company across all their services
- **Service Financial Value**: Revenue for one service for one company
- **Financial Percentage**: Service revenue / Company total

#### Phase 5: Export to CSV
Creates detailed financial breakdown for analysis.

---

## Configuration

### Output File Path

```xml
<set var="dumpFile">C:\temp\dump.csv</set>
```

**To change output location**:
```xml
<set var="dumpFile">D:\Financial\ServiceRevenue.csv</set>
```

### Customizing Financial Field

The script queries a specific financial field. In the example:
```xml
<attr name="fol_fb5ytotal" />
```

**To use a different financial field**:
1. Identify your revenue field name
2. Modify the script:
```xml
<attr name="your_revenue_field" />
```
3. Update aggregation logic:
```xml
<set var="Total">{Total+nowGroups[key][aid][rid]}</set>
```

Ensure field is of type Decimal or Currency.

---

## CSV Output Structure

### Column Definitions

| Column | Description | Example |
|--------|-------------|---------|
| **ServiceGroup** | Semicolon-separated list of services in this combination | "(0) Tax;(0) Audit" |
| **GroupTotalFinancial** | Total revenue for all companies in this service combination | 5,250,000 |
| **GroupServiceFinancial** | Total revenue for this specific service across all companies in group | 2,100,000 |
| **Company** | Company ID (GUID) | abc123-def456... |
| **Service** | Individual service name | "(0) Tax Services" |
| **FinancialValue** | Revenue for this service for this company | 150,000 |
| **FinancialPercentage** | Percentage of company's total revenue from this service | 0.60 (60%) |
| **FinancialCompanyTotal** | Total revenue for this company across all services | 250,000 |

### Example Output

```csv
ServiceGroup,GroupTotalFinancial,GroupServiceFinancial,Company,Service,FinancialValue,FinancialPercentage,FinancialCompanyTotal
"(0) Tax;(0) Audit;(0) Advisory",5250000,2100000,GUID-ABC,"(0) Tax Services",150000,0.60,250000
"(0) Tax;(0) Audit;(0) Advisory",5250000,1890000,GUID-ABC,"(0) Audit Services",75000,0.30,250000
"(0) Tax;(0) Audit;(0) Advisory",5250000,1260000,GUID-ABC,"(0) Advisory Services",25000,0.10,250000
"(0) Tax;(0) Audit;(0) Advisory",5250000,2100000,GUID-DEF,"(0) Tax Services",180000,0.45,400000
```

---

## Interpreting Results

### Group-Level Analysis

**GroupTotalFinancial**: Total revenue from all companies with this service combination
- Indicates total value of this service pattern
- Higher values = more valuable combinations to focus on

**GroupServiceFinancial**: Total revenue for one service across all companies in the group
- Shows which service drives the most revenue in this combination
- Helps prioritize services within bundles

**Example**:
```
Service Combination: Tax + Audit + Advisory
GroupTotalFinancial: $5,250,000
- Tax Services: $2,100,000 (40%)
- Audit Services: $1,890,000 (36%)
- Advisory Services: $1,260,000 (24%)
```
*Tax is the highest-value service in this combination*

### Company-Level Analysis

**FinancialCompanyTotal**: Total revenue from this company
- Identifies high-value accounts
- Prioritize cross-sell efforts to high-revenue companies

**FinancialValue**: Revenue from one service for this company
- Shows service contribution to company revenue
- Understand which services drive company relationships

**FinancialPercentage**: Service revenue as percentage of company total
- Dominant service: >50%
- Balanced portfolio: ~33% each for 3 services
- Minor service: <20%

**Example**:
```
Company ABC: Total $250,000
- Tax: $150,000 (60%) - Dominant service
- Audit: $75,000 (30%) - Secondary service
- Advisory: $25,000 (10%) - Minor service
```
*Tax is the primary relationship driver for this company*

---

## Using the Report Results

### Step 1: Import to Excel

1. Open CSV in Excel
2. Format financial columns as currency
3. Format percentage columns as percentages
4. Apply filters for analysis

### Step 2: Identify High-Value Service Combinations

**Sort by GroupTotalFinancial** (descending):
- Focus on combinations with highest total revenue
- These are the most valuable service patterns

**Example analysis**:
```
Combination A: $5,250,000
Combination B: $3,100,000
Combination C: $850,000
→ Prioritize Combination A for cross-sell efforts
```

### Step 3: Service Prioritization Within Combinations

**For each high-value combination**:
- Review GroupServiceFinancial
- Identify which service contributes most revenue
- Prioritize selling that service first

**Example**:
```
In "Tax + Audit + Advisory" combination:
Tax contributes 40% of revenue
→ Lead with Tax, cross-sell Audit and Advisory
```

### Step 4: Account Prioritization

**Filter by FinancialCompanyTotal**:
- Identify high-revenue accounts
- Focus cross-sell efforts on these accounts first
- Higher revenue = higher potential for additional services

**Segment companies**:
- Tier 1: >$500K total revenue
- Tier 2: $250K - $500K
- Tier 3: <$250K

Target higher tiers first for maximum ROI.

### Step 5: Service Mix Analysis

**Review FinancialPercentage**:
- Balanced (each service ~33%): Healthy diversification
- Imbalanced (one service >70%): Opportunity for cross-sell
- Identify companies with low percentage in a service → Upsell opportunity

**Example**:
```
Company has Tax (70%), Audit (20%), Advisory (10%)
Advisory is underutilized → Upsell Advisory services
```

---

## Advanced Analysis Techniques

### Revenue Potential Calculation

**Identify gaps**:
```
Company A: Tax ($150K), Audit ($75K), No Advisory
Similar companies with all three average $50K in Advisory
→ Revenue potential: $50K if Company A adds Advisory
```

**Aggregate across all gap companies**:
- Total potential revenue from cross-sell

### Service Bundle Pricing

**Use financial data to design bundles**:
```
Typical revenue split in Tax+Audit+Advisory:
- Tax: $150K (40%)
- Audit: $135K (36%)
- Advisory: $90K (24%)
Total: $375K

Bundle pricing: $350K (7% discount for all three)
```

### ROI Forecasting

**Model revenue impact**:
```
Current state: 100 companies with Tax only
Average Tax revenue: $100K each = $10M total

If 50% add Audit (avg $75K):
Additional revenue: 50 × $75K = $3.75M
ROI on cross-sell campaign = ?
```

---

## Script Execution

### Running the Script

1. **Verify Prerequisites**:
   - Financial data is populated
   - RevenueServiceProcessing is current
   - Practice area IDs are in Revenue Service JSON

2. **Execute**:
   - Load Report_FinancialAnalysis.xml in Sync360
   - Run the script
   - Wait for completion (may take 2-5 minutes for large datasets)

3. **Locate Output**:
   - Open C:\temp\dump.csv

4. **Analyze**:
   - Import to Excel
   - Create pivot tables
   - Calculate opportunity sizes

---

## Important Notes

### Practice Area Dependency

This script assumes Revenue Services include practice area IDs:
```json
{
    "rules": [{
        "conditions": [{
            "_and": [{
                "attr": "vs360_practiceareaid",
                "op": "eq",
                "value": "PRACTICE_AREA_GUID"
            }]
        }]
    }]
}
```

**If your implementation differs**:
- Modify the script to extract your service identifier
- Adjust the financial data query accordingly

### Financial Data Accuracy

Ensure:
- Financial field is populated
- Values are current
- Currency is consistent
- Null values are handled (script sums only where data exists)

---

## Troubleshooting

### Issue: No financial data in output
**Possible causes**:
- Financial field is empty/null
- Practice area mapping incorrect
- Source table doesn't contain financial data
- Companies in combinations don't have financial records

**Solutions**:
- Verify financial field name matches your data
- Check practice area IDs are correct
- Manually query source table to confirm data exists

### Issue: GroupTotalFinancial doesn't match sum
**Expected**: This can occur if companies have services outside the combination
- Script only sums services within the specific combination
- Company may have additional services not in this group

### Issue: Revenue seems too low/high
**Check**:
- Field is correct (annual, lifetime, etc.)
- Currency/units match expectations
- Date filters if applicable (script doesn't filter by date)

---

## Best Practices

1. **Validate financial data**: Spot-check results against source system
2. **Use with other reports**: Combine with ServicesMatrix for complete picture
3. **Regular updates**: Re-run after significant financial data changes
4. **Segmented analysis**: Filter by time period, region, etc. for targeted insights
5. **Act on insights**: Use to prioritize actual sales and marketing efforts

---

## Integration with Business Strategy

### Sales Strategy
- High-value combinations = sales bundle priorities
- High-revenue accounts = account management focus
- Service mix imbalances = cross-sell opportunities

### Marketing Strategy
- Promote high-value combinations in campaigns
- Create case studies from high-revenue multi-service accounts
- Target segments with highest revenue potential

### Financial Planning
- Revenue forecasting based on service adoption patterns
- Budget allocation for service development (focus on high-revenue services)
- ROI modeling for cross-sell initiatives

---

**Next**: Run Report_SegmentsData for segment-based analysis, or use insights from this report to prioritize business development efforts.
