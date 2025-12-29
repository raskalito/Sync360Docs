# Prerequisites and Configuration

## Prerequisites

Before using the White Space Solution, ensure you have the following in place:

### 1. Dataverse Environment
- Active Microsoft Dataverse environment
- Sufficient permissions to:
  - Create and modify records in solution tables
  - Execute Sync360 scripts
  - Access custom tables and relationships

### 2. Solution Installation
- Revenue Service Analytics Dataverse solution installed
- All required tables created (see Dataverse Tables documentation)
- Required relationships configured

### 3. Sync360 Application
- Sync360 application installed and configured
- Connection to your Dataverse environment established
- Appropriate Sync360 licenses

### 4. File System Access
- Write permissions to output directory for report generation
- Default path: `C:\temp\`
- Alternative paths can be configured in individual report scripts

### 5. Source Data
- Existing Dataverse tables/customizations containing your service/product data
- Understanding of which fields indicate service purchases
- Sample queries to identify service adoption


## Configuration File Setup

### Location
The primary configuration file is located at:
```
WhiteSpace/@private/Configuration.xml
```

**IMPORTANT**: You must configure this file before running any processing scripts. This file is included by most scripts and defines how the solution interacts with your Dataverse environment.


## Configuration.xml - Detailed Guide

### Save Mode Configuration

```xml
<set var="Config['SaveMode']">Dynamics</set>
```

**Options**:
- `Dynamics` - Write directly to Dataverse using standard API (recommended)
- `SQL` - Write to SQL database with matching schema (advanced scenarios)

**When to Use SQL Mode**:
- High-volume processing where performance is critical
- Custom integration scenarios
- Requires SQL connection configuration in Sync360
- Requires matching N:N tables in SQL database

**Recommendation**: Start with `Dynamics` mode. Only use `SQL` if you have specific performance requirements and SQL infrastructure in place.


### Table Configuration

#### Primary Entity Configuration

```xml
<set var="Config['OtherTable']">account</set>
<set var="Config['OtherTablePrimaryKey']">accountid</set>
```

**`Config['OtherTable']`**:
- The primary entity being analyzed (typically "account" for companies)
- Can be changed to any Dataverse table (e.g., "contact" for individual analysis)

**`Config['OtherTablePrimaryKey']`**:
- Primary key field of the entity
- Must match the table's actual primary key field name

**Customization Example**:
```xml
<!-- To analyze Contacts instead of Accounts -->
<set var="Config['OtherTable']">contact</set>
<set var="Config['OtherTablePrimaryKey']">contactid</set>
```


#### Relationship Table Configuration

```xml
<set var="Config['MatchedNN']">vs360_account_vs360_revenueservice_matched</set>
<set var="Config['UnmatchedNN']">vs360_account_vs360_revenueservice_unmatched</set>
```

**`Config['MatchedNN']`**:
- Many-to-many relationship table for services that companies HAVE purchased
- Stores the intersect between Company and Revenue Service

**`Config['UnmatchedNN']`**:
- Many-to-many relationship table for services that companies have NOT purchased
- Only includes companies within the same service group

**Important**: These table names must match the actual relationship table names created in your Dataverse solution.


### Revenue Service Orders Configuration

```xml
<set var="Config['EnableOrders']">{true}</set>
<set var="Config['OrdersNN']">vs360_revenueservice_orders</set>
<set var="Config['OrdersTable']">vs360_revenueserviceorder</set>
<set var="Config['OrdersPrimaryKey']">vs360_revenueserviceorderid</set>
<set var="Config['OrdersKey']">vs360_name</set>
```

#### Enable Orders Flag

**`Config['EnableOrders']`**:
- `{true}` - Enable detailed order tracking (creates/updates order records)
- `{false}` - Disable order processing (only track matched/unmatched relationships)

**When to Enable**:
- You want to track individual service instances
- You need date ranges for service engagements
- You want to capture revenue per service instance
- You need detailed reporting with temporal data

**When to Disable**:
- You only need to know if a company has ever purchased a service
- Simpler data model is preferred
- Performance optimization for very large datasets

#### Orders Table Configuration

**`Config['OrdersNN']`**: Relationship table linking Revenue Services to Orders

**`Config['OrdersTable']`**: Main orders table name

**`Config['OrdersPrimaryKey']`**: Primary key of orders table

**`Config['OrdersKey']`**: Unique identifier field used to match orders (typically the name field)


### Orders Field Mapping

The `OrdersMapping` configuration defines how fields from source data map to the Revenue Service Order table.

```xml
<set var="OrdersMapping">{new List()}</set>

<set var="OrdersMapping[]">
    <attr name="src">customer_field</attr>
    <attr name="dst">vs360_accountid</attr>
    <attr name="type">lookup</attr>
</set>

<set var="OrdersMapping[]">
    <attr name="src">startdate_field</attr>
    <attr name="dst">vs360_startdate</attr>
    <attr name="type">date</attr>
</set>

<set var="OrdersMapping[]">
    <attr name="src">enddate_field</attr>
    <attr name="dst">vs360_enddate</attr>
    <attr name="type">date</attr>
</set>

<set var="OrdersMapping[]">
    <attr name="src">revenue_field</attr>
    <attr name="dst">vs360_revenue</attr>
    <attr name="type">decimal</attr>
</set>

<set var="OrdersMapping[]">
    <attr name="src">orderid_field</attr>
    <attr name="dst">vs360_name</attr>
    <attr name="type">string</attr>
</set>
```

#### Understanding Field Mapping

Each mapping entry contains:
- **`src`**: Field name in the Revenue Service query JSON (placeholder that will be replaced with actual source field)
- **`dst`**: Field name in the Revenue Service Order table
- **`type`**: Data type (lookup, date, decimal, string)

#### How It Works

In your Revenue Service JSON configuration (`vs360_selectedcombinations`), you define fields like:
```json
{
    "rules": [{
        "customer_field": "vs360_accountid",
        "startdate_field": "vs360_startdate",
        "enddate_field": "vs360_enddate",
        "revenue_field": "fol_fb5ytotal",
        "orderid_field": "vs360_jobid"
    }]
}
```

The mapping translates these source fields to the destination Revenue Service Order fields.

#### Customization Example

If you have additional fields to track:

```xml
<!-- Add Employee mapping -->
<set var="OrdersMapping[]">
    <attr name="src">employee_field</attr>
    <attr name="dst">vs360_employeeid</attr>
    <attr name="type">lookup</attr>
</set>

<!-- Add Status mapping -->
<set var="OrdersMapping[]">
    <attr name="src">status_field</attr>
    <attr name="dst">vs360_status</attr>
    <attr name="type">int</attr>
</set>
```

Then update your Revenue Service JSON to include these field references.


## Configuration Validation Checklist

Before running any processing scripts, verify:

- [ ] `SaveMode` is set to `Dynamics` (unless you have SQL configured)
- [ ] `OtherTable` matches your target entity (usually `account`)
- [ ] `OtherTablePrimaryKey` matches the actual primary key field
- [ ] `MatchedNN` table exists in Dataverse
- [ ] `UnmatchedNN` table exists in Dataverse
- [ ] `EnableOrders` is set based on your requirements
- [ ] If orders enabled: `OrdersTable`, `OrdersNN`, and `OrdersPrimaryKey` are correct
- [ ] `OrdersMapping` includes all fields you want to track
- [ ] Field mappings in `OrdersMapping` match your Revenue Service JSON structure


## Environment-Specific Configuration

### Development Environment
```xml
<set var="Config['SaveMode']">Dynamics</set>
<set var="Config['EnableOrders']">{true}</set>
<!-- Full functionality for testing -->
```

### Production Environment
```xml
<set var="Config['SaveMode']">SQL</set>
<set var="Config['EnableOrders']">{true}</set>
<!-- Optimized for performance -->
```

### Analysis-Only Environment
```xml
<set var="Config['SaveMode']">Dynamics</set>
<set var="Config['EnableOrders']">{false}</set>
<!-- Simplified for correlation analysis only -->
```


## Common Configuration Scenarios

### Scenario 1: Standard Company Analysis
- `OtherTable`: account
- `EnableOrders`: true
- Track which companies purchased which services with dates and revenue

### Scenario 2: Contact-Level Analysis
- `OtherTable`: contact
- Modify relationship tables accordingly
- Analyze individual contacts instead of companies

### Scenario 3: Simple Correlation Analysis
- `EnableOrders`: false
- Skip detailed order tracking
- Focus on service correlation patterns only


## Troubleshooting Configuration Issues

### Error: "Table not found"
- Verify table names in configuration match Dataverse
- Check solution is fully deployed
- Confirm Sync360 connection has access to tables

### Error: "Field does not exist"
- Verify primary key field names
- Check OrdersMapping destination fields exist in Revenue Service Order table
- Ensure source fields are defined in Revenue Service JSON

### Performance Issues
- Consider switching to SQL mode for large datasets
- Disable orders if not needed
- Review service query complexity




**Configuration Best Practice**: Keep a backup copy of your Configuration.xml before making changes. Document any customizations for future reference.
