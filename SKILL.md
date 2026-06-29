---
name: containersdash
description: Generate and open the Kubernetes Container Cost Dashboard. Use when the user asks for container dashboard, k8s costs, kubernetes spend, cluster costs, container optimization, idle resources, or namespace breakdown.
---

# Kubernetes Container Cost Dashboard

When triggered, pull live container cost data from Cloudability MCP and generate an interactive HTML dashboard:

## Steps

1. Pull container data from Cloudability using cost reports with container dimensions:
   - **Cluster summary**: dimensions=`container_cluster_name`, metrics=`unblended_cost,total_amortized_cost`, sorted by cost DESC (last 30 days)
   - **Namespace detail**: dimensions=`container_cluster_name,container_namespace`, metrics=`unblended_cost,total_amortized_cost`, sorted by cost DESC (last 30 days)
   - **Weekly trend**: dimensions=`container_cluster_name,year_week`, metrics=`unblended_cost,total_amortized_cost`, filtered to top 7 clusters (last 13 weeks)
   - **Region/Vendor**: dimensions=`container_cluster_name,vendor,region`, metrics=`unblended_cost`, sorted by cost DESC (last 30 days)

2. Build a self-contained HTML dashboard at `/Users/kingyeazey/container-dashboard.html` with Chart.js (CDN) and 4 tabs:
   - **Overview**: KPI cards (total spend, idle cost, idle %, cluster count), vendor donut, region bar chart, weekly trend line chart
   - **Top Clusters**: horizontal bar chart of top 20 clusters, detailed table with name/cost/amortized/region/vendor
   - **Optimization**: idle vs utilized stacked bar per cluster, top idle namespaces table, total potential savings callout
   - **KPIs**: key metric cards, cost distribution chart, week-over-week change indicators

3. Open the dashboard in the browser:
   ```bash
   open /Users/kingyeazey/container-dashboard.html
   ```

## Design

- Dark blue/purple gradient theme
- Chart.js via CDN
- Responsive grid layout with smooth tab transitions
- Color coding: idle=coral/red, utilized=teal/green, AWS=orange, OCI=red, Azure=blue, GCP=green
- All currency formatted with $ and commas

## Key Metrics to Calculate

- Total Container Spend (clusters with names, excluding "not set")
- Total Idle Cost (sum of IDLE RESOURCES namespace costs)
- Idle Rate % (idle / total allocated)
- Cluster Count
- Cost per Cluster average
- Top Region and Top Vendor
- Week-over-week change %

## Notes

- Container dimensions: `container_cluster_name`, `container_namespace`
- The `fairshare_cost`, `utilized_cost`, `idle_cost` metrics may not be available in all environments — use namespace="IDLE RESOURCES" to calculate idle costs instead
- Cluster naming convention reveals vendor: `*-akp-*` = AWS EKS, `*-oke-*` = OCI OKE
- Region decoded from cluster prefix: uw2=us-west-2, ew1=eu-west-1, ec1=eu-central-1, ase2=ap-southeast-2, ane1=ap-northeast-1, as1=ap-south-1, cc1=ca-central-1, ash1=us-ashburn-1, frk1=eu-frankfurt-1, lon1=uk-london-1, sao1=sa-saopaulo-1
