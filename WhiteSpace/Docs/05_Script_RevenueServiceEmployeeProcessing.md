# Script: RevenueServiceEmployeeProcessing.xml

## Purpose

RevenueServiceEmployeeProcessing creates relationships between employees and Revenue Services based on employee involvement with those services. This script enables employee expertise mapping, resource planning, and talent management analytics by tracking which employees work with which services. Unlike the main RevenueServiceProcessing script which focuses on companies, this script is employee-focused and can run independently, though the two scripts work together to provide a complete picture of both the demand side (companies purchasing services) and the supply side (employees delivering services). The script uses the same configuration-driven approach with JSON queries, and it automatically synchronizes employee-service relationships to reflect the current state of your data.


## When to Use This Script

Use RevenueServiceEmployeeProcessing when you need to map employee expertise to services, enabling you to answer questions about who has experience with which service types. The script is valuable for resource planning, helping you understand which employees work on which service types and whether you have sufficient capacity. It supports talent management initiatives by building competency matrices or skill inventories, and it aids capacity analysis by revealing employee workload distribution across services. Run the script after employee data changes, such as when employee assignments are updated, new projects are staffed, or team structures are reorganized.


## What You Need Before Running

### Prerequisites
1. **Configuration.xml**: Properly configured
2. **Employee table**: vs360_employee table with employee records
3. **Revenue Services configured**: Must include "employees" section in JSON
4. **Source data**: Data linking employees to service activities

### Revenue Service JSON Requirements

For this script to function, Revenue Services must have an **"employees"** section in their `vs360_selectedcombinations` JSON:

```json
{
    "rules": [...],
    "employees": [
        {
            "entity": "source_table",
            "customer_field": "employee_field_name",
            "conditions": [...],
            "links": [...]
        }
    ]
}
```

**Important**: If a Revenue Service doesn't have an "employees" section, this script will skip it for employee processing (but won't error).


## What the Script Does

### Process Flow

```
1. Load all active Revenue Services
2. Group services by vs360_group number
3. Load existing employee-service relationships
4. For each Revenue Service with "employees" query:
   - Execute the employees query
   - Identify employees associated with this service
   - Store in current employee-service dictionary
5. Compare current vs existing relationships:
   - Add new employee-service associations
   - Remove outdated associations
```

### Detailed Operations

#### Phase 1: Initialization

The script begins by explicitly disabling order processing, as orders are not relevant for employee tracking. It loads Configuration.xml to access all necessary settings and initializes the data structures needed for employee-service tracking.

#### Phase 2: Load Data

The script loads all active Revenue Services from Dataverse and retrieves existing employee-service relationships from the `vs360_employee_vs360_revenueservice` table. This establishes the baseline against which current employee associations will be compared.

#### Phase 3: Execute Employee Queries

For each Revenue Service, the script parses the JSON configuration from the `vs360_selectedcombinations` field and checks whether an "employees" section exists. If the section is present, the script executes the dynamic query using the same DynamicQuery.xml engine used by the main processing script, collecting the employee IDs that match the specified criteria. This builds a comprehensive map of which employees are currently associated with each service.

#### Phase 4: Update Relationships

The script compares the current employee-service associations (from the queries) with the existing relationships (from the database). It adds new associations for employees who are now working with services they weren't previously associated with, and it removes old associations for employees who are no longer working with services they were previously linked to. This ensures the relationship table always reflects the current state.


## Configuring Employee Queries

### In GenerateRevenueService.xml

When creating Revenue Service records, include an "employees" section in the template:

```xml
<set var="template"><![CDATA[{
    "rules": [
        {
            "entity": "vs360_job",
            "field": "vs360_accountid",
            "conditions": [...]
        }
    ],
    "employees": [
        {
            "entity": "vs360_job",
            "customer_field": "vs360_employeeid",
            "conditions": [
                {
                    "_and": [
                        {
                            "attr": "vs360_practiceareaid",
                            "op": "eq",
                            "value": "SERVICE_ID_PLACEHOLDER",
                            "type": "string"
                        }
                    ]
                }
            ]
        }
    ]
}]]></set>
```

### Key Fields in "employees" Section

| Field | Description | Example |
|-------|-------------|---------|
| `entity` | Source table containing employee data | "vs360_job", "engagement", "timesheet" |
| `customer_field` | Field that contains employee reference | "vs360_employeeid", "ownerid" |
| `conditions` | Criteria to match employees to service | Same format as rules conditions |
| `links` | Optional joins to related tables | Same format as rules links |

### Common Scenarios

#### Scenario 1: Employees from Job/Engagement Table
```json
"employees": [
    {
        "entity": "vs360_job",
        "customer_field": "vs360_employeeid",
        "conditions": [
            {
                "_and": [
                    {
                        "attr": "vs360_practiceareaid",
                        "op": "eq",
                        "value": "PRACTICE_AREA_GUID",
                        "type": "string"
                    },
                    {
                        "attr": "statecode",
                        "op": "eq",
                        "value": 0,
                        "type": "int"
                    }
                ]
            }
        ]
    }
]
```
*Finds employees who worked on jobs in a specific practice area*

#### Scenario 2: Employees from Timesheet
```json
"employees": [
    {
        "entity": "timesheet",
        "customer_field": "employeeid",
        "conditions": [
            {
                "_and": [
                    {
                        "attr": "service_type",
                        "op": "eq",
                        "value": "CONSULTING",
                        "type": "string"
                    },
                    {
                        "attr": "hours",
                        "op": "gt",
                        "value": 10,
                        "type": "decimal"
                    }
                ]
            }
        ]
    }
]
```
*Finds employees who logged more than 10 hours on consulting services*

#### Scenario 3: Employees with Specific Certification
```json
"employees": [
    {
        "entity": "vs360_employee",
        "customer_field": "vs360_employeeid",
        "conditions": [
            {
                "_and": [
                    {
                        "attr": "certification",
                        "op": "eq",
                        "value": "CERT_XYZ",
                        "type": "string"
                    }
                ]
            }
        ],
        "links": [
            {
                "type": "inner",
                "relationship": "employee_certifications",
                "to": "employeeid",
                "from": "vs360_employeeid",
                "conditions": [
                    {
                        "_and": [
                            {
                                "attr": "status",
                                "op": "eq",
                                "value": "Active"
                            }
                        ]
                    }
                ]
            }
        ]
    }
]
```
*Finds employees with active certifications for a service*


## Script Execution

### Running the Script

1. **Verify Prerequisites**:
   - Revenue Services have "employees" sections in their JSON
   - Employee table exists and has records
   - Source data is available

2. **Execute**:
   - Load RevenueServiceEmployeeProcessing.xml in Sync360
   - Run the script
   - Monitor for any errors

3. **Review Results**:
   - Check `vs360_employee_vs360_revenueservice` table
   - Verify employee-service associations are correct
   - Spot-check a few employees

### Execution Time

Typically faster than RevenueServiceProcessing:
- Fewer records to process (employees vs companies)
- No order management
- Simple relationship creation

**Expected**: Seconds to a few minutes depending on employee count and query complexity


## Expected Results

After successful execution, the `vs360_employee_vs360_revenueservice` table will contain:

| Employee ID | Revenue Service ID |
|-------------|-------------------|
| EMP-001-GUID | Service-A-GUID |
| EMP-001-GUID | Service-B-GUID |
| EMP-002-GUID | Service-A-GUID |
| EMP-003-GUID | Service-C-GUID |

**Interpretation**:
- Employee 001 works with Services A and B
- Employee 002 works with Service A
- Employee 003 works with Service C


## Use Cases for Employee-Service Data

### 1. Expertise Mapping
Identify which employees have experience with which services:
- "Who can staff a new engagement in Service X?"
- "Which employees have the broadest service experience?"

### 2. Resource Planning
Understand capacity and workload:
- "Do we have enough employees for Service A?"
- "Is Employee X overcommitted across multiple services?"

### 3. Training & Development
Identify skill gaps:
- "Which services have insufficient employee coverage?"
- "Which employees need training in Service Y?"

### 4. Team Formation
Build balanced teams:
- "Form a team with expertise across Services A, B, C"
- "Who are the subject matter experts for Service X?"

### 5. Succession Planning
Identify dependencies:
- "If Employee X leaves, which services are at risk?"
- "Which services have only one expert?"


## Integration with Other Scripts

### Relationship to RevenueServiceProcessing

The two scripts are independent and can run separately, but they provide complementary insights. RevenueServiceProcessing answers "What services do companies buy?" while RevenueServiceEmployeeProcessing answers "Who delivers these services?" Together, they enable you to match supply (employee expertise) with demand (company purchases and white space opportunities).

### Relationship to Reports

Employee-service data can be incorporated into custom reports to enhance your analysis. For example, you can cross-reference employee expertise with unmatched company services to identify whether you have the right staff to pursue new opportunities. You can also analyze employee distribution across service groups to understand whether your talent is aligned with your market opportunities.


## Customization Options

Different organizations track employee involvement in different ways. The script supports multiple approaches through the employee query configuration.

**Timesheet-based** tracking identifies employees by the time they've logged against specific service types:
```xml
<!-- In Revenue Service JSON -->
"employees": [{
    "entity": "timesheet",
    "customer_field": "employeeid",
    "conditions": [{
        "_and": [
            {"attr": "project_type", "op": "eq", "value": "SERVICE_TYPE"},
            {"attr": "date", "op": "ge", "value": "2024-01-01", "type": "date"}
        ]
    }]
}]
```

**Role-based** tracking associates employees with services based on their organizational role or department assignment:
```xml
"employees": [{
    "entity": "vs360_employee",
    "customer_field": "vs360_employeeid",
    "conditions": [{
        "_and": [
            {"attr": "role", "op": "eq", "value": "CONSULTANT"},
            {"attr": "department", "op": "eq", "value": "SERVICE_DEPT"}
        ]
    }]
}]
```

**Certification-based** tracking links employees to services based on certifications or qualifications they hold (see Scenario 3 above for a complete example).


## Troubleshooting

### Issue: No employee relationships created
**Possible causes**:
- Revenue Services don't have "employees" section in JSON
- Employee queries not returning results
- Employee table empty or inaccessible

**Debug**:
1. Check sample Revenue Service JSON has "employees" section
2. Test employee query manually
3. Verify employee table exists and has records

### Issue: Wrong employees associated with services
**Check**:
- Employee query conditions are correct
- Field names in "customer_field" are accurate
- Conditions match your business logic

### Issue: Relationships not updating
**Solutions**:
- Verify script completed without errors
- Check permissions to write to relationship table
- Re-run script after verifying configuration


## Best Practices

1. **Define clear criteria**: Employee-service relationship should be meaningful (not just "employee exists")
2. **Include thresholds**: Consider minimum hours, dates, or other qualifiers to ensure relevance
3. **Keep queries simple**: Employee queries typically simpler than company queries
4. **Regular updates**: Run periodically to keep expertise mapping current
5. **Validate results**: Spot-check employee associations to ensure accuracy


## Advanced: Historical vs Current

The script tracks **current** employee-service relationships. If you need historical tracking, there are two approaches you can consider.

The **snapshot approach** involves running the script periodically, exporting the results to a historical table, and comparing snapshots over time. This provides a historical record of how employee expertise has evolved, useful for tracking skill development and identifying trends in resource allocation.

The **date filtering approach** includes date ranges in your employee queries to scope the relationships to specific time periods:
```json
"conditions": [{
    "_and": [
        {"attr": "date", "op": "ge", "value": "2024-01-01", "type": "date"},
        {"attr": "date", "op": "le", "value": "2024-12-31", "type": "date"}
    ]
}]
```

This allows you to generate point-in-time views of employee expertise for specific periods, which is valuable for performance reviews or historical analysis.
