# Onion Architecture


## TOC

5.1 Repositories

5.2 Views

5.3 Entrypoints


## 1.1 Why does monitoring matter?


<!-- .slide: data-background="slides/assets/cockpit.jpg"  --> 


<!-- .slide: data-background="slides/assets/cockpit_black.png"  --> 


## 1.2 Availability overview

Note:
Easier than defining an available system, it's defining an unavailable system; an unavailable system cannot perform its function and will fail by default.

Availability defines whether a system can fulfill its intended function at a point in time. 

In addition to being used as a reporting tool, the historical availability measurement can also describe the probability that your system will perform as expected in the future.


Question: How often is the system available?


### Observation

When you visit shakespeare.com, you normally get back the <b>200 OK</b> status code and an HTML blob. 

Very rarely, you see a <b>500 Internal Server</b> error or a connection failure.


### Hypothesis
If "availability" is the percentage of requests per day that return 200 OK, the system will be 99.7% available.


### Measure
Tail the response logs of the Shakespeare service’s web servers and dump them into a logs-processing system.


### Analyze
Take a daily availability measurement as the percentage of 200 OK responses vs. the total number of requests.


### Interpret
After seven days, there’s a minimum of 99.7% availability on any given day.


<!-- .slide: data-background="slides/assets/shakespare.png"  --> 


### All good?

Note:
Well, we happily report to our boss, hey, our service on its worst day had 99.7% of availability. And you receive your boss praise!

On the next day, your boss comes to you furious, that users are complaining about not being able to search for their favorite book.


### Availability definition problems
You've made the classic mistake of basing your definition of availability on a measurement that does not match user expectations or business objectives.


### It's all about the business 

Note:
We should have picked a metric that defines what our business is trying to achieve.

As we sell books, querying for a book seems like a good candidate to measure on, because is on par with what feels to our users that our system is available.

We can define them, that queries taking up to 5 seconds are considered within the acceptable range.


## 1.3 SLO, SLA, and SLI


### SLO
The numerical target for system availability, defined by our business needs.

Note: 
SRE begins with the idea that a prerequisite to success is availability.

We should define what is the target system availability so we can spend resources accordingly, this is a business decision.

The more available we want a system to be, the more we will have diminishing returns in trying to do so. And we will always have a cost of opportunity involved in doing it.


### SLA
The agreement you make to those who depend on your service.

Note:
A promise to someone using your service that its availability SLO should meet a certain level over a certain period, and if it fails to do so then some kind of penalty will take place.


### SLI
Measurements of the real numbers of your performance.

Note:
This is essentially how we are doing and how we measure if we meet our SLA.


## 1.4 The components of good monitoring


### 1. Logging
Capturing all of the information necessary to describe the state of the microservice at any given time.


### 2. Key metrics
The key metrics are derived from our SLO definition and are constantly measured, forming our SLIs.

Note:
This usually takes place in health dashboards. Those indicate how our services are behaving. 

We can have insights from its past behavior easily by looking at charts and indicators.


### 3. Alerting
The detection of failures, as well as the detection of changes within key metrics that could lead to a failure.

Note:
Importantly, alerts should also be triggered whenever a key metric is not
seen. 

Thresholds for key metrics can be very difficult to set without historical data, this can be accomplished by either monitoring it closely after its release or by setting up stress tests.

All alerts need to be actionable. Non-actionable alerts are those that are triggered and then resolved (or ignored) by the developer(s) on call for the microservice because
they are not important, not relevant, do not signify that anything is wrong with the microservice, or alert on a problem that cannot be resolved by the developer(s).

Any alert that cannot be immediately acted on by the on-call developer(s) should be removed from the pool of alerts, reassigned to the relevant on-call rotation, or (if possible) changed so that it becomes actionable. Clear instructions are delivered with each alert.


### 4. On-Call Rotations
Scheduled rotating shifts for detecting, mitigating, and resolving any issue that arises before it causes an outage or impacts the business itself.
