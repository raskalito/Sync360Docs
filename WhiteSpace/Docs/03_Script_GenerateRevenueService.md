# Script: GenerateRevenueService.xml

## Purpose

The GenerateRevenueService script is a client-specific template that creates initial Revenue Service records in Dataverse. Unlike the other scripts in the White Space Solution which are generally reusable, this script must be customized for each client implementation to match their specific data model, services, and business logic. This is typically a one-time or infrequent use script, run during initial setup or when adding new services to the system. The script is designed to be idempotent, meaning it can be safely re-run without creating duplicate records, as it checks for existing services before creating new ones.


## When to Use This Script

You'll use GenerateRevenueService during initial implementation when setting up Revenue Service records for the first time. This is a critical step in deploying the White Space Solution, as it establishes the foundational service definitions that all subsequent processing and reporting will reference. The script is also useful when adding new services to an existing implementation, whether you're expanding your service catalog or refining your service definitions. Additionally, if you need to perform bulk updates to query definitions across multiple services, running a modified version of this script can be more efficient than manually editing individual Revenue Service records in Dataverse.


## What You Need Before Running

### 1. Understand Your Client's Data Model
- Identify the source table(s) containing service/product purchase data
- Determine which fields indicate a service was purchased
- Understand the criteria that define each service

### 2. Define Service Groups
- Decide which services should be analyzed together
- Assign a group number (e.g., Group 0, Group 1, etc.)
- Services in the same group will be processed together

### 3. Map Services to Data
For each service you want to track:
- Service name/description
- Group identifier
- Query criteria (fields, conditions, linked tables)
- Any specific thresholds or rules


## Script Structure Overview

The script consists of three main sections:

### 1. Query Template Definition
```xml
<set var="template"><![CDATA[
{
    "rules": [...]
}
]]></set>
```
Defines the JSON structure used for Revenue Service query configuration.

### 2. Service Data Definition
```xml
<set var="Data">{new Dictionary()}</set>
<set var="Data['0']">{new List()}</set>
<set var="Data['0'][]"><attr name="code">...</attr><attr name="grp">...</attr></set>
```
Lists all services to create, organized by group.

### 3. Processing Loop
Iterates through service definitions, creates JSON configurations, and generates Revenue Service records.


## Customization Guide

### Step 1: Customize the Query Template

The template defines how to identify if a company has purchased a service. This is the heart of the Revenue Service definition and must accurately reflect your client's data model. The provided example uses a query against a job table with practice area and revenue criteria:

```xml
<set var="template"><![CDATA[{
    "rules": [
        {
            "entity": "vs360_job",
            "field": "vs360_accountid",
            "conditions": [
                {
                    "_and": [
                        {
                            "attr": "vs360_practiceareaid",
                            "op": "eq",
                            "value": "GGGGGGGG",
                            "type": "string"
                        },
                        {
                            "attr": "fol_fb5ytotal",
                            "op": "ge",
                            "value": 500,
                            "type": "decimal"
                        }
                    ]
                }
            ],
            "links": [
                {
                    "type": "inner",
                    "relationship": "vs360_accountdata",
                    "to": "vs360_clientreferenceid",
                    "from": "vs360_accountdataid",
                    "conditions": [
                        {
                            "_and": [
                                {
                                    "attr": "vs360_clientenddate",
                                    "op": "null"
                                }
                            ]
                        }
                    ]
                }
            ]
        }
    ]
}]]></set>
```

This example queries the `vs360_job` table and returns the `vs360_accountid` (company) field. It applies two conditions: the practice area must match a specific ID (represented by the "GGGGGGGG" placeholder which will be replaced dynamically), and the total revenue must be greater than or equal to 500. The query also joins to the `vs360_accountdata` table with an additional condition that the client end date must be null, ensuring only active clients are included.

When customizing this for your client, replace it with logic that matches their specific data structure:

```xml
<set var="template"><![CDATA[{
    "rules": [
        {
            "entity": "your_source_table",
            "field": "your_company_field",
            "conditions": [
                {
                    "_and": [
                        {
                            "attr": "your_service_identifier_field",
                            "op": "eq",
                            "value": "PLACEHOLDER",
                            "type": "string"
                        },
                        {
                            "attr": "your_status_field",
                            "op": "eq",
                            "value": 1,
                            "type": "int"
                        }
                    ]
                }
            ]
        }
    ]
}]]></set>
```

It's important to use a placeholder value (like "GGGGGGGG" or "PLACEHOLDER") in your template. This placeholder will be replaced with actual service-specific values during the processing loop, allowing you to use one template for all services while customizing the specific identifiers.


### Step 2: Define Your Service Data

The Data dictionary organizes services by group number, with each group containing a list of service definitions:

```xml
<set var="Data">{new Dictionary()}</set>
<set var="Data['0']">{new List()}</set>
```

The key is the group number (as a string), and the value is a list of service definitions. Each service definition contains a `code` attribute (a unique identifier used to look up service details) and a `grp` attribute (a category or prefix for service naming).

The example script uses numeric codes and abbreviations:

```xml
<set var="Data['0'][]"><attr name="code">0424</attr><attr name="grp">BL</attr></set>
<set var="Data['0'][]"><attr name="code">0415</attr><attr name="grp">BL</attr></set>
```

For your implementation, customize this to reflect your service structure:

```xml
<set var="Data">{new Dictionary()}</set>

<!-- Group 0: Core Services -->
<set var="Data['0']">{new List()}</set>
<set var="Data['0'][]"><attr name="code">SVC001</attr><attr name="grp">CORE</attr></set>
<set var="Data['0'][]"><attr name="code">SVC002</attr><attr name="grp">CORE</attr></set>
<set var="Data['0'][]"><attr name="code">SVC003</attr><attr name="grp">CORE</attr></set>

<!-- Group 1: Premium Services -->
<set var="Data['1']">{new List()}</set>
<set var="Data['1'][]"><attr name="code">PREM001</attr><attr name="grp">PREM</attr></set>
<set var="Data['1'][]"><attr name="code">PREM002</attr><attr name="grp">PREM</attr></set>
```


### Step 3: Customize the Lookup Logic

The script needs to retrieve service details based on the code you defined in the previous step. The example looks up services from a practice area table:

```xml
<select from="crm" entity="vs360_practicearea" var="rec">
    <where>
        <condition attr="fol_foleygroupcode" op="eq">{val.code}</condition>
    </where>
    <attr name="vs360_practiceareaid"/>
    <attr name="vs360_name"/>
</select>
```

This queries a lookup table (`vs360_practicearea`), matches records by code, and retrieves the ID and name for use in subsequent processing.

For your implementation, replace this with your service lookup logic. If you have a service catalog table, query it:

```xml
<select from="crm" entity="your_service_catalog_table" var="rec">
    <where>
        <condition attr="your_service_code_field" op="eq">{val.code}</condition>
    </where>
    <attr name="your_service_id_field"/>
    <attr name="your_service_name_field"/>
</select>
```

Alternatively, if you don't have a lookup table and want to hard-code service names, skip the select statement and define the values manually:

```xml
<!-- Skip the select, define manually -->
<set var="p">{new Dictionary()}</set>
<set var="p['your_service_id_field']">{val.code}</set>
<set var="p['your_service_name_field']">Service Name for {val.code}</set>
```


### Step 4: Customize the Template Replacement

The script dynamically replaces placeholders in the template with actual service-specific values. The example replaces "GGGGGGGG" with the practice area ID:

```xml
<set var="tx">{template}</set>
<set var="tx">{tx.Replace('GGGGGGGG',p.vs360_practiceareaid.ToString())}</set>
```

Customize this to match your placeholder and field names:

```xml
<set var="tx">{template}</set>
<set var="tx">{tx.Replace('PLACEHOLDER',p.your_service_id_field.ToString())}</set>

<!-- Multiple replacements if needed -->
<set var="tx">{tx.Replace('PLACEHOLDER2',p.another_field.ToString())}</set>
```

If your template includes multiple placeholders that need different values for each service, add multiple replacement operations as shown above.


### Step 5: Customize the Service Name Format

The service name format determines how Revenue Services appear in Dataverse and in reports. The example creates names with a category prefix:

```xml
<set var="name">({val.grp}) {p.vs360_name}</set>
```

This creates names like "(BL) Business Law", where "BL" is the category and "Business Law" is the service name retrieved from the lookup.

Customize the format to match your naming conventions:

```xml
<!-- Format: (Category) Service Name -->
<set var="name">({val.grp}) {p.your_service_name_field}</set>

<!-- Alternative format: Group Number - Service Name -->
<set var="name">{gid} - {p.your_service_name_field}</set>

<!-- Simple format: Just the service name -->
<set var="name">{p.your_service_name_field}</set>
```


## Script Execution Steps

1. **Customize the script** following the guide above
2. **Validate** your customizations:
   - Verify table names exist in Dataverse
   - Confirm field names are correct
   - Test query logic with a small sample
3. **Save** the modified script
4. **Run the script** in Sync360:
   - Load GenerateRevenueService.xml
   - Execute
   - Monitor for errors
5. **Verify results** in Dataverse:
   - Check Revenue Service table
   - Confirm records were created with correct names
   - Review JSON in `vs360_selectedcombinations` field


## What the Script Does

The script follows a systematic process to create Revenue Service records. It begins by initializing the query template and service data structures that you've customized. It then loops through each group in the Data dictionary, processing the services within that group one by one.

For each service, the script looks up service details from your source table (or uses hard-coded values if you've configured it that way). It creates a customized JSON query by taking the template and replacing placeholders with service-specific values, then formats the service name according to your naming convention. Before creating anything, it checks whether a Revenue Service already exists with this name to prevent duplicates. If the service doesn't already exist, it creates a new Revenue Service record with the formatted name, group number, and the customized JSON query configuration.


## Expected Results

After running the script successfully, you'll see new Revenue Service records created in Dataverse. Each record will have a unique name formatted according to your specification (such as "(GRP) Service Name"), an assigned group number, and a JSON query configuration stored in the `vs360_selectedcombinations` field. All created services will be in an active state and ready for processing. The script's duplicate checking ensures that no duplicate records are created, even if you run it multiple times.

**Example Results**:

| Name | Group | Status |
|------|-------|--------|
| (CORE) Consulting Services | 0 | Active |
| (CORE) Implementation Services | 0 | Active |
| (PREM) Managed Services | 1 | Active |
| (PREM) Support Services | 1 | Active |


## Common Customization Scenarios

### Scenario 1: Simple Product List
No lookup table, just create services from a static list:

```xml
<set var="Data['0']">{new List()}</set>
<set var="services">{new List()}</set>
<set var="services[]"><attr name="name">Product A</attr><attr name="id">PROD-A</attr></set>
<set var="services[]"><attr name="name">Product B</attr><attr name="id">PROD-B</attr></set>

<for var="svc" in="services">
    <set var="tx">{template}</set>
    <set var="tx">{tx.Replace('PLACEHOLDER', svc.id)}</set>
    <set var="name">{svc.name}</set>

    <!-- Check if exists and create if not -->
    <select from="crm" entity="vs360_revenueservice" var="rev">
        <where>
            <condition attr="vs360_name" op="eq">{name}</condition>
            <condition attr="statecode" op="eq">{0}</condition>
        </where>
    </select>
    <if condition="rev.Count eq 0">
        <create in="crm" entity="vs360_revenueservice" var="id">
            <attr name="vs360_name">{name}</attr>
            <attr name="vs360_group">{0}</attr>
            <attr name="vs360_selectedcombinations">{tx}</attr>
        </create>
    </if>
</for>
```

### Scenario 2: Multiple Query Templates
Different services need different query structures:

```xml
<set var="templateA"><![CDATA[{...query for type A services...}]]></set>
<set var="templateB"><![CDATA[{...query for type B services...}]]></set>

<set var="Data['0'][]"><attr name="code">A001</attr><attr name="grp">TYPE-A</attr><attr name="template">A</attr></set>
<set var="Data['0'][]"><attr name="code">B001</attr><attr name="grp">TYPE-B</attr><attr name="template">B</attr></set>

<!-- In processing loop -->
<for var="val" in="Data[grp]">
    <if condition="val.template eq 'A'">
        <set var="tx">{templateA}</set>
    </if>
    <if condition="val.template eq 'B'">
        <set var="tx">{templateB}</set>
    </if>
    <!-- Continue with replacements... -->
</for>
```


## Troubleshooting

### Issue: No records created
- **Check**: Verify service lookup query returns results
- **Check**: Ensure Revenue Service table is accessible
- **Review**: Script output/logs for errors

### Issue: Duplicate records
- Script should prevent this, but if it occurs:
- Deactivate or delete duplicates manually
- Verify name matching logic in script

### Issue: Invalid JSON in vs360_selectedcombinations
- **Validate**: JSON syntax in template (use JSON validator)
- **Check**: Placeholder replacement is working correctly
- **Test**: Query structure matches expected format

### Issue: Template doesn't match my data model
- This is expected - the template is just an example
- Completely rewrite the template section to match your needs
- Consider starting with a simple query and expanding


## Best Practices

1. **Test with one service first**: Comment out most services, run with one, verify, then expand
2. **Keep a backup**: Save original script before customizing
3. **Document your logic**: Add comments explaining your query criteria
4. **Version control**: Track changes to this script as it evolves with your client
5. **Validate JSON**: Use a JSON validator on your template before running
6. **Incremental changes**: Add services over time rather than trying to create everything at once
