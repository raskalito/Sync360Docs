# Report: Report_ServicesMatrix.xml

## Purpose

Report_ServicesMatrix creates a correlation matrix showing the percentage of companies that have both Service A and Service B for all service combinations. This reveals which services are most commonly purchased together, enabling targeted cross-selling and upselling strategies. The report provides correlation analysis showing service-to-service relationships with percentage-based calculations of adoption rates for service pairs. The output is formatted as an easy-to-read cross-reference matrix and exported to CSV for Excel pivot tables and visualization.


## When to Use This Report

Run Report_ServicesMatrix after RevenueServiceProcessing has completed, as matched company-service data must exist first. The report is valuable for cross-sell analysis when identifying which services to offer together, and for service bundling when designing service packages based on actual data rather than assumptions. Use it for sales enablement by providing sales representatives with "next best service" guidance informed by patterns in your customer base. From a product strategy perspective, the matrix helps you understand service dependencies and affinities, revealing which services naturally complement each other in customer purchasing behavior. This is typically run during the initial analysis phase and then periodically reviewed on a quarterly or annual basis.


## What You Need Before Running

### Prerequisites
1. **RevenueServiceProcessing completed**: Must have matched company-service data
2. **Multiple services**: Need at least 2+ services for meaningful correlations
3. **Write access to output directory**: Default C:\temp\

### Recommended Data Volume
For meaningful results:
- At least 50+ companies with matched services
- Multiple companies with 2+ services
- Sufficient variation in service combinations


## What the Script Does

### Process Flow

```
1. Load all active Revenue Services
2. Load matched company-service relationships
3. Build company-to-services mapping
4. For each Revenue Service (Service A):
   - Count total companies with Service A
   - For each other Revenue Service (Service B):
     - Count companies with BOTH A and B
     - Calculate percentage: (Both / Total A) * 100
5. Build matrix with services as rows and columns
6. Export to CSV
```

### Detailed Operations

#### Phase 1: Load Data

The script retrieves all active Revenue Services from Dataverse and loads matched relationships from the `vs360_account_vs360_revenueservice_matched` table, ordering them by Revenue Service ID for consistency in the output matrix.

#### Phase 2: Build Company Mappings

The script creates a comprehensive dictionary mapping each company to all the services they have. For example, Company-123 might map to [Service-A, Service-B, Service-D], while Company-456 maps to [Service-A, Service-C], and Company-789 to [Service-B, Service-C, Service-D]. This complete mapping enables efficient correlation calculations.

#### Phase 3: Build Service-to-Service Matrices

For each service, the script creates dictionaries tracking which other services co-occur and which specific companies have both services. This allows the script to answer questions like "Which companies have both Tax and Audit services?"

#### Phase 4: Calculate Correlations

For each service pair, the script counts how many companies have Service A, counts how many companies have both A and B, and calculates the percentage as (Both ÷ Total A) × 100. This percentage reveals how likely it is that a company with Service A also has Service B.

#### Phase 5: Generate CSV

The script creates one row per service with columns for all services showing correlation percentages, resulting in a complete matrix that's easy to visualize in Excel or other tools.


## Configuration

### Output File Path

```xml
<set var="dumpFile">C:\temp\RevenueServiceAndOtherServices.csv</set>
```

**To change output location**:
```xml
<set var="dumpFile">D:\Reports\ServicesCorrelation.csv</set>
```

Ensure directory exists and is writable.


## CSV Output Structure

### Format

The CSV is a matrix where:
- **Rows**: Each Revenue Service (source)
- **Columns**: Each Revenue Service (target) + TotalRecords
- **Cell values**: Percentage and count in format: "XX.XX% (count)"

### Column Definitions

| Column | Description |
|--------|-------------|
| **Service** | The source service (row header) |
| **[Service Name]** | Percentage of source service companies that also have this service |
| **TotalRecords** | Total companies with the source service |

### Example Output

```csv
Service,Service A,Service B,Service C,TotalRecords
Service A,100.00% (150),65.33% (98),42.00% (63),150
Service B,75.38% (98),100.00% (130),51.54% (67),130
Service C,58.88% (63),62.62% (67),100.00% (107),107
```

### Reading the Matrix

From Service A's row perspective, 100% of companies with Service A have Service A (always 100% for the diagonal), 65.33% of companies with Service A also have Service B (98 companies), and 42% of companies with Service A also have Service C (63 companies), with a total of 150 companies having Service A. The key insight here is that if a company has Service A, there's a 65% chance they also have Service B, making Service B a strong cross-sell candidate for Service A customers.


## Interpreting Results

### High Correlation (70%+ overlap)

High correlation means services are strongly linked, often purchased together, suggesting they may be complementary offerings or part of a natural bundle. When you see this pattern, consider creating service packages or bundles, training sales teams to present these services together, evaluating whether group consolidation makes sense, and developing upsell campaigns to offer one service when the other is purchased. For example, if Tax Services correlates with Audit Services at 85%, this indicates that 85% of companies with Tax also have Audit, presenting a strong bundle opportunity.

### Medium Correlation (30-70% overlap)

Medium correlation indicates a moderate association where services are sometimes purchased together but the relationship is situational. This suggests potential cross-sell opportunities that require more targeting. Focus on understanding why some companies buy both while others don't, identify company characteristics that predict purchasing both services, and develop targeted cross-sell campaigns. For instance, if Consulting correlates with Advisory at 55%, investigate what patterns distinguish the 55% who buy both from the 45% who buy only one.

### Low Correlation (<30% overlap)

Low correlation signals a weak or non-existent association where services are typically purchased separately, likely serving different use cases or different customer segments. In these cases, understand why these services are separate, avoid forcing bundling that doesn't make sense, and recognize that they may serve fundamentally different customer needs. If Tax Services only correlates with IT Consulting at 15%, this reflects genuinely different services for different needs that should remain separate offerings.

### Asymmetric Correlations

Correlations can be asymmetric, revealing important market dynamics. If Service A → Service B shows 80% but Service B → Service A shows only 40%, this means most companies with A also have B, but only some companies with B have A, indicating that B has a broader customer base than A. In this situation, you should aggressively upsell B to A customers (high probability of success) but be more selective when upselling A to B customers, perhaps using targeting to identify which B customers are most likely to also need A.


## Using the Report Results

### Step 1: Import to Excel

1. Open CSV in Excel
2. Use conditional formatting to highlight high percentages
3. Create a heat map visualization

**Excel heat map**:
- Green: High correlation (70%+)
- Yellow: Medium correlation (30-70%)
- Red: Low correlation (<30%)

### Step 2: Identify Cross-Sell Opportunities

**High-value targets**:
- Services with high correlation but not 100%
- "Service A → Service B: 75%" means 25% of A customers don't have B
- These are qualified cross-sell leads

**Example analysis**:
```
Consulting Services → Advisory: 70% (210 of 300 companies)
→ 90 companies with Consulting but NO Advisory
→ Cross-sell Advisory to these 90 companies
```

### Step 3: Build Service Bundles

Identify natural bundles:
- Look for clusters of high correlations
- Group services frequently purchased together
- Create packages or offerings

**Example**:
```
Services A, B, C all have 75%+ correlations with each other
→ Create "Complete Package" with A+B+C
```

### Step 4: Sales Enablement

Create "next best service" guides:
```
If customer has: Tax Services
Most likely to buy next:
1. Audit Services (85%)
2. Compliance (72%)
3. Advisory (58%)
```


## Advanced Analysis Techniques

### Segmented Correlation Analysis

Run the report for different company segments by filtering companies by segment (e.g., industry), modifying the script to only include filtered companies, and comparing correlations across segments. Service correlations may differ by segment - technology companies might show different patterns than healthcare companies, and large companies different patterns than small ones.

### Time-Based Analysis

Run the report at different time periods to track how correlations evolve. Compare initial implementation to six months later to annual comparisons. Track whether correlations are increasing (indicating successful bundling efforts), identify new patterns emerging over time, and measure whether cross-sell efforts are working.

### Gap Analysis

Identify the biggest gaps by examining high correlations to find the largest absolute numbers of companies that could be cross-sold. For example, if Service A has 200 companies and Service B has 180 companies with a 60% correlation (120 companies have both), the gap is 80 companies with A but not B. Prioritize cross-sell efforts by gap size to maximize impact.


## Script Execution

### Running the Script

1. **Prepare**:
   - Ensure C:\temp\ directory exists
   - RevenueServiceProcessing has been run recently

2. **Execute**:
   - Load Report_ServicesMatrix.xml in Sync360
   - Run the script
   - Wait for completion (typically 30 seconds - 2 minutes)

3. **Locate Output**:
   - Open C:\temp\RevenueServiceAndOtherServices.csv

4. **Analyze**:
   - Import to Excel
   - Apply formatting and create visualizations
   - Identify opportunities


## Example Use Cases

### Use Case 1: Cross-Sell Campaign

**Goal**: Increase Advisory Services adoption

**Steps**:
1. Run Report_ServicesMatrix
2. Identify which service customers most commonly also have Advisory
3. Example result: "Tax → Advisory: 65%"
4. **Action**: Target the 35% of Tax customers without Advisory

**Campaign**: "Tax customers: Add Advisory services"

### Use Case 2: Service Bundle Design

**Goal**: Create a "Professional Services" package

**Steps**:
1. Review matrix for high correlations
2. Find services with 70%+ mutual correlations
3. Example: Consulting, Advisory, Compliance all 75%+ correlated
4. **Action**: Create "Professional Services Bundle" with these three

**Benefit**: Increase revenue per customer, simplify sales process

### Use Case 3: Sales Prioritization

**Goal**: Help sales reps know what to pitch next

**Steps**:
1. Generate matrix
2. For each service, rank others by correlation percentage
3. Create "Next Best Service" reference cards
4. **Tool**: Sales reps look up current service, see top recommendations

**Result**: Data-driven sales conversations

### Use Case 4: Service Strategy

**Goal**: Decide which services to invest in

**Steps**:
1. Review correlations
2. Identify services with high correlations to multiple others
3. These are "anchor services" - drive adoption of others
4. **Action**: Invest more in anchor services

**Example**: Tax services drives Audit and Compliance adoption → Focus on Tax growth


## Troubleshooting

### Issue: All percentages are 100% or 0%
**Causes**:
- All companies have all services (100%)
- No companies have multiple services (0%)
- Data insufficient for analysis

**Solutions**:
- Verify diverse service adoption in matched data
- Check that multiple companies exist with varied service mixes

### Issue: Missing services in output
**Possible causes**:
- Service has no companies (0 total records)
- Service is inactive
- Matched data incomplete

**Debug**:
- Check matched relationships table
- Verify all services have at least some companies

### Issue: Percentages don't seem accurate
**Check**:
- RevenueServiceProcessing run recently
- Matched data is current
- Manually verify a few examples against Dataverse data


## Best Practices

1. **Run after processing**: Ensure matched data is current
2. **Regular updates**: Quarterly or after major data changes
3. **Combine with other reports**: Use alongside Report_PotentialGroups for complete picture
4. **Validate with business logic**: Ensure correlations make business sense
5. **Act on insights**: Use data to drive actual cross-sell and bundling efforts
6. **Track over time**: Monitor how correlations change as strategies are implemented


## Visualization Tips

### Excel Heat Map
1. Select data range (exclude headers)
2. Conditional Formatting → Color Scales
3. Green (high) to Red (low)

### Pivot Table Analysis
1. Import CSV
2. Create pivot table
3. Filter by percentage thresholds
4. Identify top correlations

### Chart Creation
1. Select a service row
2. Create bar chart of correlations
3. Visualize "If customer has X, probability of Y"
