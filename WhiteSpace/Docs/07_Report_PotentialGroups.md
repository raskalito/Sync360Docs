# Report: Report_PotentialGroups.xml

## Purpose

Report_PotentialGroups identifies natural groupings of services based on actual purchase patterns in your data. The report analyzes which services are frequently purchased together and suggests new service groups that could be created for more targeted analysis. This pattern discovery is data-driven, based on actual company purchase behavior rather than assumptions, and it exports results to a CSV file for easy review. The report includes group suggestions with recommendations about which patterns warrant creation of new service groups based on statistical significance.


## When to Use This Report

Run Report_PotentialGroups during the initial analysis phase after your first RevenueServiceProcessing run to understand natural service clustering. Use it when evaluating if your current groups are optimal and considering restructuring, or when looking for non-obvious service relationships that might not be apparent from business knowledge alone. The report is valuable before restructuring your service groups to ensure the new structure aligns with actual patterns. Run it periodically, such as quarterly or annually, to identify emerging patterns as your service mix and customer base evolve. This is typically a one-time report during initial implementation, then revisited periodically as needed.


## What You Need Before Running

### Prerequisites
1. **RevenueServiceProcessing completed**: Must have matched company-service data
2. **Multiple companies with multiple services**: Patterns require sufficient data
3. **Write access to output directory**: Default C:\temp\

### Recommended Data Volume
For meaningful results:
- At least 50+ companies with matched services
- At least 3+ services defined
- Multiple companies purchasing multiple services


## What the Script Does

### Process Flow

```
1. Load all active Revenue Services
2. Load matched company-service relationships
3. Group companies by their service combinations
4. Identify patterns where:
   - Multiple companies (>1) have the exact same set of services
   - These companies have 2+ services
5. Calculate statistics for each pattern:
   - Number of companies with this combination
   - List of services in the combination
   - Service count
6. Generate suggestions for new groups
7. Export to CSV file
```

### Detailed Operations

#### Phase 1: Load Data

The script retrieves all active Revenue Services from Dataverse and loads matched relationships from the `vs360_account_vs360_revenueservice_matched` table, establishing the foundation for pattern analysis.

#### Phase 2: Create Company Groupings

For each company, the script builds a unique key from all their matched services and groups companies by identical service combinations. For example, if Company A has Services X, Y, and Z, it gets a key of "XYZ". Company B with the same services gets the same key and is grouped together, while Company C with only Services X and Y gets a different key "XY" and belongs to a different group.

#### Phase 3: Filter and Analyze

The script keeps only patterns where more than one company has the same combination (ensuring the pattern isn't just an outlier) and where companies have two or more services in the combination (single-service "patterns" aren't useful for grouping analysis). For each pattern, it calculates the group count showing how many companies share this combination, identifies which services are in the group, counts the services, and suggests a new group number based on your current maximum.

#### Phase 4: Generate Recommendations

The script creates a "UseThisGroup" flag based on a threshold, marking patterns with 100 or more companies as TRUE (recommended for group creation) and those with fewer companies as FALSE. It also generates JSON structures for creating new Revenue Services with the suggested group assignment, making it easy to implement the recommendations.

#### Phase 5: Export to CSV

All results are written to `C:\temp\dump.csv` for review and analysis outside of the Sync360 environment.


## Configuration

### Output File Path

```xml
<set var="dumpFile">C:\temp\dump.csv</set>
```

**To change output location**:
1. Edit the script
2. Modify the file path:
```xml
<set var="dumpFile">D:\Reports\PotentialGroups.csv</set>
```
3. Ensure directory exists and is writable


## CSV Output Structure

### Column Definitions

| Column | Description | Example |
|--------|-------------|---------|
| **GroupCount** | Number of companies with this service combination | 127 |
| **Services** | Semicolon-separated list of service names | "(0) Tax;(0) Audit;(0) Advisory" |
| **ServicesCount** | Number of services in combination | 3 |
| **UseThisGroup** | Recommendation flag (TRUE/FALSE) | TRUE |
| **NewRecords** | JSON array for creating new Revenue Service records | [{"name":"(5) Tax",...}] |

### Example Output

```csv
GroupCount,Services,ServicesCount,UseThisGroup,NewRecords
127,"(0) Tax;(0) Audit;(0) Advisory",3,TRUE,"[{...JSON...}]"
85,"(0) Tax;(0) Consulting",2,FALSE,"[{...JSON...}]"
156,"(0) Audit;(0) Advisory;(0) Compliance",3,TRUE,"[{...JSON...}]"
45,"(0) Tax;(0) Audit;(0) Advisory;(0) Compliance",4,FALSE,"[{...JSON...}]"
```

### Interpreting Results

**High GroupCount** indicates a strong pattern where many companies purchase this combination, making it a good candidate for creating a dedicated service group for focused analysis.

**UseThisGroup = TRUE** means the script recommends creating this as a new group based on the 100+ company threshold, suggesting it's a statistically significant pattern worth dedicated analysis.

**Services List** shows which services naturally cluster together, revealing cross-selling patterns and identifying service bundles that customers typically purchase as a set.


## Using the Report Results

### Step 1: Review the CSV

Open `C:\temp\dump.csv` in Excel or similar tool.

**Sort by**:
- **GroupCount** (descending): See most common patterns first
- **ServicesCount**: Analyze by bundle size
- **UseThisGroup**: Focus on recommended groups

### Step 2: Identify Meaningful Patterns

Ask:
- Does this service combination make business sense?
- Is this an intentional bundle or coincidence?
- Would separate analysis of this group provide value?
- Is the pattern stable or temporary?

### Step 3: Decide on New Groups

**Create a new group when**:
- High company count (100+ recommended)
- Services are logically related
- Separate analysis would yield insights
- Pattern is stable over time

**Don't create a group when**:
- Low company count (statistical noise)
- Services are unrelated (coincidental)
- Pattern is temporary or seasonal
- Adds complexity without value

### Step 4: Create New Revenue Services (Optional)

If you decide to create a new group:

**Option A: Use PostRevenueGAGroupCreation script**
- Edit the CSV to mark only desired groups as `UseThisGroup = TRUE`
- Run PostRevenueGAGroupCreation.xml (see documentation)
- Script reads CSV and creates Revenue Service records

**Option B: Manual creation**
- Use the pattern as guidance
- Manually create new Revenue Service records
- Assign them a new group number
- Copy JSON configurations from existing services


## Example Analysis Scenario

### Initial State
Group 0 contains all services:
- Tax Services
- Audit Services
- Advisory Services
- Consulting Services
- Compliance Services

### Report Results
```
Pattern 1: Tax + Audit + Advisory (150 companies)
Pattern 2: Consulting + Compliance (120 companies)
Pattern 3: Tax + Consulting (45 companies)
```

### Analysis
**Pattern 1**: Strong correlation, 150 companies
- Create Group 1: "Core Services" (Tax, Audit, Advisory)
- Focus on companies with one but not all

**Pattern 2**: Strong correlation, 120 companies
- Create Group 2: "Professional Services" (Consulting, Compliance)
- Separate analysis from core

**Pattern 3**: Lower count, possibly coincidental
- Don't create dedicated group
- Monitor in future analyses

### Actions
1. Create Group 1 with Tax, Audit, Advisory services
2. Create Group 2 with Consulting, Compliance services
3. Re-run RevenueServiceProcessing
4. Analyze each group independently


## Understanding the NewRecords JSON

The **NewRecords** column contains JSON for creating Revenue Service records:

```json
[
    {
        "name": "(5) Tax Services",
        "vs360_selectedcombinations": "{...original query JSON...}",
        "vs360_group": 5
    },
    {
        "name": "(5) Audit Services",
        "vs360_selectedcombinations": "{...original query JSON...}",
        "vs360_group": 5
    }
]
```

This JSON suggests a new group number (which auto-increments from your current maximum), creates names with the new group prefix, preserves the original query JSON from the source services, and is ready to be consumed by the PostRevenueGAGroupCreation script for automated group creation.


## Script Execution

### Running the Script

1. **Prepare**:
   - Ensure C:\temp\ directory exists (or modify path)
   - Verify RevenueServiceProcessing has been run

2. **Execute**:
   - Load Report_PotentialGroups.xml in Sync360
   - Run the script
   - Wait for completion (typically under 1 minute)

3. **Locate Output**:
   - Open C:\temp\dump.csv
   - Review the patterns

4. **Analyze**:
   - Import into Excel
   - Sort, filter, and analyze patterns
   - Identify groups to create


## Advanced Usage

### Adjusting the Threshold

The script uses a 100-company threshold for `UseThisGroup`:

```xml
<set var="csvRow['UseThisGroup']">{nowGroups[key]['l'].Count ge 100}</set>
```

**To adjust**:
- Change `100` to your preferred threshold
- Lower for smaller datasets (e.g., 20-50)
- Higher for larger datasets requiring stronger patterns (e.g., 200+)

### Filtering by Service Count

To only see bundles of 3+ services:
```xml
<if condition="nowGroups[key]['l'].Count gt 1 and nowGroups[key]['d'].Count ge 3">
    <!-- existing code -->
</if>
```

### Excluding Certain Services

If certain services create noise in the analysis:
```xml
<for var="rid" in="dictGrouping[aid]">
    <!-- Skip specific service -->
    <if condition="rid ne 'GUID-OF-SERVICE-TO-EXCLUDE'">
        <set var="key">{key}{rid}</set>
    </if>
</for>
```


## Troubleshooting

### Issue: Empty CSV file
**Possible causes**:
- No companies have multiple services
- Matched data doesn't exist
- All companies have unique service combinations

**Solutions**:
- Verify RevenueServiceProcessing has run
- Check that multiple companies exist with services
- Review matched relationships table

### Issue: Too many patterns
**Causes**:
- Large dataset with many service combinations
- Low threshold for pattern significance

**Solutions**:
- Increase the threshold (100 → 200+)
- Filter by ServicesCount (focus on larger bundles)
- Focus on UseThisGroup = TRUE only

### Issue: No patterns marked as UseThisGroup
**Causes**:
- No pattern has 100+ companies
- Dataset too small

**Solutions**:
- Lower the threshold in script
- Review patterns manually regardless of flag
- Consider dataset may be insufficient for grouping


## Best Practices

1. **Run early**: Execute during initial analysis phase
2. **Review critically**: Not all patterns warrant new groups
3. **Consider business logic**: Statistical patterns should align with business reality
4. **Start conservative**: Create 1-2 new groups initially, expand later
5. **Validate patterns**: Verify patterns are stable over multiple runs
6. **Document decisions**: Track why certain groups were/weren't created


## Integration with Other Scripts

### Workflow Integration

```
1. GenerateRevenueService → Create initial services (all in one group)
2. RevenueServiceProcessing → Process companies
3. Report_PotentialGroups → Identify natural groupings
4. PostRevenueGAGroupCreation → Create new grouped services
5. RevenueServiceProcessing → Re-process with new groups
6. Report_ServicesMatrix → Analyze correlations within groups
```
