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

The primary configuration file is located at `WhiteSpace/@private/Configuration.xml`. This file plays a critical role in the White Space Solution, as it defines how the solution interacts with your Dataverse environment. You must configure this file before running any processing scripts, as most scripts include and depend on these settings to function correctly.

The configuration file controls several key aspects of the solution's behavior, including how data is saved (directly to Dataverse or through SQL), which tables are used for analysis, how relationships are tracked, and whether detailed order records should be created. Understanding and properly configuring these settings is essential for successful implementation.


## Configuration.xml - Detailed Guide

### Save Mode Configuration

The Save Mode setting determines how the solution writes data back to your environment:

```xml
<set var="Config['SaveMode']">Dynamics</set>
```

**Options**:
- `Dynamics` - Write directly to Dataverse using standard API (recommended)
- `SQL` - Write to SQL database with matching schema (advanced scenarios)

The Dynamics mode is recommended for most implementations as it uses the standard Dataverse API, ensuring proper security, auditing, and plugin execution. SQL mode is designed for high-volume processing scenarios where performance is critical, but it requires additional SQL infrastructure setup, including a SQL connection configuration in Sync360 and matching many-to-many tables in the SQL database. Unless you have specific performance requirements and the necessary SQL infrastructure already in place, start with Dynamics mode.


### Table Configuration

#### Primary Entity Configuration

The solution needs to know which entity you're analyzing and its primary key field:

```xml
<set var="Config['OtherTable']">account</set>
<set var="Config['OtherTablePrimaryKey']">accountid</set>
```

The `OtherTable` setting specifies the primary entity being analyzed, which is typically "account" when analyzing companies, but it can be changed to any Dataverse table. For example, if you want to analyze individual contacts instead of companies, you would change this to "contact". The `OtherTablePrimaryKey` setting must match the table's actual primary key field name to ensure proper record identification and relationship management.

**Customization Example**:
```xml
<!-- To analyze Contacts instead of Accounts -->
<set var="Config['OtherTable']">contact</set>
<set var="Config['OtherTablePrimaryKey']">contactid</set>
```


#### Relationship Table Configuration

The solution uses two separate many-to-many relationship tables to track service adoption:

```xml
<set var="Config['MatchedNN']">vs360_account_vs360_revenueservice_matched</set>
<set var="Config['UnmatchedNN']">vs360_account_vs360_revenueservice_unmatched</set>
```

The `MatchedNN` table stores the relationship between companies and services they have purchased, representing the intersection of Company and Revenue Service where a match exists. The `UnmatchedNN` table tracks services that companies have not purchased, but only for services within the same service group as those they have purchased. This separation enables the solution to identify both what companies have (for retention and upselling) and what they don't have (for cross-selling opportunities).

These table names must match the actual relationship table names created in your Dataverse solution. If your solution uses different naming conventions, update these settings accordingly.


### Revenue Service Orders Configuration

The orders feature provides detailed tracking of individual service instances, including dates and revenue information:

```xml
<set var="Config['EnableOrders']">{true}</set>
<set var="Config['OrdersNN']">vs360_revenueservice_orders</set>
<set var="Config['OrdersTable']">vs360_revenueserviceorder</set>
<set var="Config['OrdersPrimaryKey']">vs360_revenueserviceorderid</set>
<set var="Config['OrdersKey']">vs360_name</set>
```

#### Enable Orders Flag

The `EnableOrders` flag controls whether the solution creates and maintains detailed order records. When set to `{true}`, the solution tracks individual service instances with their date ranges, revenue amounts, and other details. When set to `{false}`, the solution only maintains matched and unmatched relationships without the granular order-level details.

Enable orders when you want to track individual service instances, capture date ranges for service engagements, record revenue per service instance, or generate detailed reports with temporal data. Disable orders when you only need to know if a company has ever purchased a service, prefer a simpler data model, or need performance optimization for very large datasets.

#### Orders Table Configuration

When orders are enabled, you need to specify the table structure. The `OrdersNN` setting identifies the relationship table that links Revenue Services to Orders. The `OrdersTable` specifies the main orders table name, while `OrdersPrimaryKey` identifies that table's primary key field. The `OrdersKey` setting defines the unique identifier field used to match orders, which is typically the name field that serves as a natural key for deduplication.


### Orders Field Mapping

The `OrdersMapping` configuration defines how fields from your source data map to the Revenue Service Order table fields. This mapping enables the solution to extract data from your existing tables and populate the order records correctly.

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

Each mapping entry contains three components: the source placeholder (`src`), the destination field (`dst`), and the data type (`type`). The source value is a placeholder name that you define in your Revenue Service JSON configuration. The destination is the actual field name in the Revenue Service Order table where the data should be stored. The type specifies the data type and determines how the value is processed during the mapping operation, with options including lookup (for entity references), date (for datetime fields), decimal (for numeric values), and string (for text fields).

The mapping works in conjunction with your Revenue Service JSON configuration. In the `vs360_selectedcombinations` field of each Revenue Service record, you define field references using the placeholder names specified in the `src` attributes. For example:

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

During processing, the solution reads these field references from the Revenue Service JSON, retrieves the actual data from your source tables, and uses the OrdersMapping configuration to determine which Revenue Service Order fields should receive that data.

#### Customization Example

You can extend the mapping to include additional fields that are relevant to your business. For example, if you want to track the employee associated with each service engagement and the status of the order:

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

After adding these mappings to Configuration.xml, you would update your Revenue Service JSON configurations to include the corresponding field references (employee_field and status_field), pointing to the actual source fields in your data.


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

Different environments may require different configuration approaches based on their purpose and constraints. The following examples illustrate common configuration patterns for different deployment scenarios.

### Development Environment
```xml
<set var="Config['SaveMode']">Dynamics</set>
<set var="Config['EnableOrders']">{true}</set>
<!-- Full functionality for testing -->
```

In development environments, use Dynamics mode for simplicity and enable all features including orders. This gives you the full functionality needed for testing and validation without requiring additional SQL infrastructure.

### Production Environment
```xml
<set var="Config['SaveMode']">SQL</set>
<set var="Config['EnableOrders']">{true}</set>
<!-- Optimized for performance -->
```

Production environments with high data volumes may benefit from SQL mode to optimize processing performance, while maintaining full order tracking for comprehensive business insights.

### Analysis-Only Environment
```xml
<set var="Config['SaveMode']">Dynamics</set>
<set var="Config['EnableOrders']">{false}</set>
<!-- Simplified for correlation analysis only -->
```

If you only need service correlation analysis without detailed temporal data, disabling orders simplifies the data model and improves processing speed while still providing the core matching and unmatching relationships.


## Common Configuration Scenarios

**Scenario 1: Standard Company Analysis** involves setting `OtherTable` to account and enabling orders to track which companies purchased which services along with dates and revenue information. This is the most common configuration and provides comprehensive service adoption insights.

**Scenario 2: Contact-Level Analysis** changes the focus from companies to individuals by setting `OtherTable` to contact and modifying the relationship table configurations accordingly. This approach is useful for organizations that track service purchases at the individual level rather than the company level.

**Scenario 3: Simple Correlation Analysis** disables orders by setting `EnableOrders` to false, skipping detailed order tracking to focus exclusively on service correlation patterns. This lightweight configuration is ideal when you only need to understand which services are purchased together, without needing the temporal or financial details.


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
