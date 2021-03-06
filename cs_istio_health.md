---

copyright:
  years: 2014, 2020
lastupdated: "2020-05-04"

keywords: kubernetes, iks, mesh, Prometheus, Grafana, Jaeger, Kiali, controlz, envoy

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


# Observing Istio traffic
{: #istio-health}

To log, monitor, trace, and visualize your apps that are managed by Istio on {{site.data.keyword.containerlong_notm}}, you can launch the Grafana, Jaeger, and Kiali dashboards or deploy LogDNA and Sysdig as third-party services to your worker nodes. You can also launch ControlZ and Envoy dashboards to inspect the performance of the core Istio components, such as Galley, Pilot, Mixer, and Envoy.
{: shortdesc}

## Launching the Prometheus, Grafana, Jaeger, and Kiali dashboards
{: #istio_health_extras}

For extra monitoring, tracing, and visualization of Istio, launch the [Prometheus](https://prometheus.io/){: external}, [Grafana](https://grafana.com/){: external}, [Jaeger](https://www.jaegertracing.io/){: external}, and [Kiali](https://kiali.io/){: external} dashboards.
{: shortdesc}

**Before you begin**
* [Install the `istio` managed add-on](/docs/containers?topic=containers-istio#istio_install) in a cluster.
* [Install the `istioctl` CLI](/docs/containers?topic=containers-istio#istioctl).

### Prometheus
{: #prometheus}

1. Send traffic to your service. For example, if you installed BookInfo, curl the product page microservice.
  ```
  curl http://$GATEWAY_URL/productpage
  ```
  {: pre}

2. Access the Prometheus dashboard.
  ```
  istioctl dashboard prometheus
  ```
  {: pre}

3. In Prometheus UI, click **Graph**.

4. In the **Expression** field, enter `istio_requests_total`.

5. Run the query by clicking **Execute**.

### Grafana
{: #grafana}

1. Define the credentials that you want to use to access Grafana.
  1. Copy and paste the following command. This command starts a text entry for you to enter a username, which is then encoded in base64 and stored in the `GRAFANA_USERNAME` environment variable.
    ```
    GRAFANA_USERNAME=$(read -p 'Grafana Username: ' uval && echo -n $uval | base64)
    ```
    {: pre}
  2. Copy and paste the following command. This command starts a text entry for you to enter a passphrase, which is then encoded in base64 and stored in the `GRAFANA_PASSPHRASE` environment variable.
    ```
    GRAFANA_PASSPHRASE=$(read -p 'Grafana Passphrase: ' pval && echo -n $pval | base64)
    ```
    {: pre}
  3. Apply the following configuration to store the credentials in a Kubernetes secret.
      ```
      cat <<EOF | kubectl apply -f -
      apiVersion: v1
      kind: Secret
      metadata:
        name: grafana
        namespace: istio-system
      type: Opaque
      data:
        username: $GRAFANA_USERNAME
        passphrase: $GRAFANA_PASSPHRASE
      EOF
      ```
      {: pre}

2. Access the Grafana dashboard.
  ```
  istioctl dashboard grafana
  ```
  {: pre}

3. The Istio dashboard shows metrics for the traffic that you generate when you access your app in the service mesh a few times. For more information about using the Istio Grafana dashboard, see [Viewing the Istio Dashboard](https://istio.io/docs/tasks/observability/metrics/using-istio-dashboard/){: external} in the Istio open source documentation.

### Jaeger
{: #jaeger}

1. By default, Istio generates trace spans for 1 out of every 100 requests, which is a sampling rate of 1%. You must send at least 100 requests before the first trace is visible. To send 100 requests to the `productpage` service of BookInfo, run the following command.
  ```
  for i in `seq 1 100`; do curl -s -o /dev/null http://$GATEWAY_URL/productpage; done
  ```
  {: pre}

2. Access the Jaeger dashboard.
  ```
  istioctl dashboard jaeger
  ```
  {: pre}

3. If you installed BookInfo, you can select `productpage` from the **Service** list and click **Find Traces**. Traces for the traffic that you generated when you refreshed the product page a few times are shown. For more information about using Jaeger with Istio, see [Generating traces using the BookInfo sample](https://istio.io/docs/tasks/observability/distributed-tracing/#generating-traces-using-the-bookinfo-sample){: external} in the Istio open source documentation.

### Kiali
{: #kiali}

1. Define the credentials that you want to use to access Kiali.
  1. Copy and paste the following command. This command starts a text entry for you to enter a username, which is then encoded in base64 and stored in the `KIALI_USERNAME` environment variable.
    ```
    KIALI_USERNAME=$(read -p 'Kiali Username: ' uval && echo -n $uval | base64)
    ```
    {: pre}
  2. Copy and paste the following command. This command starts a text entry for you to enter a passphrase, which is then encoded in base64 and stored in the `KIALI_PASSPHRASE` environment variable.
    ```
    KIALI_PASSPHRASE=$(read -p 'Kiali Passphrase: ' pval && echo -n $pval | base64)
    ```
    {: pre}
  3. Apply the following configuration to store the credentials in a Kubernetes secret.
      ```
      cat <<EOF | kubectl apply -f -
      apiVersion: v1
      kind: Secret
      metadata:
        name: kiali
        namespace: istio-system
        labels:
          app: kiali
      type: Opaque
      data:
        username: $KIALI_USERNAME
        passphrase: $KIALI_PASSPHRASE
      EOF
      ```
      {: pre}

2. Access the Kiali dashboard.
  ```
  istioctl dashboard kiali
  ```
  {: pre}

3. Enter the username and passphrase that you previously defined.

4. In the menu, click **Graph**.

5. In the **Select a namespace** drop-down list, select the namespace where your apps are deployed.
For more information about using Kiali to visualize your Istio-managed microservices, see [Generating a service graph](https://archive.istio.io/v1.0/docs/tasks/telemetry/kiali/#generating-a-service-graph){: external} in the Istio open source documentation.

<br />


## Launching the ControlZ component inspection and Envoy sidecar dashboards
{: #istio_inspect}

To inspect specific components of Istio, launch the [ControlZ](https://istio.io/docs/ops/diagnostic-tools/controlz/){: external} and [Envoy](https://istio.io/docs/reference/commands/istioctl/#istioctl-dashboard-envoy){: external} dashboards.
{: shortdesc}

The ControlZ dashboard accesses the Istio component ports to provide an interactive view into the internal state of each component. The Envoy dashboard provides configuration information and metrics for an Envoy sidecar proxy that runs in an app pod.

**Before you begin**
* [Install the `istio` managed add-on](/docs/containers?topic=containers-istio#istio_install) in a cluster.
* [Install the `istioctl` CLI](/docs/containers?topic=containers-istio#istioctl).

### ControlZ
{: #controlz}

1. Get the pod name for the Istio component that you want to inspect. You can inspect the component pods for `istio-citadel`, `istio-galley`, `istio-pilot`, `istio-policy`, and `istio-telemetry`.
  ```
  kubectl get pods -n istio-system | grep istio
  ```
  {: pre}

  Example output:
  ```
  NAME                                      READY   STATUS    RESTARTS   AGE
  istio-citadel-869c7f9498-wtldz            1/1     Running   0          2m
  istio-egressgateway-69bb5d4585-qxxbp      1/1     Running   0          2m
  istio-galley-75d7b5bdb9-c9d9n             1/1     Running   0          2m
  istio-ingressgateway-5c8764db74-gh8xg     1/1     Running   0          2m
  istio-pilot-55fd7d886f-vv6fb              2/2     Running   0          2m
  istio-policy-6bb6f6ddb9-s4c8t             2/2     Running   0          2m
  istio-sidecar-injector-7d9845dbb7-r8nq5   1/1     Running   0          2m
  istio-telemetry-7695b4c4d4-tlvn8          2/2     Running   0          2m
  istio-tracing-55bbf55878-z4rd2            1/1     Running   0          2m
  ```
  {: screen}

2. Access the ControlZ dashboard for that component.
  ```
  istioctl dashboard controlz <component_pod_name>.istio-system
  ```
  {: pre}

### Envoy
{: #envoy}

1. Get the name of the app pod where you want to inspect the Envoy sidecar container.
  ```
  kubectl get pods -n <namespace>
  ```
  {: pre}

2. Access the Envoy dashboard for that pod.
  ```
  istioctl dashboard envoy <pod-name>.<namespace>
  ```
  {: pre}

<br />


## Setting up logging with {{site.data.keyword.la_full_notm}}
{: #istio_health_logdna}

Seamlessly manage logs for your app container and the Envoy proxy sidecar container in each pod by deploying LogDNA to your worker nodes to forward logs to {{site.data.keyword.la_full}}.
{: shortdesc}

To use [{{site.data.keyword.la_full_notm}}](/docs/Log-Analysis-with-LogDNA?topic=LogDNA-getting-started), you deploy a logging agent to every worker node in your cluster. This agent collects logs with the extension `*.log` and extensionless files that are stored in the `/var/log` directory of your pod from all namespaces, including `kube-system`. These logs include logs from your app container and the Envoy proxy sidecar container in each pod. The agent then forwards the logs to the {{site.data.keyword.la_full_notm}} service.

To get started, set up LogDNA for your cluster by following the steps in [Managing Kubernetes cluster logs with {{site.data.keyword.la_full_notm}}](/docs/Log-Analysis-with-LogDNA/tutorials?topic=LogDNA-kube#kube).




<br />


## Setting up monitoring with {{site.data.keyword.mon_full_notm}}
{: #istio_health_sysdig}

Gain operational visibility into the performance and health of your Istio-managed apps by deploying Sysdig to your worker nodes to forward metrics to {{site.data.keyword.mon_full}}.
{: shortdesc}

The managed `istio` add-on installs Prometheus into your cluster. The `istio-mixer-telemetry` pods in your cluster are annotated with a Prometheus endpoint so that Prometheus can aggregate all telemetry data for your pods. When you deploy a Sysdig agent to the worker nodes in your cluster, Sysdig is already automatically enabled to detect and scrape the data from these Prometheus endpoints to display them in your {{site.data.keyword.cloud_notm}} monitoring dashboard.

Since all of the Prometheus work is done, all that is left for you is to deploy Sysdig in your cluster.

1. [Provision an instance of {{site.data.keyword.mon_full_notm}}](https://cloud.ibm.com/observe/monitoring/create){: external}.

2. [Configure a Sysdig agent in your cluster.](/docs/Monitoring-with-Sysdig/tutorials?topic=Sysdig-config_agent#config_agent_kube_script)

3. In the [Monitoring console](https://cloud.ibm.com/observe/monitoring){: external}, click **View Sysdig** for the instance that you provisioned.

4. In the Sysdig UI, click **Add new dashboard**.

5. Search for `Istio` and select one of Sysdig's predefined Istio dashboards.

For more information about referencing metrics and dashboards, monitoring Istio internal components, and monitoring Istio A/B deployments and canary deployments, check out [How to monitor Istio, the Kubernetes service mesh](https://sysdig.com/blog/monitor-istio/){: external} on the Sysdig blog.

