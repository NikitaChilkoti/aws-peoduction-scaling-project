## Auto Scaling

With the Application Load balancer in place:

* Traffic distribution was handled
* Single point of failure was reduced

But one problem still remained:

The system could not handle sudden traffic spikes.

If traffic increased, the same single instance had to handle everything. This would slow down the only EC2, and the complaints will be flooded.

---

## So, objective was to -

* Automatically handle increased traffic
* Maintain application availability
* Reduce manual intervention

---

# Part 1: Basic Auto Scaling

The idea is to maintain a fixed number of instances within a defined range.

### Example Configuration:

* Min: 1
* Desired: 1
* Max: 2

This ensures:

* If an instance fails → it is replaced
* But scaling is not based on actual load

---

## Step 1: Create Launch Template

1. Go to EC2 → Launch Templates → Create Template

### Configuration:

* AMI: Amazon Linux
* Instance Type: t2.micro
* Key Pair: Same as before

---

### Add user data

This ensures every new instance automatically installs and runs the application:

```
#!/bin/bash
sudo yum update -y
sudo yum install python3 -y
pip3 install flask

cat <<EOF > /home/ec2-user/app.py
from flask import Flask
app = Flask(__name__)

@app.route("/")
def home():
    return "Hello from Auto Scaling"

app.run(host="0.0.0.0", port=80)
EOF

sudo python3 /home/ec2-user/app.py
```

New instances should not need manual setup. If they do, something is wrong with the design.

---

## Step 2: Create Auto Scaling Group

1. Go to EC2 → Auto Scaling Groups → Create
2. Select Launch Template

### Configuration:

* Min: 1
* Desired: 1
* Max: 2
* Attach to ALB Target Group

---

## But this isn't enough, as scaling the number of instances based on actual system load is equally important.

---

## Step 3: Add Scaling Policy

1. Go to Auto Scaling Group
2. Select Automatic Scaling → Add Policy

### Configuration:

* Policy Type: Target Tracking
* Metric: CPU Utilization
* Target Value: 50%

---

## How it works

* If CPU > 50% → new instance is launched
* If CPU < 50% → extra instance is removed

The system now adjusts itself. And this is gold.

---

## Step 4: Simulate Load

Connect to instance:

```bash
ssh -i your-key.pem ec2-user@<public-ip>
```

Run stress command:

```bash
yes > /dev/null
```

This command is simple, effective, and slightly dangerous if forgotten. Yes, it is, I swear!

---

So, what was the expected behaviour? 

When CPU utilization increases, cloudWatch detects spike, and auto scaling triggers launch of new EC2 instance.

---

## Verify Scaling

Check:

* EC2 instances → a new instance appears
* Target Group → instance becomes healthy

---

## Stop the Test

```bash
killall yes
```

Always stop the test. ALWAYS. Otherwise, the system will keep scaling and, .... you don't want to hear the consequence. Do you?

---

## Result

| Scenario         | Behavior               |
| ---------------- | ---------------------- |
| High CPU         | New instance launched  |
| Low CPU          | Extra instance removed |
| Instance failure | Automatically replaced |

---

The system can now scale and recover automatically. But when something breaks, visibility still matters.

Because the next question is always:
Where do we actually see what went wrong? Looking at those messy logs ain't fun or even helpful, so going with centralized logging.
