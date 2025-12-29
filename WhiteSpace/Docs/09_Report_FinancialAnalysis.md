# Report: Report_FinancialAnalysis.xml

## Purpose

Report_FinancialAnalysis provides revenue-based insights into service combinations, showing the financial value associated with different service groupings to enable value-based prioritization of cross-sell opportunities and service bundling strategies. This revenue-weighted analysis incorporates financial data directly into service analysis, focusing on companies with multiple services to understand the economic value of service combinations. The report generates financial metrics including total revenue, percentages, and distributions, all exported to CSV format for financial modeling and further analysis.

**Note**: This report requires financial data to be available in your source tables (referenced as `fol_fb5ytotal` in the example script).


## When to Use This Report

Run Report_FinancialAnalysis when you need value-based prioritization to focus on high-revenue opportunities rather than just counting service combinations. The report is essential for financial modeling when building revenue forecasts for service adoption patterns, and for ROI analysis to understand the revenue impact of cross-sell efforts. Use it for executive reporting when presenting financially-focused insights to leadership, and for account prioritization when identifying high-value accounts for service expansion. This report is typically run during initial analysis and then revisited during quarterly financial reviews.


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

The script retrieves active Revenue Services and parses the `vs360_selectedcombinations` JSON from each service to extract practice area identifiers. It builds bidirectional mappings between Revenue Services and Practice Areas, enabling efficient lookup in both directions during financial data aggregation.

#### Phase 2: Load Matched Relationships

The script loads matched company-service data and groups companies by their service combinations, filtering the results to include only combinations with two or more services since single-service scenarios don't provide cross-sell insights.

#### Phase 3: Query Financial Data

For each service combination, the script queries the source table (such as vs360_job) and filters by company ID and the practice area IDs present in the combination. It retrieves the financial field value and aggregates revenue per company per service, building a comprehensive financial picture of each service pattern.

#### Phase 4: Calculate Financial Metrics

The script calculates multiple levels of financial aggregation. Group Total Financial represents the sum of all revenue across all companies in a specific service combination. Group Service Financial shows the sum of revenue for one particular service across all companies in that combination. Company Total Financial provides the total revenue for an individual company across all their services. Service Financial Value captures the revenue for one specific service for one specific company. Finally, Financial Percentage expresses each service's revenue as a percentage of the company's total revenue across all services.

#### Phase 5: Export to CSV

All calculated financial data is exported in a detailed breakdown format that enables sophisticated analysis in Excel or other analytical tools.


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


## Interpreting Results

### Group-Level Analysis

GroupTotalFinancial represents the total revenue from all companies sharing a specific service combination, indicating the total economic value of that service pattern. Higher values identify more valuable combinations that warrant focused attention and resources. GroupServiceFinancial shows the total revenue for one service across all companies in the group, revealing which service drives the most revenue within each combination and helping prioritize services within bundling strategies.

For example, a service combination of Tax + Audit + Advisory might show a GroupTotalFinancial of $5,250,000 with individual service contributions of Tax at $2,100,000 (40%), Audit at $1,890,000 (36%), and Advisory at $1,260,000 (24%). This reveals that Tax is the highest-value service in this particular combination.

### Company-Level Analysis

FinancialCompanyTotal identifies high-value accounts by showing the total revenue from each company, enabling you to prioritize cross-sell efforts toward high-revenue companies where the potential impact is greatest. FinancialValue shows the revenue contribution from one service for a specific company, helping you understand which services drive each company relationship. FinancialPercentage expresses each service's revenue as a percentage of the company's total, with a dominant service showing more than 50%, a balanced portfolio showing roughly 33% each for three services, and a minor service representing less than 20%.

For example, Company ABC with a total of $250,000 might show Tax at $150,000 (60%), Audit at $75,000 (30%), and Advisory at $25,000 (10%). This pattern indicates that Tax is the primary relationship driver for this company, suggesting that maintaining and growing the Tax relationship is critical to retaining this account.


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


## Advanced Analysis Techniques

Revenue potential calculation involves identifying gaps where companies are missing services that similar companies typically purchase. For example, if Company A has Tax ($150K) and Audit ($75K) but no Advisory, and similar companies with all three services average $50K in Advisory revenue, this represents a $50K revenue potential if Company A adds Advisory services. Aggregate this analysis across all companies with similar gaps to calculate total potential revenue from cross-sell initiatives.

Service bundle pricing can be informed by analyzing typical revenue splits. If the typical revenue split in a Tax+Audit+Advisory combination shows Tax at $150K (40%), Audit at $135K (36%), and Advisory at $90K (24%) for a total of $375K, you might design a bundle priced at $350K, offering a 7% discount for purchasing all three services together.

ROI forecasting uses the financial data to model revenue impact of cross-sell campaigns. If you currently have 100 companies with Tax only, each averaging $100K in Tax revenue for $10M total, and you expect 50% to add Audit services averaging $75K each, the additional revenue potential is 50 × $75K = $3.75M. This can then be compared to the cost of the cross-sell campaign to calculate ROI.


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


## Best Practices

1. **Validate financial data**: Spot-check results against source system
2. **Use with other reports**: Combine with ServicesMatrix for complete picture
3. **Regular updates**: Re-run after significant financial data changes
4. **Segmented analysis**: Filter by time period, region, etc. for targeted insights
5. **Act on insights**: Use to prioritize actual sales and marketing efforts


## Integration with Business Strategy

The financial analysis integrates with sales strategy by identifying high-value combinations as sales bundle priorities, focusing account management on high-revenue accounts, and targeting service mix imbalances as cross-sell opportunities. For marketing strategy, promote high-value combinations in campaigns, create case studies from high-revenue multi-service accounts, and target segments with the highest revenue potential. In financial planning, use the data for revenue forecasting based on service adoption patterns, allocate budgets for service development focusing on high-revenue services, and build ROI models for cross-sell initiatives.
