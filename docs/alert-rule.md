# Alert rule notes

## Goal

`KubernetesPodRestartAlert` is a Grafana-managed alert rule that detects a container restart in the recent five-minute window. The rule was tested against a disposable pod that repeatedly exited. The observed firing state validated rule evaluation; external notification delivery was not configured.

## Prometheus query

```promql
sum by (namespace, pod, container) (
  increase(kube_pod_container_status_restarts_total[5m])
) > 0
```

The result retains labels for namespace, pod, and container, which make it possible to identify the affected workload in the alert instance. If the query returns no series, check metric availability and allow time for a scrape.

## Grafana configuration guide

1. Open **Alerting → Alert rules → New alert rule**.
2. Name it `KubernetesPodRestartAlert`.
3. Select the provisioned Prometheus data source and enter the query above.
4. Configure the condition to fire when the query result is above `0`.
5. Choose an evaluation group/interval and pending period; document the values you select because they affect how quickly the rule fires.
6. Save the rule and verify it appears in the rules list.
7. Apply `../k8s/restart-test-loop.yaml`, then inspect the alert instance labels.
8. Delete the test pod and allow the lookback window plus another evaluation to pass before checking for recovery.

Grafana alert-rule screens can differ by version. Treat the query and threshold as the core logic, and confirm the rule preview is producing the expected series before saving.

## Alert state and notifications

An alert in Grafana's **Alerting** state means its rule condition is firing. Notification delivery requires a contact point and notification policy. Those were not part of the completed project and should not be described as configured.
