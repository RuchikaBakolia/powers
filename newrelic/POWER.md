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
   - Required: `query` (string) - Log search query
   - Optional: `from` (string) - Start time (default: "now-1h")
   - Optional: `to` (string) - End time (default: "now")
   - Optional: `head_limit` (number) - Max results (default: 100)
   - Optional: `max_tokens` (number) - Token limit for output
   - Optional: `group_by_message` (boolean) - Group similar logs
   - Optional: `extra_fields` (array) - Additional fields to include
   - Returns: Matching log entries with timestamps and attributes

2. **get_datadog_metric** - Query time-series metrics
   - Required: `query` (string) - Metric query
   - Required: `from` (string) - Start time
   - Required: `to` (string) - End time
   - Optional: `raw_data` (boolean) - Return raw datapoints vs binned
   - Optional: `use_cloud_cost` (boolean) - Query cloud cost data
   - Returns: Metric values over time with aggregations

3. **search_datadog_spans** - Search APM traces and spans
   - Required: `query` (string) - Span search query
   - Optional: `from` (string) - Start time (default: "now-1h")
   - Optional: `to` (string) - End time (default: "now")
   - Optional: `head_limit` (number) - Max results (default: 100)
   - Optional: `max_tokens` (number) - Token limit for output
   - Optional: `custom_attributes` (array) - Custom span attributes to include
   - Returns: Matching spans with trace IDs and timing

4. **get_datadog_trace** - Get full trace details by trace ID
   - Required: `trace_id` (string) - Trace ID to retrieve
   - Returns: Complete trace with all spans and relationships

5. **search_datadog_rum_events** - Search Real User Monitoring events
   - Required: `query` (string) - RUM event query
   - Optional: `from` (string) - Start time (default: "now-15m")
   - Optional: `to` (string) - End time (default: "now")
   - Optional: `head_limit` (number) - Max results (default: 100)
   - Optional: `max_tokens` (number) - Token limit for output
   - Optional: `detailed_output` (boolean) - Include full event details
   - Returns: RUM events (views, actions, errors, resources)

6. **search_datadog_incidents** - Search and list incidents
   - Optional: `query` (string) - Incident search query (default: "state:active")
   - Optional: `from` (string) - Start time
   - Optional: `to` (string) - End time
   - Returns: Incidents with severity, status, and affected services

7. **get_datadog_incident** - Get detailed incident information
   - Required: `incident_id` (string) - Incident ID
   - Returns: Full incident details with timeline and updates

8. **search_datadog_monitors** - Search alerting monitors
   - Optional: `query` (string) - Monitor search query
   - Returns: Monitors with status and configuration

9. **search_datadog_services** - List APM services
   - Optional: `detailed_output` (boolean) - Include service details
   - Returns: Services with metadata and dependencies

10. **search_datadog_dashboards** - Search dashboards
    - Optional: `query` (string) - Dashboard search query
    - Optional: `max_queries_per_dashboard` (number) - Extract widget queries
    - Returns: Dashboards with metadata and widget queries

11. **search_datadog_docs** - Search Datadog documentation
    - Required: `query` (string) - Documentation search query
    - Returns: Relevant documentation pages and guides

## Tool Usage Examples

### Searching Logs

**Find error logs:**
```javascript
usePower("datadog", "datadog", "search_datadog_logs", {
  "query": "service:api env:prod status:error",
  "from": "now-1h",
  "to": "now"
})
// Returns: Error logs from API service in last hour
```

**Search with custom attributes:**
```javascript
usePower("datadog", "datadog", "search_datadog_logs", {
  "query": "service:checkout @http.status_code:[400 TO 599]",
  "from": "now-1h",
  "to": "now",
  "extra_fields": ["@user.id", "@order.id", "@http.*"]
})
// Returns: HTTP errors with user and order context
```

### Querying Metrics

**Service response time:**
```javascript
usePower("datadog", "datadog", "get_datadog_metric", {
  "query": "avg:trace.servlet.request.duration{service:api,env:prod} by {resource_name}",
  "from": "now-4h",
  "to": "now"
})
// Returns: Average response time per endpoint
```

**CPU usage by host:**
```javascript
usePower("datadog", "datadog", "get_datadog_metric", {
  "query": "avg:system.cpu.user{env:prod} by {host}",
  "from": "now-1h",
  "to": "now"
})
// Returns: CPU usage for each production host
```

**Cloud costs:**
```javascript
usePower("datadog", "datadog", "get_datadog_metric", {
  "query": "sum:all.cost{*} by {providername}.rollup(sum, daily)",
  "from": "now-30d",
  "to": "now",
  "use_cloud_cost": true
})
// Returns: Daily costs by cloud provider
```

### Searching Traces

**Find slow requests:**
```javascript
usePower("datadog", "datadog", "search_datadog_spans", {
  "query": "service:api @duration:>100000000",  // >100ms in nanoseconds
  "from": "now-1h",
  "to": "now"
})
// Returns: Slow API requests with trace IDs
```

**Get full trace details:**
```javascript
usePower("datadog", "datadog", "get_datadog_trace", {
  "trace_id": "7d5d747be160e280504c099d984bcfe0"
})
// Returns: Complete trace with all spans and timing
```

### RUM Analysis

**Find slow page loads:**
```javascript
usePower("datadog", "datadog", "search_datadog_rum_events", {
  "query": "@type:view @view.loading_time:>3000",
  "from": "now-1h",
  "to": "now",
  "detailed_output": true
})
// Returns: Pages taking >3 seconds to load
```

**Frontend errors:**
```javascript
usePower("datadog", "datadog", "search_datadog_rum_events", {
  "query": "@type:error @application.name:\"My App\" @device.type:mobile",
  "from": "now-1h",
  "to": "now"
})
// Returns: Mobile frontend errors
```

### Incident Management

**List active incidents:**
```javascript
usePower("datadog", "datadog", "search_datadog_incidents", {
  "query": "state:(active OR stable)"
})
// Returns: Current incidents being worked on
```

**Get incident details:**
```javascript
usePower("datadog", "datadog", "get_datadog_incident", {
  "incident_id": "12345"
})
// Returns: Full incident timeline and updates
```

### Monitor Search

**Find alerting monitors:**
```javascript
usePower("datadog", "datadog", "search_datadog_monitors", {
  "query": "status:alert muted:false env:prod"
})
// Returns: Active unmuted alerts in production
```

## Combining Tools (Workflows)

### Workflow 1: Production Error Investigation

```javascript
// Step 1: Find recent errors in logs
const errorLogs = usePower("datadog", "datadog", "search_datadog_logs", {
  "query": "service:checkout env:prod status:error",
  "from": "now-1h",
  "to": "now",
  "extra_fields": ["@error.message", "@user.id"]
})

// Step 2: Find related error traces
const errorSpans = usePower("datadog", "datadog", "search_datadog_spans", {
  "query": "service:checkout status:error",
  "from": "now-1h",
  "to": "now"
})

// Step 3: Get full trace for first error
const fullTrace = usePower("datadog", "datadog", "get_datadog_trace", {
  "trace_id": errorSpans[0].trace_id
})

// Step 4: Check if there's an active incident
const incidents = usePower("datadog", "datadog", "search_datadog_incidents", {
  "query": "state:active"
})

// Step 5: Check related monitors
const monitors = usePower("datadog", "datadog", "search_datadog_monitors", {
  "query": "tag:\"service:checkout\" status:alert"
})
```

### Workflow 2: Performance Degradation Analysis

```javascript
// Step 1: Identify slow requests
const slowSpans = usePower("datadog", "datadog", "search_datadog_spans", {
  "query": "service:api @duration:>100000000",  // >100ms
  "from": "now-1h",
  "to": "now"
})

// Step 2: Check response time metrics
const latencyMetrics = usePower("datadog", "datadog", "get_datadog_metric", {
  "query": "avg:trace.servlet.request.duration{service:api} by {resource_name}",
  "from": "now-4h",
  "to": "now"
})

// Step 3: Check infrastructure metrics
const cpuMetrics = usePower("datadog", "datadog", "get_datadog_metric", {
  "query": "avg:system.cpu.user{service:api} by {host}",
  "from": "now-4h",
  "to": "now"
})

// Step 4: Check for deployment correlation
const deploymentLogs = usePower("datadog", "datadog", "search_datadog_logs", {
  "query": "service:api @deployment.version:*",
  "from": "now-4h",
  "to": "now"
})

// Step 5: Get full trace of slowest request
const slowestTrace = usePower("datadog", "datadog", "get_datadog_trace", {
  "trace_id": slowSpans[0].trace_id
})
```

### Workflow 3: User Experience Investigation

```javascript
// Step 1: Find slow page loads
const slowPages = usePower("datadog", "datadog", "search_datadog_rum_events", {
  "query": "@type:view @view.loading_time:>3000",
  "from": "now-1h",
  "to": "now",
  "detailed_output": true
})

// Step 2: Check for frontend errors
const frontendErrors = usePower("datadog", "datadog", "search_datadog_rum_events", {
  "query": "@type:error",
  "from": "now-1h",
  "to": "now"
})

// Step 3: Check backend API performance
const apiLatency = usePower("datadog", "datadog", "get_datadog_metric", {
  "query": "avg:trace.servlet.request.duration{service:api,env:prod}",
  "from": "now-1h",
  "to": "now"
})

// Step 4: Check resource loading issues
const resourceErrors = usePower("datadog", "datadog", "search_datadog_rum_events", {
  "query": "@type:resource @resource.status_code:[400 TO 599]",
  "from": "now-1h",
  "to": "now"
})
```

### Workflow 4: Service Dependency Analysis

```javascript
// Step 1: List all services
const services = usePower("datadog", "datadog", "search_datadog_services", {
  "detailed_output": true
})

// Step 2: Find traces for a service
const serviceSpans = usePower("datadog", "datadog", "search_datadog_spans", {
  "query": "service:frontend",
  "from": "now-1h",
  "to": "now"
})

// Step 3: Get full trace to see downstream calls
const dependencyTrace = usePower("datadog", "datadog", "get_datadog_trace", {
  "trace_id": serviceSpans[0].trace_id
})

// Step 4: Check latency between services
const serviceLatency = usePower("datadog", "datadog", "get_datadog_metric", {
  "query": "avg:trace.servlet.request.duration{env:prod} by {service}",
  "from": "now-4h",
  "to": "now"
})
```

## Query Syntax Guide

### Log Search Syntax

**Tags** (no @ prefix):
- `service:api` - Service name
- `env:prod` - Environment
- `status:error` - Log status level
- `host:web-1` - Hostname

**Attributes** (@ prefix required):
- `@http.status_code:500` - HTTP status
- `@user.id:abc123` - User identifier
- `@duration:>1000` - Duration in milliseconds
- `@error.message:"timeout"` - Error message

**Boolean operators:**
- AND (default): `service:api status:error`
- OR: `env:(prod OR staging)`
- Exclusion: `-status:info`

**Wildcards:**
- `service:web*` - Matches web-api, web-worker
- `@url:/api/*` - Matches any /api/ path

### Metric Query Syntax

**Structure:**
```
<AGGREGATOR>:<METRIC_NAME>{<SCOPE>} by {<GROUPING>}
```

**Aggregators:**
- `avg` - Average across series
- `sum` - Sum all series
- `min` / `max` - Min/max values
- `p95` / `p99` - Percentiles

**Examples:**
```
avg:system.cpu.user{env:prod} by {host}
sum:trace.requests.count{service:api}.rollup(sum, 300)
p95:trace.servlet.request.duration{env:prod}
```

### APM/Trace Query Syntax

**Reserved fields** (NO @ prefix):
- `service` - Service name
- `resource_name` - Endpoint/operation
- `operation_name` - Span operation
- `status` - ok or error
- `trace_id` - Specific trace

**Span attributes** (@ prefix required):
- `@http.status_code` - HTTP status
- `@http.method` - HTTP method
- `@duration` - Duration in **nanoseconds**
- `@error.message` - Error message

**Duration conversions:**
- 1ms = 1,000,000 nanoseconds
- 100ms = 100,000,000 nanoseconds
- 1s = 1,000,000,000 nanoseconds

**Examples:**
```
service:api status:error
service:api @duration:>100000000
operation_name:db.query @duration:>1000000000
```

### RUM Query Syntax

**Event types:**
- `@type:view` - Page views
- `@type:action` - User interactions
- `@type:error` - Frontend errors
- `@type:resource` - Resource loading
- `@type:vital` - Core Web Vitals

**Common attributes:**
- `@application.name` - RUM app name
- `@view.url_path` - Page URL
- `@view.loading_time` - Load duration (ms)
- `@device.type` - Device type
- `@browser.name` - Browser name
- `@user.id` - User identifier

**Examples:**
```
@type:view @view.loading_time:>3000
@type:error @browser.name:Safari
@type:resource @resource.status_code:[400 TO 599]
```

### Incident Query Syntax

**Fields:**
- `state` - active, stable, resolved
- `severity` - SEV-1, SEV-2, SEV-3, SEV-4, SEV-5
- `customer_impacted` - true or false

**Important:** Group multiple values of same field:
```
severity:(SEV-1 OR SEV-2)  ✅
severity:SEV-1 OR severity:SEV-2  ❌
```

**Examples:**
```
state:active
severity:(SEV-1 OR SEV-2) AND state:active
customer_impacted:true AND state:active
```

### Monitor Query Syntax

**Fields:**
- `status` - alert, warn, ok
- `muted` - true or false
- `tag` - Monitor tags
- `env` - Environment
- `priority` - p1, p2, etc.

**Examples:**
```
status:alert muted:false
priority:(p1 OR p2)
tag:"service:api" status:alert
```

## Best Practices

### ✅ Do:

- **Start with narrow time ranges** (15m-1h) and expand if needed
- **Use unified service tags** (service, env, version) together
- **Filter by service first** to scope queries
- **Use @ prefix correctly** (attributes yes, reserved fields no)
- **Remember duration units** (nanoseconds for spans, milliseconds for logs/RUM)
- **Group similar logs** with `group_by_message: true`
- **Set appropriate max_tokens** (5000 for overview, 50000 for details)
- **Use extra_fields/custom_attributes** for custom data
- **Leverage documentation search** for setup and instrumentation questions
- **Check incidents first** when investigating issues

### ❌ Don't:

- **Use very long time ranges** without reason (expensive and slow)
- **Forget @ prefix** for log/span attributes
- **Use @ prefix** for reserved trace fields (service, status, etc.)
- **Forget duration conversions** (5ms = 5,000,000 nanoseconds)
- **Use wildcards inside quotes** (they become literal)
- **Skip escaping special characters** (: = / \ etc.)
- **Use excessive free-text searches** (use structured tags instead)
- **Ignore unified service tagging** (always include service, env, version)
- **Query without time filters** (expensive and slow)

## Troubleshooting

### Error: "No results found"
**Cause:** Filters too restrictive or wrong field names
**Solution:**
1. Widen time range
2. Check field names (use @ for attributes)
3. Verify case-sensitivity
4. Use wildcards for partial matches

### Error: "Query timeout"
**Cause:** Query too expensive or time range too large
**Solution:**
1. Narrow time range (start with now-1h)
2. Add specific filters (service, env)
3. Reduce aggregation cardinality
4. Use head_limit to restrict results

### Error: "Invalid query syntax"
**Cause:** Malformed query string
**Solution:**
1. Check @ prefix usage
2. Escape special characters (: = / \)
3. Use double quotes for strings with spaces
4. Group OR conditions: `field:(value1 OR value2)`

### Error: "Trace not found"
**Cause:** Invalid trace ID or trace expired
**Solution:**
1. Verify trace_id format
2. Check if trace is within retention period
3. Ensure trace exists in correct account

## Configuration

**Authentication Required**: OAuth authentication via New Relic

**Setup Steps:**

1. **Enable MCP Server Access:**
   - Visit https://mcp.newrelic.com/
   - Log in with your New Relic account
   - Follow OAuth authentication flow
   - Grant necessary permissions for MCP access

2. **Configure in mcp.json:**
   ```json
   {
     "mcpServers": {
       "newrelic-mcp-server": {
         "url": "https://mcp.newrelic.com/mcp/",
         "oauth": {
           "authorizationUrl": "https://login.newrelic.com/oauth2/authorize",
           "tokenUrl": "https://login.newrelic.com/oauth2/token",
           "scopes": [
             "openid",
             "profile",
             "mcp:access"
           ],
           "usePKCE": true
         },
         "disabled": false
       }
     }
   }
   ```

3. **First Time Setup:**
   - When you first use the power, you'll be prompted to authenticate
   - A browser window will open for OAuth login
   - Log in with your New Relic credentials
   - Grant the requested permissions
   - The connection will be established automatically

4. **Note Your Account ID:**
   - Most tools require your New Relic account ID
   - Find it in New Relic: Account settings → Account information
   - Format: Usually a 7-8 digit number (e.g., "1234567")

## Tips

1. **Start narrow** - Use 1-4 hour time ranges, expand if needed
2. **Use steering file** - Comprehensive NRQL guide with golden signals
3. **Query golden signals first** - Quick health overview
4. **Always use LIMIT** - Control result size in queries
5. **Filter by entity** - Use appName or service.name to scope queries
6. **Check for deployments** - Correlate issues with recent changes
7. **Use FACET wisely** - Group data to identify patterns
8. **Leverage TIMESERIES** - Visualize trends over time
9. **Get service maps** - Understand dependencies
10. **Use entity GUIDs** - More precise than name matching

---

**Connection:** OAuth-based MCP server  
**Source:** Official New Relic  
**License:** Apache 2.0  
**Documentation:** https://docs.newrelic.com/
