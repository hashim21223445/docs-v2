---
title: K8sAutoscaleNode
description: >
  K8sAutoscaleNode triggers autoscale events for a resource on a Kubernetes cluster and outputs points for the triggered events.
note: Auto generated by tickdoc
menu:
  kapacitor_v1:
    name: K8sAutoscaleNode
    identifier: k8s_autoscale_node
    weight: 100
    parent: nodes
aliases:
  - /kapacitor/v1/nodes/k8s_autoscale_node/
---

The `k8sAutoscale` node triggers autoscale events for a resource on a Kubernetes cluster.
The node also outputs points for the triggered events.

Example:


```js
// Target 100 requests per second per host
var target = 100.0
var min = 1
var max = 100
var period = 5m
var every = period
stream
  |from()
    .measurement('requests')
    .groupBy('host', 'deployment')
    .truncate(1s)
  |derivative('value')
    .as('requests_per_second')
    .unit(1s)
    .nonNegative()
  |groupBy('deployment')
  |sum('requests_per_second')
    .as('total_requests')
  |window()
    .period(period)
    .every(every)
  |mean('total_requests')
    .as('total_requests')
  |k8sAutoscale()
    // Get the name of the deployment from the 'deployment' tag.
    .resourceNameTag('deployment')
    .min(min)
    .max(max)
    // Set the desired number of replicas based on target.
    .replicas(lambda: int(ceil("total_requests" / target)))
  |influxDBOut()
    .database('deployments')
    .measurement('scale_events')
    .precision('s')
```


The above example computes the requests per second by deployment and host.
Then the total_requests per second across all hosts is computed per deployment.
Using the mean of the total_requests over the last time period a desired number of replicas is computed
based on the target number of request per second per host.

If the desired number of replicas has changed, Kapacitor makes the appropriate API call to Kubernetes
to update the replicas spec.

Any time the k8sAutoscale node changes a replica count, it emits a point.
The point is tagged with the namespace, kind and resource name,
using the NamespaceTag, KindTag, and ResourceTag properties respectively.
In addition the group by tags will be preserved on the emitted point.
The point contains two fields: `old`, and `new` representing change in the replicas.

Available Statistics:

* increase_events: number of times the replica count was increased.
* decrease_events: number of times the replica count was decreased.
* cooldown_drops: number of times an event was dropped because of a cooldown timer.
* errors: number of errors encountered, typically related to communicating with the Kubernetes API.


### Constructor

| Chaining Method | Description |
|:---------|:---------|
| **k8sAutoscale&nbsp;(&nbsp;)** | Create a node that can trigger autoscale events for a kubernetes cluster.  |

### Property Methods

| Setters | Description |
|:---|:---|
| **[cluster](#cluster)&nbsp;(&nbsp;`value`&nbsp;`string`)** | Cluster is the name of the Kubernetes cluster to use.  |
| **[currentField](#currentfield)&nbsp;(&nbsp;`value`&nbsp;`string`)** | CurrentField is the name of a field into which the current replica count will be set as an int. If empty no field will be set. Useful for computing deltas on the current state.  |
| **[decreaseCooldown](#decreasecooldown)&nbsp;(&nbsp;`value`&nbsp;`time.Duration`)** | Only one decrease event can be triggered per resource every DecreaseCooldown interval.  |
| **[increaseCooldown](#increasecooldown)&nbsp;(&nbsp;`value`&nbsp;`time.Duration`)** | Only one increase event can be triggered per resource every IncreaseCooldown interval.  |
| **[kind](#kind)&nbsp;(&nbsp;`value`&nbsp;`string`)** | Kind is the type of resources to autoscale. Currently only "deployments", "replicasets" and "replicationcontrollers" are supported. Default: "deployments"  |
| **[kindTag](#kindtag)&nbsp;(&nbsp;`value`&nbsp;`string`)** | KindTag is the name of a tag to use when tagging emitted points with the kind. If empty the point will not be tagged with the resource. Default: kind  |
| **[max](#max)&nbsp;(&nbsp;`value`&nbsp;`int64`)** | The maximum scale factor to set. If 0 then there is no upper limit. Default: 0, a.k.a no limit.  |
| **[min](#min)&nbsp;(&nbsp;`value`&nbsp;`int64`)** | The minimum scale factor to set. Default: 1  |
| **[namespace](#namespace)&nbsp;(&nbsp;`value`&nbsp;`string`)** | Namespace is the namespace of the resource, if empty the default namespace will be used.  |
| **[namespaceTag](#namespacetag)&nbsp;(&nbsp;`value`&nbsp;`string`)** | NamespaceTag is the name of a tag to use when tagging emitted points with the namespace. If empty the point will not be tagged with the resource. Default: namespace  |
| **[quiet](#quiet)&nbsp;(&nbsp;)** | Suppress all error logging events from this node.  |
| **[replicas](#replicas)&nbsp;(&nbsp;`value`&nbsp;`ast.LambdaNode`)** | Replicas is a lambda expression that should evaluate to the desired number of replicas for the resource.  |
| **[resourceName](#resourcename)&nbsp;(&nbsp;`value`&nbsp;`string`)** | ResourceName is the name of the resource to autoscale.  |
| **[resourceNameTag](#resourcenametag)&nbsp;(&nbsp;`value`&nbsp;`string`)** | ResourceNameTag is the name of a tag that names the resource to autoscale.  |
| **[resourceTag](#resourcetag)&nbsp;(&nbsp;`value`&nbsp;`string`)** | ResourceTag is the name of a tag to use when tagging emitted points the resource. If empty the point will not be tagged with the resource. Default: resource  |



### Chaining Methods
[Alert](#alert),
[Barrier](#barrier),
[Bottom](#bottom),
[ChangeDetect](#changedetect),
[Combine](#combine),
[Count](#count),
[CumulativeSum](#cumulativesum),
[Deadman](#deadman),
[Default](#default),
[Delete](#delete),
[Derivative](#derivative),
[Difference](#difference),
[Distinct](#distinct),
[Ec2Autoscale](#ec2autoscale),
[Elapsed](#elapsed),
[Eval](#eval),
[First](#first),
[Flatten](#flatten),
[GroupBy](#groupby),
[HoltWinters](#holtwinters),
[HoltWintersWithFit](#holtwinterswithfit),
[HttpOut](#httpout),
[HttpPost](#httppost),
[InfluxDBOut](#influxdbout),
[Join](#join),
[K8sAutoscale](#k8sautoscale),
[KapacitorLoopback](#kapacitorloopback),
[Last](#last),
[Log](#log),
[Mean](#mean),
[Median](#median),
[Mode](#mode),
[MovingAverage](#movingaverage),
[Percentile](#percentile),
[Sample](#sample),
[Shift](#shift),
[Sideload](#sideload),
[Spread](#spread),
[StateCount](#statecount),
[StateDuration](#stateduration),
[Stats](#stats),
[Stddev](#stddev),
[Sum](#sum),
[SwarmAutoscale](#swarmautoscale),
[Top](#top),
[Trickle](#trickle),
[Union](#union),
[Where](#where),
[Window](#window)

---

## Properties

Property methods modify state on the calling node.
They do not add another node to the pipeline, and always return a reference to the calling node.
Property methods are marked using the `.` operator.


### Cluster

Cluster is the name of the Kubernetes cluster to use.


```js
k8sAutoscale.cluster(value string)
```


### CurrentField

CurrentField is the name of a field into which the current replica count will be set as an int.
If empty no field will be set.
Useful for computing deltas on the current state.

Example:


```js
    |k8sAutoscale()
        .currentField('replicas')
        // Increase the replicas by 1 if the qps is over the threshold
        .replicas(lambda: if("qps" > threshold, "replicas" + 1, "replicas"))
```



```js
k8sAutoscale.currentField(value string)
```


### DecreaseCooldown

Only one decrease event can be triggered per resource every DecreaseCooldown interval.


```js
k8sAutoscale.decreaseCooldown(value time.Duration)
```


### IncreaseCooldown

Only one increase event can be triggered per resource every IncreaseCooldown interval.


```js
k8sAutoscale.increaseCooldown(value time.Duration)
```


### Kind

Kind is the type of resources to autoscale.
Currently only "deployments", "replicasets" and "replicationcontrollers" are supported.
Default: "deployments"


```js
k8sAutoscale.kind(value string)
```


### KindTag

KindTag is the name of a tag to use when tagging emitted points with the kind.
If empty the point will not be tagged with the resource.
Default: kind


```js
k8sAutoscale.kindTag(value string)
```


### Max

The maximum scale factor to set.
If 0 then there is no upper limit.
Default: 0, a.k.a no limit.


```js
k8sAutoscale.max(value int64)
```


### Min

The minimum scale factor to set.
Default: 1


```js
k8sAutoscale.min(value int64)
```


### Namespace

Namespace is the namespace of the resource, if empty the default namespace will be used.


```js
k8sAutoscale.namespace(value string)
```


### NamespaceTag

NamespaceTag is the name of a tag to use when tagging emitted points with the namespace.
If empty the point will not be tagged with the resource.
Default: namespace


```js
k8sAutoscale.namespaceTag(value string)
```


### Quiet

Suppress all error logging events from this node.

```js
k8sAutoscale.quiet()
```


### Replicas

Replicas is a lambda expression that should evaluate to the desired number of replicas for the resource.


```js
k8sAutoscale.replicas(value ast.LambdaNode)
```


### ResourceName

ResourceName is the name of the resource to autoscale.


```js
k8sAutoscale.resourceName(value string)
```


### ResourceNameTag

ResourceNameTag is the name of a tag that names the resource to autoscale.


```js
k8sAutoscale.resourceNameTag(value string)
```


### ResourceTag

ResourceTag is the name of a tag to use when tagging emitted points the resource.
If empty the point will not be tagged with the resource.
Default: resource


```js
k8sAutoscale.resourceTag(value string)
```


## Chaining Methods

Chaining methods create a new node in the pipeline as a child of the calling node.
They do not modify the calling node.
Chaining methods are marked using the `|` operator.


### Alert

Create an alert node, which can trigger alerts.


```js
k8sAutoscale|alert()
```

Returns: [AlertNode](/kapacitor/v1/reference/nodes/alert_node/)

### Barrier

Create a new Barrier node that emits a BarrierMessage periodically.

One BarrierMessage will be emitted every period duration.


```js
k8sAutoscale|barrier()
```

Returns: [BarrierNode](/kapacitor/v1/reference/nodes/barrier_node/)

### Bottom

Select the bottom `num` points for `field` and sort by any extra tags or fields.


```js
k8sAutoscale|bottom(num int64, field string, fieldsAndTags ...string)
```

Returns: [InfluxQLNode](/kapacitor/v1/reference/nodes/influx_q_l_node/)

### ChangeDetect

Create a new node that only emits new points if different from the previous point.

```js
k8sAutoscale|changeDetect(field string)
```

Returns: [ChangeDetectNode](/kapacitor/v1/reference/nodes/change_detect_node/)

### Combine

Combine this node with itself. The data is combined on timestamp.


```js
k8sAutoscale|combine(expressions ...ast.LambdaNode)
```

Returns: [CombineNode](/kapacitor/v1/reference/nodes/combine_node/)

### Count

Count the number of points.


```js
k8sAutoscale|count(field string)
```

Returns: [InfluxQLNode](/kapacitor/v1/reference/nodes/influx_q_l_node/)

### CumulativeSum

Compute a cumulative sum of each point that is received.
A point is emitted for every point collected.


```js
k8sAutoscale|cumulativeSum(field string)
```

Returns: [InfluxQLNode](/kapacitor/v1/reference/nodes/influx_q_l_node/)

### Deadman

Helper function for creating an alert on low throughput, a.k.a. deadman's switch.

- Threshold: trigger alert if throughput drops below threshold in points/interval.
- Interval: how often to check the throughput.
- Expressions: optional list of expressions to also evaluate. Useful for time of day alerting.

Example:


```js
    var data = stream
        |from()...
    // Trigger critical alert if the throughput drops below 100 points per 10s and checked every 10s.
    data
        |deadman(100.0, 10s)
    //Do normal processing of data
    data...
```

The above is equivalent to this example:


```js
    var data = stream
        |from()...
    // Trigger critical alert if the throughput drops below 100 points per 10s and checked every 10s.
    data
        |stats(10s)
            .align()
        |derivative('emitted')
            .unit(10s)
            .nonNegative()
        |alert()
            .id('node \'stream0\' in task \'{{ .TaskName }}\'')
            .message('{{ .ID }} is {{ if eq .Level "OK" }}alive{{ else }}dead{{ end }}: {{ index .Fields "emitted" | printf "%0.3f" }} points/10s.')
            .crit(lambda: "emitted" <= 100.0)
    //Do normal processing of data
    data...
```

The `id` and `message` alert properties can be configured globally via the 'deadman' configuration section.

Since the [AlertNode](/kapacitor/v1/reference/nodes/alert_node/) is the last piece it can be further modified as usual.
Example:


```js
    var data = stream
        |from()...
    // Trigger critical alert if the throughput drops below 100 points per 10s and checked every 10s.
    data
        |deadman(100.0, 10s)
            .slack()
            .channel('#dead_tasks')
    //Do normal processing of data
    data...
```

You can specify additional lambda expressions to further constrain when the deadman's switch is triggered.
Example:


```js
    var data = stream
        |from()...
    // Trigger critical alert if the throughput drops below 100 points per 10s and checked every 10s.
    // Only trigger the alert if the time of day is between 8am-5pm.
    data
        |deadman(100.0, 10s, lambda: hour("time") >= 8 AND hour("time") <= 17)
    //Do normal processing of data
    data...
```



```js
k8sAutoscale|deadman(threshold float64, interval time.Duration, expr ...ast.LambdaNode)
```

Returns: [AlertNode](/kapacitor/v1/reference/nodes/alert_node/)

### Default

Create a node that can set defaults for missing tags or fields.


```js
k8sAutoscale|default()
```

Returns: [DefaultNode](/kapacitor/v1/reference/nodes/default_node/)

### Delete

Create a node that can delete tags or fields.


```js
k8sAutoscale|delete()
```

Returns: [DeleteNode](/kapacitor/v1/reference/nodes/delete_node/)

### Derivative

Create a new node that computes the derivative of adjacent points.


```js
k8sAutoscale|derivative(field string)
```

Returns: [DerivativeNode](/kapacitor/v1/reference/nodes/derivative_node/)

### Difference

Compute the difference between points independent of elapsed time.


```js
k8sAutoscale|difference(field string)
```

Returns: [InfluxQLNode](/kapacitor/v1/reference/nodes/influx_q_l_node/)

### Distinct

Produce batch of only the distinct points.


```js
k8sAutoscale|distinct(field string)
```

Returns: [InfluxQLNode](/kapacitor/v1/reference/nodes/influx_q_l_node/)

### Ec2Autoscale

Create a node that can trigger autoscale events for a ec2 autoscalegroup.


```js
k8sAutoscale|ec2Autoscale()
```

Returns: [Ec2AutoscaleNode](/kapacitor/v1/reference/nodes/ec2_autoscale_node/)

### Elapsed

Compute the elapsed time between points.


```js
k8sAutoscale|elapsed(field string, unit time.Duration)
```

Returns: [InfluxQLNode](/kapacitor/v1/reference/nodes/influx_q_l_node/)

### Eval

Create an eval node that will evaluate the given transformation function to each data point.
A list of expressions may be provided and will be evaluated in the order they are given.
The results are available to later expressions.


```js
k8sAutoscale|eval(expressions ...ast.LambdaNode)
```

Returns: [EvalNode](/kapacitor/v1/reference/nodes/eval_node/)

### First

Select the first point.


```js
k8sAutoscale|first(field string)
```

Returns: [InfluxQLNode](/kapacitor/v1/reference/nodes/influx_q_l_node/)

### Flatten

Flatten points with similar times into a single point.


```js
k8sAutoscale|flatten()
```

Returns: [FlattenNode](/kapacitor/v1/reference/nodes/flatten_node/)

### GroupBy

Group the data by a set of tags.

Can pass literal * to group by all dimensions.
Example:


```js
    |groupBy(*)
```



```js
k8sAutoscale|groupBy(tag ...interface{})
```

Returns: [GroupByNode](/kapacitor/v1/reference/nodes/group_by_node/)

### HoltWinters

Compute the Holt-Winters (/influxdb/v1/query_language/functions/#holt-winters) forecast of a data set.


```js
k8sAutoscale|holtWinters(field string, h int64, m int64, interval time.Duration)
```

Returns: [InfluxQLNode](/kapacitor/v1/reference/nodes/influx_q_l_node/)

### HoltWintersWithFit

Compute the Holt-Winters (/influxdb/v1/query_language/functions/#holt-winters) forecast of a data set.
This method also outputs all the points used to fit the data in addition to the forecasted data.


```js
k8sAutoscale|holtWintersWithFit(field string, h int64, m int64, interval time.Duration)
```

Returns: [InfluxQLNode](/kapacitor/v1/reference/nodes/influx_q_l_node/)

### HttpOut

Create an HTTP output node that caches the most recent data it has received.
The cached data is available at the given endpoint.
The endpoint is the relative path from the API endpoint of the running task.
For example, if the task endpoint is at `/kapacitor/v1/tasks/<task_id>` and endpoint is
`top10`, then the data can be requested from `/kapacitor/v1/tasks/<task_id>/top10`.


```js
k8sAutoscale|httpOut(endpoint string)
```

Returns: [HTTPOutNode](/kapacitor/v1/reference/nodes/http_out_node/)

### HttpPost

Creates an HTTP Post node that POSTS received data to the provided HTTP endpoint.
HttpPost expects 0 or 1 arguments. If 0 arguments are provided, you must specify an
endpoint property method.


```js
k8sAutoscale|httpPost(url ...string)
```

Returns: [HTTPPostNode](/kapacitor/v1/reference/nodes/http_post_node/)

### InfluxDBOut

Create an influxdb output node that will store the incoming data into InfluxDB.


```js
k8sAutoscale|influxDBOut()
```

Returns: [InfluxDBOutNode](/kapacitor/v1/reference/nodes/influx_d_b_out_node/)

### Join

Join this node with other nodes. The data is joined on timestamp.


```js
k8sAutoscale|join(others ...Node)
```

Returns: [JoinNode](/kapacitor/v1/reference/nodes/join_node/)

### K8sAutoscale

Create a node that can trigger autoscale events for a kubernetes cluster.


```js
k8sAutoscale|k8sAutoscale()
```

Returns: [K8sAutoscaleNode](/kapacitor/v1/reference/nodes/k8s_autoscale_node/)

### KapacitorLoopback

Create an kapacitor loopback node that will send data back into Kapacitor as a stream.


```js
k8sAutoscale|kapacitorLoopback()
```

Returns: [KapacitorLoopbackNode](/kapacitor/v1/reference/nodes/kapacitor_loopback_node/)

### Last

Select the last point.


```js
k8sAutoscale|last(field string)
```

Returns: [InfluxQLNode](/kapacitor/v1/reference/nodes/influx_q_l_node/)

### Log

Create a node that logs all data it receives.


```js
k8sAutoscale|log()
```

Returns: [LogNode](/kapacitor/v1/reference/nodes/log_node/)

### Mean

Compute the mean of the data.


```js
k8sAutoscale|mean(field string)
```

Returns: [InfluxQLNode](/kapacitor/v1/reference/nodes/influx_q_l_node/)

### Median

Compute the median of the data.

> **Note:** This method is not a selector.
If you want the median point, use `.percentile(field, 50.0)`.


```js
k8sAutoscale|median(field string)
```

Returns: [InfluxQLNode](/kapacitor/v1/reference/nodes/influx_q_l_node/)

### Mode

Compute the mode of the data.


```js
k8sAutoscale|mode(field string)
```

Returns: [InfluxQLNode](/kapacitor/v1/reference/nodes/influx_q_l_node/)

### MovingAverage

Compute a moving average of the last window points.
No points are emitted until the window is full.


```js
k8sAutoscale|movingAverage(field string, window int64)
```

Returns: [InfluxQLNode](/kapacitor/v1/reference/nodes/influx_q_l_node/)

### Percentile

Select a point at the given percentile. This is a selector function, no interpolation between points is performed.


```js
k8sAutoscale|percentile(field string, percentile float64)
```

Returns: [InfluxQLNode](/kapacitor/v1/reference/nodes/influx_q_l_node/)

### Sample

Create a new node that samples the incoming points or batches.

One point will be emitted every count or duration specified.


```js
k8sAutoscale|sample(rate interface{})
```

Returns: [SampleNode](/kapacitor/v1/reference/nodes/sample_node/)

### Shift

Create a new node that shifts the incoming points or batches in time.


```js
k8sAutoscale|shift(shift time.Duration)
```

Returns: [ShiftNode](/kapacitor/v1/reference/nodes/shift_node/)

### Sideload

Create a node that can load data from external sources.


```js
k8sAutoscale|sideload()
```

Returns: [SideloadNode](/kapacitor/v1/reference/nodes/sideload_node/)

### Spread

Compute the difference between `min` and `max` points.


```js
k8sAutoscale|spread(field string)
```

Returns: [InfluxQLNode](/kapacitor/v1/reference/nodes/influx_q_l_node/)

### StateCount

Create a node that tracks number of consecutive points in a given state.


```js
k8sAutoscale|stateCount(expression ast.LambdaNode)
```

Returns: [StateCountNode](/kapacitor/v1/reference/nodes/state_count_node/)

### StateDuration

Create a node that tracks duration in a given state.


```js
k8sAutoscale|stateDuration(expression ast.LambdaNode)
```

Returns: [StateDurationNode](/kapacitor/v1/reference/nodes/state_duration_node/)

### Stats

Create a new stream of data that contains the internal statistics of the node.
The interval represents how often to emit the statistics based on real time.
This means the interval time is independent of the times of the data points the source node is receiving.


```js
k8sAutoscale|stats(interval time.Duration)
```

Returns: [StatsNode](/kapacitor/v1/reference/nodes/stats_node/)

### Stddev

Compute the standard deviation.


```js
k8sAutoscale|stddev(field string)
```

Returns: [InfluxQLNode](/kapacitor/v1/reference/nodes/influx_q_l_node/)

### Sum

Compute the sum of all values.


```js
k8sAutoscale|sum(field string)
```

Returns: [InfluxQLNode](/kapacitor/v1/reference/nodes/influx_q_l_node/)

### SwarmAutoscale

Create a node that can trigger autoscale events for a Docker swarm cluster.


```js
k8sAutoscale|swarmAutoscale()
```

Returns: [SwarmAutoscaleNode](/kapacitor/v1/reference/nodes/swarm_autoscale_node/)

### Top

Select the top `num` points for `field` and sort by any extra tags or fields.


```js
k8sAutoscale|top(num int64, field string, fieldsAndTags ...string)
```

Returns: [InfluxQLNode](/kapacitor/v1/reference/nodes/influx_q_l_node/)

### Trickle

Create a new node that converts batch data to stream data.

```js
k8sAutoscale|trickle()
```

Returns: [TrickleNode](/kapacitor/v1/reference/nodes/trickle_node/)

### Union

Perform the union of this node and all other given nodes.


```js
k8sAutoscale|union(node ...Node)
```

Returns: [UnionNode](/kapacitor/v1/reference/nodes/union_node/)

### Where

Create a new node that filters the data stream by a given expression.


```js
k8sAutoscale|where(expression ast.LambdaNode)
```

Returns: [WhereNode](/kapacitor/v1/reference/nodes/where_node/)

### Window

Create a new node that windows the stream by time.

NOTE: Window can only be applied to stream edges.


```js
k8sAutoscale|window()
```

Returns: [WindowNode](/kapacitor/v1/reference/nodes/window_node/)