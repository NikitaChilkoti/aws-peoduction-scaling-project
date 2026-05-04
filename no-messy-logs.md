## Centralized Logging

At this stage:

* Application is running via systemd
* Load Balancer is distributing traffic
* Auto Scaling is handling load

The system looks strong from the outside. But when something breaks, I scream, “Where are the logs?”

Currently:

* Logs exist only inside individual EC2 instances
* If instance terminates → logs are lost
* Debugging becomes difficult

---

## So, what's the objective this time?

* Centralize application logs
* Make debugging easier
* Retain logs even if instances are replaced

---

## Problem with the current setup

Right now:

* Flask app prints logs to terminal
* systemd manages the process
* Logs are not stored centrally

So if:

* Instance crashes
* Auto Scaling replaces instance

Logs disappear with it. And that’s not ideal.

---

## The solution? CloudWatch Logs

CloudWatch Logs allow:

* Central storage of logs
* Easy search and analysis
* Persistence beyond instance lifecycle

---

## Step 1: Install cloudWatch agent

Connect to EC2:

```
ssh -i your-key.pem ec2-user@<public-ip>
```

Install agent:

```
sudo yum install amazon-cloudwatch-agent -y
```

---

## Step 2: Create configuration file

```
sudo nano /opt/aws/amazon-cloudwatch-agent/bin/config.json
```

Add the json script:

```
{
  "logs": {
    "logs_collected": {
      "files": {
        "collect_list": [
          {
            "file_path": "/var/log/messages",
            "log_group_name": "ec2-system-logs",
            "log_stream_name": "{instance_id}"
          }
        ]
      }
    }
  }
}
```

---

## Step 3: Start cloudWatch agent

```
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl \
-a fetch-config \
-m ec2 \
-c file:/opt/aws/amazon-cloudwatch-agent/bin/config.json \
-s
```

---

## Step 4: Verify logs

1. Go to CloudWatch → Logs → Log Groups
2. Open `ec2-system-logs`

You should see logs streaming from your instance.

---

## Right now, logs are still basic. This will be more useful with improved application logging.

### Update Flask App

Modify `app.py`:

```
from flask import Flask
import logging

app = Flask(__name__)

logging.basicConfig(filename='/var/log/app.log', level=logging.INFO)

@app.route("/")
def home():
    app.logger.info("Home endpoint accessed")
    return "Hello from Auto Scaling"

app.run(host="0.0.0.0", port=80)
```

---

## Update Agent Config to Include App Logs

Edit config:

```
{
  "logs": {
    "logs_collected": {
      "files": {
        "collect_list": [
          {
            "file_path": "/var/log/messages",
            "log_group_name": "ec2-system-logs",
            "log_stream_name": "{instance_id}"
          },
          {
            "file_path": "/var/log/app.log",
            "log_group_name": "app-logs",
            "log_stream_name": "{instance_id}"
          }
        ]
      }
    }
  }
}
```

Restart agent:

```
sudo systemctl restart amazon-cloudwatch-agent
```

---

## Now, look at the impact

Earlier:

* Logs scattered across instances
* Lost during failures

Now:

* Centralized logs
* Easier debugging
* Better observability

---

## What’s Next?

The system is now:

* Scalable
* Recoverable
* Observable

But one improvement remains to visualize everything in one place. Going to do Monitoring Dashboard.
