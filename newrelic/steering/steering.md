# New Relic Observability Steering Guide

This steering file provides comprehensive guidance for using the New Relic MCP (Model Context Protocol) server to query observability data using NRQL (New Relic Query Language).

## When to Use the New Relic MCP Server

Use New Relic MCP tools when you need to:
- **Investigate production issues**: Errors, performance degradation, service outages
- **Analyze application performance**: Transaction latency, throughput, error rates
- **Monitor infrastructure**: Host metrics, process data, container performance
- **Track distributed traces**: Request flows across microservices
- **Query logs**: Application and infrastructure log analysis
- **Evaluate golden signals**: Latency, traffic, errors, and saturation
- **Analyze service dependencies**: Understand service relationships
- **Monitor alerts**: Track alert violations and incidents
- **Optimize database performance**: Query slow database operations
- **Custom event analysis**: Query your custom telemetry data

## Core Principles

### 1. Start Narrow, Expand if Needed
Always begin with narrow time ranges and specific queries:
- Start: `SINCE 1 hour ago` or `SINCE 30 minutes ago`
- Expand to: `SINCE 4 hours ago` or `SINCE 24 hours ago` if needed
- Narrower ranges = faster queries and more relevant results

### 2. Use Entity-Based Filtering
Always filter by application or service when available:
- `WHERE appName = 'MyApp'`
- `WHERE \`service.name\` = 'checkout-api'`
- `WHERE entity.guid = 'MTIzNDU2...'`

### 3. Always Use LIMIT
Control result size to avoid overwhelming output:
- Default to `LIMIT 100` for most queries
- Use `LIMIT 20-50` for detailed analysis
- Use `LIMIT 500-1000` for comprehensive data gathering

### 4. Query Golden Signals First
Start with the four golden signals for quick health assessment:
- **Latency**: Response time percentiles
- **Traffic**: Request rate
- **Errors**: Error rate and count
- **Saturation**: Resource utilization

---

## NRQL Query Fundamentals

### Basic Structure

```nrql
SELECT <attributes/functions>
FROM <event_type>
WHERE <conditions>
FACET <grouping>
SINCE <time_range>
LIMIT <count>
```

### Event Types Reference

| Event Type | Description | Key Attributes |
|------------|-------------|----------------|
| **Transaction** | APM transactions | appName, name, duration, error |
| **TransactionError** | Application errors | error.class, error.message, transactionName |
| **Span** | Distributed trace spans | traceId, duration.ms, service.name, category |
| **Log** | Log events | message, level, entity.guid, service.name |
| **Metric** | Dimensional metrics | metricName, entity.guid |
| **SystemSample** | Host metrics | cpuPercent, memoryUsedPercent, hostname |
| **ProcessSample** | Process metrics | cpuPercent, memoryResidentSizeBytes |
| **NetworkSample** | Network metrics | receiveBytesPerSecond, transmitBytesPerSecond |
| **Deployment** | Deployment events | appName, revision, user |

### SELECT Clause Functions

**Aggregation Functions:**
```nrql
count(*)                              # Count events
average(duration)                     # Average value
sum(duration)                         # Sum values
min(duration), max(duration)          # Min/max values
percentile(duration, 50, 95, 99)     # Percentiles
rate(count(*), 1 minute)             # Events per time unit
uniqueCount(userId)                   # Count distinct values
latest(attribute)                     # Most recent value
```

**String Functions:**
```nrql
concat(firstName, ' ', lastName)      # Concatenate strings
substring(message, 0, 100)           # Extract substring
```

**Math Functions:**
```nrql
abs(value)                           # Absolute value
round(value, 2)                      # Round to decimals
ceil(value), floor(value)            # Round up/down
```

### WHERE Clause Patterns

**Equality & Comparison:**
```nrql
WHERE attribute = 'value'             # Exact match
WHERE attribute != 'value'            # Not equal
WHERE duration > 1000                 # Greater than
WHERE duration >= 1000                # Greater or equal
WHERE duration < 1000                 # Less than
WHERE duration BETWEEN 100 AND 1000   # Range
```

**String Matching:**
```nrql
WHERE message LIKE '%timeout%'        # Contains
WHERE message LIKE 'Error:%'          # Starts with
WHERE message LIKE '%failed'          # Ends with
WHERE message NOT LIKE '%health%'     # Doesn't contain
```

**Multiple Values:**
```nrql
WHERE status IN ('error', 'warning')  # One of several values
WHERE status NOT IN ('info', 'debug') # Not in list
```

**Boolean & Null:**
```nrql
WHERE error IS true                   # Boolean check
WHERE error IS false
WHERE attribute IS NULL               # Null check
WHERE attribute IS NOT NULL
```

**Logical Operators:**
```nrql
WHERE error IS true AND duration > 1000           # AND
WHERE status = 'error' OR status = 'warning'      # OR
WHERE (status = 'error' OR status = 'warning') AND appName = 'MyApp'  # Grouping
```

### FACET Clause

**Basic Grouping:**
```nrql
FACET appName                         # Group by one attribute
FACET appName, host                   # Group by multiple
FACET appName LIMIT 20                # Limit facets returned
```

**CASES for Custom Grouping:**
```nrql
FACET CASES (
  WHERE duration < 100 AS 'Fast',
  WHERE duration < 1000 AS 'Normal',
  WHERE duration >= 1000 AS 'Slow'
)
```

**Bucketing:**
```nrql
FACET buckets(duration, 100, 20)     # Create 20 buckets of 100ms each
```

### Time Ranges

**Relative Time:**
```nrql
SINCE 30 minutes ago                  # Last 30 minutes
SINCE 1 hour ago                      # Last hour
SINCE 4 hours ago                     # Last 4 hours
SINCE 1 day ago                       # Last 24 hours
SINCE 1 week ago                      # Last 7 days
```

**Time Range:**
```nrql
SINCE 2 hours ago UNTIL 1 hour ago   # Specific window
SINCE today                           # Since midnight today
SINCE yesterday UNTIL today           # Yesterday's data
```

**Absolute Time:**
```nrql
SINCE '2024-03-01 00:00:00'          # Absolute timestamp
```

### TIMESERIES Clause

**Visualize Trends:**
```nrql
SELECT count(*) FROM Transaction 
TIMESERIES AUTO 
SINCE 1 hour ago
# Returns: Time-bucketed data (auto interval)

SELECT average(duration) FROM Transaction 
TIMESERIES 1 minute 
SINCE 1 hour ago
# Returns: 1-minute buckets

SELECT percentile(duration, 95) FROM Transaction 
TIMESERIES MAX 
SINCE 24 hours ago
# Returns: Maximum available buckets (366 max)
```

---

## Golden Signals Queries

### 1. Latency (Response Time)

**Percentile Analysis:**
```nrql
SELECT percentile(duration, 50, 95, 99) 
FROM Transaction 
WHERE appName = 'checkout-api' 
SINCE 1 hour ago
```

**Latency by Endpoint:**
```nrql
SELECT average(duration) as 'Avg', percentile(duration, 95) as 'P95'
FROM Transaction 
WHERE appName = 'checkout-api'
FACET name 
SINCE 1 hour ago 
LIMIT 20
```

**Latency Trend:**
```nrql
SELECT percentile(duration, 95) 
FROM Transaction 
WHERE appName = 'checkout-api'
TIMESERIES AUTO 
SINCE 4 hours ago
```

**Database Query Latency:**
```nrql
SELECT average(duration.ms) as 'Avg Duration (ms)', count(*) as 'Count'
FROM Span 
WHERE category = 'datastore' 
AND \`service.name\` = 'checkout-api'
FACET db.statement 
SINCE 1 hour ago 
LIMIT 20
```

### 2. Traffic (Throughput)

**Request Rate:**
```nrql
SELECT rate(count(*), 1 minute) as 'Requests/min'
FROM Transaction 
WHERE appName = 'checkout-api'
TIMESERIES AUTO 
SINCE 1 hour ago
```

**Traffic by Endpoint:**
```nrql
SELECT count(*) as 'Total Requests'
FROM Transaction 
WHERE appName = 'checkout-api'
FACET name 
SINCE 1 hour ago 
LIMIT 20
```

**Traffic by HTTP Method:**
```nrql
SELECT count(*) 
FROM Transaction 
WHERE appName = 'checkout-api'
FACET request.method 
SINCE 1 hour ago
```

### 3. Errors

**Error Rate:**
```nrql
SELECT percentage(count(*), WHERE error IS true) as 'Error Rate %'
FROM Transaction 
WHERE appName = 'checkout-api'
SINCE 1 hour ago
```

**Error Count Trend:**
```nrql
SELECT count(*) 
FROM Transaction 
WHERE appName = 'checkout-api' AND error IS true
TIMESERIES AUTO 
SINCE 4 hours ago
```

**Errors by Type:**
```nrql
SELECT count(*) 
FROM TransactionError 
WHERE appName = 'checkout-api'
FACET error.class 
SINCE 1 hour ago 
LIMIT 20
```

**Errors by Endpoint:**
```nrql
SELECT count(*) 
FROM Transaction 
WHERE appName = 'checkout-api' AND error IS true
FACET name 
SINCE 1 hour ago 
LIMIT 20
```

### 4. Saturation (Resource Utilization)

**CPU Usage:**
```nrql
SELECT average(cpuPercent) as 'Avg CPU %', max(cpuPercent) as 'Max CPU %'
FROM SystemSample 
WHERE hostname LIKE 'prod-%'
FACET hostname 
SINCE 1 hour ago
```

**Memory Usage:**
```nrql
SELECT average(memoryUsedPercent) as 'Avg Memory %', max(memoryUsedPercent) as 'Max Memory %'
FROM SystemSample 
WHERE hostname LIKE 'prod-%'
FACET hostname 
SINCE 1 hour ago
```

**Resource Trends:**
```nrql
SELECT average(cpuPercent), average(memoryUsedPercent)
FROM SystemSample 
WHERE hostname = 'prod-web-01'
TIMESERIES AUTO 
SINCE 4 hours ago
```

**JVM Heap Usage (for Java apps):**
```nrql
SELECT average(newrelic.timeslice.value) * 100 as 'Heap Used %'
FROM Metric 
WHERE metricTimesliceName = 'Memory/Heap/Used' 
AND appName = 'MyJavaApp'
TIMESERIES AUTO 
SINCE 1 hour ago
```

---

## Common Query Patterns

### Log Analysis

**Error Logs:**
```nrql
SELECT * 
FROM Log 
WHERE level = 'ERROR' 
AND \`service.name\` = 'checkout-api'
SINCE 1 hour ago 
LIMIT 100
```

**Log Patterns:**
```nrql
SELECT count(*) 
FROM Log 
WHERE level = 'ERROR'
FACET message 
SINCE 1 hour ago 
LIMIT 50
```

**Logs with Context:**
```nrql
SELECT timestamp, message, level, \`service.name\`, hostname
FROM Log 
WHERE message LIKE '%timeout%'
SINCE 1 hour ago 
LIMIT 100
```

**Log Volume:**
```nrql
SELECT count(*) 
FROM Log 
FACET level 
TIMESERIES AUTO 
SINCE 4 hours ago
```

### Distributed Tracing

**Find Slow Traces:**
```nrql
SELECT * 
FROM Span 
WHERE duration.ms > 1000 
AND \`service.name\` = 'checkout-api'
SINCE 1 hour ago 
LIMIT 50
```

**Trace Error Analysis:**
```nrql
SELECT count(*) 
FROM Span 
WHERE error IS true 
FACET \`service.name\`, error.message 
SINCE 1 hour ago 
LIMIT 20
```

**Service-to-Service Calls:**
```nrql
SELECT average(duration.ms) as 'Avg Duration', count(*) as 'Count'
FROM Span 
WHERE \`service.name\` = 'frontend' 
AND category = 'http'
FACET \`span.kind\`, \`peer.service\` 
SINCE 1 hour ago
```

**Database Operations:**
```nrql
SELECT average(duration.ms), count(*), max(duration.ms)
FROM Span 
WHERE category = 'datastore' 
FACET db.operation, db.statement 
SINCE 1 hour ago 
LIMIT 20
```

### Transaction Analysis

**Slow Transactions:**
```nrql
SELECT average(duration), percentile(duration, 95, 99), count(*)
FROM Transaction 
WHERE appName = 'MyApp' 
AND duration > 1
FACET name 
SINCE 1 hour ago 
LIMIT 20
```

**Transaction by HTTP Status:**
```nrql
SELECT count(*) 
FROM Transaction 
WHERE appName = 'MyApp'
FACET httpResponseCode 
SINCE 1 hour ago
```

**External Service Calls:**
```nrql
SELECT average(externalDuration), count(*)
FROM Transaction 
WHERE appName = 'MyApp' 
AND externalDuration IS NOT NULL
FACET externalHost 
SINCE 1 hour ago
```

**Transaction Errors with Details:**
```nrql
SELECT error.message, error.class, transactionName, count(*)
FROM TransactionError 
WHERE appName = 'MyApp'
FACET error.class, transactionName 
SINCE 1 hour ago 
LIMIT 20
```

### Infrastructure Monitoring

**Host Overview:**
```nrql
SELECT average(cpuPercent), average(memoryUsedPercent), average(diskUsedPercent)
FROM SystemSample 
WHERE hostname LIKE 'prod-%'
FACET hostname 
SINCE 1 hour ago
```

**Process CPU Usage:**
```nrql
SELECT average(cpuPercent) as 'Avg CPU', max(cpuPercent) as 'Max CPU'
FROM ProcessSample 
WHERE commandName LIKE '%java%'
FACET hostname, processDisplayName 
SINCE 1 hour ago
```

**Network Traffic:**
```nrql
SELECT average(receiveBytesPerSecond/1024/1024) as 'RX MB/s', 
       average(transmitBytesPerSecond/1024/1024) as 'TX MB/s'
FROM NetworkSample 
FACET hostname 
SINCE 1 hour ago
```

**Disk I/O:**
```nrql
SELECT average(diskReadsPerSecond), average(diskWritesPerSecond)
FROM SystemSample 
WHERE hostname LIKE 'prod-%'
FACET hostname 
TIMESERIES AUTO 
SINCE 1 hour ago
```

---

## Common Use Cases

### 1. Investigating Production Errors

**Step 1: Check Error Rate**
```nrql
SELECT percentage(count(*), WHERE error IS true) as 'Error Rate %',
       count(*) as 'Total Requests',
       filter(count(*), WHERE error IS true) as 'Errors'
FROM Transaction 
WHERE appName = 'checkout-api'
SINCE 1 hour ago
```

**Step 2: Find Error Types**
```nrql
SELECT count(*) 
FROM TransactionError 
WHERE appName = 'checkout-api'
FACET error.class, error.message 
SINCE 1 hour ago 
LIMIT 20
```

**Step 3: Find Error Logs**
```nrql
SELECT timestamp, message, level, error.class, error.message
FROM Log 
WHERE level = 'ERROR' 
AND \`service.name\` = 'checkout-api'
SINCE 1 hour ago 
LIMIT 100
```

**Step 4: Find Related Traces**
```nrql
SELECT traceId, duration.ms, \`service.name\`, error.message
FROM Span 
WHERE error IS true 
AND \`service.name\` = 'checkout-api'
SINCE 1 hour ago 
LIMIT 20
```

**Step 5: Check for Recent Deployments**
```nrql
SELECT timestamp, revision, user, changelog
FROM Deployment 
WHERE appName = 'checkout-api'
SINCE 24 hours ago
```

### 2. Performance Degradation Analysis

**Step 1: Compare Current vs Historical Latency**
```nrql
SELECT percentile(duration, 50, 95, 99) 
FROM Transaction 
WHERE appName = 'api-service'
SINCE 1 hour ago 
COMPARE WITH 1 day ago
```

**Step 2: Find Slow Endpoints**
```nrql
SELECT average(duration) as 'Avg', percentile(duration, 95) as 'P95', count(*)
FROM Transaction 
WHERE appName = 'api-service'
FACET name 
SINCE 1 hour ago 
LIMIT 20
```

**Step 3: Check Database Performance**
```nrql
SELECT average(duration.ms) as 'Avg DB Time', count(*) as 'Query Count'
FROM Span 
WHERE category = 'datastore' 
AND \`service.name\` = 'api-service'
FACET db.operation, db.collection 
SINCE 1 hour ago 
LIMIT 20
```

**Step 4: Check Infrastructure**
```nrql
SELECT average(cpuPercent), average(memoryUsedPercent)
FROM SystemSample 
WHERE hostname LIKE '%api%'
FACET hostname 
TIMESERIES AUTO 
SINCE 4 hours ago
```

**Step 5: Identify External Service Issues**
```nrql
SELECT average(duration.ms), count(*)
FROM Span 
WHERE category = 'http' 
AND \`service.name\` = 'api-service'
FACET \`http.url\`, \`http.status_code\` 
SINCE 1 hour ago 
LIMIT 20
```

### 3. Service Dependency Analysis

**Step 1: List Service Calls**
```nrql
SELECT count(*), average(duration.ms)
FROM Span 
WHERE \`service.name\` = 'frontend'
FACET \`peer.service\`, category 
SINCE 1 hour ago
```

**Step 2: Trace Errors Across Services**
```nrql
SELECT count(*) 
FROM Span 
WHERE error IS true
FACET \`service.name\`, \`peer.service\` 
SINCE 1 hour ago
```

**Step 3: External Dependencies**
```nrql
SELECT average(duration.ms), count(*)
FROM Span 
WHERE category = 'http' 
AND \`http.url\` LIKE 'https://api.external.com%'
FACET \`http.url\`, \`http.status_code\` 
SINCE 1 hour ago
```

### 4. Alert Investigation

**Step 1: Find Related Metrics**
```nrql
SELECT average(duration), percentile(duration, 95, 99), count(*)
FROM Transaction 
WHERE appName = 'MyApp'
TIMESERIES AUTO 
SINCE 2 hours ago
```

**Step 2: Correlate with Errors**
```nrql
SELECT count(*) 
FROM Transaction 
WHERE appName = 'MyApp' AND error IS true
TIMESERIES AUTO 
SINCE 2 hours ago
```

**Step 3: Check Infrastructure Impact**
```nrql
SELECT average(cpuPercent), average(memoryUsedPercent)
FROM SystemSample 
WHERE hostname IN (SELECT uniques(host) FROM Transaction WHERE appName = 'MyApp')
TIMESERIES AUTO 
SINCE 2 hours ago
```

**Step 4: Find Related Logs**
```nrql
SELECT timestamp, message, level
FROM Log 
WHERE entity.name = 'MyApp'
SINCE 2 hours ago 
LIMIT 200
```

---

## Advanced Patterns

### Comparing Time Periods

```nrql
SELECT average(duration), percentile(duration, 95) 
FROM Transaction 
WHERE appName = 'MyApp'
SINCE 1 hour ago 
COMPARE WITH 1 day ago
```

### Filtering with Subqueries

```nrql
SELECT count(*) 
FROM Transaction 
WHERE appName IN (
  SELECT uniques(appName) FROM Transaction WHERE error IS true
)
SINCE 1 hour ago
```

### Rate of Change

```nrql
SELECT derivative(average(duration), 1 minute) 
FROM Transaction 
WHERE appName = 'MyApp'
TIMESERIES AUTO 
SINCE 1 hour ago
```

### Conditional Aggregation

```nrql
SELECT 
  filter(count(*), WHERE error IS true) as 'Errors',
  filter(count(*), WHERE error IS false) as 'Success',
  percentage(count(*), WHERE error IS true) as 'Error Rate %'
FROM Transaction 
WHERE appName = 'MyApp'
SINCE 1 hour ago
```

### Histogram Buckets

```nrql
SELECT histogram(duration, 100, 20) 
FROM Transaction 
WHERE appName = 'MyApp'
SINCE 1 hour ago
```

---

## Anti-Patterns and Common Mistakes

### ❌ DON'T: Query Without Time Range
```nrql
# Bad - defaults to last hour but be explicit
SELECT * FROM Transaction WHERE appName = 'MyApp'

# Good
SELECT * FROM Transaction WHERE appName = 'MyApp' SINCE 1 hour ago
```

### ❌ DON'T: Use SELECT * Without LIMIT
```nrql
# Bad - can return too much data
SELECT * FROM Log WHERE level = 'ERROR' SINCE 1 day ago

# Good
SELECT * FROM Log WHERE level = 'ERROR' SINCE 1 hour ago LIMIT 100
```

### ❌ DON'T: Forget to Filter by App/Service
```nrql
# Bad - queries entire account
SELECT count(*) FROM Transaction SINCE 1 hour ago

# Good
SELECT count(*) FROM Transaction WHERE appName = 'MyApp' SINCE 1 hour ago
```

### ❌ DON'T: Use Vague LIKE Patterns
```nrql
# Bad - slow query
SELECT * FROM Log WHERE message LIKE '%error%' SINCE 1 day ago

# Good - more specific and time-limited
SELECT * FROM Log WHERE level = 'ERROR' AND message LIKE '%timeout%' SINCE 1 hour ago LIMIT 100
```

### ❌ DON'T: Query Raw Data for Long Time Ranges
```nrql
# Bad - too much data
SELECT * FROM Transaction WHERE appName = 'MyApp' SINCE 30 days ago

# Good - use aggregations
SELECT count(*), average(duration) 
FROM Transaction 
WHERE appName = 'MyApp' 
FACET dateOf(timestamp) 
SINCE 30 days ago
```

### ❌ DON'T: Ignore FACET Cardinality
```nrql
# Bad - can return thousands of facets
SELECT count(*) FROM Transaction FACET traceId SINCE 1 day ago

# Good - limit facets
SELECT count(*) FROM Transaction FACET name SINCE 1 hour ago LIMIT 20
```

---

## Performance Tips

### 1. Query Optimization
- **Filter early**: Use WHERE clauses before FACET
- **Narrow time ranges**: Start with 1 hour, expand if needed
- **Use LIMIT**: Always limit results
- **Filter by entity**: Use appName, service.name, or entity.guid
- **Avoid SELECT ***: Specify needed attributes

### 2. Aggregation Strategies
- **Use appropriate functions**: count(*), average(), percentile()
- **Group wisely**: FACET on low-cardinality attributes
- **Leverage TIMESERIES**: For trend visualization
- **Use COMPARE WITH**: For time period comparisons

### 3. Time Range Best Practices
```nrql
# Good progression
1. SINCE 30 minutes ago  → Quick check
2. SINCE 1 hour ago      → Standard investigation
3. SINCE 4 hours ago     → Pattern analysis
4. SINCE 24 hours ago    → Historical trends
```

### 4. Efficient Workflows
- Query golden signals first (quick health check)
- Use entity GUIDs for precise targeting
- Leverage FACET to identify problem areas
- Use TIMESERIES to spot when issues started
- Check for deployments/changes around issue time

---

## Summary Checklist

Before making New Relic queries, ensure you:
- ✅ Start with narrow time ranges (30 minutes - 1 hour)
- ✅ Always use LIMIT to control result size
- ✅ Filter by appName or service.name to scope queries
- ✅ Query golden signals first for health overview
- ✅ Use FACET to group and identify patterns
- ✅ Include TIMESERIES for trend visualization
- ✅ Use appropriate aggregation functions
- ✅ Check for recent deployments during investigations
- ✅ Leverage entity GUIDs for precise targeting
- ✅ Use WHERE clauses early in query execution

This steering file represents essential knowledge for effectively querying New Relic observability data through the MCP server using NRQL. Follow these patterns for accurate, performant, and actionable results.
