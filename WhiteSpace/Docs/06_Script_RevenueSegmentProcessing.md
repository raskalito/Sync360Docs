# Script: RevenueSegmentProcessing.xml

## Purpose

RevenueSegmentProcessing analyzes service adoption patterns across different company segments such as industry, size, or region. The script calculates comprehensive statistics showing how each service performs within each segment value, enabling targeted marketing and business development strategies. This segment-based analysis breaks down service adoption by company characteristics, generating statistical output with matched and unmatched percentages for each segment. The script offers flexible segmentation capabilities, allowing you to analyze any field on the company record, and it performs automatic calculations to update counts and percentages based on the current state of your data.


## When to Use This Script

Run RevenueSegmentProcessing after completing RevenueServiceProcessing, as it requires matched and unmatched company-service data to exist first. Use this script when you need segment analysis to understand service adoption patterns by company characteristics, or when you need targeted insights to identify which segments have higher or lower adoption rates. The script is valuable for marketing strategy development when building segment-specific campaigns or offers. Typically, this script is run less frequently than RevenueServiceProcessing, often on a weekly or monthly basis, as segment patterns change more slowly than individual company-service relationships.


## What You Need Before Running

### Prerequisites
1. **RevenueServiceProcessing completed**: Must have matched company-service data
2. **Revenue Segment records created**: Defined segments in vs360_revenuesegment table
3. **Segment fields populated**: Company records must have values in segment fields
4. **Configuration.xml**: Properly configured

### Creating Revenue Segment Records

Before running this script, you must manually create Revenue Segment records in Dataverse:

**Example Revenue Segment**:
- **Name**: "Industry"
- **Group**: 0 (must match a Revenue Service group)
- **Query**: JSON defining which field to analyze
- **Total**: (will be calculated by script)
- **Has Value**: (will be calculated by script)

**Query JSON Format**:
```json
{
    "rules": {
        "field": "industrycode"
    }
}
```

This tells the script to segment companies by their "industrycode" field.


## What the Script Does

### High-Level Process Flow

```
1. Load all Revenue Services (organized by group)
2. Load all Revenue Segments
3. Load existing Segment Values and Dispersions
4. For each Revenue Segment:
   - Determine which field to analyze
5. For each group:
   - Load companies with matched services in that group
   - For each segment in that group:
     - Collect values from the segment field
     - Count companies per value
     - Calculate service adoption per value
6. Create/Update Revenue Segment Values
7. Create Revenue Segment Dispersions (statistics per service per value)
```

### Detailed Operations

#### Phase 1: Load Data

The script begins by loading all active Revenue Services and organizing them by group. It then loads all Revenue Segment definitions, existing Revenue Segment Values from the vs360_rsvalue table, and existing Revenue Segment Dispersions from the vs360_rsdispersion table. This comprehensive data loading establishes the baseline for the analysis.

#### Phase 2: Parse Segment Configurations

For each Revenue Segment, the script parses the JSON configuration from the `vs360_query` field and extracts the field name that should be analyzed. It creates a mapping between segments and their associated groups to ensure proper segmentation across the service landscape.

#### Phase 3: Analyze Companies

For each Revenue Service group, the script queries all companies that have matched services within that group. For each segment field defined for the group, it extracts the unique values present in the data, counts the total number of companies with each value, and tracks which services each company has. This creates a comprehensive map of segment values and service adoption.

#### Phase 4: Calculate Statistics

For each unique segment value discovered, the script calculates the total number of companies with that value. Then, for each service within the group, it determines the matched count (companies with this segment value AND this service), the unmatched count (companies with this segment value but NOT this service), and the corresponding percentages. These statistics reveal adoption patterns within each segment.

#### Phase 5: Write Results

The final phase writes all calculated data back to Dataverse. The script updates Revenue Segment totals with overall statistics, creates or updates Revenue Segment Values with company counts for each discovered value, and creates Revenue Segment Dispersions containing the detailed statistics for each service-segment combination.


## Revenue Segment Configuration

### Creating a Revenue Segment

**Manual steps in Dataverse**:

1. Create new Revenue Segment record
2. Set **Name**: Descriptive name (e.g., "Industry", "Region", "Size")
3. Set **Group**: Number matching a Revenue Service group
4. Set **Query**: JSON specifying the field

**Query JSON Examples**:

```json
{
    "rules": {
        "field": "industrycode"
    }
}
```

```json
{
    "rules": {
        "field": "address1_stateorprovince"
    }
}
```

```json
{
    "rules": {
        "field": "numberofemployees"
    }
}
```

### Multiple Segments per Group

You can create multiple segments for the same group:

| Name | Group | Field |
|------|-------|-------|
| Industry | 0 | industrycode |
| Region | 0 | address1_stateorprovince |
| Size | 0 | numberofemployees |

The script will analyze all segments for the group.


## Expected Results

### Revenue Segment Updates
The Revenue Segment record is updated with:
- **vs360_total**: Total companies analyzed in this segment
- **vs360_hasvalue**: Number of companies with a non-null value for this field

### Revenue Segment Values Created
For each unique value found in the segment field:

**Example** (Industry segment):
| Name | Total | Segment |
|------|-------|---------|
| technology | 150 | Industry |
| healthcare | 85 | Industry |
| manufacturing | 62 | Industry |
| (null) | 10 | Industry |

### Revenue Segment Dispersions Created
For each value and each service, statistics are calculated:

**Example** (Service "Consulting" in "Technology" industry):
| Segment Value | Service | Matched | Unmatched | Matched % | Unmatched % |
|---------------|---------|---------|-----------|-----------|-------------|
| technology | Consulting | 120 | 30 | 80.0% | 20.0% |
| healthcare | Consulting | 20 | 65 | 23.5% | 76.5% |
| manufacturing | Consulting | 50 | 12 | 80.6% | 19.4% |

**Interpretation**:
- 80% of technology companies have Consulting services
- Only 23.5% of healthcare companies have Consulting services
- Manufacturing has high adoption (80.6%)


## Using Segment Data

The segment statistics enable sophisticated business development targeting. High adoption segments suggest focusing on retention and upselling opportunities. For example, if technology companies have 80% adoption of Consulting services, you should concentrate on upselling complementary services to these technology clients who already trust your consulting capabilities. Conversely, low adoption segments indicate new customer acquisition opportunities. Healthcare companies with only 23.5% adoption of Consulting services represent a significant untapped market for targeted sales efforts.

For market analysis, segment data helps identify best-fit segments by revealing which industries, regions, or company sizes have the highest adoption rates. This shows where the solution is most valuable and where marketing efforts should concentrate. Gap analysis using this data identifies underserved segments, untapped potential, and situations where different positioning or messaging might be needed.

From a product strategy perspective, segment data reveals service-segment fit, showing which services work well in which segments. This information can guide decisions about whether certain services should be marketed differently per segment, or whether there are segment-specific needs that require service customization or new offerings.


## Script Execution

### Running the Script

1. **Create Revenue Segments**:
   - Define segments in Dataverse
   - Set group numbers
   - Configure query JSON

2. **Execute**:
   - Load RevenueSegmentProcessing.xml in Sync360
   - Run the script
   - Monitor progress (includes logging)

3. **Review Results**:
   - Check Revenue Segment Values table
   - Review Revenue Segment Dispersions
   - Analyze statistics

### Execution Time

Moderate processing time:
- Depends on number of companies with matched services
- Number of segments and unique values
- Complexity of segment fields

**Expected**: 1-5 minutes for typical datasets


## Output Data Structure

### Example Complete Dataset

**Revenue Segment**:
- Name: Industry
- Group: 0
- Total: 297 companies
- Has Value: 287 companies (10 have null industry)

**Revenue Segment Values** (vs360_rsvalue):
| Name | Total |
|------|-------|
| technology | 150 |
| healthcare | 85 |
| manufacturing | 52 |

**Revenue Segment Dispersions** (vs360_rsdispersion):
| Value | Service | Matched | Unmatched | Matched % | Unmatched % |
|-------|---------|---------|-----------|-----------|-------------|
| technology | Service A | 120 | 30 | 80.0% | 20.0% |
| technology | Service B | 90 | 60 | 60.0% | 40.0% |
| healthcare | Service A | 20 | 65 | 23.5% | 76.5% |
| healthcare | Service B | 40 | 45 | 47.1% | 52.9% |


## Advanced Usage

The script supports dynamic segmentation based on calculated fields. You can create segments using any field that exists on the company record, including custom calculated fields. Simply reference the field name in your segment query JSON and ensure the field is populated on company records before running the script.

For numeric fields like revenue or employee count, values are stored as-is in the segment values. However, for more meaningful analysis, consider creating a calculated field with defined ranges such as "Small (1-50)", "Medium (51-200)", and "Large (201+)". This makes the segment data more interpretable and actionable.

Multi-dimensional analysis is possible by creating multiple segments to analyze from different angles. You can create both Industry and Region segments to understand the intersection of these dimensions, or combine Size and Industry segments to see how company size affects service adoption within each industry. Run all segments and compare results across dimensions to gain deeper insights.


## Integration with Reports

The Report_SegmentsData.xml script exports the segment analysis data to CSV format for further analysis (see Report_SegmentsData documentation). Use this exported segment data to create dashboards in your BI tools, build Excel pivot tables for interactive exploration, perform statistical analysis to identify trends, and visualize adoption patterns through charts and graphs.


## Troubleshooting

### Issue: No Segment Values created
**Possible causes**:
- Revenue Segment query JSON incorrect
- Field name doesn't exist on company table
- No companies have values in the field
- RevenueServiceProcessing not run yet

**Debug**:
1. Verify field exists: Check company records in Dataverse
2. Test field has values: Query companies manually
3. Validate JSON syntax
4. Confirm matched companies exist

### Issue: Incorrect statistics
**Check**:
- RevenueServiceProcessing run recently
- Matched/unmatched data is current
- Segment field values are correct
- Group numbers match between Revenue Service and Revenue Segment

### Issue: Values not lowercase
**Expected behavior**: Script converts values to lowercase and trims whitespace
```
"Technology" → "technology"
"  Healthcare " → "healthcare"
```

If case matters in your analysis, this is by design for consistency.

### Issue: Many null values
**Causes**:
- Company records missing segment field values
- Field not populated in source data

**Solutions**:
- Populate missing data
- Create "Unknown" default value
- Filter out nulls in reporting


## Best Practices

1. **Populate segment fields**: Ensure companies have values before running
2. **Use consistent values**: Standardize segment field values (avoid variations like "Tech" vs "Technology")
3. **Limit segments initially**: Start with 1-2 key segments, expand as needed
4. **Run after service processing**: Ensure matched data is current
5. **Regular updates**: Schedule if segment field values change frequently
6. **Validate statistics**: Spot-check percentages against actual data


## Common Segment Examples

### Industry Segmentation
```json
{"rules": {"field": "industrycode"}}
```
**Use**: Understand industry-specific adoption patterns

### Geographic Segmentation
```json
{"rules": {"field": "address1_stateorprovince"}}
```
**Use**: Regional marketing and sales strategies

### Size Segmentation
```json
{"rules": {"field": "company_size_category"}}
```
**Use**: Target by company size

### Lifecycle Stage
```json
{"rules": {"field": "lifecycle_stage"}}
```
**Use**: Different strategies for prospects, customers, advocates
