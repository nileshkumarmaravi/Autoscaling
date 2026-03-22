<div align="center">

# ⚖️ AWS Auto Scaling Group — Complete Guide

### 🚀 Traffic ke Hisaab se Servers Auto Badhao Ghataao — Step by Step

<br>

![AWS](https://img.shields.io/badge/Amazon_AWS-FF9900?style=for-the-badge&logo=amazonaws&logoColor=white)
![AutoScaling](https://img.shields.io/badge/Auto_Scaling-EC2-FF9900?style=for-the-badge&logo=amazon-ec2&logoColor=white)
![LoadBalancer](https://img.shields.io/badge/Load_Balancer-ALB-527FFF?style=for-the-badge&logo=amazonaws&logoColor=white)
![CloudWatch](https://img.shields.io/badge/CloudWatch-Monitoring-FF4F8B?style=for-the-badge&logo=amazon-cloudwatch&logoColor=white)
![Ubuntu](https://img.shields.io/badge/Ubuntu-E95420?style=for-the-badge&logo=ubuntu&logoColor=white)

> 💡 **Auto Scaling = Traffic badhne pe servers automatically add ho jaate hain, kam hone pe hata dete hain — Zero downtime, Zero waste!**

</div>

---

## 📌 Table of Contents

| # | Topic |
|---|-------|
| 01 | [🤔 Auto Scaling Kya Hai?](#-auto-scaling-kya-hai) |
| 02 | [🗺️ Architecture Overview](#️-architecture-overview) |
| 03 | [📐 Scaling Types](#-scaling-types) |
| 04 | [🔧 Launch Template](#-launch-template) |
| 05 | [⚖️ Auto Scaling Group Banao](#️-auto-scaling-group-banao) |
| 06 | [📊 Scaling Policies](#-scaling-policies) |
| 07 | [🔔 CloudWatch Alarms](#-cloudwatch-alarms) |
| 08 | [⚡ Load Balancer Integration](#-load-balancer-integration) |
| 09 | [🔍 Monitoring & Testing](#-monitoring--testing) |
| 10 | [💰 Cost Optimization](#-cost-optimization) |
| 11 | [❌ Common Errors & Fixes](#-common-errors--fixes) |
| 12 | [📋 Cheat Sheet](#-cheat-sheet) |

---

## 🤔 Auto Scaling Kya Hai?

```
╔══════════════════════════════════════════════════════════════╗
║              AUTO SCALING SIMPLE SAMAJHO                    ║
╠══════════════════════════════════════════════════════════════╣
║                                                              ║
║   🏪 DUKAN KA EXAMPLE:                                      ║
║                                                              ║
║   Normal din    → 2 cashier kaafi hain                      ║
║   Sale ka din   → 10 cashier bulao automatically!           ║
║   Raat ko       → 1 cashier hi kaafi                        ║
║                                                              ║
║   ══════════════════════════════════════════════════════    ║
║                                                              ║
║   🌐 AWS AUTO SCALING:                                       ║
║                                                              ║
║   Normal traffic  → 2 EC2 instances                         ║
║   High traffic    → 10 EC2 instances auto add! ⚡           ║
║   Low traffic     → 1 EC2 instance pe wapas ✅              ║
║                                                              ║
║   ✅ No Downtime      ✅ No Manual Work                      ║
║   ✅ Cost Save        ✅ Always Available                    ║
║                                                              ║
╚══════════════════════════════════════════════════════════════╝
```

**Auto Scaling Kyun Zaroori Hai?**

| 🎯 Problem | ✅ Auto Scaling Solution |
|-----------|------------------------|
| 😰 Traffic spike → Server crash | Auto add instances |
| 💸 Idle servers → Paisa waste | Auto remove instances |
| 😴 Manual monitoring | CloudWatch auto detect |
| 🔧 Manual instance add | Automatic launch |
| ❌ Single point of failure | Multiple AZ me spread |

---

## 🗺️ Architecture Overview

```
╔═════════════════════════════════════════════════════════════════╗
║              AUTO SCALING FULL ARCHITECTURE                    ║
╠═════════════════════════════════════════════════════════════════╣
║                                                                 ║
║   👥 USERS (1000s of requests)                                  ║
║        │                                                        ║
║        ▼                                                        ║
║   ┌─────────────────────────────────────────────────────────┐  ║
║   │         ⚖️  APPLICATION LOAD BALANCER (ALB)             │  ║
║   │         Traffic ko evenly distribute karta hai          │  ║
║   └──────────┬──────────────┬──────────────┬───────────────┘  ║
║              │              │              │                    ║
║         ┌────▼───┐     ┌────▼───┐     ┌────▼───┐              ║
║         │ 🖥️ EC2 │     │ 🖥️ EC2 │     │ 🖥️ EC2 │              ║
║         │   #1   │     │   #2   │     │   #3   │              ║
║         └────────┘     └────────┘     └────────┘              ║
║              ↑              ↑              ↑                    ║
║         ┌────┴──────────────┴──────────────┴───────────────┐  ║
║         │          ⚖️  AUTO SCALING GROUP (ASG)             │  ║
║         │                                                   │  ║
║         │   Min: 1  |  Desired: 3  |  Max: 10              │  ║
║         └───────────────────────┬───────────────────────────┘  ║
║                                 │                               ║
║         ┌───────────────────────▼───────────────────────────┐  ║
║         │           📊 CLOUDWATCH ALARM                      │  ║
║         │   CPU > 70% → Scale OUT (add instance)            │  ║
║         │   CPU < 30% → Scale IN  (remove instance)         │  ║
║         └───────────────────────────────────────────────────┘  ║
╚═════════════════════════════════════════════════════════════════╝
```

---

## 📐 Scaling Types

```
╔══════════════════════════════════════════════════════════════════╗
║                    SCALING KE TYPES                             ║
╠══════════════════════════════════════════════════════════════════╣
║                                                                  ║
║  📈 SCALE OUT (Horizontal - Badhana)                             ║
║  ┌──────────────────────────────────────────────────────────┐   ║
║  │  🖥️          →      🖥️  🖥️  🖥️                          │   ║
║  │  1 Server           3 Servers added                      │   ║
║  │  Traffic badhne pe zyada servers add karo                │   ║
║  └──────────────────────────────────────────────────────────┘   ║
║                                                                  ║
║  📉 SCALE IN (Horizontal - Ghataana)                             ║
║  ┌──────────────────────────────────────────────────────────┐   ║
║  │  🖥️  🖥️  🖥️  →      🖥️                                  │   ║
║  │  3 Servers          1 Server (idle pe)                   │   ║
║  │  Traffic kam hone pe extra servers hata do               │   ║
║  └──────────────────────────────────────────────────────────┘   ║
║                                                                  ║
║  ⬆️  SCALE UP (Vertical - Upgrade karna)                        ║
║  ┌──────────────────────────────────────────────────────────┐   ║
║  │  t2.micro → t2.large (zyada RAM/CPU)                     │   ║
║  │  ⚠️ Downtime hota hai | ASG me use nahi hota             │   ║
║  └──────────────────────────────────────────────────────────┘   ║
║                                                                  ║
║  ⬇️  SCALE DOWN (Vertical - Downgrade karna)                    ║
║  ┌──────────────────────────────────────────────────────────┐   ║
║  │  t2.large → t2.micro (kam RAM/CPU)                       │   ║
║  │  ⚠️ Cost bachane ke liye, par downtime                   │   ║
║  └──────────────────────────────────────────────────────────┘   ║
║                                                                  ║
║   ASG SIRF HORIZONTAL SCALING KARTA HAI ✅                      ║
╚══════════════════════════════════════════════════════════════════╝
```

---

## 🔧 Launch Template

```
╔══════════════════════════════════════════════════════════════╗
║            LAUNCH TEMPLATE KYA HOTA HAI?                    ║
╠══════════════════════════════════════════════════════════════╣
║                                                              ║
║   Launch Template = EC2 ka Blueprint                         ║
║                                                              ║
║   Isme define karte hain:                                    ║
║   ┌──────────────────────────────────────────────────────┐  ║
║   │  🖼️  AMI (OS Image)    → Ubuntu 22.04               │  ║
║   │  💻  Instance Type     → t2.micro                   │  ║
║   │  🔑  Key Pair          → MyKey.pem                  │  ║
║   │  🔒  Security Group    → WebSG (port 80, 22)        │  ║
║   │  💾  Storage           → 8 GB gp3                   │  ║
║   │  📝  User Data Script  → Nginx auto install         │  ║
║   └──────────────────────────────────────────────────────┘  ║
║                                                              ║
║   ASG is template se naye instances launch karta hai!        ║
╚══════════════════════════════════════════════════════════════╝
```

```bash
# 🔧 Step 1: Security Group banao
aws ec2 create-security-group \
    --group-name ASG-WebSG \
    --description "Auto Scaling Web Server SG" \
    --vpc-id vpc-xxxxxxxx

# HTTP allow
aws ec2 authorize-security-group-ingress \
    --group-id sg-xxxxxxxx \
    --protocol tcp --port 80 \
    --cidr 0.0.0.0/0

# SSH allow (sirf apna IP)
aws ec2 authorize-security-group-ingress \
    --group-id sg-xxxxxxxx \
    --protocol tcp --port 22 \
    --cidr $(curl -s ifconfig.me)/32


# 🔧 Step 2: User Data Script banao
# (Har naye instance pe yeh auto run hoga)
cat > userdata.sh << 'SCRIPT'
#!/bin/bash
apt update -y
apt install nginx -y
systemctl start nginx
systemctl enable nginx

# Instance ki info show karo webpage pe
INSTANCE_ID=$(curl -s http://169.254.169.254/latest/meta-data/instance-id)
AZ=$(curl -s http://169.254.169.254/latest/meta-data/placement/availability-zone)

cat > /var/www/html/index.html << EOF
<!DOCTYPE html>
<html>
<head>
  <title>Auto Scaled Server</title>
  <style>
    body { font-family: sans-serif; text-align: center;
           background: linear-gradient(135deg,#1a1a2e,#16213e);
           color: white; padding: 50px; min-height: 100vh; }
    .card { background: rgba(255,255,255,0.1); padding: 30px;
            border-radius: 15px; display: inline-block; }
    h1 { color: #FF9900; }
  </style>
</head>
<body>
  <div class="card">
    <h1>☁️ AWS Auto Scaled Server!</h1>
    <p>🖥️ Instance ID: <b>$INSTANCE_ID</b></p>
    <p>📍 Availability Zone: <b>$AZ</b></p>
    <p>✅ Server chal raha hai!</p>
  </div>
</body>
</html>
EOF
SCRIPT


# 🔧 Step 3: Launch Template banao
aws ec2 create-launch-template \
    --launch-template-name "WebServer-Template" \
    --version-description "v1 - Nginx Web Server" \
    --launch-template-data "{
        \"ImageId\": \"ami-0f58b397bc5c1f2e8\",
        \"InstanceType\": \"t2.micro\",
        \"KeyName\": \"MyKey\",
        \"SecurityGroupIds\": [\"sg-xxxxxxxx\"],
        \"UserData\": \"$(base64 -w 0 userdata.sh)\",
        \"BlockDeviceMappings\": [{
            \"DeviceName\": \"/dev/sda1\",
            \"Ebs\": {
                \"VolumeSize\": 8,
                \"VolumeType\": \"gp3\"
            }
        }],
        \"TagSpecifications\": [{
            \"ResourceType\": \"instance\",
            \"Tags\": [{\"Key\": \"Name\", \"Value\": \"ASG-WebServer\"}]
        }]
    }"

# ✅ Template check karo
aws ec2 describe-launch-templates \
    --launch-template-names "WebServer-Template" \
    --output table
```

---

## ⚖️ Auto Scaling Group Banao

```
╔══════════════════════════════════════════════════════════════╗
║              ASG KEY SETTINGS SAMJHO                        ║
╠══════════════════════════════════════════════════════════════╣
║                                                              ║
║   MIN SIZE    = Hamesha itne servers toh honge hi           ║
║                (Even at midnight — minimum guarantee)        ║
║                                                              ║
║   DESIRED     = Normal time me yahan instances raho         ║
║                (ASG yahi maintain karta hai by default)      ║
║                                                              ║
║   MAX SIZE    = Itne se zyada kabhi nahi badhega             ║
║                (Cost control ke liye)                        ║
║                                                              ║
║   EXAMPLE:                                                   ║
║   ┌──────────┬────────────┬───────────────────────────────┐ ║
║   │  Min: 1  │ Desired: 2 │  Max: 5                       │ ║
║   │          │            │                               │ ║
║   │  Raat ko │  Normal    │  High traffic pe              │ ║
║   │  1 server│  2 servers │  5 tak badh sakta             │ ║
║   └──────────┴────────────┴───────────────────────────────┘ ║
╚══════════════════════════════════════════════════════════════╝
```

```bash
# ⚖️ Auto Scaling Group banao
aws autoscaling create-auto-scaling-group \
    --auto-scaling-group-name "WebServer-ASG" \
    --launch-template "LaunchTemplateName=WebServer-Template,Version=1" \
    --min-size 1 \
    --max-size 5 \
    --desired-capacity 2 \
    --vpc-zone-identifier "subnet-aaaa1111,subnet-bbbb2222" \
    --availability-zones "ap-south-1a" "ap-south-1b" \
    --health-check-type EC2 \
    --health-check-grace-period 300 \
    --tags "Key=Name,Value=ASG-Instance,PropagateAtLaunch=true" \
           "Key=Environment,Value=Production,PropagateAtLaunch=true"

# ✅ ASG check karo
aws autoscaling describe-auto-scaling-groups \
    --auto-scaling-group-names "WebServer-ASG" \
    --output table

# 📊 ASG activity dekho
aws autoscaling describe-scaling-activities \
    --auto-scaling-group-name "WebServer-ASG" \
    --output table

# 🔄 Desired capacity manually change karo
aws autoscaling set-desired-capacity \
    --auto-scaling-group-name "WebServer-ASG" \
    --desired-capacity 3

# ❌ ASG delete karo
aws autoscaling delete-auto-scaling-group \
    --auto-scaling-group-name "WebServer-ASG" \
    --force-delete
```

---

## 📊 Scaling Policies

```
╔══════════════════════════════════════════════════════════════════╗
║                  SCALING POLICY TYPES                           ║
╠══════════════════════════════════════════════════════════════════╣
║                                                                  ║
║  🎯 TARGET TRACKING (Recommended — Sabse Easy)                   ║
║  ┌──────────────────────────────────────────────────────────┐   ║
║  │  "CPU ko 50% pe rakho" — AWS khud calculate karta hai    │   ║
║  │  CPU badhega → AWS servers badhayega                     │   ║
║  │  CPU ghatega → AWS servers hatayega                      │   ║
║  └──────────────────────────────────────────────────────────┘   ║
║                                                                  ║
║  📈 STEP SCALING (Thresholds pe based)                           ║
║  ┌──────────────────────────────────────────────────────────┐   ║
║  │  CPU 70-80%  → 1 instance add karo                       │   ║
║  │  CPU 80-90%  → 2 instances add karo                      │   ║
║  │  CPU > 90%   → 3 instances add karo                      │   ║
║  └──────────────────────────────────────────────────────────┘   ║
║                                                                  ║
║  📅 SCHEDULED SCALING (Time-based)                               ║
║  ┌──────────────────────────────────────────────────────────┐   ║
║  │  9 AM  → 5 instances (office time)                       │   ║
║  │  6 PM  → 2 instances                                     │   ║
║  │  11 PM → 1 instance (raat ko)                            │   ║
║  └──────────────────────────────────────────────────────────┘   ║
║                                                                  ║
║  🔮 PREDICTIVE SCALING (AI-based)                                ║
║  ┌──────────────────────────────────────────────────────────┐   ║
║  │  AWS ML se future traffic predict karta hai              │   ║
║  │  Pehle se hi servers ready karta hai! 🤖                 │   ║
║  └──────────────────────────────────────────────────────────┘   ║
╚══════════════════════════════════════════════════════════════════╝
```

```bash
# 🎯 TARGET TRACKING POLICY (CPU 50% pe rakho)
aws autoscaling put-scaling-policy \
    --auto-scaling-group-name "WebServer-ASG" \
    --policy-name "CPU-Target-50-Policy" \
    --policy-type TargetTrackingScaling \
    --target-tracking-configuration '{
        "PredefinedMetricSpecification": {
            "PredefinedMetricType": "ASGAverageCPUUtilization"
        },
        "TargetValue": 50.0,
        "ScaleOutCooldown": 300,
        "ScaleInCooldown": 300
    }'


# 📈 STEP SCALING POLICY (Scale Out)
aws autoscaling put-scaling-policy \
    --auto-scaling-group-name "WebServer-ASG" \
    --policy-name "ScaleOut-Policy" \
    --policy-type StepScaling \
    --adjustment-type ChangeInCapacity \
    --step-adjustments '[
        {
            "MetricIntervalLowerBound": 0,
            "MetricIntervalUpperBound": 20,
            "ScalingAdjustment": 1
        },
        {
            "MetricIntervalLowerBound": 20,
            "ScalingAdjustment": 2
        }
    ]'


# 📅 SCHEDULED SCALING (Office hours)
# Subah 9 baje 5 instances
aws autoscaling put-scheduled-update-group-action \
    --auto-scaling-group-name "WebServer-ASG" \
    --scheduled-action-name "Morning-ScaleOut" \
    --recurrence "0 3 * * MON-FRI" \
    --min-size 3 \
    --desired-capacity 5 \
    --max-size 10

# Raat 11 baje 1 instance
aws autoscaling put-scheduled-update-group-action \
    --auto-scaling-group-name "WebServer-ASG" \
    --scheduled-action-name "Night-ScaleIn" \
    --recurrence "0 17 * * MON-FRI" \
    --min-size 1 \
    --desired-capacity 1 \
    --max-size 10
```

---

## 🔔 CloudWatch Alarms

```
╔══════════════════════════════════════════════════════════════╗
║           CLOUDWATCH ALARM → AUTO SCALING FLOW              ║
╠══════════════════════════════════════════════════════════════╣
║                                                              ║
║   EC2 Instances                                             ║
║       │  Metrics bhejte hain                                ║
║       ▼                                                      ║
║   📊 CloudWatch                                             ║
║       │  CPU, Memory, Network monitor karta hai             ║
║       ▼                                                      ║
║   🔔 ALARM                                                  ║
║       │  Threshold cross hua?                               ║
║       │                                                     ║
║   ┌───▼────────────┐     ┌─────────────────────────────┐    ║
║   │ CPU > 70%      │────►│ Scale OUT → 1 instance add  │    ║
║   │ (HIGH ALARM)   │     │ Email notification bhejo    │    ║
║   └────────────────┘     └─────────────────────────────┘    ║
║                                                              ║
║   ┌────────────────┐     ┌─────────────────────────────┐    ║
║   │ CPU < 30%      │────►│ Scale IN → 1 instance hata  │    ║
║   │ (LOW ALARM)    │     │ Cost bacha!                  │    ║
║   └────────────────┘     └─────────────────────────────┘    ║
╚══════════════════════════════════════════════════════════════╝
```

```bash
# 🔔 Scale OUT Alarm (CPU > 70%)
aws cloudwatch put-metric-alarm \
    --alarm-name "ASG-High-CPU" \
    --alarm-description "CPU 70% se upar — scale out karo" \
    --metric-name CPUUtilization \
    --namespace AWS/EC2 \
    --statistic Average \
    --period 300 \
    --threshold 70 \
    --comparison-operator GreaterThanThreshold \
    --evaluation-periods 2 \
    --dimensions "Name=AutoScalingGroupName,Value=WebServer-ASG" \
    --alarm-actions arn:aws:autoscaling:ap-south-1:ACCOUNT:scalingPolicy:xxx

# 🔔 Scale IN Alarm (CPU < 30%)
aws cloudwatch put-metric-alarm \
    --alarm-name "ASG-Low-CPU" \
    --alarm-description "CPU 30% se neeche — scale in karo" \
    --metric-name CPUUtilization \
    --namespace AWS/EC2 \
    --statistic Average \
    --period 300 \
    --threshold 30 \
    --comparison-operator LessThanThreshold \
    --evaluation-periods 2 \
    --dimensions "Name=AutoScalingGroupName,Value=WebServer-ASG" \
    --alarm-actions arn:aws:autoscaling:ap-south-1:ACCOUNT:scalingPolicy:yyy

# 📊 Alarms list dekho
aws cloudwatch describe-alarms \
    --alarm-names "ASG-High-CPU" "ASG-Low-CPU" \
    --output table
```

---

## ⚡ Load Balancer Integration

```
╔══════════════════════════════════════════════════════════════════╗
║              ALB + ASG INTEGRATION                              ║
╠══════════════════════════════════════════════════════════════════╣
║                                                                  ║
║   BINA ALB:                    ALB KE SAATH:                    ║
║                                                                  ║
║   User → Direct EC2 IP         User → ALB → EC2 #1             ║
║   EC2 band → Error! ❌                   → EC2 #2             ║
║                                           → EC2 #3             ║
║                                ✅ High Availability             ║
║                                ✅ Health Check                  ║
║                                ✅ Even distribution             ║
╚══════════════════════════════════════════════════════════════════╝
```

```bash
# ⚡ Step 1: Application Load Balancer banao
aws elbv2 create-load-balancer \
    --name "WebServer-ALB" \
    --subnets subnet-aaaa1111 subnet-bbbb2222 \
    --security-groups sg-xxxxxxxx \
    --scheme internet-facing \
    --type application

# ⚡ Step 2: Target Group banao
aws elbv2 create-target-group \
    --name "WebServer-TG" \
    --protocol HTTP \
    --port 80 \
    --vpc-id vpc-xxxxxxxx \
    --health-check-path "/" \
    --health-check-interval-seconds 30 \
    --healthy-threshold-count 2 \
    --unhealthy-threshold-count 3

# ⚡ Step 3: Listener banao
aws elbv2 create-listener \
    --load-balancer-arn arn:aws:elasticloadbalancing:... \
    --protocol HTTP \
    --port 80 \
    --default-actions "Type=forward,TargetGroupArn=arn:aws:..."

# ⚡ Step 4: ASG ko Target Group se attach karo
aws autoscaling attach-load-balancer-target-groups \
    --auto-scaling-group-name "WebServer-ASG" \
    --target-group-arns arn:aws:elasticloadbalancing:...

# ✅ ALB DNS name dekho (yahan se website access hogi)
aws elbv2 describe-load-balancers \
    --names "WebServer-ALB" \
    --query 'LoadBalancers[*].DNSName' \
    --output text
```

---

## 🔍 Monitoring & Testing

```bash
# ════════════════════════════════════════════
# 📊 ASG STATUS DEKHO
# ════════════════════════════════════════════

# ASG details
aws autoscaling describe-auto-scaling-groups \
    --auto-scaling-group-names "WebServer-ASG" \
    --query 'AutoScalingGroups[0].{
        Name:AutoScalingGroupName,
        Min:MinSize,
        Desired:DesiredCapacity,
        Max:MaxSize,
        Instances:length(Instances)
    }'

# Running instances dekho
aws autoscaling describe-auto-scaling-instances \
    --output table

# Scaling activities (history)
aws autoscaling describe-scaling-activities \
    --auto-scaling-group-name "WebServer-ASG" \
    --max-items 10 \
    --output table


# ════════════════════════════════════════════
# 🧪 STRESS TEST (Scaling test karo)
# ════════════════════════════════════════════

# EC2 pe SSH karo aur stress test chalao:
ssh -i MyKey.pem ubuntu@<EC2-IP>

# Stress tool install karo
sudo apt install stress -y

# CPU 100% pe le jao (scaling trigger hogi)
stress --cpu 4 --timeout 300

# Dusri terminal me dekho:
watch -n 5 aws autoscaling describe-auto-scaling-instances \
    --output table
# Naye instances add hote dikhenge! 🎉


# ════════════════════════════════════════════
# 📈 METRICS DEKHO
# ════════════════════════════════════════════

# CPU metrics (last 1 hour)
aws cloudwatch get-metric-statistics \
    --namespace AWS/EC2 \
    --metric-name CPUUtilization \
    --dimensions "Name=AutoScalingGroupName,Value=WebServer-ASG" \
    --start-time $(date -u -d '1 hour ago' +%Y-%m-%dT%H:%M:%S) \
    --end-time $(date -u +%Y-%m-%dT%H:%M:%S) \
    --period 300 \
    --statistics Average \
    --output table
```

---

## 💰 Cost Optimization

```
╔══════════════════════════════════════════════════════════════════╗
║               COST SAVE KARNE KE TIPS                           ║
╠══════════════════════════════════════════════════════════════════╣
║                                                                  ║
║  💡 TIP 1: SPOT INSTANCES USE KARO                               ║
║  ┌──────────────────────────────────────────────────────────┐   ║
║  │  On-Demand : $0.012/hr (t2.micro)                        │   ║
║  │  Spot      : $0.003/hr (75% discount!) 🎉               │   ║
║  │  Mix use karo: 20% On-Demand + 80% Spot                  │   ║
║  └──────────────────────────────────────────────────────────┘   ║
║                                                                  ║
║  💡 TIP 2: COOLDOWN PERIOD SET KARO                              ║
║  ┌──────────────────────────────────────────────────────────┐   ║
║  │  Cooldown = Scaling ke baad wait time                    │   ║
║  │  300 seconds (5 min) recommended                         │   ║
║  │  Jaldi jaldi scale mat karo — cost badhti hai!           │   ║
║  └──────────────────────────────────────────────────────────┘   ║
║                                                                  ║
║  💡 TIP 3: RIGHT INSTANCE TYPE                                   ║
║  ┌──────────────────────────────────────────────────────────┐   ║
║  │  Zyada bada instance = Zyada cost                        │   ║
║  │  CloudWatch se actual usage dekho                        │   ║
║  │  t2.micro → t2.small decide karo                         │   ║
║  └──────────────────────────────────────────────────────────┘   ║
║                                                                  ║
║  💡 TIP 4: SCHEDULED SCALING (Raat ko scale down)                ║
║  ┌──────────────────────────────────────────────────────────┐   ║
║  │  Raat 11 PM → Min: 1, Desired: 1 (sirf ek server)       │   ║
║  │  Subah 9 AM → Min: 2, Desired: 3 (normal capacity)      │   ║
║  │  Weekend   → Desired: 1 (agar traffic kam ho)            │   ║
║  └──────────────────────────────────────────────────────────┘   ║
╚══════════════════════════════════════════════════════════════════╝
```

```bash
# 💰 Spot + On-Demand Mix ASG banao
aws autoscaling create-auto-scaling-group \
    --auto-scaling-group-name "WebServer-ASG-Mixed" \
    --mixed-instances-policy '{
        "LaunchTemplate": {
            "LaunchTemplateSpecification": {
                "LaunchTemplateName": "WebServer-Template",
                "Version": "1"
            },
            "Overrides": [
                {"InstanceType": "t2.micro"},
                {"InstanceType": "t3.micro"},
                {"InstanceType": "t2.small"}
            ]
        },
        "InstancesDistribution": {
            "OnDemandBaseCapacity": 1,
            "OnDemandPercentageAboveBaseCapacity": 20,
            "SpotAllocationStrategy": "lowest-price"
        }
    }' \
    --min-size 1 \
    --max-size 10 \
    --desired-capacity 2 \
    --vpc-zone-identifier "subnet-aaaa1111,subnet-bbbb2222"
```

---

## ❌ Common Errors & Fixes

```
╔══════════════════════════════════════════════════════════════════╗
║              COMMON ASG ERRORS & SOLUTIONS                      ║
╠══════════════════════════════════════╦═══════════════════════════╣
║  ❌ Error                            ║  ✅ Fix                   ║
╠══════════════════════════════════════╬═══════════════════════════╣
║  Instance launch fail ho raha        ║  AMI ID / Key Pair check  ║
╠══════════════════════════════════════╬═══════════════════════════╣
║  Health check fail                   ║  Port 80 security group   ║
║                                      ║  me open hai?             ║
╠══════════════════════════════════════╬═══════════════════════════╣
║  Scaling nahi ho raha                ║  CloudWatch alarm check   ║
║                                      ║  karo, policy dekho       ║
╠══════════════════════════════════════╬═══════════════════════════╣
║  Max size pe already                 ║  Max size badhao ya       ║
║                                      ║  traffic optimize karo    ║
╠══════════════════════════════════════╬═══════════════════════════╣
║  Instance terminate ho raha bar bar  ║  Grace period badhao      ║
║                                      ║  (health-check-grace-     ║
║                                      ║   period 300+)            ║
╠══════════════════════════════════════╬═══════════════════════════╣
║  ALB me instances nahi aa rahe       ║  Target Group attach karo ║
╠══════════════════════════════════════╬═══════════════════════════╣
║  Bahut fast scale ho raha            ║  Cooldown period badhao   ║
╚══════════════════════════════════════╩═══════════════════════════╝
```

---

## 📋 Cheat Sheet

```
╔════════════════════════════════════════════════════════════════════╗
║              AUTO SCALING GROUP QUICK CHEAT SHEET                ║
╠══════════════════════════════╦═════════════════════════════════════╣
║  🔧 LAUNCH TEMPLATE          ║                                     ║
║  Banao                       ║  aws ec2 create-launch-template     ║
║  List karo                   ║  aws ec2 describe-launch-templates  ║
║  Delete karo                 ║  aws ec2 delete-launch-template     ║
╠══════════════════════════════╬═════════════════════════════════════╣
║  ⚖️  AUTO SCALING GROUP       ║                                     ║
║  Banao                       ║  aws autoscaling create-auto-...    ║
║  List karo                   ║  aws autoscaling describe-auto-...  ║
║  Update karo                 ║  aws autoscaling update-auto-...    ║
║  Delete karo                 ║  aws autoscaling delete-auto-...    ║
║  Desired capacity set        ║  aws autoscaling set-desired-cap... ║
╠══════════════════════════════╬═════════════════════════════════════╣
║  📊 SCALING POLICIES         ║                                     ║
║  Policy add karo             ║  aws autoscaling put-scaling-policy ║
║  Policies list               ║  aws autoscaling describe-policies  ║
║  Policy delete               ║  aws autoscaling delete-policy      ║
╠══════════════════════════════╬═════════════════════════════════════╣
║  📅 SCHEDULED SCALING        ║                                     ║
║  Schedule add karo           ║  aws autoscaling put-scheduled-...  ║
║  Schedules list              ║  aws autoscaling describe-sched...  ║
╠══════════════════════════════╬═════════════════════════════════════╣
║  🔍 MONITORING               ║                                     ║
║  Instances dekho             ║  aws autoscaling describe-auto-...  ║
║  Activities dekho            ║  aws autoscaling describe-scaling.. ║
║  Alarms dekho                ║  aws cloudwatch describe-alarms     ║
╠══════════════════════════════╬═════════════════════════════════════╣
║  💡 KEY CONCEPTS             ║                                     ║
║  Min Size                    ║  Minimum servers (hamesha on)       ║
║  Desired Capacity            ║  Normal time me servers             ║
║  Max Size                    ║  Maximum servers (cost limit)       ║
║  Cooldown                    ║  Scaling ke baad wait (300s)        ║
║  Health Check                ║  Instance alive hai ya nahi         ║
║  Scale Out                   ║  Servers add karo                   ║
║  Scale In                    ║  Servers hataao                     ║
╚══════════════════════════════╩═════════════════════════════════════╝
``` 


