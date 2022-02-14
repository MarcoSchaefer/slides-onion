# Monitoring solutions


## TOC

2.1 Logging

2.2 Metrics

2.3 Alerting

2.4 On-Call Rotations


![Observability Ecosystem](./slides/assets/observability_overview.jpg)
Note:
The ecosystem of monitoring and observability solutions is wide and complex. 

There are many possible solutions, and usually, a production stack is a combination of them. 

Each solution brings its weaknesses and strengths, and the best one is usually picked by budget, familiarity, features, and team knowledge.


## Logging


Should be able to collect, in a distributed manner, logs from different services.

Note:
Honorable mentions to Fluentd and Logstash.


Needs to store massive amounts of data, that are being streamed from our applications.

Note:
Honorable mention to ElasticSearch and bucket services.


Empowers users to group, analyze, and filter logs across services.

Note:
Honorable mentions to Kibana, Stackdriver. 

Many of the commercial solutions pack all these in one bundle, like AWS CloudWatch, DataDog, HoneyComb, and Graylog.

But it's important to note that you can use a combination of services specialized in each job. In fact, many of these bundles are just using them behind the scenes.


## Metrics
Ingesting, storing, querying, and visualizing time-series data.

Note:
As you might have noticed, these are the same requisites of logging. The difference is just that we are not limited to text, we have quantitative information instead of a qualitative one. 

These metrics are just samples collected with a predefined frequency, in contrast with logs that are always emitted. Logs crystalize past service state by stating events, and metrics represent service state across a period.

Honorable mentions to metrics collection: Prometheus, NewRelic, and Metricbeat.

Honorable mentions to metrics visualization: Grafana, NewRelic, and Kibana.


## Alerting
Tracking metrics over time and notifying stakeholders if it violates a threshold.

Note: This is usually consumed from either a log engine or a metric system or both.

Honorable mentions to: Grafana, Prometheus Alertmanager and Kibana alerting.


## On-Call Rotations
Setting a rotation schedule and notifying people when things go wrong.

Note:
Honorable mentions: OpsGenie and PagerDuty.
