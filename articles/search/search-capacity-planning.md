---
title:  Estimate capacity for query and index workloads
titleSuffix: Azure AI Search
description: Learn how capacity is structured and used in Azure AI Search, and how to estimate the resources needed for indexing and query workloads.

manager: nitinme
author: haileytap
ms.author: haileytapia
ms.service: azure-ai-search
ms.custom:
  - ignite-2023
  - ignite-2024
ms.topic: conceptual
ms.date: 04/22/2025
---

# Estimate and manage capacity of a search service

In Azure AI Search, capacity is based on *replicas* and *partitions* that can be scaled to your workload. Replicas are copies of the search engine. Partitions are units of storage. Each new search service starts with one each, but you can add or remove replicas and partitions independently to accommodate fluctuating workloads. Adding capacity increases the [cost of running a search service](search-sku-manage-costs.md#billable-events).

The physical characteristics of replicas and partitions, such as processing speed and disk IO, vary by [pricing tier](search-sku-tier.md). On a standard search service, the replicas and partitions are faster and larger than those of a basic service.

Changing capacity isn't instantaneous. It can take up to an hour to commission or decommission partitions, especially on services with large amounts of data.

When scaling a search service, you can choose from the following tools and approaches:

+ [Azure portal](#adjust-capacity)
+ [Azure PowerShell](search-manage-powershell.md#scale-replicas-and-partitions)
+ [Azure CLI](/cli/azure/search/service#az-search-service-create-optional-parameters)
+ [Management REST API](/rest/api/searchmanagement/services/create-or-update)

> [!NOTE]
> If your service was created before April or May 2024, a one-time upgrade to higher storage limits might be available at no extra cost. For more information, see [Upgrade your search service](search-how-to-upgrade.md).

## Concepts: search units, replicas, partitions

Capacity is expressed in *search units* that can be allocated in combinations of *partitions* and *replicas*.  

| Concept  | Definition|
|----------|-----------|
|*Search unit* | A single increment of total available capacity (36 units). A minimum of one unit is required to run the service. The first replica and partition pair is the first search unit. However, each extra instance of a replica *or* a partition consumes an extra search unit. For example, you start with one replica and partition (one search unit), add a second replica, you're now consuming two search units. A search unit is also the billing unit for an Azure AI Search service. |
|*Replica* | Instances of the search service, used primarily to load balance query operations. Each replica hosts one copy of an index. If you allocate three replicas, you have three copies of an index available for servicing query requests.|
|*Partition* | Physical storage and I/O for read/write operations (for example, when rebuilding or refreshing an index). Each partition has a slice of the total index. If you allocate three partitions, your index is divided into thirds. |

Review the [partitions and replicas table](#partition-and-replica-combinations) for possible combinations that stay under the 36 unit limit.

## When to add capacity

Initially, a service is allocated a minimal level of resources consisting of one partition and one replica. The [tier you choose](search-sku-tier.md) determines partition size and speed, and each tier is optimized around a set of characteristics that fit various scenarios. If you choose a higher-end tier, you might [need fewer partitions](search-performance-tips.md#service-capacity) than if you go with S1. One of the questions you need to answer through self-directed testing is whether a larger and more expensive partition yields better performance than two cheaper partitions on a service provisioned at a lower tier.

A single service must have sufficient resources to handle all workloads (indexing and queries). Neither workload runs in the background. You can schedule indexing for times when query requests are naturally less frequent, but the service doesn't otherwise prioritize one task over another. Additionally, a certain amount of redundancy smooths out query performance when services or nodes are updated internally.

Guidelines for determining whether to add capacity include:

+ Meeting the high availability criteria for service-level agreement.
+ The frequency of HTTP 503 (Service unavailable) errors is increasing.
+ The frequency of HTTP 429 (Too many requests) errors is increasing, an indication of low storage.
+ Large query volumes are expected.
+ A [one-time upgrade](#how-to-upgrade-capacity) to newer infrastructure and larger partitions isn’t sufficient.
+ The current number of partitions isn’t adequate for indexing workloads.

As a general rule, search applications tend to need more replicas than partitions, particularly when the service operations are biased toward query workloads. Each replica is a copy of your index, allowing the service to load balance requests against multiple copies. Azure AI Search manages all load balancing and replication of an index, and you can alter the number of replicas allocated for your service at any time. You can allocate up to 12 replicas in a Standard search service and 3 replicas in a Basic search service. Replica allocation can be made either from the [Azure portal](search-create-service-portal.md) or one of the programmatic options.

Extra partitions are helpful for intensive indexing workloads. Extra partitions spread read/write operations across a larger number of compute resources.

Finally, larger indexes take longer to query. As such, you might find that every incremental increase in partitions requires a smaller but proportional increase in replicas. The complexity of your queries and query volume factors into how quickly query execution is turned around.

> [!NOTE]
> Adding more replicas or partitions increases the cost of running the service, and can introduce slight variations in how results are ordered. Be sure to check the [pricing calculator](https://azure.microsoft.com/pricing/calculator/) to understand the billing implications of adding more nodes. The [chart below](#chart) can help you cross-reference the number of search units required for a specific configuration. For more information on how extra replicas affect query processing, see [Ordering results](search-pagination-page-layout.md#ordering-results).

<a name="adjust-capacity"></a>

## How to upgrade capacity

Some Azure AI Search capabilities are only available to new services. One such capability is higher storage capacity, which applies to [services created after April 2024](search-limits-quotas-capacity.md#service-limits). However, if you created your service before April 2024, you can get higher capacity without recreating your service by performing a one-time upgrade. For more information, see [Upgrade your search service](search-how-to-upgrade.md).

## How to change capacity

To increase or decrease the capacity of your service, you have two options:

+ [Add or remove partitions and replicas](#add-or-remove-partitions-and-replicas)
+ [Change your pricing tier](#change-your-pricing-tier)

### Add or remove partitions and replicas

1. Sign in to the [Azure portal](https://portal.azure.com/) and select your search service.

1. From the left pane, select **Settings** > **Scale**.

   The following screenshot shows a Standard service provisioned with one replica and partition. The formula at the bottom indicates how many search units are being used (1). If the unit price was $100 (not a real price), the monthly cost of running this service would be $100 on average.

   :::image type="content" source="media/search-capacity-planning/initial-values.png" alt-text="Screenshot of the Scale page showing the current replica and partition values." border="true" lightbox="media/search-capacity-planning/initial-values.png":::

1. Use the slider to increase or decrease the number of partitions. Select **Save**.

   This example adds a second replica and partition. Notice the search unit count; it's now four because the billing formula is replicas multiplied by partitions (2 x 2). Doubling capacity more than doubles the cost of running the service. If the search unit cost was $100, the new monthly bill would now be $400.

   For the current per unit costs of each tier, visit the [pricing page](https://azure.microsoft.com/pricing/details/search/).

   :::image type="content" source="media/search-capacity-planning/add-two-each.png" alt-text="Screenshot of the Scale page with added replicas and partitions." border="true" lightbox="media/search-capacity-planning/add-two-each.png":::

1. Check your notifications to confirm that the operation started.

   :::image type="content" source="media/search-capacity-planning/portal-notifications.png" alt-text="Screenshot of the notification of the scaling operation in the Azure portal." border="true" lightbox="media/search-capacity-planning/portal-notifications.png":::

   This operation can take several hours to complete. You can’t cancel the process after it starts, and there’s no real-time monitoring of replica and partition adjustments. However, the following message displays while changes are underway.

   :::image type="content" source="media/search-capacity-planning/updating-message.png" alt-text="Screenshot of the Updating message in the Azure portal." border="true" lightbox="media/search-capacity-planning/updating-message.png":::

### Change your pricing tier

> [!NOTE]
> The 2025-02-01-preview supports changes between Basic and Standard (S1, S2, and S3) tiers. Currently, you can only switch from a lower tier to a higher tier, such as going from Basic to S1. Your region also can't have [capacity constraints on the higher tier](search-region-support.md).

Your [pricing tier](search-sku-tier.md) determines the maximum storage of your search service. If you need more <!-- or less capacity -->capacity, you can switch to a different pricing tier that accommodates your storage needs.

In addition to capacity, changing your pricing tier affects the workload and maximum limits of your service. Before you proceed, compare the [service limits](search-limits-quotas-capacity.md) of your current tier and your desired tier. These include limits on:

+ Partition storage
+ Indexes
+ Vectors
+ Indexers
+ Shared private link resources
+ Synonyms
+ Index aliases
+ Semantic ranker throttling

Generally, switching to a higher tier increases your [storage limit](search-limits-quotas-capacity.md#service-limits) and [vector limit](search-limits-quotas-capacity.md#vector-index-size-limits), increases request throughput, and decreases latency<!-- , while switching to a lower tier decreases your storage limit and vector limit, decreases request throughput, and increases latency -->.

To change your pricing tier:

1. Sign in to the [Azure portal](https://portal.azure.com/) and select your search service.

1. From the left pane, select **Settings** > **Scale**.

1. Under your current tier, select **Change Pricing Tier**.

   :::image type="content" source="media/search-capacity-planning/change-pricing-tier.png" alt-text="Screenshot of the Change Pricing Tier button in the Azure portal." border="true" lightbox="media/search-capacity-planning/change-pricing-tier.png":::

1. On the **Select Pricing Tier** page, choose a higher tier from the list. Currently, you can only move up between Basic, S1, S2, and S3. Other pricing tiers are unavailable and appear dimmed.

1. To switch to the higher tier, select **Select**.

   :::image type="content" source="media/search-capacity-planning/pricing-tier-list.png" alt-text="Screenshot of the Select Pricing Tier page and the list of higher tiers in the Azure portal." border="true" lightbox="media/search-capacity-planning/pricing-tier-list.png":::

   This operation can take several hours to complete. You can’t cancel the process after it starts, and there’s no real-time monitoring of tier changes. However, on the **Overview** page, a **Provisioning** status indicates the operation is underway for your service.

   :::image type="content" source="media/search-capacity-planning/provisioning-status.png" alt-text="Screenshot of the service Overview page with a Provisioning status." border="true" lightbox="media/search-capacity-planning/provisioning-status.png":::

## How scale requests are handled

Upon receipt of a scale request, the search service:

1. Checks whether the request is valid.
1. Starts backing up data and system information.
1. Checks whether the service is already in a provisioning state (currently adding or eliminating either replicas or partitions).
1. Starts provisioning.

Scaling a service can take as little as 15 minutes or well over an hour, depending on the size of the service and the scope of the request. Backup can take several minutes, depending on the amount of data and number of partitions and replicas.

The above steps aren't entirely consecutive. For example, the system starts provisioning when it can safely do so, which could be while backup is winding down.

## Errors during scaling

The error message "Service update operations aren't allowed at this time because we're processing a previous request" is caused by repeating a request to scale down or up when the service is already processing a previous request.

Resolve this error by checking service status to verify provisioning status:

1. Use the [Management REST API](/rest/api/searchmanagement), [Azure PowerShell](search-manage-powershell.md), or [Azure CLI](/cli/azure/search) to get service status.
1. Call [Get Service (REST)](/rest/api/searchmanagement/services/get) or equivalent for PowerShell or the CLI.
1. Check the response for ["provisioningState": "provisioning"](/rest/api/searchmanagement/services/get#provisioningstate)

If status is "Provisioning", wait for the request to complete. Status should be either "Succeeded" or "Failed" before another request is attempted. There's no status for backup. Backup is an internal operation and it's unlikely to be a factor in any disruption of a scale exercise.

If your search service appears to be stalled in a provisioning state, check for orphaned indexes that are unusable, with zero query volumes and no index updates. An unusable index can block changes to service capacity. In particular, look for indexes that are [CMK-encrypted](search-security-manage-encryption-keys.md), whose keys are no longer valid. You should either delete the index or restore the keys to bring the index back online and unblock your scale operation.

<a id="chart"></a>

## Partition and replica combinations

The following chart applies to Standard tier and higher. It shows all possible combinations of partitions and replicas, subject to the 36 search unit maximum per service. 

|   | **1 partition** | **2 partitions** | **3 partitions** | **4 partitions** | **6 partitions** | **12 partitions** |
| --- | --- | --- | --- | --- | --- | --- |
| **1 replica** |1 SU |2 SU |3 SU |4 SU |6 SU |12 SU |
| **2 replicas** |2 SU |4 SU |6 SU |8 SU |12 SU |24 SU |
| **3 replicas** |3 SU |6 SU |9 SU |12 SU |18 SU |36 SU |
| **4 replicas** |4 SU |8 SU |12 SU |16 SU |24 SU |N/A |
| **5 replicas** |5 SU |10 SU |15 SU |20 SU |30 SU |N/A |
| **6 replicas** |6 SU |12 SU |18 SU |24 SU |36 SU |N/A |
| **12 replicas** |12 SU |24 SU |36 SU |N/A |N/A |N/A |

Basic search services have lower search unit counts.

+ On search services created before April 3, 2024, Basic services can have exactly one partition and up to three replicas for a maximum limit of three SUs. The only adjustable resource is replicas. However, you might be able to increase your partition count by [upgrading your service](search-how-to-upgrade.md).

+ On search services created after April 3, 2024 in [supported regions](search-limits-quotas-capacity.md#service-limits), Basic services can have up to three partitions and three replicas. The maximum SU limit is nine to support a full complement of partitions and replicas.

For search services on any billable tier, regardless of creation date, you need a minimum of two replicas for high availability on queries.

For billing rates per tier and currency, see the [Azure AI Search pricing page](https://azure.microsoft.com/pricing/details/search/).

## Estimate capacity using a billable tier

The size of the indexes you expect to build determines storage needs. There are no solid heuristics or generalities that help with estimates. The only way to determine the size of an index is [build one](search-what-is-an-index.md). Its size is based on tokenization and embeddings, and whether you enable suggesters, filtering, and sorting, or can take advantage of [vector compression](vector-search-how-to-quantization.md).

We recommend estimating on a billable tier, Basic or above. The Free tier runs on physical resources shared by multiple customers and is subject to factors beyond your control. Only the dedicated resources of a billable search service can accommodate larger sampling and processing times for more realistic estimates of index quantity, size, and query volumes during development. 

1. [Review service limits at each tier](search-limits-quotas-capacity.md#service-limits) to determine whether lower tiers can support the number of indexes you need. Consider whether you need multiple copies of an index for active development, testing, and production. 

   A search service is subject to object limits (maximum number of indexes, indexers, skillsets, etc.) and storage limits. Whichever limit is reached first is the effective limit. 

1. [Create a service at a billable tier](search-create-service-portal.md). Tiers are optimized for certain workloads. For example, the Storage Optimized tier has a limit of 10 indexes because it's designed to support a low number of large indexes.

    + Start low, at Basic or S1, if you're not sure about the projected load.

    + Start high, at S2 or even S3, if testing includes large-scale indexing and query loads.

    + Start with Storage Optimized, at L1 or L2, if you're indexing a large amount of data and query load is relatively low, as with an internal business application.

1. [Build an initial index](search-what-is-an-index.md) to determine how source data translates to an index. This is the only way to estimate index size. Attributes on the field definitions affect physical storage requirements:

   + For keyword search, marking fields as filterable and sortable [increases index size](search-what-is-an-index.md#example-demonstrating-the-storage-implications-of-attributes-and-suggesters).

   + For vector search, you can [set parameters to reduce vector size](vector-search-how-to-configure-compression-storage.md).

1. [Monitor storage, service limits, query volume, and latency](monitor-azure-cognitive-search.md) in the Azure portal. the Azure portal shows you queries per second, throttled queries, and search latency. All of these values can help you decide if you selected the right tier.

1. Add replicas for high availability or to mitigate slow query performance.

   There are no guidelines on how many replicas are needed to accommodate query loads. Query performance depends on the complexity of the query and competing workloads. Although adding replicas clearly results in better performance, the result isn't strictly linear: adding three replicas doesn't guarantee triple throughput. For guidance in estimating QPS for your solution, see [Analyze performance](search-performance-analysis.md)and [Monitor queries](search-monitor-queries.md).

For an [inverted index](https://en.wikipedia.org/wiki/Inverted_index), size and complexity are determined by content, not necessarily by the amount of data that you feed into it. A large data source with high redundancy could result in a smaller index than a smaller dataset that contains highly variable content. So it's rarely possible to infer index size based on the size of the original dataset.

Storage requirements can be inflated if you include data that will never be searched. Ideally, documents contain only the data that you need for the search experience.

## Service-level agreement considerations

The Free tier and preview features aren't covered by [service-level agreements (SLAs)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). For all billable tiers, SLAs take effect when you provision sufficient redundancy for your service.

+ Two or more replicas satisfy query (read) SLAs.

+ Three or more replicas satisfy query and indexing (read-write) SLAs.

The number of partitions doesn't affect SLAs.

## Next steps

> [!div class="nextstepaction"]
> [Plan and manage costs](search-sku-manage-costs.md)
