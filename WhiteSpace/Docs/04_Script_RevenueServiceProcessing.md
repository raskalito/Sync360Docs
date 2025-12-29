# Script: RevenueServiceProcessing.xml

## Purpose

RevenueServiceProcessing is the **core processing script** that executes all Revenue Service queries and classifies companies into matched (purchased) and unmatched (not purchased) buckets. This script should be run after Revenue Services are defined and is typically scheduled to run automatically on an ongoing basis.

**Key Characteristics**:
- **Main processing engine**: Executes the business logic for the entire solution
- **Data-driven**: Uses Revenue Service definitions to determine what to process
- **Idempotent**: Can be run repeatedly; updates relationships to current state
- **Automatic cleanup**: Removes outdated associations when data changes
- **Configurable**: Behavior controlled by Configuration.xml


## When to Use This Script

Run RevenueServiceProcessing when:
- **Initial data load**: First time processing after creating Revenue Services
- **Data updates**: After source data changes (new sales, updated records)
- **Schedule-based**: Regular automated runs (daily, weekly, etc.)
- **On-demand analysis**: When you need current matched/unmatched data
- **After service definition changes**: When Revenue Service queries are modified


## What You Need Before Running

### Prerequisites
1. **Configuration.xml**: Properly configured (see Prerequisites and Configuration documentation)
2. **Revenue Services**: At least one active Revenue Service record exists
3. **Source data**: Data in your source tables that the queries will evaluate
4. **Sync360 connection**: Active connection to Dataverse

### Optional
- **Orders enabled**: If using detailed order tracking (`Config['EnableOrders'] = true`)
- **SQL connection**: If using SQL save mode


## What the Script Does

### High-Level Process Flow

```
1. Load all active Revenue Services
2. Group services by vs360_group number
3. Load existing matched/unmatched relationships
4. Load existing orders (if enabled)
5. For each Revenue Service:
   - Execute the query defined in vs360_selectedcombinations
   - Identify companies that match the criteria
   - Create/update order records (if enabled)
6. For each company with at least one matched service:
   - Create matched relationships for services they have
   - Create unmatched relationships for services they don't have (in same group)
7. Clean up outdated relationships:
   - Remove matched relationships for companies that no longer qualify
   - Remove unmatched relationships for companies that now match
8. Update or delete order records as needed
```

### Detailed Operations

#### Phase 1: Load Configuration and Existing Data
- Includes Configuration.xml
- Loads all active Revenue Services (`statecode = 0`)
- Organizes services by group number
- Loads existing matched/unmatched company-service relationships
- Loads existing order records (if orders enabled)

#### Phase 2: Execute Queries
For each Revenue Service:
- Parses the JSON from `vs360_selectedcombinations`
- Executes the dynamic query against source tables
- Identifies companies that meet the criteria
- Tracks services found for each company

If orders enabled:
- Creates or updates Revenue Service Order records
- Maps fields based on OrdersMapping configuration
- Links orders to Revenue Services

#### Phase 3: Create/Update Relationships
For each company with at least one service:
- **Matched relationships**: Creates associations for services the company has
- **Unmatched relationships**: Creates associations for services in the same group that the company doesn't have

#### Phase 4: Cleanup
- **Remove stale matched**: Companies that previously matched but no longer do
- **Remove stale unmatched**: Companies that were unmatched but now match
- **Update orders**: Modify order records if source data changed
- **Delete orders**: Remove orders for services that no longer match


## Configuration Impact

The script behavior varies based on Configuration.xml settings:

### SaveMode: Dynamics vs SQL

**Dynamics Mode** (`Config['SaveMode'] = 'Dynamics'`):
- Writes directly to Dataverse
- Uses standard API operations
- Slower but simpler setup
- No additional infrastructure needed

**SQL Mode** (`Config['SaveMode'] = 'SQL'`):
- Writes to SQL database
- Requires matching table structure
- Faster for high-volume processing
- Requires SQL connection in Sync360

### EnableOrders: True vs False

**Orders Enabled** (`Config['EnableOrders'] = true`):
- Creates/updates Revenue Service Order records
- Tracks temporal data (start date, end date)
- Captures revenue amounts
- Links orders to services and companies
- More detailed data, slower processing

**Orders Disabled** (`Config['EnableOrders'] = false`):
- Only creates matched/unmatched relationships
- No order records created
- Faster processing
- Simpler data model
- Sufficient for correlation analysis only


## Script Execution

### Running the Script

1. **Prepare**:
   - Ensure Configuration.xml is correct
   - Verify Revenue Services are active and properly configured
   - Confirm Sync360 connection is established

2. **Execute**:
   - Load RevenueServiceProcessing.xml in Sync360
   - Click Execute/Run
   - Monitor progress (script includes logging)

3. **Monitor**:
   - Watch for errors in Sync360 output
   - Check log messages for processing status
   - Verify record counts make sense

4. **Verify**:
   - Check matched/unmatched relationship tables in Dataverse
   - Review Revenue Service Order records (if enabled)
   - Spot-check a few companies to confirm accuracy

### Execution Time Expectations

Processing time depends on:
- Number of Revenue Services
- Complexity of queries
- Volume of source data
- Number of companies
- Orders enabled/disabled
- Dynamics vs SQL mode

**Typical benchmarks**:
- 10 services, 1,000 companies: 2-5 minutes (Dynamics)
- 50 services, 10,000 companies: 15-30 minutes (Dynamics)
- Large volumes: Consider SQL mode for better performance


## Expected Results

After successful execution:

### Matched Relationships
The `vs360_account_vs360_revenueservice_matched` table contains records for:
- Each company that meets the criteria for a service
- Only companies with at least one service in a group

**Example**:
| Company ID | Revenue Service ID |
|------------|-------------------|
| GUID-123 | Service-A-GUID |
| GUID-123 | Service-B-GUID |
| GUID-456 | Service-B-GUID |

*Company 123 has Services A and B; Company 456 has Service B*

### Unmatched Relationships
The `vs360_account_vs360_revenueservice_unmatched` table contains records for:
- Each company that does NOT meet the criteria for a service
- Only for companies that have at least one OTHER service in the same group

**Example** (assuming Services A, B, C are all in Group 0):
| Company ID | Revenue Service ID |
|------------|-------------------|
| GUID-123 | Service-C-GUID |
| GUID-456 | Service-A-GUID |
| GUID-456 | Service-C-GUID |

*Company 123 doesn't have Service C; Company 456 doesn't have Services A or C*

### Order Records (if enabled)
The `vs360_revenueserviceorder` table contains:
- Individual service instances
- Company association
- Date ranges (start/end)
- Revenue amounts
- Status information

**Example**:
| Name | Company | Start Date | End Date | Revenue |
|------|---------|------------|----------|---------|
| JOB-001 | GUID-123 | 2024-01-15 | 2024-06-30 | $50,000 |
| JOB-002 | GUID-456 | 2024-03-01 | (null) | $25,000 |


## Important Behaviors

### Group-Based Processing
- Companies are only processed if they have at least one service in a group
- A company with no services in a group won't appear in matched or unmatched tables
- This ensures analysis focuses on relevant companies

### Automatic Synchronization
- Re-running the script updates relationships to match current data
- If source data changes, matched/unmatched associations update accordingly
- Stale data is automatically cleaned up

### Order Lifecycle
- Orders are created when a service is first identified
- Orders are updated if source data changes (e.g., end date added)
- Orders are deleted if the service no longer matches for that company

### Dynamic Query Execution
- Queries are read from JSON at runtime
- No code changes needed to modify service criteria
- Complex queries with joins and conditions are supported


## Included Scripts

The script includes additional private scripts from `@private` folder:

### Configuration.xml
Provides all configuration variables (required).

### DynamicQuery.xml
Handles parsing and execution of JSON queries. Supports:
- Complex conditions (_and, _or operators)
- Table joins (inner, left)
- Multiple rules per service
- Field mapping for orders

### Conditions.xml
Additional condition processing logic.


## Advanced Features

### Extension Point: LostOpportunities
The script includes a commented extension:
```xml
<!--Extensions -->
<set var="IgnoreServiceUnmatched">{new Dictionary()}</set>
<!--<include name="LostOpportunities" />-->
```

You can create custom extension logic by:
1. Creating a script in `@private/YourExtension.xml`
2. Including it at this point
3. Using `IgnoreServiceUnmatched` to exclude certain services from unmatched processing

**Use case**: If you want to mark some service combinations as "not applicable" rather than "unmatched opportunity"

### Multi-Status Support
The script supports status tracking for orders:
- Status can be set based on rules
- Different statuses can drive different behaviors
- Useful for workflow integration


## Scheduling for Ongoing Operations

After initial setup and analysis, schedule this script to run automatically:

### Daily Schedule
Recommended when:
- Source data updates daily
- Timely insights are critical
- Service adoption changes frequently

### Weekly Schedule
Recommended when:
- Source data is relatively stable
- Performance considerations (large datasets)
- Analysis is not time-critical

### On-Demand Only
Recommended when:
- Source data changes infrequently
- Manual control preferred
- One-time or periodic analysis

**Sync360 Scheduling**: Refer to Sync360 documentation for setting up scheduled script execution.


## Troubleshooting

### Issue: Script runs but no relationships created
**Possible causes**:
- Revenue Service queries not returning results (check source data)
- SaveMode/table configuration mismatch
- Permissions issues writing to relationship tables

**Debug steps**:
1. Verify Revenue Services exist and are active
2. Test query JSON manually in Dataverse
3. Check script logs for errors
4. Verify Configuration.xml table names

### Issue: Performance is very slow
**Solutions**:
- Switch to SQL mode if available
- Disable orders if not needed
- Optimize Revenue Service queries (add conditions to limit results)
- Break into multiple groups and process separately

### Issue: Relationships not updating
**Check**:
- Script completed without errors
- Configuration.xml is correct
- Sync360 has write permissions
- Not using stale data/cached results

### Issue: Orders have incorrect data
**Check**:
- OrdersMapping configuration matches your Revenue Service JSON
- Field names in mapping are correct
- Data types match between source and destination

### Issue: JSON parsing errors
**Causes**:
- Invalid JSON in vs360_selectedcombinations
- Missing required fields in JSON structure

**Fix**:
- Validate JSON syntax
- Ensure JSON follows expected structure
- Check GenerateRevenueService script for proper JSON generation


## Best Practices

1. **Test incrementally**: Start with one service group, verify results, then expand
2. **Monitor first runs**: Watch closely when running for the first time
3. **Verify results**: Spot-check matched/unmatched data for accuracy
4. **Schedule wisely**: Don't over-schedule; find the right frequency for your needs
5. **Optimize queries**: Keep Revenue Service queries as simple as possible while meeting requirements
6. **Use logging**: Add log statements if you need more visibility into processing
7. **Backup before changes**: If modifying the script, test in development first


## Performance Optimization Tips

1. **Limit query scope**: Add date filters or status conditions to Revenue Service queries
2. **Use SQL mode**: For large datasets, SQL mode significantly improves performance
3. **Disable orders**: If you don't need temporal/revenue data, disable orders
4. **Process by group**: If you have many groups, consider separate scheduled runs per group
5. **Index source tables**: Ensure source tables have proper indexes on fields used in queries


