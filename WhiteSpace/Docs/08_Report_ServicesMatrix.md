# Report: Report_ServicesMatrix.xml

## Purpose

Report_ServicesMatrix creates a correlation matrix showing the percentage of companies that have both Service A and Service B for all service combinations. This reveals which services are most commonly purchased together, enabling targeted cross-selling and upselling strategies.

**Key Characteristics**:
- **Correlation analysis**: Shows service-to-service relationships
- **Percentage-based**: Calculates adoption rates for service pairs
- **Matrix format**: Easy-to-read cross-reference table
- **CSV export**: Ready for Excel pivot tables and visualization


## When to Use This Report

Run Report_ServicesMatrix when:
- **After RevenueServiceProcessing**: Matched company-service data must exist
- **Cross-sell analysis**: Identifying which services to offer together
- **Service bundling**: Designing service packages based on data
- **Sales enablement**: Providing reps with "next best service" guidance
- **Product strategy**: Understanding service dependencies and affinities

**Typical usage**: Initial analysis phase and periodic reviews (quarterly/annually)


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
- Retrieves all active Revenue Services
- Loads matched relationships from `vs360_account_vs360_revenueservice_matched`
- Orders by Revenue Service ID for consistency

#### Phase 2: Build Company Mappings
Creates dictionary of which services each company has:
```
Company-123: [Service-A, Service-B, Service-D]
Company-456: [Service-A, Service-C]
Company-789: [Service-B, Service-C, Service-D]
```

#### Phase 3: Build Service-to-Service Matrices
For each service, creates dictionaries tracking:
- Which other services co-occur
- Which companies have both services

#### Phase 4: Calculate Correlations
For each service pair:
- Count companies with Service A
- Count companies with both A and B
- Calculate percentage: (Both ÷ Total A) × 100

#### Phase 5: Generate CSV
Creates one row per service with columns for all services showing correlation percentages.


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

**Row perspective** (Service A row):
- 100% of companies with Service A have Service A (always 100%)
- 65.33% of companies with Service A also have Service B (98 companies)
- 42.00% of companies with Service A also have Service C (63 companies)
- Total companies with Service A: 150

**Key insight**: "If a company has Service A, there's a 65% chance they also have Service B"


## Interpreting Results

### High Correlation (70%+ overlap)

**Meaning**: Services are strongly linked
- Often purchased together
- May be complementary offerings
- Part of a natural bundle

**Action**:
- Create service packages/bundles
- Train sales to present together
- Consider group consolidation
- Upsell one when other is purchased

**Example**:
```
Tax Services → Audit Services: 85%
```
*85% of companies with Tax also have Audit → Strong bundle opportunity*

### Medium Correlation (30-70% overlap)

**Meaning**: Moderate association
- Sometimes purchased together
- Situational relationship
- Potential cross-sell opportunity

**Action**:
- Targeted cross-sell campaigns
- Understand why some buy both, others don't
- Identify company characteristics that predict both

**Example**:
```
Consulting → Advisory: 55%
```
*Some companies buy both, some just one → Investigate patterns*

### Low Correlation (<30% overlap)

**Meaning**: Weak or no association
- Typically purchased separately
- Different use cases
- Different customer segments

**Action**:
- Understand why these are separate
- Don't force bundling
- May serve different segments

**Example**:
```
Tax Services → IT Consulting: 15%
```
*Different services for different needs → Keep separate*

### Asymmetric Correlations

Note that correlations can be asymmetric:
```
Service A → Service B: 80%
Service B → Service A: 40%
```

**Meaning**:
- Most companies with A also have B
- But only some companies with B have A
- B has a broader customer base than A

**Action**:
- Upsell B to A customers (high probability)
- Selective upsell of A to B customers (need targeting)


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

Run the report for different company segments:
1. Filter companies by segment (e.g., industry)
2. Modify script to only include filtered companies
3. Compare correlations across segments

**Insight**: Service correlations may differ by segment
- Technology companies: Different pattern than Healthcare
- Large companies: Different than small companies

### Time-Based Analysis

Run the report at different time periods:
- Initial implementation
- 6 months later
- Annual comparison

**Track**:
- Are correlations increasing (successful bundling)?
- Are new patterns emerging?
- Are cross-sell efforts working?

### Gap Analysis

Identify the biggest gaps:
```
Service A has 200 companies
Service B has 180 companies
A → B correlation: 60% (120 companies have both)
Gap: 80 companies with A but not B
```

Prioritize cross-sell efforts by gap size.


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


