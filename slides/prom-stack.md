# Prom Stack


## TOC
1. Prometheus
2. Grafana


## Prometheus
<!-- .slide: data-background-color="white"  --> 
<img style="background-image: white;" src="/slides/assets/prometheus_architecture.png" />

Note:
Prometheus architecture encompasses 3 main points we discussed before:
Ingesting, storing, and querying metrics data.

Ingestion can happen by service discovery, where Prometheus knows available services that expose metrics and scrape metrics from them, this is what we call a pull model. Ingestion can also happen by using the push model, where the service is responsible for posting data to the Prometheus store.

Prometheus stores data in a time series "database", which can be later retrieved by querying an http server. 

It also has an alerting component that can be used.


<!-- .slide: data-background-color="white"  --> 
<img style="background-image: white;" src="/slides/assets/metrics.png" />

Note:
Prometheus exposes four different kinds of metrics:
- A counter is a metric that can only go up, so it either stays the same or is increased.
- A gauge is a metric that can go up and down.
- An histogram is a group of counters that are all increased when the sampled observation it's lower than a value.
For instance, we can organize buckets that count all requests that take up to 1ms, 10ms, 100ms, 1s, 10s, and those beyond 10s.
- A summary is like a histogram but instead of using nominal values, it uses quantiles.

And we use a query language to get insights from these different metric types.


# Demo time!

Note:
To better illustrate Prometheus, we will set up a very simple project where we can see it in action!
1. Quick overview of what our service is doing.
2. Spin our service
```sh
npm i && npm run start
```
3. Show the two application routes "/" and "/bad", one returning 200 and the other 500. Show their answer.
4. We will expose two kinds of metrics. Automatically collected metrics by the "prom-client" library and custom metrics that we defined. Show the "/metrics" route and the request duration middleware.
5. Spin prometheus
```sh
 docker run -p 9090:9090 -v "$(pwd)/prometheus-data/prometheus.yml":/etc/prometheus/prometheus.yml prom/prometheus
```
6. Run a stress test and see how our collected metrics behaved. 
```sh
wrk -t12 -c400 -d30s "http://127.0.0.1:3001"
wrk -t12 -c400 -d10s "http://127.0.0.1:3001/bad"
```
7. Show localhost:3001/metrics on how data is disposed on the route.
8. Show the Prometheus UI how we can query it to gather information
```
    Show: http_request_duration_ms_count
    Show: http_request_duration_ms_sum
    Show: http_request_duration_ms_sum / http_request_duration_ms_count
    Show: http_request_duration_ms_bucket
    I can check the number of requests by checking the count metric, but I could calculate the request/s using the rate operator. 
    Show: rate(http_request_duration_ms_count[30s])
    Show: histogram_quantile(0.95, rate(http_request_duration_ms_bucket[1m]))
    Show: sum(rate(http_request_duration_ms_count[1m])) by (service, route, method, code)  * 60
    Show: avg(nodejs_external_memory_bytes / 1024 / 1024) by (service)
    Show: sum(increase(http_request_duration_ms_count{code=~"^5..$"}[1m])) /  sum(increase(http_request_duration_ms_count[1m]))
```
9. Notice that we could define our SLA. If our service 0.95 quantile is higher than 20s, we could trigger an alert!
10. But hey, doesn't it look bad?!


## Grafana
Grafana allows you to query, visualize, alert on, and explore your metrics, logs, and traces no matter where they are stored.


# Demo time !
Note:
We will run in docker Grafana: 
```sh
docker run -i -p 3000:3000 grafana/grafana
```

Name: Average duration by status

Show: http_request_duration_ms_sum / http_request_duration_ms_count

Type: time -> ms

Name: Requests/second by route and code

Show: rate(http_request_duration_ms_count[30s])

Legend: {{route}} - [{{code}}]

Type: Throughtput -> rps

Name: Requests duration by quantile:  $quantile

Do: Create a custom variable using quantile values: 0.5, 0.8, 0.9, 0.95, 0.99

Label: {{route}} - [{{code}}]

Show: histogram_quantile($quantile, rate(http_request_duration_ms_bucket[1m]))

Name: Requests duration

Format: Heatmap

Data format: Time series bucket

Label: {{ le }}

Show: sum(rate(http_request_duration_ms_bucket[1m])) by (le) 

Name: Memory usage 

Show: nodejs_external_memory_bytes

Type: Bytes (SI)

Name: Success rate

Show: "sum(increase(http_request_duration_ms_count{code=~"^5..$"}[1m])) /  sum(increase(http_request_duration_ms_count[1m]))"

Then change to: "1 - sum(increase(http_request_duration_ms_count{code=~"^5..$"}[1m])) /  sum(increase(http_request_duration_ms_count[1m]))"

Type: Percent 0-1

Threshold: 0.97 for success rate (green)

Bullshit time RSS: https://status.aws.amazon.com/rss/elasticbeanstalk-us-west-2.rss

Save the dashboard. 

Create an alert for the Sucess rate that shows our SLA.

Create folder.

Create alert:
IS BELOW: 0.95
Evaluate: every 30s for 2min
Add info -> Description -> Success rate is bellow SLA! CALL 911 now!

Run wrk with more failures:
wrk -t2 -c20 -d300s "http://127.0.0.1:3001"
wrk -t12 -c500 -d300s "http://127.0.0.1:3001/bad"
