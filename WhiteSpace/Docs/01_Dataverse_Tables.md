# Dataverse Tables Structure

## Overview

This document describes the Dataverse tables used by the Revenue Service Analytics Solution. These tables are deployed as part of the Dataverse solution package (not included in this repository) and must be installed before using the Sync360 scripts.

## Core Tables

### 1. Revenue Service (vs360_revenueservice)

The primary abstraction table that defines service/product definitions and their identification criteria.

**Purpose**: Represents a service or product offering and contains the logic to determine if a company has purchased it.

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

**Relationships**:
- Many-to-Many with Company (Account) via matched/unmatched relationship tables
- Many-to-Many with Revenue Service Order
- Many-to-Many with Employee
- One-to-Many with Revenue Segment Dispersion

---

### 2. Company (account)

Standard Dataverse Account table representing companies/customers.

**Purpose**: Represents the companies being analyzed for service adoption patterns.

**Key Fields Used**:
- `accountid` - Primary key
- Additional custom fields as defined in your implementation

**Relationships**:
- Many-to-Many with Revenue Service (via matched/unmatched tables)

---

### 3. Revenue Service Order (vs360_revenueserviceorder)

Tracks individual service orders or engagements associated with companies and services.

**Purpose**: Optional detailed tracking of specific service instances (dates, revenue, etc.)

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

**Relationships**:
- Lookup to Company (Account)
- Many-to-Many with Revenue Service

---

### 4. Employee (vs360_employee)

Represents employees associated with service delivery.

**Purpose**: Track which employees work with which services (for resource planning, expertise mapping)

**Key Fields**:

| Field Name | Type | Description |
|------------|------|-------------|
| `vs360_employeeid` | Unique Identifier | Primary key |
| Additional fields as per implementation | | |

**Relationships**:
- Many-to-Many with Revenue Service

---

### 5. Revenue Segment (vs360_revenuesegment)

Defines analytical segments for grouping companies by characteristics.

**Purpose**: Analyze service adoption patterns across different company segments (e.g., by industry, size, region)

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

**Relationships**:
- One-to-Many with Revenue Segment Value

---

### 6. Revenue Segment Value (vs360_rsvalue)

Stores distinct values found in a segment field.

**Purpose**: Captures each unique value for a segment (e.g., if segment is "Industry", values might be "Technology", "Healthcare", etc.)

**Key Fields**:

| Field Name | Type | Description |
|------------|------|-------------|
| `vs360_rsvalueid` | Unique Identifier | Primary key |
| `vs360_name` | Text | The segment value (lowercase, trimmed) |
| `vs360_total` | Whole Number | Count of companies with this value |
| `vs360_revenuesegmentid` | Lookup | Parent Revenue Segment |

**Relationships**:
- Lookup to Revenue Segment
- One-to-Many with Revenue Segment Dispersion

---

### 7. Revenue Segment Dispersion (vs360_rsdispersion)

Statistical breakdown of service adoption within a segment value.

**Purpose**: Shows how each service performs within each segment value (e.g., "80% of Technology companies purchased Service A")

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

**Relationships**:
- Lookup to Revenue Segment Value
- Lookup to Revenue Service

---

## Relationship Tables (Many-to-Many)

### 8. Company-Revenue Service Matched (vs360_account_vs360_revenueservice_matched)

**Purpose**: Intersect table storing companies that HAVE purchased each service

**Fields**:
- `accountid` - Company identifier
- `vs360_revenueserviceid` - Revenue Service identifier

---

### 9. Company-Revenue Service Unmatched (vs360_account_vs360_revenueservice_unmatched)

**Purpose**: Intersect table storing companies that have NOT purchased each service (but are in the same service group)

**Fields**:
- `accountid` - Company identifier
- `vs360_revenueserviceid` - Revenue Service identifier

---

### 10. Revenue Service-Order (vs360_revenueservice_orders)

**Purpose**: Links Revenue Services to Revenue Service Orders

**Fields**:
- `vs360_revenueserviceid` - Revenue Service identifier
- `vs360_revenueserviceorderid` - Order identifier

---

### 11. Employee-Revenue Service (vs360_employee_vs360_revenueservice)

**Purpose**: Links Employees to Revenue Services they work with

**Fields**:
- `vs360_employeeid` - Employee identifier
- `vs360_revenueserviceid` - Revenue Service identifier

---

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

---

## Important Notes

### Group Concept
Revenue Services with the same `vs360_group` number are analyzed together. Processing only considers companies that have at least one matched service within a group. All services in that group are then evaluated for that company.

### Matched vs Unmatched
- **Matched**: Company meets the criteria defined in the Revenue Service query
- **Unmatched**: Company does NOT meet the criteria, but is part of the processing scope (has at least one other service in the same group)

### Active Records Only
Most processing scripts only work with active Revenue Service records (`statecode = 0`). Deactivate services you no longer want to include in analysis.

### Configuration Independence
The tables can work with any source data in your Dataverse environment. The `vs360_selectedcombinations` JSON allows complete flexibility in defining what constitutes a "purchased service."

---

**Next**: Proceed to Prerequisites and Configuration to set up your environment for running the Sync360 scripts.
