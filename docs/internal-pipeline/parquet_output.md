---
layout: default
---
\[[Front page](../index.md)\] \[[Internal data pipeline](../internal_data_pipeline.md)\]

# Format of received Kafka messages from `ccx-XXX-insights-operator-archive-features` topic

Parquet Factory is a program that can read data from several data sources,
aggregate the data received from them and generate a set of Parquet files with
the aggregated data, storing them in a selected S3 or Ceph bucket. It is used
to generate different data aggregations in the CCX Internal Data Pipeline,
reading data from Kafka topics and Thanos service.

## Schema version

1 (unofficial)

## Description

Six types of Parquet files (tables) are generated by Parquet Factory service:

* `cluster_info`
* `rule_hits`
* `cluster_thanos_info`
* `failing_operator_conditions`
* `alerts`
* `gatherers_info`

Format of these tables are described below.

## Basic format

---
**NOTE**

byte array is used in Parquet files also to represent string (among other data
structures). For more information please look at
[https://parquet.apache.org/documentation/latest/](https://parquet.apache.org/documentation/latest/).

---

### Format of `cluster_info` table

* `cluster_id` (byte array) cluster ID represented as UUID
* `current_version` (byte array) cluster version represented as string
* `platform` (byte array) cluster platform specification
* `collected_at` (timestamp) timestamp of report represented with millisecond precision
* `desired_version` (byte array) desired cluster version represented as string
* `initial_version` (byte array) initial cluster version represented as string
* `network_type` (byte array) the type of network used by the cluster represented as string
* `archive_path` (byte array) path to the object stored in Ceph



#### `cluster_id` attribute

`cluster_id` uses its canonical textual representation: the 16 octets of a
UUID are represented as 32 hexadecimal (base-16) digits, displayed in five
groups separated by hyphens, in the form 8-4-4-4-12 for a total of 36
characters (32 hexadecimal characters and 4 hyphens). For more information
please look at
[https://en.wikipedia.org/wiki/Universally_unique_identifier](https://en.wikipedia.org/wiki/Universally_unique_identifier).

An example of UUID:

```
3ba9b042-b8b8-4714-98e9-17915c2eeb95
```

#### `current_version` attribute

Current cluster version represented as string, for example "4.5.24" or even
"4.6.0-0.ci.test-2020-12-01-123456-ci-xx-yyyyyyy"


#### `platform` attribute

Cluster platform specification, for example "BareMetal", "OpenStack", "Azure"
etc.


#### `collected_at` attribute

Timestamp of report represented with millisecond precision, for example
"2021-02-08 00:22:19" (this is the display format, the precision is higher
internally)


#### `desired_version` attribute

Desired cluster version represented as string, for example "4.5.24" or even
"4.6.0-0.ci.test-2020-12-01-123456-ci-xx-yyyyyyy"


#### `initial_version` attribute

Initial cluster version represented as string, for example "4.5.24" or even
"4.6.0-0.ci.test-2020-12-01-123456-ci-xx-yyyyyyy"

#### `network_type` attribute

This column represent the type of network used by the cluster. Some well-known
values are "OpenshiftSDN", "NCP" or "Kuryr".

#### `archive_path` attribute

This attribute contains path to object stored in Ceph. It must be real path
with chunks splitted by slash character. The format of path is:

```
archives/compressed/{PREFIX}/{CLUSTER_ID}/{YEAR+MONTH}/{DAY}/{ID}.tar.gz |
```

An example of path:

```
archives/compressed/0a/0aaaaaaa-bbbb-cccc-dddd-ffffffffffff/202102/08/003201.tar.gz |
```


### Format of `rule_hits` table

* `cluster_id` (byte array) cluster ID represented as UUID
* `rule_id` (byte array)
* `collected_at` (timestamp) timestamp of report represented in milliseconds
* `archive_path` (byte array) path to the object stored in Ceph


#### `cluster_id` attribute

`cluster_id` uses its canonical textual representation: the 16 octets of a
UUID are represented as 32 hexadecimal (base-16) digits, displayed in five
groups separated by hyphens, in the form 8-4-4-4-12 for a total of 36
characters (32 hexadecimal characters and 4 hyphens). For more information
please look at
[https://en.wikipedia.org/wiki/Universally_unique_identifier](https://en.wikipedia.org/wiki/Universally_unique_identifier).

An example of UUID:

```
3ba9b042-b8b8-4714-98e9-17915c2eeb95
```

#### `rule_id` attribute

It contains rule name and a key that are joined by | character.

Example of rule IDs:

```
pods_check_containers|POD_CONTAINER_ISSUE
operators_check|OPERATOR_ISSUE
pods_check|POD_ISSUE
```

#### `collected_at` attribute

Timestamp of report represented with millisecond precision, for example
"2021-02-08 00:22:19" (this is the display format, the precision is higher
internally)


#### `archive_path` attribute

This attribute contains path to object stored in Ceph. It must be real path
with chunks splitted by slash character. The format of path is:

```
archives/compressed/{PREFIX}/{CLUSTER_ID}/{YEAR+MONTH}/{DAY}/{ID}.tar.gz |
```

An example of path:

```
archives/compressed/0a/0aaaaaaa-bbbb-cccc-dddd-ffffffffffff/202102/08/003201.tar.gz |
```


### Format of `cluster_thanos_info` table

* `cluster_id` (byte array) cluster ID represented as UUID
* `ebs_account` (byte array) account number
* `email_domain` (byte array) an e-mail domain
* `managed` (bool) flag whether the cluster is managed or not
* `support` (byte array) specifies the support level of a cluster
* `collected_at` (timestamp) timestamp of report represented with millisecond precision


#### `cluster_id` attribute

`cluster_id` uses its canonical textual representation: the 16 octets of a
UUID are represented as 32 hexadecimal (base-16) digits, displayed in five
groups separated by hyphens, in the form 8-4-4-4-12 for a total of 36
characters (32 hexadecimal characters and 4 hyphens). For more information
please look at
[https://en.wikipedia.org/wiki/Universally_unique_identifier](https://en.wikipedia.org/wiki/Universally_unique_identifier).

An example of UUID:

```
3ba9b042-b8b8-4714-98e9-17915c2eeb95
```

#### `ebs_account` attribute

Account number, which is usually a positive integer represented as string, for
example "123456".

#### `email_domain` attribute

An e-mail domain, for example: "redhat.com"

#### `managed` (bool) attribute

Boolean flag containing info if the cluster is managed or not.

#### `support` attribute

Specifies the level of (customer) support for this cluster.

#### `collected_at` attribute

Timestamp of report represented with millisecond precision, for example
"2021-02-08 00:22:19" (this is the display format, the precision is higher
internally)

### Format of `failing_operator_conditions` table

* `cluster_id` (byte array) cluster ID represented as UUID
* `operator` (byte array) specifies which is the reporting operator
* `condition_type` (byte array) indicates the type of status reported by the operator
* `condition_status` (bool) flag whether the reported operator is active or not
* `reason` (byte array) specifies the main reason that the operator is reporting
* `raw_message` (byte array) is the whole error/information message reported by the operator
* `archive_path` (byte array) path to the object stored in Ceph
* `last_transition_time` (timestamp) timestamp of the last reported transition to this status
* `collected_at` (timestamp) timestamp of report represented with millisecond precision

#### `cluster_id` attribute

`cluster_id` uses its canonical textual representation: the 16 octets of a
UUID are represented as 32 hexadecimal (base-16) digits, displayed in five
groups separated by hyphens, in the form 8-4-4-4-12 for a total of 36
characters (32 hexadecimal characters and 4 hyphens). For more information
please look at
[https://en.wikipedia.org/wiki/Universally_unique_identifier](https://en.wikipedia.org/wiki/Universally_unique_identifier).

An example of UUID:

```
3ba9b042-b8b8-4714-98e9-17915c2eeb95
```

#### `operator` attribute

Name of the operator reporting its condition.

Some examples of operators that can be received:

* `authentication`
* `kube-apiserver`
* `monitoring`
* `network`

#### `condition_type` attribute

Type of the condition that is being reported by the operator. It will be a byte
array from the following set of expected values:

* `Available`
* `Degraded`
* `Progressing`
* `Upgradeable`
* `Failing`

#### `condition_status` attribute

Boolean flag that specifies the status of the given type reported by the operator.

#### `reason` attribute

A byte array containing a brief explanation about the operator condition status change.

#### `raw_message` attribute

A byte array containing the full description of the condition status change.

#### `archive_path` attribute

This attribute contains path to object stored in Ceph. It must be real path
with chunks splitted by slash character. The format of path is:

```
archives/compressed/{PREFIX}/{CLUSTER_ID}/{YEAR+MONTH}/{DAY}/{ID}.tar.gz |
```

An example of path:

```
archives/compressed/0a/0aaaaaaa-bbbb-cccc-dddd-ffffffffffff/202102/08/003201.tar.gz |
```

#### `last_transition_time` attribute

Timestamp of the last transition to the reported status for the operator,
represented with millisecond precision, for example "2021-02-08 00:22:19" (this
is the display format, the precision is higher internally)

#### `collected_at` (timestamp) timestamp of report represented with millisecond precision

Timestamp of report represented with millisecond precision, for example
"2021-02-08 00:22:19" (this is the display format, the precision is higher
internally)


### Format of `alerts` table

* `cluster_id` (byte array) cluster ID represented as UUID
* `name` (byte array) specifies the name of the reported alert
* `state` (byte array) indicates the state of the alert (firing/pending/...)
* `severity` (byte array) indicates the severeness of the alert
* `namespace` (byte array) is the namespace the cluster belongs to, if any
* `job` (byte array) is the name of the job during which the alert triggered, if any
* `labels` (byte array) JSON containing the alert's labels - necessary additional information
* `archive_path` (byte array) path to the object stored in Ceph

#### `cluster_id` attribute

`cluster_id` uses its canonical textual representation: the 16 octets of a
UUID are represented as 32 hexadecimal (base-16) digits, displayed in five
groups separated by hyphens, in the form 8-4-4-4-12 for a total of 36
characters (32 hexadecimal characters and 4 hyphens). For more information
please look at
[https://en.wikipedia.org/wiki/Universally_unique_identifier](https://en.wikipedia.org/wiki/Universally_unique_identifier).

An example of UUID:

```
3ba9b042-b8b8-4714-98e9-17915c2eeb95
```

#### `name` attribute

Name of the alert being reported.

Some examples of alerts:

* `Watchdog`
* `APIRemovedInNextReleaseInUse`
* `KubePodCrashLooping`
* `KubeAPIErrorBudgetBurn`
* `ClusterOperatorDegraded`

#### `state` attribute

Indicates whether the alert is pending or firing.

Examples:

* `pending`
* `firing`

#### `severity` attribute

Indicates how severe the alert is. Indicates the seriousness of the alert.

Examples:

* `warning`
* `critical`
* `alert`
* `info`

#### `namespace` attribute

This is the name of the namespace to which the cluster belongs to, if it belongs to any - the field may be empty.
The value is extracted from the labels attribute.

Examples:

* `openshift-logging`
* `openshift-kube-controller-manager`
* `openshift-cluster-version`

#### `job` attribute

Name of the job during which the alert triggered, if any. The field may be empty.
The value is extracted from the labels attribute.

Examples:

* `kube-state-metrics`
* `cluster-version-operator`

#### `labels` attribute

A JSON encoded string/byte array containing necessary information for the alert about the cluster.

Examples:

* `{"prometheus":"openshift-monitoring/k8s","prometheus_replica":"prometheus-k8s-0"`
* `{"group":"apiextensions.k8s.io","prometheus":"openshift-monitoring/k8s","prometheus_replica":"prometheus-k8s-0","resource":"customresourcedefinitions","version":"v1beta1"}`

#### `archive_path` attribute

This attribute contains path to object stored in Ceph. It must be real path
with chunks splitted by slash character. The format of path is:

```
archives/compressed/{PREFIX}/{CLUSTER_ID}/{YEAR+MONTH}/{DAY}/{ID}.tar.gz |
```

An example of path:

```
archives/compressed/0a/0aaaaaaa-bbbb-cccc-dddd-ffffffffffff/202102/08/003201.tar.gz |
```

### Format of `gatherers_info` table

* `cluster_id` (byte array) cluster ID represented as UUID
* `name` (byte array) specifies the name of the reported gatherer
* `duration_in_ms` (int) time in milliseconds that the gatherer was running
* `archive_path` (byte array) path to the object stored in Ceph
* `collected_at` (timestamp) timestamp of report represented with millisecond precision

#### `cluster_id` attribute

`cluster_id` uses its canonical textual representation: the 16 octets of a
UUID are represented as 32 hexadecimal (base-16) digits, displayed in five
groups separated by hyphens, in the form 8-4-4-4-12 for a total of 36
characters (32 hexadecimal characters and 4 hyphens). For more information
please look at
[https://en.wikipedia.org/wiki/Universally_unique_identifier](https://en.wikipedia.org/wiki/Universally_unique_identifier).

An example of UUID:

```
3ba9b042-b8b8-4714-98e9-17915c2eeb95
```

#### `name` attribute

The `name` indicates one of the gatherer modules that was used during the
archive generation in the form of a Go function name (`package.Function`).

An example of function name:

```
clusterconfig.GatherPodDisruptionBudgets
```

#### `duration_in_ms` attribute

This attribute indicates the time (in milliseconds) that took to the Insights
Operator to run this gathering function.

#### `archive_path` attribute

This attribute contains path to object stored in Ceph. It must be real path
with chunks splitted by slash character. The format of path is:

```
archives/compressed/{PREFIX}/{CLUSTER_ID}/{YEAR+MONTH}/{DAY}/{ID}.tar.gz |
```

An example of path:

```
archives/compressed/0a/0aaaaaaa-bbbb-cccc-dddd-ffffffffffff/202102/08/003201.tar.gz |
```

#### `collected_at` attribute

Timestamp of report represented with millisecond precision, for example
"2021-02-08 00:22:19" (this is the display format, the precision is higher
internally)



## Possible enhancements

* Version (positive integer) should be included in the generated Parquet files
  into separate column.
* For strings with fixed (and known) length, fixed_len_byte_array type should
  be used to speedup Parquet file generation and consuming.
* Using dictionaries for `cluster_id` might not be optimal - more study needed.

## Examples

### Content of `cluster_info` table

```
+--------------------------------------|-------------------|------------|---------------------|-------------------|-------------------|------------------|-------------------------------------------------------------------------------------+
| cluster_id                           | cluster_version   | platform   | collected_at        | desired_version   | initial_version   | network_type     | archive_path                                                                        |
|--------------------------------------|-------------------|------------|---------------------|-------------------|-------------------|----------------------|---------------------------------------------------------------------------------|
| 1e1ec4a5-1234-4f6c-838d-4e1c19300787 | 4.6.16            | None       | 2021-03-18 11:20:21 | 4.6.16            | 4.6.16            | OpenshiftSDN     | archives/compressed/1e/1e1ec4a5-1234-4f6c-838d-4e1c19300787/202103/18/112021.tar.gz |
| 5e6538de-1234-4740-abbe-93afdef885a7 | 4.7.0             | None       | 2021-03-18 11:54:37 | 4.7.0             | 4.7.0             | Kurir            | archives/compressed/5e/5e6538de-1234-4740-abbe-93afdef885a7/202103/18/115437.tar.gz |
| e9ae142e-1234-4c49-a937-80f57a7abb52 | 4.6.16            | None       | 2021-03-18 11:04:22 | 4.6.16            | 4.6.16            | NCP              | archives/compressed/e9/e9ae142e-1234-4c49-a937-80f57a7abb52/202103/18/110422.tar.gz |
| d8757ed6-1234-4860-8065-983f838e581e | 4.6.17            | None       | 2021-03-18 11:13:50 | 4.6.17            | 4.6.17            | OpenshiftSDN     | archives/compressed/d8/d8757ed6-1234-4860-8065-983f838e581e/202103/18/111350.tar.gz |
| 735c7c4d-1234-438f-a494-225f664c72a4 | 4.6.16            | VSphere    | 2021-03-18 11:38:46 | 4.6.16            | 4.3.28            | OpenshiftSDN     | archives/compressed/73/735c7c4d-1234-438f-a494-225f664c72a4/202103/18/113846.tar.gz |
+--------------------------------------|-------------------|------------|---------------------|-------------------|-------------------|------------------|-------------------------------------------------------------------------------------+
```
---
**NOTE**

`cluster_id` is mocked, these clusters are not real.

---

### Content of `rule_hits` table

```
+--------------------------------------|-------------------------------------------|---------------------|-------------------------------------------------------------------------------------+
| cluster_id                           | rule_id                                   | collected_at        | archive_path                                                                        |
|--------------------------------------|-------------------------------------------|---------------------|-------------------------------------------------------------------------------------|
| 00000000-0000-0000-0000-000000000000 | pods_check_containers|POD_CONTAINER_ISSUE | 2021-02-08 00:59:34 | archives/compressed/00/00000000-0000-0000-0000-000000000000/202102/08/005934.tar.gz |
| 11111111-1111-1111-1111-111111111111 | operators_check|OPERATOR_ISSUE            | 2021-02-08 00:59:34 | archives/compressed/11/11111111-1111-1111-1111-111111111111/202102/08/005934.tar.gz |
| 22222222-2222-2222-2222-222222222222 | pods_check|POD_ISSUE                      | 2021-02-08 00:59:34 | archives/compressed/22/22222222-2222-2222-2222-222222222222/202102/08/005934.tar.gz |
+--------------------------------------|-------------------------------------------|---------------------|-------------------------------------------------------------------------------------+
```

---
**NOTE**

`cluster_id` is mocked, these clusters are not real.

---

### Content of `cluster_thanos_info` table

```
+--------------------------------------|---------------|-------------------|-----------|--------------|---------------------+
| cluster_id                           |   ebs_account | email_domain      | managed   | support      | collected_at        |
|--------------------------------------|---------------|-------------------|-----------|--------------|---------------------|
| 12345678-ec5b-4552-9e2d-76c7bc4dee8f |    b'1234567' | us.ibm.com        | False     | None         | 2021-03-18 07:59:00 |
| 12345678-ef92-4ee2-84c9-478f99b76fdb |    b'1234500' | customer1.com     | False     | Self-Support | 2021-03-18 07:59:00 |
| 12345678-c145-4b12-850b-56a9de766d9c |    b'1234567' | us.ibm.com        | False     | None         | 2021-03-18 07:59:00 |
| 12345678-6d6f-490d-a6e2-0e01fbbb962d |    b'1234600' | customer2.com     | False     | None         | 2021-03-18 07:59:00 |
| 12345678-bde1-4e51-84e7-378030599471 |    b'1234700' | customer3.com     | False     | Premium      | 2021-03-18 07:59:00 |
| 12345678-f0c3-4b44-80d2-ed6b5fc6c95f |    b'1234567' | us.ibm.com        | False     | Eval         | 2021-03-18 07:59:00 |
| 12345678-8eb8-4d85-828d-ce940f1b9778 |    b'1234800' | us.ibm.com        | False     | Eval         | 2021-03-18 07:59:00 |
| 12345678-8169-4a66-8c17-ce3a15eb81d3 |     b'123000' | customer4.co      | False     | Premium      | 2021-03-18 07:59:00 |
+--------------------------------------|---------------|-------------------|-----------|--------------|---------------------+

```

---
**NOTE**

`cluster_id`, `ebs_account`, `email_domain` are mocked.

---

### Content of `failing_operator_conditions` table

```
+--------------------------------------|----------------|----------------|------------------|------------------------------------|---------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------|------------------------|---------------------+
| cluster_id                           | operator       | condition_type | condition_status | reason                             | raw_message                                                                                 | archive_path                                                                        | last_transition_time   | collected_at        |
|--------------------------------------|----------------|----------------|------------------|------------------------------------|---------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------|----------------------------------------------|
| 12345678-acc8-4439-9393-c12140c20033 | authentication | Progressing    | True             | _OAuthServerDeploymentNotReady     | Progressing: not all deployment replicas are ready                                          | archives/compressed/12/12345678-acc8-4439-9393-c12140c20033/202104/28/074816.tar.gz | 2021-04-28 07:27:34    | 2021-04-28 07:48:16 |
| 12345678-acc8-4439-9393-c12140c20033 | dns            | Progressing    | True             | Reconciling                        | At least 1 DNS DaemonSet is progressing.                                                    | archives/compressed/12/12345678-acc8-4439-9393-c12140c20033/202104/28/074816.tar.gz | 2021-04-28 07:28:16    | 2021-04-28 07:48:16 |
| 12345678-acc8-4439-9393-c12140c20033 | kube-apiserver | Progressing    | True             | NodeInstaller                      | NodeInstallerProgressing: 3 nodes are at revision 47; 0 nodes have achieved new revision 55 | archives/compressed/12/12345678-acc8-4439-9393-c12140c20033/202104/28/074816.tar.gz | 2021-04-28 07:27:54    | 2021-04-28 07:48:16 |
+--------------------------------------|----------------|----------------|------------------|------------------------------------|---------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------|------------------------|---------------------+
```

---
**NOTE**

`cluster_id` is mocked, these clusters are not real.

---


### Content of `alerts` table

```
+--------------------------------------|------------------------------------|---------|------------|---------------------------|--------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------+
| cluster_id                           | name                               | state   | severity   | namespace                 | job                      | labels                                                                                                                                                                                                                                                                                                                                                                                     | archive_path                                                                        |
|--------------------------------------|------------------------------------|---------|------------|---------------------------|--------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------|
| 00335bc8-1234-5678-add6-b6ba003bad09 | Watchdog                           | firing  | none       |                           |                          | {"prometheus":"openshift-monitoring/k8s","prometheus_replica":"prometheus-k8s-1"}                                                                                                                                                                                                                                                                                                          | archives/compressed/00/00335bc8-1234-5678-add6-b6ba003bad09/202106/05/122338.tar.gz |
| 00335bc8-1234-5678-add6-b6ba003bad09 | UpdateAvailable                    | firing  | info       | openshift-cluster-version | cluster-version-operator | {"channel":"stable-4.7","endpoint":"metrics","instance":"10.20.30.128:9099","job":"cluster-version-operator","namespace":"openshift-cluster-version","pod":"cluster-version-operator-55f5795c8f-sz2v7","prometheus":"openshift-monitoring/k8s","prometheus_replica":"prometheus-k8s-1","service":"cluster-version-operator","upstream":"\u003cdefault\u003e"}                              | archives/compressed/00/00335bc8-1234-5678-add6-b6ba003bad09/202106/05/122338.tar.gz |
| 73ea4458-1234-5678-8d1d-476c565fa5ab | UpdateAvailable                    | firing  | info       | openshift-cluster-version | cluster-version-operator | {"channel":"stable-4.6","endpoint":"metrics","instance":"10.20.3.4:9099","job":"cluster-version-operator","namespace":"openshift-cluster-version","pod":"cluster-version-operator-fd6fbbd78-5m99k","prometheus":"openshift-monitoring/k8s","prometheus_replica":"prometheus-k8s-0","service":"cluster-version-operator","upstream":"https://api.openshift.com/api/upgrades_info/v1/graph"} | archives/compressed/73/73ea4458-1234-5678-8d1d-476c565fa5ab/202106/05/122348.tar.gz |
| 73ea4458-1234-5678-8d1d-476c565fa5ab | Watchdog                           | firing  | none       |                           |                          | {"prometheus":"openshift-monitoring/k8s","prometheus_replica":"prometheus-k8s-0"}                                                                                                                                                                                                                                                                                                          | archives/compressed/73/73ea4458-1234-5678-8d1d-476c565fa5ab/202106/05/122348.tar.gz |
| 73ea4458-1234-5678-8d1d-476c565fa5ab | AlertmanagerReceiversNotConfigured | firing  | warning    |                           |                          | {"prometheus":"openshift-monitoring/k8s","prometheus_replica":"prometheus-k8s-0"}                                                                                                                                                                                                                                                                                                          | archives/compressed/73/73ea4458-1234-5678-8d1d-476c565fa5ab/202106/05/122348.tar.gz |
+--------------------------------------|------------------------------------|---------|------------|---------------------------|--------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------+
```

---
**NOTE**

`cluster_id` is mocked, these clusters are not real.
`namespace` and `job` is duplicated in `labels` for backwards compatibility

---

### Content of `gathering_info` table

```
+--------------------------------------|--------------------------------------------|------------------|-------------------------------------------------------------------------------------|---------------------+
| cluster_id                           | name                                       |   duration_in_ms | archive_path                                                                        | collected_at        |
|--------------------------------------|--------------------------------------------|------------------|-------------------------------------------------------------------------------------|---------------------|
| 12345678-acc8-4439-9393-c12140c20033 | clusterconfig.GatherPodDisruptionBudgets   |               26 | archives/compressed/12/12345678-acc8-4439-9393-c12140c20033/202108/02/144656.tar.gz | 2021-08-02 14:46:56 |
| 12345678-acc8-4439-9393-c12140c20033 | clusterconfig.GatherSAPVsystemIptablesLogs |               27 | archives/compressed/12/12345678-acc8-4439-9393-c12140c20033/202108/02/144656.tar.gz | 2021-08-02 14:46:56 |
| 12345678-acc8-4439-9393-c12140c20033 | clusterconfig.GatherClusterImageRegistry   |               28 | archives/compressed/12/12345678-acc8-4439-9393-c12140c20033/202108/02/144656.tar.gz | 2021-08-02 14:46:56 |
| 12345678-acc8-4439-9393-c12140c20033 | clusterconfig.GatherSAPDatahubs            |               29 | archives/compressed/12/12345678-acc8-4439-9393-c12140c20033/202108/02/144656.tar.gz | 2021-08-02 14:46:56 |
| 12345678-acc8-4439-9393-c12140c20033 | clusterconfig.GatherContainerRuntimeConfig |               30 | archives/compressed/12/12345678-acc8-4439-9393-c12140c20033/202108/02/144656.tar.gz | 2021-08-02 14:46:56 |
+--------------------------------------|--------------------------------------------|------------------|-------------------------------------------------------------------------------------|---------------------+
```

---
**NOTE**

`cluster_id` is mocked, these clusters are not real.

---
