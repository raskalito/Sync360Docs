# Script: RevenueServiceProcessing.xml

## Purpose

RevenueServiceProcessing is the core processing script of the White Space Solution, responsible for executing all Revenue Service queries and classifying companies into matched (purchased) and unmatched (not purchased) buckets. This script serves as the main processing engine that executes the business logic for the entire solution, reading Revenue Service definitions to determine what to process. The script is designed to be idempotent, meaning it can be run repeatedly and will update relationships to reflect the current state of your data. It automatically cleans up outdated associations when data changes, ensuring your analysis always reflects the most current information. The script's behavior is controlled by Configuration.xml, allowing you to adjust processing without modifying the script itself.


## When to Use This Script

You'll run RevenueServiceProcessing during your initial data load, the first time you're processing data after creating your Revenue Services. This establishes the baseline of matched and unmatched relationships. After that, run it whenever your source data changes due to new sales, updated records, or other modifications. For ongoing operations, the script is typically scheduled to run automatically on a daily, weekly, or monthly basis depending on how frequently your data changes and how current you need your insights to be. You should also run it on-demand whenever you need current matched and unmatched data for analysis, and after making changes to Revenue Service query definitions to ensure the new criteria are applied to your data.


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

The script begins by including Configuration.xml to load all necessary settings, then loads all active Revenue Services from Dataverse (those with `statecode = 0`). It organizes these services by their group number to enable group-based processing. The script also loads existing matched and unmatched company-service relationships, as well as existing order records if orders are enabled. This initial data loading phase ensures the script has a complete picture of the current state before making any changes.

#### Phase 2: Execute Queries

For each Revenue Service, the script parses the JSON configuration from the `vs360_selectedcombinations` field and executes the dynamic query against your source tables. This identifies which companies meet the criteria defined for each service. The script tracks which services are found for each company, building a comprehensive map of the service landscape.

If orders are enabled in your configuration, the script creates or updates Revenue Service Order records during this phase. It maps fields from your source data to the order records based on the OrdersMapping configuration, and links these orders to the appropriate Revenue Services and companies. This provides the detailed temporal and financial tracking that enables more sophisticated analysis.

#### Phase 3: Create/Update Relationships

For each company that has at least one service in a group, the script creates or updates relationships. Matched relationships are created for services the company has, establishing the positive associations. Unmatched relationships are created for services in the same group that the company doesn't have, identifying the white space opportunities where cross-selling or upselling could occur.

#### Phase 4: Cleanup

The final phase ensures data accuracy by removing stale information. The script removes matched relationships for companies that previously qualified but no longer do based on current data. It also removes unmatched relationships for companies that were previously unmatched but now match the service criteria. If orders are enabled, the script updates order records if source data has changed and deletes orders for services that no longer match for a particular company.


## Configuration Impact

The script's behavior varies significantly based on your Configuration.xml settings, particularly the SaveMode and EnableOrders options.

### SaveMode: Dynamics vs SQL

Dynamics mode writes directly to Dataverse using standard API operations. This approach is slower than SQL mode but requires simpler setup with no additional infrastructure needed. It's the recommended starting point for most implementations. SQL mode, in contrast, writes to a SQL database with a matching table structure. This provides faster performance for high-volume processing scenarios but requires a SQL connection configured in Sync360 and matching many-to-many tables in your SQL database.

### EnableOrders: True vs False

When orders are enabled, the script creates and updates Revenue Service Order records, tracking temporal data such as start and end dates, capturing revenue amounts, and linking orders to services and companies. This provides more detailed data but results in slower processing. When orders are disabled, the script only creates matched and unmatched relationships without creating any order records. This results in faster processing with a simpler data model that's sufficient for correlation analysis when you don't need the granular details about individual service engagements.


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

Understanding how the script processes data is critical for interpreting results correctly. The processing is group-based, meaning companies are only included if they have at least one service in a particular group. A company with no services in a group won't appear in the matched or unmatched tables for that group. This ensures that analysis focuses on companies that are relevant to each service grouping.

The script provides automatic synchronization between your source data and the matched/unmatched relationships. Re-running the script updates relationships to match the current state of your data. If source data changes, the matched and unmatched associations update accordingly, and stale data is automatically cleaned up without manual intervention.

For orders, the script manages a complete lifecycle. Orders are created when a service is first identified for a company. They're updated if source data changes, such as when an end date is added to an ongoing engagement. Orders are deleted if the service no longer matches for that company, ensuring the order table always reflects current reality.

The dynamic query execution capability means that queries are read from JSON at runtime, so no code changes are needed to modify service criteria. You can update the query definition in a Revenue Service record and the next script run will use the new criteria. The script supports complex queries with joins and conditions, providing flexibility to match your business logic.


## Included Scripts

The script includes additional private scripts from the `@private` folder that provide essential functionality:

**Configuration.xml** provides all configuration variables and is required for the script to run. This is where SaveMode, table names, orders settings, and field mappings are defined.

**DynamicQuery.xml** handles the parsing and execution of JSON queries. It supports complex conditions with _and and _or operators, table joins (both inner and left), multiple rules per service, and field mapping for orders. This is the engine that makes the JSON-based query definitions work.

**Conditions.xml** provides additional condition processing logic that supports the dynamic query execution.


## Advanced Features

### Extension Point: LostOpportunities

The script includes a commented extension point that allows for custom logic:
```xml
<!--Extensions -->
<set var="IgnoreServiceUnmatched">{new Dictionary()}</set>
<!--<include name="LostOpportunities" />-->
```

You can create custom extension logic by creating a script in `@private/YourExtension.xml`, including it at this extension point, and using the `IgnoreServiceUnmatched` dictionary to exclude certain services from unmatched processing. This is useful when you want to mark some service combinations as "not applicable" rather than treating them as "unmatched opportunity." For example, you might determine that certain services are never sold to certain types of companies, and you don't want these appearing as cross-sell opportunities.

### Multi-Status Support

The script supports status tracking for orders, allowing status to be set based on rules defined in your Revenue Service queries. Different statuses can drive different behaviors in subsequent processing or reporting. This is particularly useful for workflow integration where you need to track services in different lifecycle stages.


## Scheduling for Ongoing Operations

After initial setup and analysis, schedule this script to run automatically to keep your data current.

A daily schedule is recommended when your source data updates daily, timely insights are critical to your business operations, and service adoption changes frequently. Weekly scheduling works well when source data is relatively stable, you have performance considerations due to large datasets, or the analysis is not time-critical and weekly updates are sufficient. Some implementations work best with on-demand execution only, particularly when source data changes infrequently, manual control is preferred for organizational reasons, or you're conducting one-time or periodic analysis rather than ongoing monitoring.

Refer to Sync360 documentation for details on setting up scheduled script execution within your environment.


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
