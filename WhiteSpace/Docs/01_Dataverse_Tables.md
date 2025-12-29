# Dataverse Tables Structure

## Overview

This document describes the Dataverse tables used by the White Space Solution. These tables are deployed as part of the Dataverse solution package (not included in this repository) and must be installed before using the Sync360 scripts.

## Core Tables

### 1. Revenue Service (vs360_revenueservice)

The Revenue Service table serves as the primary abstraction table that defines service and product definitions along with their identification criteria. It represents a service or product offering and contains the logic to determine if a company has purchased it.

**Key Fields**:

| Field Name | Type | Description |
|------------|------|-------------|
| `vs360_revenueserviceid` | Unique Identifier | Primary key |
| `vs360_name` | Text | Service name (format: "(GroupNumber) Service Name") |
| `vs360_group` | Whole Number | Group identifier for related services |
| `vs360_selectedcombinations` | Text (Multi-line) | JSON configuration defining the query criteria |
| `statecode` | Choice | Active (0) or Inactive (1) |

**JSON Configuration Structure** (`vs360_selectedcombinations`):
```json
{
    "rules": [
        {
            "entity": "source_table_name",
            "field": "company_field_name",
            "conditions": [...],
            "links": [...]
        }
    ],
    "employees": [
        {
            "entity": "source_table_name",
            "customer_field": "employee_field_name",
            "conditions": [...],
            "links": [...]
        }
    ]
}
```

The Revenue Service table maintains several important relationships. It has a many-to-many relationship with Company (Account) through the matched and unmatched relationship tables. It also connects to Revenue Service Order and Employee tables through many-to-many relationships. Additionally, it has a one-to-many relationship with Revenue Segment Dispersion for statistical analysis.

### 2. Company (account)

The Company table is the standard Dataverse Account table representing companies and customers. It serves as the foundation for analyzing service adoption patterns across your customer base. The primary key is the `accountid` field, and you can leverage additional custom fields as defined in your specific implementation. The table connects to Revenue Service through many-to-many relationships via the matched and unmatched tables.

### 3. Revenue Service Order (vs360_revenueserviceorder)

The Revenue Service Order table tracks individual service orders or engagements associated with companies and services. This table provides optional detailed tracking of specific service instances, including dates and revenue information.

**Key Fields**:

| Field Name | Type | Description |
|------------|------|-------------|
| `vs360_revenueserviceorderid` | Unique Identifier | Primary key |
| `vs360_name` | Text | Order identifier |
| `vs360_accountid` | Lookup | Related company |
| `vs360_startdate` | Date | Service start date |
| `vs360_enddate` | Date | Service end date |
| `vs360_revenue` | Decimal | Revenue amount |
| `vs360_status` | Choice | Order status |

This table includes a lookup relationship to Company (Account) and maintains a many-to-many relationship with Revenue Service to enable detailed tracking of service instances.

### 4. Employee (vs360_employee)

The Employee table represents employees associated with service delivery. Its purpose is to track which employees work with which services, enabling resource planning and expertise mapping capabilities.

**Key Fields**:

| Field Name | Type | Description |
|------------|------|-------------|
| `vs360_employeeid` | Unique Identifier | Primary key |
| Additional fields as per implementation | | |

The Employee table connects to Revenue Service through a many-to-many relationship, allowing the system to track employee expertise and service involvement.

### 5. Revenue Segment (vs360_revenuesegment)

The Revenue Segment table defines analytical segments for grouping companies by characteristics. It enables analysis of service adoption patterns across different company segments such as industry, size, or region.

**Key Fields**:

| Field Name | Type | Description |
|------------|------|-------------|
| `vs360_revenuesegmentid` | Unique Identifier | Primary key |
| `vs360_name` | Text | Segment name |
| `vs360_group` | Whole Number | Associated Revenue Service group |
| `vs360_query` | Text (Multi-line) | JSON configuration for segment field |
| `vs360_total` | Whole Number | Total companies in this segment |
| `vs360_hasvalue` | Whole Number | Companies with a value for this segment |

**JSON Configuration Structure** (`vs360_query`):
```json
{
    "rules": {
        "field": "field_name_on_company"
    }
}
```

The Revenue Segment table has a one-to-many relationship with Revenue Segment Value to store distinct values found within each segment.

### 6. Revenue Segment Value (vs360_rsvalue)

The Revenue Segment Value table stores distinct values found in a segment field. For example, if a segment is defined as "Industry", this table would capture unique values such as "Technology", "Healthcare", "Manufacturing", and so forth.

**Key Fields**:

| Field Name | Type | Description |
|------------|------|-------------|
| `vs360_rsvalueid` | Unique Identifier | Primary key |
| `vs360_name` | Text | The segment value (lowercase, trimmed) |
| `vs360_total` | Whole Number | Count of companies with this value |
| `vs360_revenuesegmentid` | Lookup | Parent Revenue Segment |

This table maintains a lookup relationship to Revenue Segment and has a one-to-many relationship with Revenue Segment Dispersion for statistical calculations.

### 7. Revenue Segment Dispersion (vs360_rsdispersion)

The Revenue Segment Dispersion table provides a statistical breakdown of service adoption within each segment value. It shows how each service performs within each segment value, such as "80% of Technology companies purchased Service A".

**Key Fields**:

| Field Name | Type | Description |
|------------|------|-------------|
| `vs360_rsdispersionid` | Unique Identifier | Primary key |
| `vs360_rsvalueid` | Lookup | Related Revenue Segment Value |
| `vs360_revenueserviceid` | Lookup | Related Revenue Service |
| `vs360_matched` | Whole Number | Count of companies with this service |
| `vs360_unmatched` | Whole Number | Count of companies without this service |
| `vs360_matchedpercent` | Decimal | Percentage matched |
| `vs360_unmatchedpecent` | Decimal | Percentage unmatched |

This table connects to both Revenue Segment Value and Revenue Service through lookup relationships, enabling detailed statistical analysis.

## Relationship Tables (Many-to-Many)

### 8. Company-Revenue Service Matched (vs360_account_vs360_revenueservice_matched)

This intersect table stores companies that HAVE purchased each service. It contains the `accountid` field representing the company identifier and the `vs360_revenueserviceid` field representing the Revenue Service identifier.

### 9. Company-Revenue Service Unmatched (vs360_account_vs360_revenueservice_unmatched)

This intersect table stores companies that have NOT purchased each service but are in the same service group. Like the matched table, it contains the `accountid` and `vs360_revenueserviceid` fields to establish the relationship.

### 10. Revenue Service-Order (vs360_revenueservice_orders)

This table links Revenue Services to Revenue Service Orders, containing the `vs360_revenueserviceid` and `vs360_revenueserviceorderid` fields to establish the many-to-many relationship.

### 11. Employee-Revenue Service (vs360_employee_vs360_revenueservice)

This table links Employees to Revenue Services they work with. It contains the `vs360_employeeid` and `vs360_revenueserviceid` fields to track employee expertise and service involvement.

## Data Model Diagram

```
┌──────────────────┐
│  Revenue Service │
│   (Core Table)   │
└────────┬─────────┘
         │
         ├─────────────────────────┐
         │                         │
         ▼                         ▼
┌─────────────────┐      ┌─────────────────┐
│  Company (N:N)  │      │   Employee      │
│   - Matched     │      │     (N:N)       │
│   - Unmatched   │      └─────────────────┘
└─────────────────┘
         │
         ▼
┌──────────────────────┐
│ Revenue Service      │
│      Order           │
│  (Optional Detail)   │
└──────────────────────┘

┌──────────────────┐
│ Revenue Segment  │
│                  │
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│ RS Value         │
│                  │
└────────┬─────────┘
         │
         ▼
┌──────────────────┐       ┌──────────────────┐
│ RS Dispersion    ├──────→│  Revenue Service │
│                  │       │                  │
└──────────────────┘       └──────────────────┘
```

## Important Concepts

### Group Concept

Revenue Services with the same `vs360_group` number are analyzed together as a cohesive unit. The processing logic only considers companies that have at least one matched service within a group. Once a company qualifies through this initial match, all services in that group are then evaluated for that company, creating both matched and unmatched relationships as appropriate.

### Matched vs Unmatched

The distinction between matched and unmatched is fundamental to how the solution works. A matched relationship means the company meets the criteria defined in the Revenue Service query, indicating they have purchased or engaged with that service. An unmatched relationship means the company does NOT meet the criteria, but they are still part of the processing scope because they have at least one other service in the same group. This creates the "white space" opportunity for cross-selling.

### Active Records Only

Most processing scripts only work with active Revenue Service records, identified by `statecode = 0`. If you want to exclude certain services from analysis, you should deactivate them rather than delete them, preserving the historical definition while removing them from current processing.

### Configuration Independence

The tables are designed to work with any source data in your Dataverse environment. The `vs360_selectedcombinations` JSON field allows complete flexibility in defining what constitutes a "purchased service", making the solution adaptable to diverse business scenarios and data models without requiring changes to the underlying table structure.
