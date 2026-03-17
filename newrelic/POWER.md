---
name: "newrelic"
displayName: "New Relic Observability"
description: "Query logs, metrics, traces, and events from New Relic using NRQL for production debugging and performance analysis"
keywords: ["newrelic", "observability", "monitoring", "nrql", "logs", "metrics", "apm", "traces", "alerts"]
author: "New Relic"
---

# Onboarding

Before proceeding, let the user know that the MCP server is currently in preview and they may need to request access by visiting https://mcp.newrelic.com/

# Overview

The New Relic Observability Power provides comprehensive access to your New Relic monitoring data through NRQL (New Relic Query Language). Query logs, metrics, APM traces, infrastructure data, and custom events for production debugging and performance analysis.

**Key capabilities:**
- **NRQL Queries**: Execute powerful queries against your telemetry data
- **APM**: Monitor application performance, transactions, and errors
- **Logs**: Search and analyze application and infrastructure logs
- **Metrics**: Query dimensional metrics and golden signals
- **Distributed Tracing**: Investigate request flows across services
- **Alerts**: Query and analyze alert violations and incidents
- **Entity Management**: Discover and query services, hosts, and applications

**Authentication**: Requires New Relic API key and account ID.

## Available Steering Files

This power has the following steering files:
- **steering** - Comprehensive NRQL guide with query patterns, golden signals, and troubleshooting workflows

## Available MCP Servers

### newrelic-mcp-server
**Connection:** OAuth-based MCP server at `https://mcp.newrelic.com/mcp/`

**Tool Categories:**
The MCP server provides 30+ tools organized into 6 categories: Discovery, Data Access, Alerting, Incident Response, Performance Analytics, and Advanced Analysis.

**Entity and Account Management (tag: discovery):**

1. **convert_time_period_to_epoch_ms** - Convert time period to epoch milliseconds
   - Converts relative time (e.g., "last 30 minutes") to epoch ms

2. **get_dashboard** - Fetch dashboard details
   - Get details about a specific dashboard

3. **get_entity** - Fetch entities by GUID or search by name
   - Search and retrieve New Relic entities

4. **list_related_entities** - List entities related to a given entity
   - Shows entities 1 hop away from a given entity GUID

5. **list_available_new_relic_accounts** - List all accessible accounts
   - Returns all account IDs available to the user

6. **list_dashboards** - List all dashboards
   - Returns dashboards for a New Relic account

7. **list_entity_types** - List complete entity type catalog
   - Shows all entity types with domain/type definitions

8. **search_entity_with_tag** - Search entities by tag
   - Find entities using specific tag key and value

**Data Access (tag: data-access):**

9. **execute_nrql_query** - Execute NRQL query against NRDB
   - Run any NRQL query to retrieve data

10. **natural_language_to_nrql_query** - Convert natural language to NRQL
    - Converts natural language request into NRQL, executes it, returns results

**Alerts and Monitoring (tag: alerting):**

11. **list_alert_conditions** - List alert conditions for a policy
    - Get alert condition details for specific policy

12. **search_incident** - Search alert events with filtering
    - List all alert events (open and closed) with flexible filters

13. **list_alert_policies** - List alert policies
    - Get alert policies for account, optionally filter by name

14. **list_recent_issues** - List all open issues
    - Shows all currently open issues in New Relic

15. **list_synthetic_monitors** - List synthetic monitors
    - Get automated tests checking service availability and performance

**Incident Response (tag: incident-response):**

16. **analyze_deployment_impact** - Analyze deployment performance impact
    - Evaluate how a deployment affected entity performance

17. **generate_alert_insights_report** - Generate alert intelligence report
    - Create analysis report for a specific issue

18. **generate_user_impact_report** - Generate end-user impact analysis
    - Analyze end-user impact for a specific issue

19. **list_entity_error_groups** - Fetch error groups from Errors Inbox
    - Get error groups for entity within time window

20. **list_change_events** - List change event history
    - Show deployment/change events for an entity

**Performance Analytics (tag: performance-analytics):**

21. **analyze_entity_logs** - Analyze application logs
    - Identify error patterns, anomalies, and recurring issues

22. **analyze_golden_metrics** - Analyze golden metrics
    - Evaluate throughput, response time, error rate, saturation

23. **analyze_kafka_metrics** - Analyze Kafka metrics
    - Examine consumer lag, producer throughput, message latency, partition balance

24. **analyze_threads** - Analyze thread metrics
    - Review thread state, CPU usage, memory consumption

25. **analyze_transactions** - Analyze transaction performance
    - Identify slow and error-prone transactions

26. **list_garbage_collection_metrics** - List GC and memory metrics
    - Get garbage collection metrics for JVM applications

27. **list_recent_logs** - List recent logs
    - Fetch recent logs for specified account and entity GUID

**Advanced Analysis (tag: advanced-analysis):**

Some tools appear in multiple categories:
- **analyze_deployment_impact** (also in incident-response)
- **analyze_entity_logs** (also in performance-analytics)
- **generate_alert_insights_report** (also in incident-response)
- **generate_user_impact_report** (also in incident-response)
- **natural_language_to_nrql_query** (also in data-access)

## Tool Usage Examples

### Using Natural Language

**The simplest way to use New Relic MCP tools is through natural language:**

```
"What entities are in my account?"
"Show me recent alerts for my web application"
"What are the top errors from the last hour?"
"Analyze the performance impact of the latest deployment"
```

Your AI agent will automatically select the appropriate tools and format results.

### NRQL Queries

**Execute NRQL directly:**
```javascript
usePower("newrelic", "newrelic-mcp-server", "execute_nrql_query", {
  "query": "SELECT count(*) FROM Transaction WHERE appName = 'MyApp' AND error IS true FACET error.class SINCE 1 hour ago"
})
// Returns: Error counts by error class
```

**Convert natural language to NRQL:**
```javascript
usePower("newrelic", "newrelic-mcp-server", "natural_language_to_nrql_query", {
  "request": "Show me the slowest transactions in the last hour"
})
// Automatically generates and executes appropriate NRQL
```

### Entity Discovery

**Find entities by name:**
```javascript
usePower("newrelic", "newrelic-mcp-server", "get_entity", {
  "name_pattern": "checkout-api"
})
// Returns: Entities matching the pattern
```

**Search by tag:**
```javascript
usePower("newrelic", "newrelic-mcp-server", "search_entity_with_tag", {
  "tag_key": "environment",
  "tag_value": "production"
})
// Returns: All production entities
```

**List related entities:**
```javascript
usePower("newrelic", "newrelic-mcp-server", "list_related_entities", {
  "entity_guid": "MTIzNDU2fEFQTXxBUFBMSUNBVElPTnwxMjM0NTY"
})
// Returns: Services, databases, hosts related to this entity
```

### Performance Analysis

**Analyze golden metrics:**
```javascript
usePower("newrelic", "newrelic-mcp-server", "analyze_golden_metrics", {
  "entity_guid": "MTIzNDU2fEFQTXxBUFBMSUNBVElPTnwxMjM0NTY",
  "time_window": "SINCE 1 hour ago"
})
// Returns: Throughput, response time, error rate, saturation analysis
```

**Analyze transactions:**
```javascript
usePower("newrelic", "newrelic-mcp-server", "analyze_transactions", {
  "entity_guid": "MTIzNDU2fEFQTXxBUFBMSUNBVElPTnwxMjM0NTY",
  "time_window": "SINCE 4 hours ago"
})
// Returns: Slow and error-prone transaction analysis
```

**Analyze logs:**
```javascript
usePower("newrelic", "newrelic-mcp-server", "analyze_entity_logs", {
  "entity_guid": "MTIzNDU2fEFQTXxBUFBMSUNBVElPTnwxMjM0NTY",
  "time_window": "SINCE 1 hour ago"
})
// Returns: Error patterns, anomalies, recurring issues
```

### Alert Management

**List open issues:**
```javascript
usePower("newrelic", "newrelic-mcp-server", "list_recent_issues", {
  "account_id": "123456"
})
// Returns: All currently open issues
```

**Search incidents:**
```javascript
usePower("newrelic", "newrelic-mcp-server", "search_incident", {
  "account_id": "123456",
  "filters": {
    "state": "OPEN",
    "priority": "CRITICAL"
  }
})
// Returns: Open critical incidents
```

**Generate alert insights:**
```javascript
usePower("newrelic", "newrelic-mcp-server", "generate_alert_insights_report", {
  "issue_id": "abc-123-def"
})
// Returns: Detailed analysis report with insights
```

### Incident Response

**Analyze deployment impact:**
```javascript
usePower("newrelic", "newrelic-mcp-server", "analyze_deployment_impact", {
  "entity_guid": "MTIzNDU2fEFQTXxBUFBMSUNBVElPTnwxMjM0NTY",
  "deployment_time": "2024-03-05T10:30:00Z"
})
// Returns: Performance impact analysis of the deployment
```

**Check error groups:**
```javascript
usePower("newrelic", "newrelic-mcp-server", "list_entity_error_groups", {
  "entity_guid": "MTIzNDU2fEFQTXxBUFBMSUNBVElPTnwxMjM0NTY",
  "time_window": "SINCE 1 hour ago"
})
// Returns: Error groups from Errors Inbox
```

**View change events:**
```javascript
usePower("newrelic", "newrelic-mcp-server", "list_change_events", {
  "entity_guid": "MTIzNDU2fEFQTXxBUFBMSUNBVElPTnwxMjM0NTY"
})
// Returns: Deployment and configuration change history
```

## Combining Tools (Workflows)

### Workflow 1: Production Error Investigation

```javascript
// Step 1: Find the entity
const entity = usePower("newrelic", "newrelic-mcp-server", "get_entity", {
  "name_pattern": "checkout-api"
})

// Step 2: Check golden metrics for health overview
const health = usePower("newrelic", "newrelic-mcp-server", "analyze_golden_metrics", {
  "entity_guid": entity.guid,
  "time_window": "SINCE 1 hour ago"
})

// Step 3: Analyze error groups
const errorGroups = usePower("newrelic", "newrelic-mcp-server", "list_entity_error_groups", {
  "entity_guid": entity.guid,
  "time_window": "SINCE 1 hour ago"
})

// Step 4: Analyze logs for patterns
const logAnalysis = usePower("newrelic", "newrelic-mcp-server", "analyze_entity_logs", {
  "entity_guid": entity.guid,
  "time_window": "SINCE 1 hour ago"
})

// Step 5: Check for recent deployments
const changes = usePower("newrelic", "newrelic-mcp-server", "list_change_events", {
  "entity_guid": entity.guid
})

// Step 6: Query specific error details with NRQL
const errorDetails = usePower("newrelic", "newrelic-mcp-server", "execute_nrql_query", {
  "query": `SELECT count(*), error.class, error.message 
            FROM TransactionError 
            WHERE entity.guid = '${entity.guid}' 
            FACET error.class, error.message 
            SINCE 1 hour ago 
            LIMIT 20`
})
```

### Workflow 2: Performance Degradation Analysis

```javascript
// Step 1: Get the application entity
const app = usePower("newrelic", "newrelic-mcp-server", "get_entity", {
  "name_pattern": "api-service"
})

// Step 2: Analyze transactions to find slow ones
const transactions = usePower("newrelic", "newrelic-mcp-server", "analyze_transactions", {
  "entity_guid": app.guid,
  "time_window": "SINCE 4 hours ago"
})

// Step 3: Check golden metrics trend
const metrics = usePower("newrelic", "newrelic-mcp-server", "analyze_golden_metrics", {
  "entity_guid": app.guid,
  "time_window": "SINCE 4 hours ago"
})

// Step 4: For JVM apps, check GC metrics
const gcMetrics = usePower("newrelic", "newrelic-mcp-server", "list_garbage_collection_metrics", {
  "entity_guid": app.guid,
  "time_window": "SINCE 4 hours ago"
})

// Step 5: Analyze thread performance
const threads = usePower("newrelic", "newrelic-mcp-server", "analyze_threads", {
  "entity_guid": app.guid,
  "time_window": "SINCE 4 hours ago"
})

// Step 6: Check if related to recent deployment
const deployment = usePower("newrelic", "newrelic-mcp-server", "analyze_deployment_impact", {
  "entity_guid": app.guid,
  "deployment_time": "2024-03-05T10:00:00Z"
})

// Step 7: Query specific performance data
const perfData = usePower("newrelic", "newrelic-mcp-server", "execute_nrql_query", {
  "query": `SELECT average(duration), percentile(duration, 50, 95, 99), count(*) 
            FROM Transaction 
            WHERE entity.guid = '${app.guid}' 
            FACET name 
            SINCE 4 hours ago 
            TIMESERIES AUTO`
})
```

### Workflow 3: Alert Investigation

```javascript
// Step 1: List recent open issues
const issues = usePower("newrelic", "newrelic-mcp-server", "list_recent_issues", {
  "account_id": "123456"
})

// Step 2: Generate insights for critical issue
const insights = usePower("newrelic", "newrelic-mcp-server", "generate_alert_insights_report", {
  "issue_id": issues[0].id
})

// Step 3: Generate user impact report
const userImpact = usePower("newrelic", "newrelic-mcp-server", "generate_user_impact_report", {
  "issue_id": issues[0].id
})

// Step 4: Get the affected entity
const entity = usePower("newrelic", "newrelic-mcp-server", "get_entity", {
  "guid": issues[0].entity_guid
})

// Step 5: Check related entities
const related = usePower("newrelic", "newrelic-mcp-server", "list_related_entities", {
  "entity_guid": entity.guid
})

// Step 6: Analyze logs during alert time
const logs = usePower("newrelic", "newrelic-mcp-server", "analyze_entity_logs", {
  "entity_guid": entity.guid,
  "time_window": `SINCE ${issues[0].created_at}`
})

// Step 7: Check for correlated changes
const changes = usePower("newrelic", "newrelic-mcp-server", "list_change_events", {
  "entity_guid": entity.guid
})
```

### Workflow 4: Using Natural Language (Simplest Approach)

The easiest way is to simply ask questions in natural language:

```
"What are the slowest transactions in my checkout-api service in the last hour?"
"Show me all critical alerts that are currently open"
"Analyze the deployment impact from this morning at 10am"
"What errors are happening in production right now?"
"Compare the error rate today vs yesterday"
```

The AI agent will automatically:
1. Use `natural_language_to_nrql_query` to convert your question
2. Execute the appropriate NRQL query
3. Or call specialized analysis tools like `analyze_golden_metrics`
4. Format and present the results

## NRQL Query Syntax Guide

### Basic Structure

```nrql
SELECT <attributes/functions>
FROM <event_type>
WHERE <conditions>
FACET <grouping>
SINCE <time_range>
LIMIT <count>
```

### Event Types

- **Transaction** - APM transaction data
- **TransactionError** - Application errors
- **Span** - Distributed trace spans
- **Log** - Log events
- **Metric** - Dimensional metrics
- **SystemSample** - Infrastructure host data
- **ProcessSample** - Process metrics
- **NetworkSample** - Network metrics
- **Custom events** - Your custom telemetry

### Common Functions

**Aggregation:**
- `count(*)` - Count events
- `average(attribute)` - Average value
- `sum(attribute)` - Sum values
- `min(attribute)`, `max(attribute)` - Min/max values
- `percentile(attribute, 50, 95, 99)` - Percentiles
- `rate(count(*), 1 minute)` - Events per time unit

**Filtering:**
- `WHERE attribute = 'value'`
- `WHERE attribute LIKE '%pattern%'`
- `WHERE attribute > 100`
- `WHERE attribute IN ('value1', 'value2')`
- `WHERE attribute IS NULL / IS NOT NULL`

**Grouping:**
- `FACET attribute` - Group by attribute
- `FACET CASES(...)` - Custom grouping

### Time Ranges

- `SINCE 1 hour ago` - Relative time
- `SINCE 30 minutes ago UNTIL 5 minutes ago` - Time range
- `SINCE '2024-03-01 00:00:00'` - Absolute time
- `SINCE today` - Calendar anchors

### Examples

**Error rate by endpoint:**
```nrql
SELECT count(*) 
FROM Transaction 
WHERE error IS true 
FACET request.uri 
SINCE 1 hour ago
```

**95th percentile latency:**
```nrql
SELECT percentile(duration, 95) 
FROM Transaction 
WHERE appName = 'MyApp' 
TIMESERIES AUTO 
SINCE 24 hours ago
```

**Database query performance:**
```nrql
SELECT average(duration.ms) as 'Avg Duration', count(*) as 'Count'
FROM Span 
WHERE category = 'datastore' 
FACET db.statement 
SINCE 1 hour ago 
LIMIT 20
```

**Log error patterns:**
```nrql
SELECT count(*) 
FROM Log 
WHERE level = 'ERROR' 
FACET message 
SINCE 1 hour ago 
LIMIT 50
```

## Best Practices

### ✅ Do:

- **Start with narrow time ranges** (1-4 hours) and expand if needed
- **Use LIMIT** to control result size
- **Use FACET** to group related data
- **Query golden signals first** for quick health check
- **Leverage entity GUIDs** for precise targeting
- **Use TIMESERIES** to visualize trends
- **Filter by appName or service.name** to scope queries
- **Use percentiles** (p95, p99) for latency analysis
- **Check alerts first** when investigating issues
- **Use WHERE clauses** to narrow data early

### ❌ Don't:

- **Query without time ranges** (defaults to last hour but be explicit)
- **Use SELECT * without LIMIT** (can return too much data)
- **Forget to filter by service/app** (queries entire account)
- **Ignore entity relationships** (use service maps)
- **Query raw data for long time ranges** (use aggregations)
- **Use vague patterns** in LIKE clauses (slow queries)
- **Forget to check retention limits** (data availability varies)
- **Ignore NRQL query costs** (complex queries consume more resources)

## Golden Signals Reference

The four golden signals for service health:

1. **Latency** - Response time distribution
   ```nrql
   SELECT percentile(duration, 50, 95, 99) 
   FROM Transaction 
   SINCE 1 hour ago
   ```

2. **Traffic** - Request rate
   ```nrql
   SELECT rate(count(*), 1 minute) 
   FROM Transaction 
   TIMESERIES AUTO 
   SINCE 1 hour ago
   ```

3. **Errors** - Error rate and count
   ```nrql
   SELECT percentage(count(*), WHERE error IS true) 
   FROM Transaction 
   SINCE 1 hour ago
   ```

4. **Saturation** - Resource utilization
   ```nrql
   SELECT average(cpuPercent), average(memoryUsedPercent) 
   FROM SystemSample 
   SINCE 1 hour ago
   ```

## Troubleshooting

### No Results Returned
**Cause:** Query filters too restrictive or wrong event type
**Solution:**
1. Widen time range (`SINCE 24 hours ago`)
2. Verify event type exists (check Data Explorer)
3. Remove some WHERE clauses
4. Check attribute names (case-sensitive)

### Query Timeout
**Cause:** Query too complex or time range too large
**Solution:**
1. Narrow time range
2. Add more WHERE filters
3. Reduce FACET cardinality
4. Use LIMIT to restrict results
5. Avoid SELECT * for large datasets

### Missing Attributes
**Cause:** Attribute not available for that event type
**Solution:**
1. Use Data Explorer to see available attributes
2. Check instrumentation configuration
3. Verify custom attributes are being sent
4. Use correct attribute name (case-sensitive)

### Incomplete Trace Data
**Cause:** Distributed tracing not configured or sampled
**Solution:**
1. Verify distributed tracing is enabled
2. Check sampling configuration
3. Ensure all services are instrumented
4. Check for trace context propagation issues

## Configuration

**Authentication Required**: New Relic API key and account ID

**Setup Steps:**

1. **Get New Relic Credentials:**
   - Log in to New Relic
   - Navigate to API Keys page
   - Create a User API key (not Ingest keys)
   - Note your account ID from account settings

2. **Enable MCP Server:**
   - Visit https://mcp.newrelic.com/
   - Follow OAuth authentication flow
   - Grant necessary permissions

3. **Configure in mcp.json:**
   ```json
   {
     "mcpServers": {
       "newrelic-mcp-server": {
         "url": "https://mcp.newrelic.com/mcp/",
         "oauth": {
           "authorizationUrl": "https://login.newrelic.com/oauth2/authorize",
           "tokenUrl": "https://login.newrelic.com/oauth2/token",
           "scopes": ["openid", "profile", "mcp:access"],
           "usePKCE": true
         },
         "disabled": false
       }
     }
   }
   ```

## Tips

1. **Start with golden signals** - Quick health check of services
2. **Use steering file** - Comprehensive NRQL patterns and workflows
3. **Check alerts first** - Active alerts often indicate root cause
4. **Query by time** - Always use SINCE/UNTIL for time ranges
5. **Use FACET wisely** - Group data to find patterns
6. **Leverage TIMESERIES** - Visualize trends over time
7. **Check service maps** - Understand dependencies
8. **Use entity GUIDs** - Precise targeting vs. name matching
9. **Query logs with context** - Include entity.guid or service.name
10. **Monitor query performance** - Use LIMIT and WHERE to optimize

---

**Connection:** OAuth-based MCP server  
**Source:** Official New Relic  
**License:** Apache 2.0  
**Documentation:** https://docs.newrelic.com/