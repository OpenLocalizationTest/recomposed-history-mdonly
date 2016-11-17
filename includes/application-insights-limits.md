There are some limits on the number of metrics and events per application (that is, per instrumentation key). 

Limits depend on the [pricing plan](https://azure.microsoft.com/pricing/details/application-insights/) that you choose.

| **Resource** | **Default Limit** |
| --- | --- | --- |
| Total data per day | 100 GB | You can reduce by setting a cap.
| Free data per month | 1 GB | Additional data charged per GB
| Total data rate for request, event, dependency, page view and availability telemetry |200/s - 500/s | Dependent on pricing plan
| [Trace and Log](../articles/application-insights/app-insights-search-diagnostic-logs.md) data rate |200/s - 500/s | 
| [Exception](../articles/application-insights/app-insights-asp-net-exceptions.md) data rate |50/s |
| Data retention | 90 days | for [Search](../articles/application-insights/app-insights-diagnostic-search.md), [Analytics](../articles/application-insights/app-insights-analytics.md) and [Metrics explorer](../articles/application-insights/app-insights-metrics-explorer.md)
| [Availability test](../articles/application-insights/app-insights-monitor-web-app-availability.md) data retention | 30 days |
| [Property](../articles/application-insights/app-insights-api-custom-events-metrics.md#properties) and [Metric](../articles/application-insights/app-insights-api-custom-events-metrics.md#properties)<sup>2</sup> name count | 200 | 
| Property and metric name length | 150 |
| Property value string length | 8192 |
| Distinct values for properties<sup>3,4</sup> | 100 | >100 => can't use property as filter in Metrics Explorer
| Trace and Exception message length | 10000 |
| [Availability tests](../articles/application-insights/app-insights-monitor-web-app-availability.md)  | 10 |

1. All these numbers are per instrumentation key.
2. Metric names are defined both in TrackMetric and in the measurement parameter of other Track*() calls. Metric names are global per instrumentation key.
3. [Properties][apiproperties] can be used for filtering and group-by only while they have less than 100 unique values for each property. After the number of unique values exceeds 100, you can still search the property, but no longer use it for filters or group-by.
4. Standard properties such as Request Name and Page URL are limited to 1000 unique values per week. After 1000 unique values, additional values are marked as "Other values." The original values can still be used for full text search and filtering.


[About pricing and quotas in Application Insights](../articles/application-insights/app-insights-pricing.md)