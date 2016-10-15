# Provider Resource Usage API

The term provider applies to the service administrator and to any
delegated providers. Service admins and delegated providers can use the
Provider Usage API to view the usage of their direct tenants. For
example, P0 can call the Provider API to get usage information on P1 and
P2, and P1 can call for usage information on P3 and P4.

![](media/image1.png){width="1.4902952755905512in"
height="1.6417913385826772in"}


## API call reference

### Request

The request gets consumption details for the requested subscriptions and
for the requested time frame. There is no request body.

This usage API is a Provider API, so the caller must be assigned an
Owner, Contributor, or Reader role in the provider’s subscription.

  **Method**   **Request URI**
  ------------ ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
  GET          https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce/subscriberUsageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity}&subscriberId={sub1.1}&api-version=2015-06-01-preview&continuationToken={token-value}

### Arguments

  **Argument**               **Description**
  -------------------------- ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
  *armendpoint*              Azure Resource Manager endpoint of your Azure Stack environment.
  *subId*                    Subscription ID of the user who is making the call.
  *reportedStartTime*        Start time of the query. The value for *DateTime* should be in UTC and at the beginning of the hour, for example, 13:00. For daily aggregation, set this value to UTC midnight. The format is *escaped* ISO 8601, for example, 2015-06-16T18%3a53%3a11%2b00%3a00Z, where colon is escaped to %3a and plus is escaped to %2b so that it is URI friendly.
  *reportedEndTime*          End time of the query. The constraints that apply to *reportedStartTime* also apply to this argument. The value for *reportedEndTime* cannot be in the future.
  *aggregationGranularity*   Optional parameter that has two discrete potential values: daily and hourly. As the values suggest, one returns the data in daily granularity, and the other is an hourly resolution. The daily option is the default.
  *subscriberId*             Subscription ID. To get filtered data, the subscription ID of a direct tenant of the provider is required. If no subscription ID parameter is specified, the call returns usage data for all the provider’s direct tenants.
  *api-version*              Version of the protocol that is used to make this request. You must use 2015-06-01-preview.
  *continuationToken*        Token retrieved from the last call to the Usage API provider. This is needed when a response is greater than 1,000 lines. This is the bookmark for progress. If not present, the data is retrieved from the beginning of the day or hour, based on the granularity passed in.



### Response

GET
/subscriptions/sub1/providers/Microsoft.Commerce/subscriberUsageAggregates?reportedStartTime=reportedStartTime=2014-05-01T00%3a00%3a00%2b00%3a00&reportedEndTime=2015-06-01T00%3a00%3a00%2b00%3a00&aggregationGranularity=Daily&subscriberId=sub1.1&api-version=1.0

{

"value": \[

{

"id":
"/subscriptions/sub1.1/providers/Microsoft.Commerce/UsageAggregate/sub1.1-

meterID1",

"name": "sub1.1-meterID1",

"type": "Microsoft.Commerce/UsageAggregate",

"properties": {

"subscriptionId":"sub1.1",

"usageStartTime": "2015-03-03T00:00:00+00:00",

"usageEndTime": "2015-03-04T00:00:00+00:00",

> "instanceData":"{\\"Microsoft.Resources\\":{\\"resourceUri\\":\\"resourceUri1\\",\\"location\\
>
> ":\\"Alaska\\",\\"tags\\":null,\\"additionalInfo\\":null}}",

"quantity":2.4000000000,

"meterId":"meterID1"

}

},

…

### Response details

  --------------------------------------------------------------------------------------------------------------------------------
  **Argument**       **Description**
  ------------------ -------------------------------------------------------------------------------------------------------------
  *id*               Unique ID of the usage aggregate

  *name*             Name of the usage aggregate

  *type*             Resource definition

  *subscriptionId*   Subscription identifier of the Azure Stack user

  *usageStartTime*   UTC start time of the usage bucket to which this usage aggregate belongs

  *usageEndTime*     UTC end time of the usage bucket to which this usage aggregate belongs

  *instanceData*     Key-value pairs of instance details (in a new format):

                     -   *resourceUri*: Fully qualified resource ID, which includes the resource groups and the instance name

                     -   *location*: Region in which this service was run

                     -   *tags*: Resource tags that are specified by the user

                     -   *additionalInfo*: More details about the resource being consumed, for example, OS version or image type



  *quantity*         Amount of resource consumption that occurred in this time frame

  *meterId*          Unique ID for the resource that was consumed (also called *ResourceID*)
  --------------------------------------------------------------------------------------------------------------------------------

### Next steps

[Tenant resource usage API reference](tenant-resource-usage-api.md)

[Usage-related FAQ](usage-related-faq.md)
