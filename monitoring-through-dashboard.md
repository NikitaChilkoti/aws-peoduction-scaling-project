## Monitoring Dashboard

At this stage, the system has:

* Load Balancer distributing traffic
* Auto Scaling handling demand
* CloudWatch monitoring metrics
* Logs centralized for debugging

Everything is working well.

But there’s just one problem as of now, The information is scattered.

To understand system health, I still need to:

* Open multiple tabs
* Check metrics individually
* Piece things together mentally

This takes time. And that slows down decision-making, no?

---

## Now, the objective is to:

* Visualize key system metrics in one place
* Improve observability
* Enable faster troubleshooting

---

# Solution: CloudWatch dashboard

It will provide:

* Centralized view of metrics
* Real-time system visibility
* Quick understanding of system behavior

---

## Step 1: Create dashboard

1. Go to CloudWatch → Dashboards
2. Click **Create Dashboard**
3. Name it: `production-dashboard` or anything of your choice! I named this. Duhhh!

---

## Step 2: Add widgets

### 1. CPU Utilization (EC2)

* Type: Line graph
* Metric: EC2 → Per-Instance → CPUUtilization

This helps answer:

* Is the system under load?
* Are scaling policies working?

---

### 2. Network traffic

* Metrics:

  * NetworkIn
  * NetworkOut

This shows:

* Incoming and outgoing traffic patterns

---

### 3. Auto scaling group metrics

* GroupDesiredCapacity
* GroupInServiceInstances

This tells:

* How many instances are running
* Whether scaling is happening as expected

---

### 4. Load balancer metrics

* RequestCount
* TargetResponseTime

This helps you understand:

* Traffic volume
* Application performance

---

## Step 3: Arrange dashboard

Organize widgets so that:

* Top → Traffic and requests
* Middle → Instance performance
* Bottom → Scaling behavior

A good dashboard should answer:
“What is happening right now?” within a few seconds.

---

## Now, there is -

* A single view of system health
* Real-time monitoring
* Faster debugging capability

---

At this point, the system is:

* Reliable
* Scalable
* Observable

The final step is to make it repeatable and consistent. This sounds like loud and clear.

Because manual setup can be one time, but automation works every time.
Last is setting Infrastructure as Code.
