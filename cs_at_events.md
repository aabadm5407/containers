---

copyright:
  years: 2017, 2020
lastupdated: "2020-05-06"

keywords: kubernetes, iks, audit

subcollection: containers

---

{:codeblock: .codeblock}
{:deprecated: .deprecated}
{:download: .download}
{:external: target="_blank" .external}
{:faq: data-hd-content-type='faq'}
{:gif: data-image-type='gif'}
{:help: data-hd-content-type='help'}
{:important: .important}
{:new_window: target="_blank"}
{:note: .note}
{:pre: .pre}
{:preview: .preview}
{:screen: .screen}
{:shortdesc: .shortdesc}
{:support: data-reuse='support'}
{:table: .aria-labeledby="caption"}
{:tip: .tip}
{:troubleshoot: data-hd-content-type='troubleshoot'}
{:tsCauses: .tsCauses}
{:tsResolve: .tsResolve}
{:tsSymptoms: .tsSymptoms}


# {{site.data.keyword.at_full_notm}} events
{: #at_events}

You can view, manage, and audit user-initiated activities in your {{site.data.keyword.containerlong}} community Kubernetes or OpenShift cluster by using the {{site.data.keyword.at_full}} service.
{: shortdesc}

{{site.data.keyword.containerlong_notm}} automatically generates cluster management events and forwards these event logs to {{site.data.keyword.at_full_notm}}. To access these logs, you must [provision an instance of {{site.data.keyword.at_full_notm}}](/docs/Activity-Tracker-with-LogDNA?topic=logdnaat-getting-started).

You can also collect Kubernetes API audit logs from your cluster and forward them to {{site.data.keyword.la_full_notm}}. To access Kubernetes audit logs, you must [create an audit webhook in your cluster](/docs/containers?topic=containers-health#webhook_logdna).
{: tip}

## Tracking cluster management events
{: #cluster-events}

The following list of the cluster management events are sent to {{site.data.keyword.at_full_notm}}.
{: shortdesc}

|Action|Description|
|------|-----------|
| `containers-kubernetes.account-credentials.set` | Infrastructure credentials in a region for a resource group are set. |
| `containers-kubernetes.account-credentials.unset` | Infrastructure credentials in a region for a resource group are unset. |
| `containers-kubernetes.alb.create` | An Ingress ALB is created. |
| `containers-kubernetes.alb.delete` | An Ingress ALB is deleted. |
| `containers-kubernetes.apikey.reset` | An API key is reset for a region and resource group. |
| `containers-kubernetes.cluster.config` | A `kubeconfig` file that contains the certificates and secrets to access a cluster is requested. |
| `containers-kubernetes.cluster.create` | A classic or VPC cluster is created or failed to create. |
| `containers-kubernetes.cluster.delete` | A cluster is deleted. |
| `containers-kubernetes.cluster.update` | A refresh or update of the Kubernetes master is requested.|
| `containers-kubernetes.cluster-feature.enable` | A feature, such as the public or private service endpoint, is enabled on a cluster. |
| `containers-kubernetes.logging-config.create` | A log forwarding configuration is created. |
| `containers-kubernetes.logging-config.delete` | A log forwarding configuration is deleted. |
| `containers-kubernetes.logging-config.update` | A log forwarding configuration is updated. |
| `containers-kubernetes.logging-config.refresh` | A log forwarding configuration is refreshed. |
| `containers-kubernetes.logging-filter.create` | A logging filter is created. |
| `containers-kubernetes.logging-filter.delete` | A logging filter is deleted. |
| `containers-kubernetes.logging-filter.update` | A logging filter is updated. |
| `containers-kubernetes.logging-autoupdate.changed` | The logging add-on auto updater is enabled or disabled. |
| `containers-kubernetes.mzlb.create` | A multizone load balancer is created. |
| `containers-kubernetes.mzlb.delete` | A multizone load balancer is deleted. |
| `containers-kubernetes.service.bind` | A service is bound to a cluster. |
| `containers-kubernetes.service.unbind` | A service is unbound from a cluster. |
| `containers-kubernetes.subnet.add` | An existing IBM Cloud infrastructure subnet is added to a cluster. |
| `containers-kubernetes.subnet.create` | A subnet is created. |
| `containers-kubernetes.usersubnet.add` | A user-managed subnet is added to a cluster. |
| `containers-kubernetes.usersubnet.delete` | A user-managed subnet is removed from a cluster. |
| `containers-kubernetes.version.update` | The Kubernetes version of a cluster master node is updated. |
| `containers-kubernetes.worker.create` | A worker node is created. |
| `containers-kubernetes.worker.delete` | A worker node is deleted. |
| `containers-kubernetes.worker.reboot` | A worker node is rebooted. |
| `containers-kubernetes.worker.reload` | A worker node is reloaded. |
| `containers-kubernetes.worker.update` | A worker node is updated.|
| `containers-kubernetes.workerpool.create` | A worker pool is created.|
| `containers-kubernetes.workerpool.update` | A worker pool is updated. |
| `containers-kubernetes.zone.update` | The networking attributes for a zone that a worker pool uses are updated. |
{: caption="Cluster management events" caption-side="top"}


## Tracking logging and monitoring configuration events
{: #at-lm}

The following list of the logging and monitoring configuration events are sent to {{site.data.keyword.at_full_notm}} by the {{site.data.keyword.containerlong_notm}} observability plug-in.
{: shortdesc}

|Action|Description|
|------|-----------|
| `containers-kubernetes.observe.logging.create` | A LogDNA logging configuration is created for the cluster. |
| `containers-kubernetes.observe.logging.modify` | A LogDNA logging configuration is updated. |
| `containers-kubernetes.observe.logging.remove` | A LogDNA logging configuration is removed from the cluster. |
| `containers-kubernetes.observe.monitoring.create` | A Sysdig monitoring configuration is created for the cluster. |
| `containers-kubernetes.observe.monitoring.modify` | A Sysdig monitoring configuration is updated. |
| `containers-kubernetes.observe.monitoring.remove` | A Sysdig monitoring configuration is removed from the cluster. |
{: caption="Logging and monitoring management events" caption-side="top"}



## Viewing your cluster events
{: #at-ui}

To [view events](/docs/Activity-Tracker-with-LogDNA?topic=logdnaat-view_events) that are sent to {{site.data.keyword.at_full_notm}}, you select the {{site.data.keyword.at_short}} instance that matches with the location of your {{site.data.keyword.containerlong_notm}} cluster.
{: shortdesc}

The following table shows the {{site.data.keyword.at_short}} location where your events are sent to. To view your events, make sure that you have an {{site.data.keyword.at_short}} instance in the location that matches your cluster location. Note that clusters in the Montreal, Toronto, and Washington, D.C. locations forward all events to the Dallas {{site.data.keyword.at_short}} location.

| {{site.data.keyword.containerlong_notm}} classic location | {{site.data.keyword.at_short}} event location |
|-----|-----|-----|
| Dallas (dal10, dal12, dal13) | Dallas |
| Mexico City (mex01) | Dallas |
| Montreal (mon01) | Dallas |
| San Jose (sjc03, sjc04) | Dallas |
| São Paulo (sao01) | Dallas |
| Toronto (tor01) | Dallas |
| Washington, D.C. (wdc04, wdc06, wdc07) | Dallas |
| Amsterdam (ams03) | Frankfurt |
| Frankfurt (fra02, fra04, fra05) | Frankfurt |
| Milan (mil01) | Frankfurt |
| Oslo (osl01) | Frankfurt |
| Paris (par01) | Frankfurt |
| London (lon02, lon04, lon05, lon06) | London |
| Sydney (syd01, syd04, syd05) | Sydney |
| Melbourne (mel01) | Sydney |
| Chennai (che01) | Tokyo |
| Hong Kong SAR of the PRC (hkg02) | Tokyo |
| Seoul (seo01) | Tokyo |
| Singapore (sng01) | Tokyo |
| Tokyo (tok02, tok04, tok05) | Tokyo |
{: caption="Corresponding {{site.data.keyword.at_short}} instance and {{site.data.keyword.containerlong_notm}} cluster locations." caption-side="top"}
{: class="simple-tab-table"}
{: #atlocationclassic}
{: tab-title="Locations for classic clusters"}
{: tab-group="at-locations"}

| {{site.data.keyword.containerlong_notm}} VPC location | {{site.data.keyword.at_short}} event location |
|-----|-----|-----|
| Dallas (us-south-1, us-south-2, us-south-3) | Dallas |
| Washington, D.C. (us-east-1, us-east-2, us-east-3) | Dallas |
| Frankfurt (eu-de-1, eu-de-2, eu-de-3) | Frankfurt |
| London (eu-gb-1, eu-gb-2, eu-gb-3) | London |
| Sydney (au-syd-1, au-syd-2, au-syd-3) | Sydney |
| Tokyo (jp-tok-1, jp-tok-2, jp-tok-3) | Tokyo |
{: class="simple-tab-table"}
{: caption="Corresponding {{site.data.keyword.at_short}} instance and {{site.data.keyword.containerlong_notm}} cluster locations." caption-side="top"}
{: #atlocationvpc}
{: tab-title="Locations for VPC clusters"}
{: tab-group="at-locations"}



