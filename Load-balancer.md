## Introducing Load Balancer, So there's no more single point of failure.

The system was now stable.

But there was a critical limitation:

* Only **one EC2 instance**
* If it goes down → entire application goes down 


## So, objective was to-

* Distribute incoming traffic
* Improve availability
* Remove single point of failure

---

## And the solution I could possibly understand is Application Load Balancer (ALB)

ALB acts as:

* Entry point for users
* Traffic distributor
* Health checker

> Instead of users directly hitting EC2, they now go through ALB, in short, they go through a channel.

---

## Step 1: Create target group

1. Go to **EC2 → Target Groups**
2. Click **Create Target Group**

### Configuration:

* Target type: Instance
* Protocol: HTTP
* Port: 80

Register EC2 instance

---

## Step 2: Create load balancer

1. Go to **EC2 → Load Balancers**
2. Click **Create Load Balancer → Application load balancer**

### Configuration:

* Scheme: Internet-facing
* Listener: HTTP (Port 80)
* Availability zone: Select subnet

---

## Step 3: Attach target group

* Forward traffic to created target group

---

## Step 4: Configure security group

Allow:

* HTTP (80) from internet

---

## Step 5: Test the setup

* Copy ALB DNS name
* Open in browser

### Result:

* Application loads successfully via ALB

---

## Behavior Now

| Scenario      | Result                       |
| ------------- | ---------------------------- |
| EC2 healthy   | Traffic served normally      |
| EC2 unhealthy | ALB detects via health check |

---

Load Balancer improved availability, now acts as smart traffic controller, and enables scaling architecture

> So, earlier: User → EC2 and now: User → ALB → EC2 

---

## Now that traffic is managed…

But what will happen when traffic will suddenly increase? For that let's go on to adding an auto scaling group.
