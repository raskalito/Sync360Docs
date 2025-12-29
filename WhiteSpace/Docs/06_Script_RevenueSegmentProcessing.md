# Script: RevenueSegmentProcessing.xml

## Purpose

RevenueSegmentProcessing analyzes service adoption patterns across different company segments (e.g., industry, size, region). It calculates statistics showing how each service performs within each segment value, enabling targeted marketing and business development strategies.

**Key Characteristics**:
- **Segment-based analysis**: Breaks down service adoption by company characteristics
- **Statistical output**: Calculates matched/unmatched percentages per segment
- **Flexible segmentation**: Can analyze any field on the company record
- **Automatic calculations**: Updates counts and percentages based on current data

---

## When to Use This Script

Run RevenueSegmentProcessing when:
- **After RevenueServiceProcessing**: Matched/unmatched data must exist first
- **Segment analysis needed**: You want to understand service adoption by segment
- **Targeted insights**: Need to identify which segments have higher/lower adoption rates
- **Marketing strategy**: Building segment-specific campaigns or offers
- **Periodic analysis**: Updating segment statistics as data changes

**Typical frequency**: Weekly or monthly (less frequent than RevenueServiceProcessing)

---

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

---

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
- Loads all active Revenue Services (grouped)
- Loads all Revenue Segments
- Loads existing Revenue Segment Values (vs360_rsvalue)
- Loads existing Revenue Segment Dispersions (vs360_rsdispersion)

#### Phase 2: Parse Segment Configurations
For each Revenue Segment:
- Parses the JSON from `vs360_query`
- Extracts the field name to analyze
- Maps segments to their groups

#### Phase 3: Analyze Companies
For each Revenue Service group:
- Queries companies with matched services
- For each segment field in that group:
  - Extracts unique values
  - Counts total companies with each value
  - Tracks which services each company has

#### Phase 4: Calculate Statistics
For each unique segment value:
- **Total companies** with this value
- **Per service**:
  - Matched count (companies with this value AND this service)
  - Unmatched count (companies with this value but NOT this service)
  - Matched percentage
  - Unmatched percentage

#### Phase 5: Write Results
- Updates Revenue Segment totals
- Creates/updates Revenue Segment Values
- Creates Revenue Segment Dispersions with statistics

---

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

---

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

---

## Using Segment Data

### Business Development Targeting

**High adoption segments**: Focus on retention and upselling
```
Technology companies have 80% adoption of Consulting
→ Upsell complementary services to technology clients
```

**Low adoption segments**: Focus on new customer acquisition
```
Healthcare companies have only 23.5% adoption of Consulting
→ Target healthcare companies for Consulting services
```

### Market Analysis

**Identify best-fit segments**:
- Which industries/regions have highest adoption?
- Where is the solution most valuable?
- Where should marketing focus?

**Gap analysis**:
- Which segments are underserved?
- Where is there untapped potential?
- Which segments need different positioning?

### Product Strategy

**Service-segment fit**:
- Which services work well in which segments?
- Should certain services be marketed differently per segment?
- Are there segment-specific needs?

---

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

---

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

---

## Advanced Usage

### Dynamic Segmentation

Create segments based on calculated fields:
```json
{
    "rules": {
        "field": "custom_calculated_segment"
    }
}
```

Ensure the field exists and is populated on company records.

### Numeric Segmentation

For numeric fields (like revenue, employee count):
- Values are stored as-is
- Consider creating a calculated field with ranges:
  - "Small (1-50)", "Medium (51-200)", "Large (201+)"

### Multi-Dimensional Analysis

Create multiple segments to analyze from different angles:
- Industry × Region matrix (run both segments)
- Size × Industry matrix
- Compare results across dimensions

---

## Integration with Reports

The Report_SegmentsData.xml script exports this data to CSV for further analysis (see Report_SegmentsData documentation).

Use segment data to:
- Create dashboards
- Build Excel pivot tables
- Perform statistical analysis
- Visualize adoption patterns

---

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

---

## Best Practices

1. **Populate segment fields**: Ensure companies have values before running
2. **Use consistent values**: Standardize segment field values (avoid variations like "Tech" vs "Technology")
3. **Limit segments initially**: Start with 1-2 key segments, expand as needed
4. **Run after service processing**: Ensure matched data is current
5. **Regular updates**: Schedule if segment field values change frequently
6. **Validate statistics**: Spot-check percentages against actual data

---

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

---

**Next**: Run Report scripts to export and analyze the segment data, or proceed to PostRevenueGAGroupCreation if creating new service groups.
