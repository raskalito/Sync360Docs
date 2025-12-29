# White Space Solution - Overview

## Introduction

The White Space Solution is a comprehensive Dataverse-based system that provides an abstraction layer between your existing Dataverse customizations and analytical insights. This solution enables organizations to identify service sales patterns, discover correlations between services, and generate actionable intelligence for business development opportunities.

## Business Purpose

This solution addresses a key challenge in professional services and other industries: understanding which products or services are commonly purchased together, and identifying cross-selling or upselling opportunities. By creating a flexible abstraction layer, the solution provides the capability to define service criteria through configurable queries that determine whether a specific service or product was sold to a company. It enables organizations to analyze service patterns by identifying which services are frequently purchased together, and to discover correlations by finding relationships between different services across the customer base. These insights can then be used to generate targeted business development actions and opportunities.

## How It Works

### The Abstraction Layer Concept

Rather than hard-coding logic for specific products or services, this solution uses the Revenue Service table as an abstraction layer. Each Revenue Service record represents a product or service and contains three key elements. First, it includes a service definition with a name and group identifier. Second, it contains a query configuration, which is a JSON-based query that defines how to identify if a company has purchased this service. Finally, it stores processing results in the form of relationships to companies, showing which services have been matched (purchased) and which remain unmatched (not purchased).

### Processing Flow

The solution operates in a two-bucket model for each company. When processing occurs, services are classified as either matched services, meaning the company has purchased them based on query criteria, or unmatched services, meaning they are in the same service group but the company has not purchased them. This classification is performed across all companies that have at least one service from a particular service group, creating a comprehensive dataset for analysis.

## Key Components

### Configuration Layer

The configuration layer defines how the solution interacts with your Dataverse environment through configuration scripts. These scripts work in conjunction with abstraction queries, which are JSON-based queries that identify service purchases based on your specific business criteria.

### Processing Engine

The processing engine is responsible for evaluating all companies against all service definitions through service processing. It also tracks relationships between employees and services via employee processing, and analyzes service data across different company segments through segment processing capabilities.

### Analytics & Reporting

The analytics and reporting component provides several key outputs. The Services Matrix shows correlation percentages between different services, revealing which ones are commonly purchased together. The Potential Groups analysis identifies natural groupings of services based on actual purchase patterns. Financial Analysis provides revenue-based insights into service combinations, while Segment Analysis breaks down service adoption by company characteristics such as industry, region, or size.

## Solution Architecture

The architecture follows a clear data flow pattern. At the top level sits the Revenue Service Definition, which acts as the abstraction layer configured per client. This feeds into the Revenue Service Processing component, which executes the queries and classifies companies. The results are stored as Matched and Unmatched Relationships, representing the Company to Service associations. Finally, the Analytics Reports component generates insights from this relationship data, enabling business decisions and actions.

```
┌─────────────────────────────────────────────────────────────┐
│                    Revenue Service Definition               │
│  (Abstraction Layer - Configured per Client)                │
└─────────────────────┬───────────────────────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────────────────────┐
│              Revenue Service Processing                     │
│  (Executes queries, classifies companies)                   │
└─────────────────────┬───────────────────────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────────────────────┐
│           Matched/Unmatched Relationships                   │
│  (Company ↔ Service associations stored)                    │
└─────────────────────┬───────────────────────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────────────────────┐
│              Analytics Reports                              │
│  (Generate insights from relationship data)                 │
└─────────────────────────────────────────────────────────────┘
```

## Implementation Phases

### Phase 1: Initial Setup

The initial setup phase focuses on preparing the solution for your specific environment. This involves configuring the solution for your Dataverse environment, customizing the GenerateRevenueService script to match your data model, and generating the initial Revenue Service records that will drive the analysis.

### Phase 2: Initial Processing

During the initial processing phase, you will run the Revenue Service Processing to classify all companies according to their service adoption patterns. Following this classification, you will execute analytics reports to identify patterns and correlations within your data.

### Phase 3: Analysis & Refinement

The analysis and refinement phase involves reviewing the correlation reports generated during processing to understand service relationships. You will identify meaningful service groupings based on actual purchase patterns and adjust Revenue Service groups based on these findings to optimize the analysis for your specific business context.

### Phase 4: Ongoing Operations

In the ongoing operations phase, you will schedule automatic Revenue Service Processing to keep data current as your business evolves. The insights generated should be used to drive business development activities such as cross-selling and upselling campaigns. Additionally, you will maintain service definitions as your business offerings and market conditions change over time.

## Universal Applicability

While examples and references may reference professional services, this solution is industry-agnostic. The abstraction layer approach makes it suitable for professional services firms such as legal, consulting, and accounting practices. Technology and SaaS companies can use it for feature adoption tracking and upselling opportunities. Manufacturing companies can analyze product combinations and bundles. Healthcare organizations can examine service packages and treatment combinations. Retail businesses can identify product affinities and cross-selling opportunities. In fact, any industry where understanding purchase patterns provides value can benefit from this solution.

## Benefits

The solution offers flexibility through its ability to define any criteria for service identification without code changes. It provides scalability by processing thousands of companies and services efficiently through automated batch operations. The insights delivered are valuable because the solution discovers non-obvious correlations in your data that might not be apparent through manual analysis. The results are actionable, generating targeted opportunities based on data-driven insights rather than intuition alone. Finally, the solution is maintainable because you can update service definitions without changing underlying code, allowing the system to evolve with your business.

## Getting Started

To implement this solution, you will work through several areas of documentation. The Dataverse Tables section will help you understand the data model and table structures. The Prerequisites and Configuration section covers environment setup and configuration requirements. The Script Documentation provides detailed guidance on how to use each script for processing and analysis. The Implementation Workflow section offers a step-by-step guide for deploying the solution in your environment.

**Document Version**: 1.0
**Last Updated**: 2025
**Target Audience**: Dataverse Consultants
