# Master DevOps Interview Guide for Full Stack Engineers: AWS, Terraform, Docker, Kubernetes, CI/CD, Linux

**For:** Goram Siva Prasad | **Role:** Full Stack Engineer (6 months to 1 year experience) with basic DevOps | **Round:** Technical (theory + hands-on code)

> The director said the role needs "some fundamental DevOps knowledge like Terraform and AWS". Expect the DevOps part to be about 20% of the interview: AWS basics, Terraform, Docker, a simple CI/CD pipeline, Linux and deployment of a full stack app. This guide covers those in depth and the rest (Kubernetes, Ansible, monitoring, security) at fundamentals level. Type every code sample yourself, and run what you can on an AWS free tier account or locally.

---

## Table of Contents

1. [How to answer in the interview](#1-how-to-answer-in-the-interview)
2. [Resume based questions](#2-resume-based-questions)
3. [DevOps fundamentals](#3-devops-fundamentals)
4. [Linux and shell](#4-linux-and-shell)
5. [Networking basics](#5-networking-basics)
6. [Git and branching](#6-git-and-branching)
7. [AWS core services](#7-aws-core-services)
8. [AWS architecture for a full stack app](#8-aws-architecture-for-a-full-stack-app)
9. [Terraform concepts](#9-terraform-concepts)
10. [Terraform code](#10-terraform-code)
11. [Ansible](#11-ansible)
12. [Docker concepts](#12-docker-concepts)
13. [Docker code](#13-docker-code)
14. [Kubernetes fundamentals](#14-kubernetes-fundamentals)
15. [CI/CD: Jenkins, GitHub Actions, CodeDeploy, Maven, SonarQube](#15-cicd-jenkins-github-actions-codedeploy-maven-sonarqube)
16. [Nginx and web servers](#16-nginx-and-web-servers)
17. [Monitoring and logging](#17-monitoring-and-logging)
18. [Security in DevOps (DevSecOps)](#18-security-in-devops-devsecops)
19. [Scripting: Bash and Python](#19-scripting-bash-and-python)
20. [Scenario and troubleshooting questions](#20-scenario-and-troubleshooting-questions)
21. [Step-by-step deployments](#21-step-by-step-deployments-what-interviewers-love-to-ask-walk-me-through-it)
22. [More Terraform: real-world pieces](#22-more-terraform-real-world-pieces)
23. [More AWS: reliability, networking and security extras](#23-more-aws-reliability-networking-and-security-extras)
24. [More Linux and networking](#24-more-linux-and-networking)
25. [More Docker, Kubernetes and CI/CD](#25-more-docker-kubernetes-and-cicd)
26. [Observability and reliability extras](#26-observability-and-reliability-extras)
27. [Hands-on practice plan and rapid-fire questions](#27-hands-on-practice-plan-and-rapid-fire-questions)
28. [Command cheat sheet](#28-command-cheat-sheet)
29. [Interview day strategy](#29-interview-day-strategy)
30. [Last day revision checklist](#30-last-day-revision-checklist)

---

## 1. How to answer in the interview

Use this pattern for theory questions:
1. **Definition** in one line.
2. **Why we use it.**
3. **A small example or what you did with it.**
4. **A pitfall or comparison** (shows depth).

**Example, "What is Terraform?"**
"Terraform is an Infrastructure as Code tool. I describe the servers, networks and databases I want in `.tf` files, and Terraform creates, changes or deletes them to match. It keeps a state file to know what already exists, and `terraform plan` shows the changes before `apply`. I used it to create a VPC, subnets, security groups and an RDS database, so the environment can be recreated the same way every time instead of clicking in the console."

**For hands-on tasks:**
- Ask what the requirement is (region, environment, size, public or private).
- Write the smallest working version first, then add variables, tags and security.
- Name resources clearly, use variables instead of hard-coded values, and never put secrets in code.
- Say how you would verify it (`terraform plan`, `docker ps`, `curl` the health endpoint).

**Be honest about depth.** If a part of your DevOps experience came from practice or lab projects rather than production, say so plainly: "I built this in a practice environment." That is a fine answer for a 0 to 1 year role. Overstating is risky because the interviewers will ask a follow-up.

---

## 2. Resume based questions

Prepare each answer with **Problem, What I did, Tools, Result.** Adjust the details to match what you really did.

### Q1. Explain the AWS infrastructure you set up with Terraform (VPC and RDS).
**Say it like this (adapt):**
"We needed a repeatable AWS environment for the application. I wrote Terraform to create a VPC with public and private subnets, route tables and an internet gateway, security groups, EC2 instances for the app, and an RDS database in private subnets so it was not exposed to the internet. The state was stored remotely so the team shared it. Ansible handled configuration of the servers after they were created."

**Follow-up questions to prepare:**
- **Why public and private subnets?** Public subnets hold things that must face the internet (load balancer, bastion, NAT gateway). Private subnets hold the app servers and database, which are reachable only from inside the VPC.
- **How did the app reach RDS?** The RDS security group allowed port 5432 (or 3306) only from the application's security group, not from `0.0.0.0/0`.
- **Where did you keep the Terraform state?** Remote backend in S3 with encryption and locking, so two people cannot apply at the same time and the state is not lost.
- **How did you handle the database password?** A sensitive variable or a secret manager (AWS Secrets Manager or SSM Parameter Store), never in Git.
- **Terraform vs Ansible?** Terraform provisions infrastructure (creates servers and networks). Ansible configures software on servers (install packages, copy config, start services).
- **What happens if someone changes a resource manually in the console?** That is drift. `terraform plan` shows the difference, and `apply` brings it back to the code (or you update the code to match).
- **How do you test changes safely?** `terraform fmt`, `validate`, `plan`, review the plan, apply to a dev environment first, then production.

### Q2. How did you deploy the healthcare app (EC2, ALB, Nginx, PM2)?
"The Node.js app ran on an EC2 instance, managed by PM2 so it restarts if it crashes and uses all CPU cores. Nginx was a reverse proxy in front of it (handling gzip and forwarding to the Node port). An Application Load Balancer sat in front for health checks and traffic distribution. Payment-flow uptime stayed reliable over 5 months."
Likely follow-ups: why a reverse proxy, what the ALB health check path was, how you deployed a new version (pull code, install, restart PM2, or a pipeline), how you handled HTTPS (ACM certificate on the ALB), what you did when a server crashed (PM2 restart, ALB removing the unhealthy target), and how you would add a second instance (Auto Scaling group and a launch template).

### Q3. Explain the CI/CD pipeline you built with Jenkins.
"On every push to the main branch, Jenkins checked out the code, installed dependencies, ran the tests, built a Docker image, pushed it to Amazon ECR, and deployed the new version. I also scanned code quality (SonarQube) and used AWS CodeDeploy for deployments to EC2 (use only the parts you did). Prometheus and Grafana monitored the services after deployment."
Follow-ups: what is in your Jenkinsfile, how credentials were stored (Jenkins credentials store, not in the script), what happened when tests failed (pipeline stops, no deploy), how you rolled back (redeploy the previous image tag), and how you avoided downtime.

### Q4. How did you containerize the applications (Docker, Kubernetes)?
"I wrote Dockerfiles for the frontend and backend, used multi-stage builds to keep images small, and ran them with docker-compose locally. I know the Kubernetes fundamentals: pods, deployments, services, config maps and secrets, and how rolling updates and probes work." Be precise about the level: "fundamentals" is the honest word on your resume.

### Q5. How did you monitor the applications (Prometheus and Grafana)?
"Prometheus scraped metrics from the applications and servers (node exporter), and Grafana showed dashboards: request rate, error rate, latency, CPU and memory. I set alerts for high error rate and resource usage." Be ready to name a few PromQL queries (section 17).

### Q6. What was the hardest DevOps problem you solved?
Pick a real one: a failed deployment, a security group blocking traffic, an app that could not connect to RDS, a disk that filled up, an ALB health check failing, a Terraform state conflict. Explain the symptom, how you investigated (logs, `curl`, security groups, `terraform plan`), the fix and what you changed to prevent it.

---

## 3. DevOps fundamentals

### Q1. What is DevOps?
A culture and set of practices that bring development and operations together, so software is built, tested, released and monitored quickly and reliably. Key ideas: automation, collaboration, continuous feedback, and treating infrastructure as code.

### Q2. What is the DevOps lifecycle?
Plan, code, build, test, release, deploy, operate, monitor, then feedback goes back to plan. Typical tools: Git (code), Jenkins or GitHub Actions (CI/CD), Docker (packaging), Terraform and Ansible (infrastructure), Kubernetes (orchestration), Prometheus and Grafana (monitoring).

### Q3. CI vs continuous delivery vs continuous deployment?
- **Continuous Integration:** developers merge small changes often, and each change is built and tested automatically.
- **Continuous Delivery:** every passing change is ready to release, with a manual approval for production.
- **Continuous Deployment:** every passing change goes to production automatically.

### Q4. What is Infrastructure as Code (IaC)? Benefits?
Defining infrastructure in code files instead of manual clicks. Benefits: repeatable environments, version control and code review for infrastructure, faster recovery, fewer manual mistakes, and documentation by code. Tools: Terraform, CloudFormation, Pulumi, Ansible.

### Q5. Declarative vs imperative?
Declarative: you describe the **desired end state** and the tool works out the steps (Terraform, Kubernetes YAML, CloudFormation). Imperative: you write the **steps** (bash scripts, `aws` CLI commands).

### Q6. What is immutable infrastructure? Pets vs cattle?
Immutable: you never patch servers in place, you replace them with new ones built from a new image. Pets are unique servers you nurse, cattle are identical disposable servers. Containers and auto scaling follow the cattle idea.

### Q7. Deployment strategies?
- **Recreate:** stop the old version, start the new one (downtime).
- **Rolling:** replace instances gradually (no downtime, mixed versions for a while).
- **Blue-green:** run the new version (green) next to the old (blue), then switch traffic. Instant rollback by switching back, but needs double resources.
- **Canary:** send a small share of traffic to the new version, watch metrics, then increase.

### Q8. What is a rollback and how do you plan for it?
Returning to the last good version. Keep previous image tags or artifacts, make database changes backwards compatible, use health checks that stop a bad rollout, and automate rollback on failed health checks (CodeDeploy, Kubernetes `rollout undo`).

### Q9. What is the 12-factor app (key points)?
One codebase, explicit dependencies, config in environment variables, backing services as attached resources, separate build and run stages, stateless processes, port binding, disposability (fast start and graceful shutdown), dev and prod parity, logs as streams to stdout.

### Q10. What is SRE and what are SLI, SLO, SLA?
Site Reliability Engineering applies software engineering to operations. **SLI** is a measurement (for example 99.9% of requests succeed under 300 ms). **SLO** is the target for that measurement. **SLA** is the contract with customers, often with penalties. An **error budget** is the allowed failure that lets teams balance new features against reliability.

### Q11. What are the four golden signals?
Latency, traffic, errors and saturation. RED method (rate, errors, duration) is for services. USE method (utilization, saturation, errors) is for resources like CPU and disks.

### Q12. What are DORA metrics?
Deployment frequency, lead time for changes, change failure rate, and time to restore service. They measure delivery performance.

### Q13. What is a bastion host (jump box)?
A hardened server in a public subnet used to SSH into private servers. Today AWS Systems Manager Session Manager is often preferred because it needs no open SSH port.

### Q14. High availability vs fault tolerance vs disaster recovery?
High availability: the system keeps working if a component fails (multiple AZs, load balancer). Fault tolerance: it continues with no interruption at all. Disaster recovery: restoring after a major failure. **RTO** is how long recovery may take, **RPO** is how much data you may lose.

### Q15. Vertical vs horizontal scaling?
Vertical: bigger machine. Horizontal: more machines behind a load balancer. Horizontal needs stateless apps and is the cloud-friendly way.

---

## 4. Linux and shell

### Q1. Commands you must know
```bash
pwd; ls -la; cd /var/log            # navigate
mkdir -p a/b; cp -r src dst; mv a b; rm -rf folder   # careful with rm -rf
cat file; less file; head -n 20 file; tail -n 100 file; tail -f app.log   # read, follow logs
grep -rni "error" /var/log/app/     # search text (recursive, ignore case, line numbers)
find / -name "*.log" -mtime -1      # find files changed in the last day
chmod 755 script.sh; chown ubuntu:ubuntu file       # permissions and owner
ps aux | grep node; top; htop       # processes and CPU
df -h; du -sh *; free -m            # disk usage, folder sizes, memory
systemctl status nginx; systemctl restart nginx; systemctl enable nginx
journalctl -u myapp -f --since "10 min ago"         # service logs
curl -I https://example.com; curl -s localhost:3000/health
ss -tulpn                           # listening ports and which process owns them
ssh -i key.pem ubuntu@1.2.3.4; scp file user@host:/path; rsync -avz src/ user@host:/dst/
tar -czvf backup.tar.gz folder/; tar -xzvf backup.tar.gz
kill PID; kill -9 PID; pkill -f node
```

### Q2. Explain file permissions (755, 644)
Permissions are three groups (owner, group, others), each with read (4), write (2), execute (1). `755` = owner can read, write, execute (7), group and others can read and execute (5). `644` = owner read and write (6), others read only (4). `chmod +x script.sh` makes a script executable. SSH private keys need `chmod 400` or `600`, otherwise SSH refuses them.

### Q3. What is a process? How do you find and kill one?
A running program. Find with `ps aux | grep name` or `pgrep`, find by port with `ss -tulpn | grep :3000`. `kill PID` sends SIGTERM (polite stop, lets the app clean up). `kill -9` sends SIGKILL (force, no cleanup), use only if needed.

### Q4. What does `systemd` do? How do you run an app as a service?
`systemd` starts and manages services at boot and restarts them if they fail.
```ini
# /etc/systemd/system/myapp.service
[Unit]
Description=My Node app
After=network.target

[Service]
User=ubuntu
WorkingDirectory=/home/ubuntu/app
EnvironmentFile=/home/ubuntu/app/.env
ExecStart=/usr/bin/node src/server.js
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
```
```bash
sudo systemctl daemon-reload
sudo systemctl enable --now myapp
sudo systemctl status myapp
```
(PM2 does a similar job for Node apps: `pm2 start`, `pm2 startup`, `pm2 save`.)

### Q5. Pipes, redirection and useful text tools
```bash
command > out.txt        # overwrite
command >> out.txt       # append
command 2> err.txt       # errors only
command > all.txt 2>&1   # output and errors
cat app.log | grep ERROR | sort | uniq -c | sort -rn | head    # top error messages
awk '{print $1}' access.log | sort | uniq -c | sort -rn | head # top client IPs from an Nginx log
sed -i 's/old/new/g' file.conf                                  # replace text in a file
```

### Q6. Cron jobs
```bash
crontab -e
# minute hour day month weekday command
0 2 * * *  /home/ubuntu/backup.sh >> /var/log/backup.log 2>&1     # every day at 2:00
*/5 * * * * /home/ubuntu/healthcheck.sh                            # every 5 minutes
```

### Q7. How do you check why a server is slow?
CPU: `top` or `htop` (load average, which process). Memory: `free -m` (is swap used, any OOM kills in `dmesg | grep -i kill`). Disk: `df -h` (full disk) and `iostat`. Network: `ss -s`, `curl` timings. Then look at the application logs.

### Q8. Disk is full. What do you do?
`df -h` to find the partition, `du -sh /var/* | sort -h` to find large folders, usually logs. Clean or rotate logs (`logrotate`), remove old Docker images (`docker system prune`), and check for deleted files still held open by a process (`lsof | grep deleted`). Then add monitoring and alerts so you find out before it fills.

### Q9. Users and sudo
`adduser`, `usermod -aG sudo user`, `passwd`. Use `sudo` for admin tasks, disable root SSH login and password login, and use SSH keys.

### Q10. SSH key authentication
Generate a key pair (`ssh-keygen -t ed25519`). The **public key** goes into `~/.ssh/authorized_keys` on the server, the **private key** stays on your machine. SSH config (`~/.ssh/config`) saves host settings. Disable password authentication in `/etc/ssh/sshd_config` (`PasswordAuthentication no`).

### Q11. Package management
Ubuntu and Debian: `apt update && apt install nginx`. Amazon Linux and RHEL: `dnf` or `yum`. Check installed software with `dpkg -l` or `rpm -qa`.

### Q12. Environment variables and shell basics
`export KEY=value`, `echo $KEY`, `printenv`. Put persistent variables in `~/.bashrc` or a service's `EnvironmentFile`, never hard-code secrets into scripts committed to Git.

---

## 5. Networking basics

### Q1. What happens when you type a URL in the browser?
1. The browser checks its cache, then asks DNS to turn the domain into an IP address.
2. It opens a TCP connection (three-way handshake) to that IP on port 443.
3. For HTTPS, a TLS handshake agrees on encryption keys and the server proves its identity with a certificate.
4. The browser sends an HTTP request. A load balancer or reverse proxy forwards it to the application.
5. The application (and database) produce a response, and the browser renders it.

### Q2. OSI vs TCP/IP model
OSI has 7 layers (physical, data link, network, transport, session, presentation, application). The practical TCP/IP view has 4: link, internet (IP), transport (TCP and UDP), application (HTTP, DNS, SSH). Useful: **L4** load balancers route by IP and port, **L7** load balancers route by HTTP content (path, host, headers).

### Q3. TCP vs UDP
TCP is connection-oriented, reliable and ordered (web, SSH, databases). UDP is connectionless and faster but not reliable (DNS queries, video streaming, games).

### Q4. DNS and record types
DNS maps names to IPs. **A** (name to IPv4), **AAAA** (IPv6), **CNAME** (alias to another name), **MX** (mail), **TXT** (verification, SPF), **NS** (name servers). TTL says how long answers are cached. Debug with `dig example.com` or `nslookup`.

### Q5. Common ports
22 SSH, 80 HTTP, 443 HTTPS, 53 DNS, 3306 MySQL, 5432 PostgreSQL, 27017 MongoDB, 6379 Redis, 3000 and 8000 common app ports, 9090 Prometheus, 3000 Grafana (default), 9100 node exporter.

### Q6. IP addresses, CIDR and subnetting
- Private ranges: `10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16`.
- CIDR notation `10.0.0.0/16` means the first 16 bits are the network. Number of addresses is `2^(32 - prefix)`: `/16` = 65,536, `/24` = 256, `/28` = 16.
- In an AWS subnet, 5 addresses are reserved, so a `/24` has 251 usable.
- A typical split: VPC `10.0.0.0/16`, public subnets `10.0.1.0/24` and `10.0.2.0/24`, private subnets `10.0.11.0/24` and `10.0.12.0/24` (in different Availability Zones).

### Q7. NAT, proxy and reverse proxy
**NAT** lets private servers reach the internet without being reachable from it (a NAT gateway in AWS). A **forward proxy** sits in front of clients. A **reverse proxy** (Nginx, ALB) sits in front of servers: it handles TLS, load balancing, caching and hides the backend.

### Q8. Load balancing algorithms
Round robin, least connections, IP hash (same client to the same server), weighted. Health checks remove unhealthy servers from rotation.

### Q9. HTTP vs HTTPS and TLS certificates
HTTPS is HTTP inside TLS: it encrypts traffic, proves the server's identity, and prevents tampering. Certificates are issued by a certificate authority (Let's Encrypt, AWS ACM). In AWS, put the certificate on the ALB or CloudFront, and forward plain HTTP inside the VPC (or use TLS end to end if required).

### Q10. Common HTTP errors from a proxy
`502 Bad Gateway`: the proxy got an invalid or no response from the backend (app crashed or wrong port). `503`: no healthy backend or overloaded. `504 Gateway Timeout`: the backend was too slow. `404` on the ALB: no matching rule. `403`: blocked by a rule or permission.

### Q11. Firewalls and network troubleshooting
Security groups, NACLs, `ufw` and `iptables`. Troubleshoot step by step: DNS resolves (`dig`), host reachable (`ping` may be blocked), port open (`nc -zv host 443` or `telnet`), service listening (`ss -tulpn`), then the application logs. `curl -v` shows the whole HTTP conversation, `traceroute` shows the path.

### Q12. What is a CDN?
A network of edge servers that cache content close to users, which lowers latency and server load (CloudFront). Good for static files like your React build.

---

## 6. Git and branching

### Q1. Essential commands
```bash
git clone url; git status; git add .; git commit -m "message"; git push origin main
git pull --rebase; git fetch; git switch -c feature/login; git merge feature/login
git log --oneline --graph --decorate; git diff; git stash; git stash pop
git tag v1.2.0; git push --tags
```

### Q2. Merge vs rebase?
Merge combines branches and keeps history with a merge commit. Rebase replays your commits on top of the latest base for a straight history. Never rebase commits that others already use (shared branches).

### Q3. `git reset` vs `git revert`?
`reset` moves the branch pointer back (`--soft`, `--mixed`, `--hard`) and rewrites history, fine for local work. `revert` creates a **new commit** that undoes an old one, safe for shared branches.

### Q4. `git fetch` vs `git pull`?
`fetch` downloads changes without changing your files. `pull` is `fetch` plus merge (or rebase).

### Q5. How do you resolve a merge conflict?
Open the files, choose what to keep between `<<<<<<<`, `=======`, `>>>>>>>`, remove the markers, `git add`, then `git commit` (or `git rebase --continue`). Run the tests afterwards.

### Q6. Branching strategies
- **Git Flow:** `main`, `develop`, feature, release and hotfix branches (more process, scheduled releases).
- **GitHub Flow:** short-lived feature branches merged to `main` through pull requests (simple, continuous delivery).
- **Trunk-based:** very small changes merged to `main` often, with feature flags (works best with strong CI).

### Q7. Other things asked
- **`git cherry-pick <commit>`:** apply one commit onto the current branch (for hotfixes).
- **`.gitignore`:** files Git must not track (`node_modules`, `.env`, `.terraform`, `*.tfstate`).
- **A secret was committed. What now?** Rotate the secret immediately (it is compromised), then remove it from history (`git filter-repo` or BFG) and add a secret scanner (`gitleaks`, `git-secrets`). Deleting it in a new commit is not enough.
- **What is a pull request review for?** Quality, shared knowledge and catching bugs. CI checks must pass before merging, and `main` should be a protected branch.
- **Git hooks:** scripts that run on events (a `pre-commit` hook can run a linter).

---
## 7. AWS core services

### Q1. What is cloud computing? IaaS, PaaS, SaaS?
Renting computing resources over the internet and paying for what you use. **IaaS** gives raw infrastructure (EC2, VPC), **PaaS** gives a platform to run code (Elastic Beanstalk, App Runner, Lambda), **SaaS** is a finished application (Gmail). Benefits: scalability, pay as you go, global reach, less hardware to manage.

### Q2. Region, Availability Zone and Edge location?
A **Region** is a geographic area (for example `ap-south-1` Mumbai). An **Availability Zone (AZ)** is one or more separate data centers inside a region, with independent power and network. Spread your app over at least 2 AZs for high availability. **Edge locations** serve CloudFront (CDN) and Route 53 close to users.

### Q3. What is the AWS shared responsibility model?
AWS is responsible for security **of** the cloud (data centers, hardware, hypervisor). You are responsible for security **in** the cloud (your data, IAM, OS patches on EC2, security groups, application security, encryption choices).

### EC2
**Q4. What is EC2? Key concepts?**
Virtual servers. Key concepts: **AMI** (image used to launch an instance), **instance type** (CPU and memory, for example `t3.micro`, `m5.large`), **key pair** (SSH access), **security group** (virtual firewall), **EBS volume** (disk), **user data** (a script that runs at first boot), **instance profile** (attaches an IAM role), **Elastic IP** (static public IP).

**Q5. EC2 pricing options?**
**On-Demand** (pay per second, flexible), **Reserved Instances or Savings Plans** (commit 1 or 3 years for a discount), **Spot** (up to about 90% cheaper, can be interrupted, good for batch or fault tolerant work), **Dedicated Hosts** (compliance).

**Q6. Instance families?**
`t` burstable general purpose (small apps), `m` general purpose, `c` compute optimized, `r` memory optimized, `i` storage optimized, `g` and `p` GPU. A `t3.micro` is free-tier eligible in many accounts.

**Q7. EBS vs instance store vs EFS?**
**EBS** is a network disk attached to one instance (persistent, snapshots to S3, types `gp3` general SSD, `io2` high IOPS). **Instance store** is temporary disk on the host (lost when the instance stops). **EFS** is a shared network file system (NFS) mountable by many instances.

**Q8. What is user data?**
A script that runs once at launch, used for bootstrapping (install Docker, pull the app).
```bash
#!/bin/bash
dnf update -y
dnf install -y docker
systemctl enable --now docker
docker run -d -p 80:3000 --restart always myrepo/myapp:1.0
```

**Q9. How do you connect to an EC2 instance?**
SSH with the key pair (`ssh -i key.pem ec2-user@ip`, the user is `ec2-user` for Amazon Linux and `ubuntu` for Ubuntu), or **Session Manager** (no open port, access controlled by IAM). Make sure the security group allows port 22 only from your IP.

**Q10. What happens if you stop vs terminate an instance?**
Stop: the instance shuts down, EBS data stays, you pay for storage only, and the public IP changes unless it is an Elastic IP. Terminate: the instance is deleted and the root volume is deleted by default.

### VPC and networking
**Q11. What is a VPC?**
A Virtual Private Cloud is your own isolated network in AWS where you control IP ranges, subnets, routing and firewalls.

**Q12. Components of a VPC?**
- **CIDR block** (for example `10.0.0.0/16`).
- **Subnets** (ranges inside the VPC, each in one AZ). **Public subnet** has a route to an Internet Gateway, **private subnet** does not.
- **Internet Gateway (IGW):** connects the VPC to the internet.
- **Route tables:** decide where traffic goes (`0.0.0.0/0` to the IGW for public subnets, to a NAT gateway for private ones).
- **NAT Gateway:** lets private instances reach the internet (updates, external APIs) without being reachable from it. It lives in a public subnet and costs money per hour.
- **Security groups and NACLs:** firewalls.
- **VPC endpoints:** private access to AWS services (S3, DynamoDB) without going through the internet.
- **VPC peering and Transit Gateway:** connect VPCs.

**Q13. Security group vs NACL?**
| | Security Group | Network ACL |
|---|---|---|
| Level | instance (network interface) | subnet |
| State | **stateful** (return traffic automatically allowed) | **stateless** (must allow both directions) |
| Rules | allow only | allow and deny |
| Default | deny all inbound, allow all outbound | allow all |
Use security groups for most work, and NACLs as an extra subnet-level layer.

**Q14. What makes a subnet public?**
Its route table has a route `0.0.0.0/0 -> Internet Gateway`, and instances in it have a public IP. Without that route it is private.

**Q15. How can a private instance access the internet?**
Through a NAT gateway placed in a public subnet. The private subnet's route table sends `0.0.0.0/0` to the NAT gateway.

**Q16. Can you connect to the database in a private subnet from your laptop?**
Not directly. Use a bastion host, Session Manager port forwarding, or a VPN.

### IAM
**Q17. What is IAM?**
Identity and Access Management controls who can do what in AWS. Parts: **users** (people), **groups** (users with shared permissions), **roles** (temporary permissions assumed by services or users, no long-term keys), **policies** (JSON documents that allow or deny actions).

**Q18. User vs role? When use a role?**
A user has long-term credentials. A role has no credentials of its own and gives **temporary** credentials to whoever assumes it. Use roles for EC2, Lambda, ECS and CI pipelines (attach an instance profile to EC2) so you never store access keys on servers.

**Q19. Example IAM policy (least privilege)**
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": ["s3:GetObject", "s3:PutObject"],
      "Resource": "arn:aws:s3:::my-app-uploads/*"
    }
  ]
}
```
Allow only the actions and resources needed. An explicit `Deny` always wins over `Allow`.

**Q20. Trust policy for an EC2 role**
```json
{
  "Version": "2012-10-17",
  "Statement": [
    { "Effect": "Allow", "Principal": { "Service": "ec2.amazonaws.com" }, "Action": "sts:AssumeRole" }
  ]
}
```
The **trust policy** says who can assume the role. The **permissions policy** says what the role can do.

**Q21. IAM best practices?**
Do not use the root account for daily work (enable MFA on it), least privilege, roles instead of access keys, MFA for users, rotate keys, use groups, review permissions, use IAM Access Analyzer, and never commit credentials to Git.

### S3
**Q22. What is S3?**
Object storage for files of any size, organized in buckets. Highly durable (99.999999999%) and scalable. Not a file system or a database. Use it for uploads, backups, static websites, logs, and Terraform state.

**Q23. Storage classes?**
Standard (frequent access), Standard-IA and One Zone-IA (infrequent access), Intelligent-Tiering (automatic), Glacier Instant, Flexible and Deep Archive (archives, cheaper, slower retrieval). Use **lifecycle rules** to move or delete old objects automatically.

**Q24. How do you secure an S3 bucket?**
Block Public Access (on by default), bucket policies and IAM policies with least privilege, encryption at rest (SSE-S3 or SSE-KMS), HTTPS only, versioning (and MFA delete for critical data), access logging, and pre-signed URLs for temporary access.

**Q25. What are pre-signed URLs?**
Temporary URLs that give time-limited access to a private object (download or upload) without making the bucket public. The browser can upload directly to S3 without passing through your server.

**Q26. How do you host a React app on S3 and CloudFront?**
Build the app (`npm run build`), upload the `dist` or `build` folder to a private S3 bucket, create a CloudFront distribution with Origin Access Control to read the bucket, add an ACM certificate (in `us-east-1` for CloudFront) and a Route 53 alias record, and configure the error page so all routes return `index.html` for client-side routing.

**Q27. What is versioning?**
Keeps every version of an object, so you can recover from accidental deletes or overwrites.

### RDS and databases
**Q28. What is RDS?**
A managed relational database service (PostgreSQL, MySQL, MariaDB, Oracle, SQL Server, Aurora). AWS handles backups, patching, failover and monitoring. You still manage schema, queries and access.

**Q29. Multi-AZ vs read replica?**
**Multi-AZ** keeps a synchronous standby in another AZ for **high availability**: automatic failover, no read traffic served by the standby. **Read replicas** are asynchronous copies used to **scale reads** (and can be promoted). They solve different problems.

**Q30. How do you secure RDS?**
Private subnets, `publicly_accessible = false`, a security group that allows the DB port only from the app's security group, encryption at rest (KMS) and in transit (SSL), strong passwords in Secrets Manager, automated backups, and IAM database authentication where useful.

**Q31. RDS backups?**
Automated backups with point-in-time recovery (retention 1 to 35 days) and manual snapshots. A restore creates a **new** instance.

**Q32. DynamoDB vs RDS?**
DynamoDB is a serverless NoSQL key-value and document database with single-digit millisecond latency at scale and flexible schema. RDS is relational (SQL, joins, transactions). Choose by access patterns.

**Q33. ElastiCache?**
Managed Redis or Memcached for caching and sessions.

### Load balancing and scaling
**Q34. ALB vs NLB vs Classic?**
**ALB** (Application Load Balancer, layer 7): routes by host, path or header, supports WebSockets, works with containers and Lambda. **NLB** (layer 4): very high performance, static IPs, TCP and UDP. Classic is legacy.

**Q35. What are target groups and health checks?**
A listener on the ALB (for example port 443) forwards to a **target group** (a set of instances or containers). The ALB calls a health check path (for example `/health`) and only sends traffic to targets that return `200`. If a target fails, it stops receiving traffic.

**Q36. What is Auto Scaling?**
An **Auto Scaling Group (ASG)** keeps the number of instances between a minimum and maximum, replaces unhealthy ones, and scales out and in by rules. It uses a **launch template** (AMI, instance type, security groups, user data). **Target tracking** scaling keeps a metric near a target (for example average CPU at 60%). Spread across multiple AZs.

**Q37. Why must the app be stateless for auto scaling?**
New instances can appear or disappear at any time. Sessions, uploads and caches must live outside the instance (Redis, S3, the database).

### Other services
**Q38. Route 53?**
DNS service. Record types: A, AAAA, CNAME, MX, TXT, and **Alias** (an AWS-specific record that points a domain, even the root `example.com`, to an ALB, CloudFront or S3). Routing policies: simple, weighted (canary), latency, failover (health checks), geolocation.

**Q39. CloudFront?**
A CDN that caches content at edge locations, and terminates TLS. Used in front of S3 or an ALB for speed and to reduce load.

**Q40. CloudWatch?**
Monitoring service: **Metrics** (CPU, network, custom), **Logs** (application and system logs through the CloudWatch agent), **Alarms** (notify or trigger actions when a metric crosses a threshold), **Dashboards**, and **Events/EventBridge** for scheduled or event-driven actions. By default EC2 does not report memory or disk usage, so install the CloudWatch agent.

**Q41. CloudTrail vs CloudWatch vs Config?**
CloudTrail records **API calls** (who did what and when, for audit). CloudWatch tracks **metrics and logs** (how the system performs). AWS Config tracks **resource configuration** and compliance over time.

**Q42. Lambda?**
Run code without managing servers. Triggered by events (API Gateway, S3, SQS, schedules). Pay per request and duration. Limits: maximum 15 minutes per run, cold starts. Good for small event-driven tasks, not long jobs.

**Q43. SQS vs SNS?**
**SQS** is a queue: producers send messages, consumers pull and process them (decoupling, retries, buffering). Standard queues have at-least-once delivery, FIFO queues keep order. **SNS** is publish and subscribe: one message goes to many subscribers (email, SMS, SQS, Lambda). A common pattern is SNS to several SQS queues.

**Q44. ECR, ECS, EKS, Fargate?**
**ECR** stores Docker images. **ECS** runs containers using AWS's own orchestrator. **EKS** is managed Kubernetes. **Fargate** is the serverless way to run ECS or EKS containers without managing servers.

**Q45. Secrets Manager vs SSM Parameter Store?**
Both store configuration and secrets. **Secrets Manager** has automatic rotation (for example RDS passwords), costs per secret. **Parameter Store** is simpler and cheaper (standard parameters are free), good for configuration and simple secrets. Read them at startup with an IAM role, and never bake secrets into images.

**Q46. KMS?**
Key Management Service creates and controls encryption keys used by S3, EBS, RDS and Secrets Manager. Keys never leave KMS in plain form.

**Q47. ACM?**
AWS Certificate Manager issues and renews free public TLS certificates for ALB, CloudFront and API Gateway.

**Q48. CloudFormation vs Terraform?**
CloudFormation is AWS-only, JSON or YAML templates, state managed by AWS (stacks). Terraform is multi-cloud, HCL language, you manage the state file, and has a large module ecosystem. Both are IaC. Many teams prefer Terraform for flexibility.

**Q49. How do you reduce AWS cost?**
Right-size instances, turn off dev resources at night, Savings Plans or Reserved Instances for steady load, Spot for fault tolerant work, S3 lifecycle rules, delete unused EBS volumes, snapshots and Elastic IPs, avoid unnecessary NAT gateway traffic (use VPC endpoints), set **billing alarms and budgets**, and use cost allocation tags.

**Q50. AWS Well-Architected pillars?**
Operational excellence, security, reliability, performance efficiency, cost optimization, sustainability.

**Q51. What is the AWS CLI? Common commands?**
```bash
aws configure                                        # set keys and region (better: use roles or SSO)
aws sts get-caller-identity                          # who am I
aws s3 ls; aws s3 cp file s3://bucket/; aws s3 sync ./dist s3://bucket --delete
aws ec2 describe-instances --query "Reservations[].Instances[].[InstanceId,State.Name]" --output table
aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin <acct>.dkr.ecr.ap-south-1.amazonaws.com
aws logs tail /aws/my-app --follow
```

---

## 8. AWS architecture for a full stack app

### Typical 3-tier architecture (draw this on a whiteboard)
```
Users
  |
Route 53 (DNS) --> CloudFront (CDN, optional) --> S3 (React build)        [frontend]
  |
Application Load Balancer (public subnets, ACM certificate, HTTPS)
  |
Auto Scaling Group / ECS tasks (private subnets, 2+ AZs)  -- Node.js / FastAPI containers
  |        \
  |         ElastiCache Redis (cache, sessions)
  |
RDS PostgreSQL (private subnets, Multi-AZ)       S3 (uploads)       Secrets Manager (credentials)

NAT Gateway (public subnet) for outbound internet from private subnets
CloudWatch (logs, metrics, alarms) + Prometheus/Grafana (optional)
ECR (images)  +  CI/CD pipeline (Jenkins or GitHub Actions)  +  Terraform (infrastructure)
```

### Explain the flow in simple words
"Users reach the domain through Route 53. Static React files come from S3 through CloudFront. API calls go to the Application Load Balancer in the public subnets, which forwards to the app servers or containers in private subnets across two Availability Zones. The app talks to RDS in private subnets and to S3 and Secrets Manager through IAM roles. Security groups only allow ALB to app, and app to database. CloudWatch collects logs and alarms."

### Security group chain
| Resource | Inbound rule |
|---|---|
| ALB security group | 443 and 80 from `0.0.0.0/0` |
| App security group | app port (3000 or 8000) **from the ALB security group only** |
| DB security group | 5432 **from the app security group only** |
| Bastion (if any) | 22 from your office IP only |

### Simple version for a small project (what you did at eAsha)
One EC2 instance (or two behind an ALB) running the app with PM2 or Docker, Nginx as reverse proxy, MongoDB Atlas or RDS, S3 for files, Route 53 and an ACM certificate on the ALB. Then explain how you would grow it: Auto Scaling, Multi-AZ, containers, CI/CD and monitoring.

### Questions to expect
- **How do you make this highly available?** At least two AZs for the ALB, app instances (ASG) and RDS Multi-AZ, health checks, and no single point of failure.
- **How do you handle deployments with no downtime?** Rolling or blue-green behind the ALB, health checks, connection draining, backwards-compatible database changes.
- **Where do secrets live?** Secrets Manager or SSM, read through the instance or task IAM role.
- **How does the frontend call the backend?** Over HTTPS to the ALB domain, with CORS allowing only the frontend origin.
- **How do you protect against attacks?** HTTPS, WAF on the ALB or CloudFront, security groups, rate limiting, patched OS and dependencies, IAM least privilege.
- **How do you back up data?** RDS automated backups and snapshots, S3 versioning, tested restores.
- **How would you add a staging environment?** The same Terraform code with different variable values (workspace or separate state per environment).

---
## 9. Terraform concepts

The director specifically asked about Terraform, so this is the most important DevOps section.

### Q1. What is Terraform? How does it work?
An open-source Infrastructure as Code tool by HashiCorp. You write resources in HCL (HashiCorp Configuration Language). Terraform reads the code, compares it with the **state file** (what it believes exists) and the real infrastructure through the **provider** (for example AWS), builds a plan, and on `apply` makes the API calls to create, update or delete resources.

**Say it like this:** "Terraform is declarative. I describe the end state, and Terraform works out what to create, change or destroy. It keeps a state file so it knows what it manages, and `terraform plan` lets me review changes before applying them."

### Q2. The Terraform workflow
```
terraform init      download providers and modules, set up the backend
terraform fmt       format the code
terraform validate  check syntax
terraform plan      preview the changes (nothing is changed)
terraform apply     make the changes (asks for confirmation)
terraform destroy   delete everything this configuration manages
```
In CI: `plan` on every pull request, review the plan, and `apply` after the merge.

### Q3. What is a provider?
A plugin that lets Terraform talk to an API (AWS, Azure, GCP, Kubernetes, GitHub). You declare it in `required_providers` and configure it with a `provider` block (region, credentials). `terraform init` downloads it.

### Q4. Resource vs data source?
A **resource** creates and manages something (`aws_instance`). A **data source** only **reads** existing information (`data "aws_ami"` to find the latest AMI, `data "aws_vpc"` to look up an existing VPC).

### Q5. What is the state file? Why is it important?
`terraform.tfstate` maps your code to real resources (ids, attributes). Terraform uses it to know what exists and what changed. If you lose it, Terraform forgets what it created and may try to create duplicates. **It can contain secrets in plain text** (database passwords), so never commit it to Git, and store it in an encrypted, access-controlled remote backend.

### Q6. What is a remote backend? State locking?
A remote backend stores the state in a shared place (S3 with encryption and versioning) so the whole team uses the same state. **Locking** prevents two `apply` runs at the same time from corrupting it. The classic S3 setup uses a DynamoDB table for locks. Newer Terraform versions can use **S3 native locking** (`use_lockfile = true`) and DynamoDB locking is being phased out. Terraform Cloud is another option.

### Q7. What are variables, locals and outputs?
- **Input variables** (`variable`) parameterize the code (`var.region`).
- **Locals** (`locals`) are named expressions inside the module to avoid repetition.
- **Outputs** (`output`) expose values after apply (a public IP, a database endpoint) and pass values between modules.
Set variable values with `terraform.tfvars`, `-var "name=value"`, `-var-file=prod.tfvars`, or environment variables `TF_VAR_name`. Mark secrets with `sensitive = true` (this hides them in the output, but they still exist in the state).

### Q8. What are modules?
A module is a folder of Terraform files that you call from other code, like a function. They make code reusable (a `vpc` module used for dev and prod). Use the public registry (`terraform-aws-modules/vpc/aws`) or your own. A module has input variables and outputs. Pin module versions.

### Q9. `count` vs `for_each`?
Both create multiple copies of a resource. `count` uses an index (`count = 3`, `count.index`). If you remove an item from the middle of the list, resources after it get re-created because their index changes. `for_each` uses keys (a set or map), so each resource is tracked by its key and removing one item affects only that item. Prefer `for_each` for collections, use `count` for simple on and off (`count = var.enabled ? 1 : 0`).

### Q10. What is `depends_on`? Implicit vs explicit dependencies?
Terraform builds a dependency graph. If resource B references `aws_vpc.main.id`, that creates an **implicit** dependency, and Terraform creates the VPC first. Use `depends_on` only for hidden dependencies Terraform cannot see.

### Q11. What is `lifecycle`?
Controls how resources change:
```hcl
lifecycle {
  create_before_destroy = true      # create the replacement first (avoid downtime)
  prevent_destroy       = true      # error if a plan would destroy it (protect databases)
  ignore_changes        = [tags]    # ignore changes made outside Terraform
}
```

### Q12. What is drift? How do you handle it?
Drift is when real infrastructure differs from the code or state (someone changed it in the console). `terraform plan` shows it. Either apply to restore the code's version, or update the code to match. Prevent drift by restricting console access and making Terraform the only way to change infrastructure. `terraform apply -refresh-only` updates the state to match reality without changing resources.

### Q13. What if the state is lost or out of sync?
Restore the previous version from S3 versioning. For resources that exist but are not in the state, use `terraform import` (or an `import` block) to bring them under management, and write matching code. Use `terraform state list`, `state show`, `state mv`, `state rm` to inspect and fix the state carefully.

### Q14. What does `terraform import` do?
Adds an existing resource to the state so Terraform can manage it. You write the resource block, then run `terraform import aws_s3_bucket.logs my-bucket-name`. Terraform 1.5+ also supports `import { to = ..., id = ... }` blocks, reviewed in a plan.

### Q15. Terraform vs Ansible vs CloudFormation vs Pulumi?
- **Terraform:** provisions infrastructure, multi-cloud, declarative, HCL, you manage state.
- **Ansible:** configures software on existing servers, agentless, procedural YAML playbooks.
- **CloudFormation:** AWS-only IaC, JSON or YAML, state managed by AWS.
- **Pulumi:** IaC using real programming languages (TypeScript, Python).
Common combination: Terraform creates the servers, Ansible configures them (or you build images with Docker and skip configuration management).

### Q16. What are provisioners? Should you use them?
`local-exec` and `remote-exec` run scripts during resource creation. They are a last resort because they are not declarative and are hard to reason about. Prefer `user_data`, pre-built images, or Ansible.

### Q17. How do you manage multiple environments (dev, staging, prod)?
Options: **separate state per environment** with different `.tfvars` files and backend keys (most common and safest), separate folders or repositories per environment calling shared modules, or **workspaces** (`terraform workspace new dev`, several states from one code, simple but easy to confuse). Use the same modules with different variable values.

### Q18. How do you handle secrets in Terraform?
Never hard-code them. Use `sensitive` variables set from environment variables or a secret manager, read secrets with data sources (`aws_secretsmanager_secret_version`), or let the service manage them (`manage_master_user_password = true` for RDS). Remember that the state still holds sensitive values, so encrypt and protect the backend.

### Q19. Important functions and expressions
`cidrsubnet(prefix, newbits, netnum)` (carve subnets), `length`, `lookup`, `merge`, `concat`, `join`, `split`, `file`, `templatefile`, `jsonencode`, `toset`, `element`, `slice`. Conditionals: `var.env == "prod" ? 3 : 1`. `for` expressions: `[for s in aws_subnet.private : s.id]`, and the splat `aws_subnet.private[*].id`.

### Q20. What is a `dynamic` block?
Generates repeated nested blocks from a collection (for example many `ingress` rules in a security group).
```hcl
dynamic "ingress" {
  for_each = var.allowed_ports
  content {
    from_port   = ingress.value
    to_port     = ingress.value
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
}
```

### Q21. What does `terraform plan -out` do?
Saves the plan to a file so `terraform apply tfplan` applies exactly what you reviewed, which is safer in CI.

### Q22. How do you replace a resource?
`terraform apply -replace="aws_instance.web"` (the old `terraform taint` is deprecated).

### Q23. `terraform refresh`, `fmt`, `validate`?
`refresh` (now `apply -refresh-only`) updates the state from reality. `fmt` formats files. `validate` checks syntax and internal consistency without calling the cloud.

### Q24. How do you version and share Terraform code?
Keep it in Git with pull requests, pin the Terraform and provider versions, commit `.terraform.lock.hcl` (provider checksums), ignore `.terraform/` and `*.tfstate*` in `.gitignore`, tag module releases.

### Q25. How do you test and secure Terraform code?
`fmt` and `validate` in CI, `plan` reviews, static scanners (`tflint`, `tfsec` or `trivy config`, `checkov`) that find open security groups or unencrypted storage, policy as code (OPA or Sentinel), and a dev environment for trial runs.

### Q26. What is OpenTofu?
A community-maintained open-source fork of Terraform, created after HashiCorp changed Terraform's license. It is largely compatible. A good one-line answer: "OpenTofu is the open-source fork, and the concepts and most code are the same."

### Q27. What happens during `terraform apply` if one resource fails?
Terraform stops, resources already created stay (and are in the state), and the failed one is not recorded. Fix the problem and run `apply` again. Terraform picks up where it left off because it compares with the state. It does not roll back automatically.

### Q28. How does Terraform decide to update in place or replace a resource?
Some argument changes can be applied in place. Others force replacement (marked `-/+` in the plan, for example changing an EC2 AMI or an RDS identifier). Always read the plan, and use `prevent_destroy` on critical resources.

### Q29. Terraform Cloud and Terragrunt?
Terraform Cloud (HCP Terraform) provides remote state, remote runs, access control and policy checks. Terragrunt is a thin wrapper that keeps backend and variable configuration DRY across many environments. Know them by name.

### Q30. How would you destroy only one resource?
`terraform destroy -target=aws_instance.web` (use rarely, only for exceptions) or remove the resource from the code and apply.

---

## 10. Terraform code

### Project layout
```
infra/
  versions.tf        (terraform block, provider, backend)
  variables.tf       (inputs)
  main.tf            (resources)
  outputs.tf
  terraform.tfvars   (values, not for secrets)
  user_data.sh
  .gitignore         (.terraform/, *.tfstate*, *.tfvars with secrets)
```

### Create the remote state bucket once (bootstrap)
```bash
aws s3api create-bucket --bucket my-tf-state-bucket --region ap-south-1 \
  --create-bucket-configuration LocationConstraint=ap-south-1
aws s3api put-bucket-versioning --bucket my-tf-state-bucket --versioning-configuration Status=Enabled
aws s3api put-bucket-encryption --bucket my-tf-state-bucket \
  --server-side-encryption-configuration '{"Rules":[{"ApplyServerSideEncryptionByDefault":{"SSEAlgorithm":"AES256"}}]}'
```

### versions.tf
```hcl
terraform {
  required_version = ">= 1.6"

  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }

  backend "s3" {                                 # backend blocks cannot use variables
    bucket       = "my-tf-state-bucket"
    key          = "myapp/dev/terraform.tfstate"
    region       = "ap-south-1"
    encrypt      = true
    use_lockfile = true                          # S3 native locking (newer Terraform). Older setups use dynamodb_table = "tf-locks"
  }
}

provider "aws" {
  region = var.region

  default_tags {                                 # tags added to every resource
    tags = {
      Project     = var.project
      Environment = var.environment
      ManagedBy   = "terraform"
    }
  }
}
```

### variables.tf
```hcl
variable "region" {
  type    = string
  default = "ap-south-1"
}

variable "project" {
  type    = string
  default = "myapp"
}

variable "environment" {
  type = string
  validation {
    condition     = contains(["dev", "staging", "prod"], var.environment)
    error_message = "environment must be dev, staging or prod."
  }
}

variable "vpc_cidr" {
  type    = string
  default = "10.0.0.0/16"
}

variable "instance_type" {
  type    = string
  default = "t3.micro"
}

variable "key_name" {
  type        = string
  description = "Name of an existing EC2 key pair"
}

variable "my_ip_cidr" {
  type        = string
  description = "Your public IP in CIDR form, for SSH, example 203.0.113.10/32"
}

variable "db_username" {
  type    = string
  default = "appuser"
}

variable "db_password" {
  type      = string
  sensitive = true                               # set with TF_VAR_db_password, never in Git
}
```

### main.tf: network
```hcl
locals {
  name = "${var.project}-${var.environment}"
  azs  = slice(data.aws_availability_zones.available.names, 0, 2)    # use 2 AZs
}

data "aws_availability_zones" "available" {
  state = "available"
}

resource "aws_vpc" "main" {
  cidr_block           = var.vpc_cidr
  enable_dns_support   = true
  enable_dns_hostnames = true
  tags                 = { Name = "${local.name}-vpc" }
}

resource "aws_internet_gateway" "igw" {
  vpc_id = aws_vpc.main.id
  tags   = { Name = "${local.name}-igw" }
}

resource "aws_subnet" "public" {
  count                   = length(local.azs)
  vpc_id                  = aws_vpc.main.id
  cidr_block              = cidrsubnet(var.vpc_cidr, 8, count.index)         # 10.0.0.0/24, 10.0.1.0/24
  availability_zone       = local.azs[count.index]
  map_public_ip_on_launch = true
  tags                    = { Name = "${local.name}-public-${count.index + 1}" }
}

resource "aws_subnet" "private" {
  count             = length(local.azs)
  vpc_id            = aws_vpc.main.id
  cidr_block        = cidrsubnet(var.vpc_cidr, 8, count.index + 10)           # 10.0.10.0/24, 10.0.11.0/24
  availability_zone = local.azs[count.index]
  tags              = { Name = "${local.name}-private-${count.index + 1}" }
}

resource "aws_route_table" "public" {
  vpc_id = aws_vpc.main.id
  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.igw.id
  }
  tags = { Name = "${local.name}-public-rt" }
}

resource "aws_route_table_association" "public" {
  count          = length(aws_subnet.public)
  subnet_id      = aws_subnet.public[count.index].id
  route_table_id = aws_route_table.public.id
}

# Optional: NAT gateway so private subnets can reach the internet (it costs money per hour)
resource "aws_eip" "nat" {
  count  = var.environment == "prod" ? 1 : 0
  domain = "vpc"
}

resource "aws_nat_gateway" "nat" {
  count         = var.environment == "prod" ? 1 : 0
  allocation_id = aws_eip.nat[0].id
  subnet_id     = aws_subnet.public[0].id
  tags          = { Name = "${local.name}-nat" }
}
```
The private subnets have no route to the internet gateway, so they stay private. For a NAT, add a private route table with `0.0.0.0/0` pointing to the NAT gateway.

### main.tf: security groups
```hcl
resource "aws_security_group" "web" {
  name        = "${local.name}-web-sg"
  description = "Web server"
  vpc_id      = aws_vpc.main.id

  ingress {
    description = "HTTP"
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
  ingress {
    description = "HTTPS"
    from_port   = 443
    to_port     = 443
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
  ingress {
    description = "SSH from my IP only"
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = [var.my_ip_cidr]
  }
  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}

resource "aws_security_group" "db" {
  name        = "${local.name}-db-sg"
  description = "Database, only reachable from the web tier"
  vpc_id      = aws_vpc.main.id

  ingress {
    description     = "PostgreSQL from web security group"
    from_port       = 5432
    to_port         = 5432
    protocol        = "tcp"
    security_groups = [aws_security_group.web.id]          # a security group as source, not a CIDR
  }
  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}
```

### main.tf: IAM role and EC2
```hcl
resource "aws_iam_role" "web" {
  name = "${local.name}-web-role"
  assume_role_policy = jsonencode({
    Version = "2012-10-17"
    Statement = [{
      Effect    = "Allow"
      Principal = { Service = "ec2.amazonaws.com" }
      Action    = "sts:AssumeRole"
    }]
  })
}

resource "aws_iam_role_policy_attachment" "ssm" {            # lets you use Session Manager instead of SSH
  role       = aws_iam_role.web.name
  policy_arn = "arn:aws:iam::aws:policy/AmazonSSMManagedInstanceCore"
}

resource "aws_iam_instance_profile" "web" {
  name = "${local.name}-web-profile"
  role = aws_iam_role.web.name
}

data "aws_ami" "al2023" {
  most_recent = true
  owners      = ["amazon"]
  filter {
    name   = "name"
    values = ["al2023-ami-2023.*-x86_64"]
  }
}

resource "aws_instance" "web" {
  ami                    = data.aws_ami.al2023.id
  instance_type          = var.instance_type
  subnet_id              = aws_subnet.public[0].id
  vpc_security_group_ids = [aws_security_group.web.id]
  key_name               = var.key_name
  iam_instance_profile   = aws_iam_instance_profile.web.name
  user_data              = file("${path.module}/user_data.sh")

  root_block_device {
    volume_type = "gp3"
    volume_size = 20
    encrypted   = true
  }

  tags = { Name = "${local.name}-web" }
}
```
`user_data.sh`:
```bash
#!/bin/bash
dnf update -y
dnf install -y docker nginx
systemctl enable --now docker nginx
```

### main.tf: RDS in private subnets
```hcl
resource "aws_db_subnet_group" "db" {
  name       = "${local.name}-db-subnets"
  subnet_ids = aws_subnet.private[*].id
}

resource "aws_db_instance" "postgres" {
  identifier              = "${local.name}-db"
  engine                  = "postgres"
  engine_version          = "16"
  instance_class          = "db.t3.micro"
  allocated_storage       = 20
  db_name                 = "appdb"
  username                = var.db_username
  password                = var.db_password
  db_subnet_group_name    = aws_db_subnet_group.db.name
  vpc_security_group_ids  = [aws_security_group.db.id]
  publicly_accessible     = false
  storage_encrypted       = true
  multi_az                = var.environment == "prod"
  backup_retention_period = 7
  skip_final_snapshot     = var.environment != "prod"
  deletion_protection     = var.environment == "prod"
}
```
To avoid a password variable, use `manage_master_user_password = true` and remove `password`. AWS then stores and rotates the password in Secrets Manager.

### outputs.tf
```hcl
output "web_public_ip" {
  value = aws_instance.web.public_ip
}

output "db_endpoint" {
  value = aws_db_instance.postgres.address
}

output "vpc_id" {
  value = aws_vpc.main.id
}

output "private_subnet_ids" {
  value = aws_subnet.private[*].id
}
```

### terraform.tfvars and running it
```hcl
environment = "dev"
key_name    = "my-keypair"
my_ip_cidr  = "203.0.113.10/32"
```
```bash
export TF_VAR_db_password='use-a-long-random-password'
terraform init
terraform fmt && terraform validate
terraform plan -out=tfplan
terraform apply tfplan
terraform output
terraform destroy          # clean up to avoid charges
```
Read the plan summary: `Plan: 18 to add, 0 to change, 0 to destroy`. Check for unexpected `destroy` or `-/+` (replace).

### `for_each` example: several hardened S3 buckets
```hcl
variable "buckets" {
  type    = set(string)
  default = ["uploads", "logs"]
}

resource "aws_s3_bucket" "this" {
  for_each = var.buckets
  bucket   = "${local.name}-${each.key}"
}

resource "aws_s3_bucket_public_access_block" "this" {
  for_each                = aws_s3_bucket.this
  bucket                  = each.value.id
  block_public_acls       = true
  block_public_policy     = true
  ignore_public_acls      = true
  restrict_public_buckets = true
}

resource "aws_s3_bucket_versioning" "this" {
  for_each = aws_s3_bucket.this
  bucket   = each.value.id
  versioning_configuration { status = "Enabled" }
}

resource "aws_s3_bucket_server_side_encryption_configuration" "this" {
  for_each = aws_s3_bucket.this
  bucket   = each.value.id
  rule {
    apply_server_side_encryption_by_default { sse_algorithm = "AES256" }
  }
}
```

### Next level: ALB and Auto Scaling group
```hcl
resource "aws_lb" "app" {
  name               = "${local.name}-alb"
  load_balancer_type = "application"
  subnets            = aws_subnet.public[*].id
  security_groups    = [aws_security_group.web.id]
}

resource "aws_lb_target_group" "app" {
  name     = "${local.name}-tg"
  port     = 3000
  protocol = "HTTP"
  vpc_id   = aws_vpc.main.id
  health_check {
    path                = "/health"
    healthy_threshold   = 2
    unhealthy_threshold = 3
    interval            = 15
  }
}

resource "aws_lb_listener" "http" {
  load_balancer_arn = aws_lb.app.arn
  port              = 80
  protocol          = "HTTP"
  default_action {
    type             = "forward"
    target_group_arn = aws_lb_target_group.app.arn
  }
}

resource "aws_launch_template" "app" {
  name_prefix            = "${local.name}-lt-"
  image_id               = data.aws_ami.al2023.id
  instance_type          = var.instance_type
  vpc_security_group_ids = [aws_security_group.web.id]
  user_data              = base64encode(file("${path.module}/user_data.sh"))
}

resource "aws_autoscaling_group" "app" {
  name                = "${local.name}-asg"
  min_size            = 2
  desired_capacity    = 2
  max_size            = 4
  vpc_zone_identifier = aws_subnet.private[*].id
  target_group_arns   = [aws_lb_target_group.app.arn]
  health_check_type   = "ELB"
  launch_template {
    id      = aws_launch_template.app.id
    version = "$Latest"
  }
}

resource "aws_autoscaling_policy" "cpu" {
  name                   = "${local.name}-cpu-target"
  autoscaling_group_name = aws_autoscaling_group.app.name
  policy_type            = "TargetTrackingScaling"
  target_tracking_configuration {
    predefined_metric_specification { predefined_metric_type = "ASGAverageCPUUtilization" }
    target_value = 60
  }
}
```
(For real use put the ASG in private subnets with a NAT gateway or VPC endpoints, and give the ALB its own security group.)

### Using a module
```hcl
module "vpc" {
  source  = "terraform-aws-modules/vpc/aws"
  version = "~> 5.0"

  name = "${local.name}-vpc"
  cidr = "10.0.0.0/16"
  azs             = ["ap-south-1a", "ap-south-1b"]
  public_subnets  = ["10.0.1.0/24", "10.0.2.0/24"]
  private_subnets = ["10.0.11.0/24", "10.0.12.0/24"]
  enable_nat_gateway = true
  single_nat_gateway = true                      # one NAT to save cost in non-prod
}
# use outputs: module.vpc.vpc_id, module.vpc.private_subnets
```
Writing your own module: put resources in `modules/vpc/` with `variables.tf` and `outputs.tf`, then `module "vpc" { source = "./modules/vpc"  cidr = "10.0.0.0/16" }`.

### Handy commands
```bash
terraform state list
terraform state show aws_instance.web
terraform state mv aws_instance.web aws_instance.app      # rename without destroying
terraform import aws_s3_bucket.logs existing-bucket-name
terraform apply -replace="aws_instance.web"
terraform workspace new staging; terraform workspace select dev; terraform workspace list
terraform apply -var-file=prod.tfvars
terraform force-unlock <LOCK_ID>                          # only if you are sure no apply is running
terraform graph | dot -Tpng > graph.png
```

### CI for Terraform (GitHub Actions idea)
```yaml
name: terraform
on:
  pull_request:
    paths: ["infra/**"]
jobs:
  plan:
    runs-on: ubuntu-latest
    permissions: { id-token: write, contents: read }
    steps:
      - uses: actions/checkout@v4
      - uses: hashicorp/setup-terraform@v3
      - uses: aws-actions/configure-aws-credentials@v4
        with:
          role-to-assume: arn:aws:iam::123456789012:role/terraform-ci
          aws-region: ap-south-1
      - run: terraform -chdir=infra init
      - run: terraform -chdir=infra fmt -check
      - run: terraform -chdir=infra validate
      - run: terraform -chdir=infra plan -no-color
```
`apply` runs only after merge to `main` (with an approval step for production).

---

## 11. Ansible

### Q1. What is Ansible?
An agentless configuration management and automation tool. It connects over SSH (no software needed on the servers) and runs tasks from YAML **playbooks** to install packages, copy files, manage services, and deploy applications.

### Q2. Key terms
- **Inventory:** the list of servers (static file or dynamic from AWS).
- **Playbook:** YAML file with plays, each targeting hosts and listing tasks.
- **Task:** one action using a **module** (`apt`, `package`, `copy`, `template`, `service`, `command`, `git`, `docker_container`).
- **Role:** a reusable, structured bundle of tasks, templates and variables.
- **Handler:** a task that runs only when notified by a change (restart Nginx when its config changed).
- **Facts:** information gathered about each host.
- **Vault:** encrypts secrets used in playbooks.

### Q3. Idempotency
Running a playbook many times gives the same result: if Nginx is already installed, the task reports `ok` and changes nothing. Use modules (`package`, `service`) rather than raw `shell` commands because modules are idempotent.

### Q4. Inventory and a playbook
```ini
# inventory.ini
[web]
10.0.1.10 ansible_user=ec2-user ansible_ssh_private_key_file=~/.ssh/key.pem
10.0.1.11 ansible_user=ec2-user ansible_ssh_private_key_file=~/.ssh/key.pem

[db]
10.0.11.20 ansible_user=ec2-user
```
```yaml
# site.yml
- name: Configure web servers
  hosts: web
  become: true                       # run as root through sudo
  vars:
    app_port: 3000
  tasks:
    - name: Install Nginx
      ansible.builtin.package:
        name: nginx
        state: present

    - name: Copy Nginx config from a template
      ansible.builtin.template:
        src: app.conf.j2
        dest: /etc/nginx/conf.d/app.conf
        mode: "0644"
      notify: Restart Nginx           # triggers the handler only if the file changed

    - name: Make sure Nginx is running and enabled
      ansible.builtin.service:
        name: nginx
        state: started
        enabled: true

    - name: Run the app container
      community.docker.docker_container:
        name: myapp
        image: myrepo/myapp:1.0
        state: started
        restart_policy: always
        published_ports: ["{{ app_port }}:3000"]

  handlers:
    - name: Restart Nginx
      ansible.builtin.service:
        name: nginx
        state: restarted
```
```bash
ansible all -i inventory.ini -m ping                    # test connectivity
ansible-playbook -i inventory.ini site.yml --check      # dry run
ansible-playbook -i inventory.ini site.yml              # run for real
ansible-playbook -i inventory.ini site.yml --limit web --tags nginx
```

### Q5. Roles and structure
```
roles/nginx/
  tasks/main.yml
  handlers/main.yml
  templates/app.conf.j2
  defaults/main.yml       (default variables)
  vars/main.yml
```
`ansible-galaxy init nginx` creates the structure. Use roles to keep playbooks small and reusable.

### Q6. Ansible Vault
`ansible-vault encrypt secrets.yml`, `ansible-playbook site.yml --ask-vault-pass`. Keeps passwords and keys out of plain text in Git. Better: fetch secrets from AWS Secrets Manager at run time.

### Q7. Ansible vs Terraform?
Terraform **creates** infrastructure (declarative, keeps state). Ansible **configures** servers (procedural tasks, agentless, no state file). They complement each other: Terraform outputs the IP addresses, Ansible configures those hosts.

### Q8. Push vs pull configuration management?
Ansible is **push** (the control machine connects to servers). Puppet and Chef are traditionally **pull** (agents on the servers fetch configuration).

### Q9. How do you deploy an application with Ansible?
Playbook tasks: pull the new image or code, stop the old service, start the new one, run a health check (`uri` module), and roll back if it fails. Use `serial: 1` to update servers one at a time (rolling update).
```yaml
- hosts: web
  serial: 1
  tasks:
    - name: Wait for health check
      ansible.builtin.uri:
        url: http://localhost:3000/health
        status_code: 200
      register: result
      until: result.status == 200
      retries: 10
      delay: 3
```

### Q10. Dynamic inventory for AWS
Use the `amazon.aws.aws_ec2` inventory plugin to discover EC2 instances by tags, so the inventory is never out of date.

### Q11. Common ad hoc commands
`ansible web -i inventory.ini -m shell -a "uptime"`, `-m copy -a "src=a dest=/tmp/a"`, `-m service -a "name=nginx state=restarted" --become`.

---
## 12. Docker concepts

### Q1. What is Docker? Why use containers?
Docker packages an application with everything it needs (runtime, libraries, config) into a **container** that runs the same way on any machine. It removes "it works on my machine" problems, makes deployments fast and repeatable, and uses fewer resources than virtual machines.

### Q2. Container vs virtual machine?
A VM includes a full guest operating system on top of a hypervisor (heavy, minutes to start, strong isolation). A container shares the host's kernel and isolates only the process (light, seconds to start, less isolation). Containers are not a security boundary as strong as VMs.

### Q3. Image vs container?
An **image** is a read-only template made of layers (like a class). A **container** is a running instance of an image (like an object). One image can run many containers.

### Q4. Image layers and build cache
Each Dockerfile instruction creates a layer. Docker caches layers and reuses them if the instruction and its inputs did not change. If one layer changes, all later layers are rebuilt. So put things that change rarely (installing dependencies) **before** things that change often (copying source code).
```dockerfile
COPY package*.json ./     # changes rarely, cached
RUN npm ci                # cached unless package files changed
COPY . .                  # changes often, goes last
```

### Q5. Main Dockerfile instructions
| Instruction | Purpose |
|---|---|
| `FROM` | base image (`FROM node:20-alpine`) |
| `WORKDIR` | working directory |
| `COPY` / `ADD` | copy files in (prefer `COPY`, `ADD` also extracts archives and downloads URLs) |
| `RUN` | run a command at **build** time (install packages) |
| `ENV` | environment variable (available at build and run) |
| `ARG` | build-time variable only |
| `EXPOSE` | documents the port (does not publish it) |
| `USER` | run as a non-root user |
| `CMD` | default command at **run** time (easy to override) |
| `ENTRYPOINT` | the fixed executable (arguments from `CMD` or the command line are added) |
| `HEALTHCHECK` | how Docker checks the container is healthy |

### Q6. `CMD` vs `ENTRYPOINT`?
`CMD` provides the default command and can be replaced by `docker run image other-command`. `ENTRYPOINT` sets the executable that always runs, and `CMD` becomes its default arguments. Use the exec form (`["node", "server.js"]`) so signals like SIGTERM reach your app and it shuts down gracefully.

### Q7. What is a multi-stage build? Why?
Several `FROM` stages in one Dockerfile: build in one stage (with compilers and dev dependencies), then copy only the output into a small final stage. The final image is much smaller and has less attack surface.

### Q8. What is `.dockerignore`?
Lists files not sent to the build context (`node_modules`, `.git`, `.env`, `*.log`, `Dockerfile`). Makes builds faster, images smaller and keeps secrets out.

### Q9. How do you keep images small and secure?
Use small base images (`alpine`, `slim`, or distroless), multi-stage builds, install only what is needed, combine related `RUN` commands, `npm ci --omit=dev`, `pip install --no-cache-dir`, run as a non-root user, pin versions (not `latest`), scan images (`trivy`, ECR scanning), and never bake secrets into images.

### Q10. Volumes vs bind mounts vs tmpfs
**Volumes** are managed by Docker and persist data beyond a container's life (databases). **Bind mounts** map a host folder into the container (development, live code reload). **tmpfs** stores data in memory only. Container filesystems are ephemeral, so anything important must be in a volume or external storage.

### Q11. Docker networking
Default **bridge** network: containers get private IPs and reach the internet through NAT. Containers on the same user-defined network resolve each other **by name** (service name in Compose). `-p 8080:3000` publishes host port 8080 to container port 3000. Other drivers: `host` (shares the host network) and `none`.

### Q12. What is Docker Compose?
A tool to define and run multi-container applications in one YAML file (app, database, cache), with networks and volumes, started with `docker compose up`. Good for local development and simple deployments.

### Q13. Docker registry
Stores and distributes images: Docker Hub, **Amazon ECR**, GitHub Container Registry. You `docker push` to it and the servers `docker pull` from it. Tag images clearly (version or git commit SHA), not only `latest`.

### Q14. Useful commands
```bash
docker build -t myapp:1.0 .            # build an image
docker run -d --name api -p 3000:3000 --env-file .env --restart unless-stopped myapp:1.0
docker ps; docker ps -a                # running containers; all containers
docker logs -f api                     # follow logs
docker exec -it api sh                 # open a shell in the container
docker stop api; docker rm api; docker rmi myapp:1.0
docker images; docker image prune; docker system prune -a    # cleanup
docker inspect api; docker stats       # details; live CPU and memory
docker volume ls; docker network ls
docker compose up -d --build; docker compose down; docker compose logs -f api
```

### Q15. A container keeps restarting or exits immediately. How do you debug?
`docker ps -a` (exit code), `docker logs <container>` (the error), check the `CMD`, environment variables and ports, run it interactively (`docker run -it --entrypoint sh image`), and check resource limits (exit code 137 often means it was killed for using too much memory, OOM). Make sure the process runs in the **foreground** (a container stops when its main process exits).

### Q16. How do you pass configuration and secrets to containers?
Environment variables (`-e`, `--env-file`, Compose `environment`), mounted config files, or at runtime from a secret manager (ECS task definitions reference Secrets Manager or SSM, Kubernetes Secrets). Do not put secrets in the Dockerfile or image layers because anyone with the image can read them.

### Q17. How do you shut a container down gracefully?
Docker sends SIGTERM, waits 10 seconds (default), then SIGKILL. Handle SIGTERM in the app (stop accepting requests, finish current work, close connections). Use the exec form for `CMD` so your app receives the signal, not a shell.

### Q18. Docker resource limits
`docker run --memory=512m --cpus=1`. In Kubernetes and ECS use requests and limits. Without limits, one container can starve the others.

### Q19. Docker health checks
```dockerfile
HEALTHCHECK --interval=30s --timeout=3s --retries=3 CMD wget -qO- http://localhost:3000/health || exit 1
```
Orchestrators use health status to restart or stop sending traffic to a container.

### Q20. How do you run databases in Docker?
Fine for development with a named volume. In production use a managed service (RDS) unless you have a clear reason, because databases need backups, upgrades and high availability handling.

---

## 13. Docker code

### React (Vite) app, multi-stage with Nginx
```dockerfile
# ---- build stage ----
FROM node:20-alpine AS build
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
ARG VITE_API_URL
ENV VITE_API_URL=$VITE_API_URL
RUN npm run build

# ---- run stage ----
FROM nginx:1.27-alpine
COPY --from=build /app/dist /usr/share/nginx/html
COPY nginx.conf /etc/nginx/conf.d/default.conf
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```
`nginx.conf` (single page app routing):
```nginx
server {
  listen 80;
  root /usr/share/nginx/html;
  index index.html;
  location / {
    try_files $uri /index.html;          # client-side routes return index.html
  }
  location /assets/ {
    expires 1y;
    add_header Cache-Control "public, immutable";
  }
}
```
`VITE_` variables are baked in at **build time**, so a different API URL needs a rebuild (or runtime configuration).

### Node.js (Express) app
```dockerfile
FROM node:20-alpine AS deps
WORKDIR /app
COPY package*.json ./
RUN npm ci --omit=dev

FROM node:20-alpine
ENV NODE_ENV=production
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .
USER node                                  # non-root user that exists in the official image
EXPOSE 3000
HEALTHCHECK --interval=30s --timeout=3s CMD wget -qO- http://localhost:3000/health || exit 1
CMD ["node", "src/server.js"]
```

### FastAPI app
```dockerfile
FROM python:3.12-slim
ENV PYTHONDONTWRITEBYTECODE=1 PYTHONUNBUFFERED=1
WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .
RUN adduser --disabled-password --gecos "" appuser && chown -R appuser /app
USER appuser

EXPOSE 8000
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
```
For OCR add system packages before the pip install: `RUN apt-get update && apt-get install -y --no-install-recommends tesseract-ocr poppler-utils && rm -rf /var/lib/apt/lists/*`.

### `.dockerignore`
```
node_modules
.git
.env
*.log
dist
__pycache__
.venv
```

### docker-compose.yml: frontend, API, database, cache
```yaml
services:
  web:
    build:
      context: ./frontend
      args:
        VITE_API_URL: http://localhost:8000
    ports: ["80:80"]
    depends_on: [api]

  api:
    build: ./backend
    ports: ["8000:8000"]
    env_file: ./backend/.env
    environment:
      DATABASE_URL: postgresql+psycopg2://app:secret@db:5432/appdb
      REDIS_URL: redis://cache:6379/0
    depends_on:
      db:
        condition: service_healthy
      cache:
        condition: service_started
    restart: unless-stopped

  db:
    image: postgres:16
    environment:
      POSTGRES_USER: app
      POSTGRES_PASSWORD: secret
      POSTGRES_DB: appdb
    volumes: ["pgdata:/var/lib/postgresql/data"]
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U app -d appdb"]
      interval: 5s
      timeout: 3s
      retries: 10

  cache:
    image: redis:7-alpine

volumes:
  pgdata:
```
`depends_on` with `condition: service_healthy` waits until the database is actually ready, not just started.

### Push an image to Amazon ECR
```bash
aws ecr create-repository --repository-name myapp --region ap-south-1
aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 123456789012.dkr.ecr.ap-south-1.amazonaws.com

docker build -t myapp:1.0 .
docker tag myapp:1.0 123456789012.dkr.ecr.ap-south-1.amazonaws.com/myapp:1.0
docker push 123456789012.dkr.ecr.ap-south-1.amazonaws.com/myapp:1.0
```
Pull it on EC2 with an instance role that allows `ecr:GetAuthorizationToken` and `ecr:BatchGetImage`.

### Deploy a container on EC2 (simple)
```bash
docker pull 123456789012.dkr.ecr.ap-south-1.amazonaws.com/myapp:1.0
docker stop api || true && docker rm api || true
docker run -d --name api --restart unless-stopped -p 3000:3000 --env-file /etc/myapp.env \
  123456789012.dkr.ecr.ap-south-1.amazonaws.com/myapp:1.0
curl -f http://localhost:3000/health
```

---

## 14. Kubernetes fundamentals

Your resume says "Kubernetes (fundamentals)", so know these concepts clearly and be honest about depth.

### Q1. What is Kubernetes? Why use it?
An open-source container orchestrator. It schedules containers across many machines, restarts failed ones, scales them, rolls out updates without downtime, and handles service discovery and load balancing. Use it when you run many containers or services and need self-healing and scaling. For a small app, ECS, a single EC2 or even Compose can be simpler.

### Q2. Architecture
- **Control plane:** **API server** (the front door, all kubectl calls go here), **etcd** (key-value store holding cluster state), **scheduler** (decides which node runs a pod), **controller manager** (runs control loops that make actual state match desired state).
- **Worker nodes:** **kubelet** (agent that runs pods and reports status), **kube-proxy** (networking rules for services), **container runtime** (containerd).
On EKS, AWS manages the control plane.

### Q3. Main objects
- **Pod:** the smallest unit, one or more containers sharing network and storage. Pods are temporary.
- **ReplicaSet:** keeps a set number of pod replicas running.
- **Deployment:** manages ReplicaSets, gives rolling updates and rollback. Use this for stateless apps.
- **Service:** a stable address and load balancing for a set of pods (selected by labels). Types: **ClusterIP** (internal only, default), **NodePort** (opens a port on every node), **LoadBalancer** (creates a cloud load balancer).
- **Ingress:** HTTP routing rules (host and path) to services, needs an ingress controller (NGINX, AWS Load Balancer Controller).
- **ConfigMap:** non-secret configuration. **Secret:** sensitive values (base64 encoded, **not encrypted by default**, so use RBAC and encryption at rest or external secrets).
- **Namespace:** virtual cluster to separate environments or teams.
- **PersistentVolume (PV) and PersistentVolumeClaim (PVC):** storage that outlives pods.
- **StatefulSet:** for stateful apps needing stable identity and storage (databases). **DaemonSet:** one pod per node (log agents, node exporter). **Job and CronJob:** run-to-completion and scheduled tasks.
- **HPA (Horizontal Pod Autoscaler):** scales replicas by CPU, memory or custom metrics.

### Q4. Pod vs container? Why are pods ephemeral?
A pod wraps containers. If a pod dies it is replaced by a new pod with a **new IP**, which is why you talk to pods through a **Service** and not directly.

### Q5. How does a Service find pods?
By **labels and selectors**. The Deployment labels its pods (`app: api`), and the Service selects `app: api`. Kubernetes keeps the endpoint list updated as pods come and go. Inside the cluster you reach it by DNS name (`api-service.namespace.svc.cluster.local`).

### Q6. Liveness vs readiness vs startup probes
**Readiness:** is the pod ready to receive traffic? If it fails, the pod is removed from the Service endpoints (not restarted). **Liveness:** is the process still healthy? If it fails, the container is restarted. **Startup:** gives slow-starting apps time before the other probes begin.

### Q7. Requests vs limits
**Requests** are the resources the scheduler reserves for the pod (used for placement). **Limits** are the maximum allowed. Exceeding the memory limit gets the container killed (`OOMKilled`), exceeding the CPU limit slows it down (throttling). Always set both.

### Q8. Rolling update and rollback
A Deployment replaces pods gradually (`maxSurge`, `maxUnavailable`), only moving on when new pods are ready, so there is no downtime. `kubectl rollout status deployment/api`, `kubectl rollout undo deployment/api` returns to the previous version.

### Q9. YAML: Deployment, Service, ConfigMap, Secret
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: api
  labels: { app: api }
spec:
  replicas: 3
  selector:
    matchLabels: { app: api }
  strategy:
    type: RollingUpdate
    rollingUpdate: { maxSurge: 1, maxUnavailable: 0 }
  template:
    metadata:
      labels: { app: api }
    spec:
      containers:
        - name: api
          image: 123456789012.dkr.ecr.ap-south-1.amazonaws.com/myapp:1.0
          ports:
            - containerPort: 3000
          envFrom:
            - configMapRef: { name: api-config }
            - secretRef: { name: api-secret }
          resources:
            requests: { cpu: "100m", memory: "128Mi" }
            limits: { cpu: "500m", memory: "256Mi" }
          readinessProbe:
            httpGet: { path: /health, port: 3000 }
            initialDelaySeconds: 5
            periodSeconds: 10
          livenessProbe:
            httpGet: { path: /health, port: 3000 }
            initialDelaySeconds: 15
            periodSeconds: 20
---
apiVersion: v1
kind: Service
metadata:
  name: api-service
spec:
  type: ClusterIP
  selector: { app: api }
  ports:
    - port: 80
      targetPort: 3000
---
apiVersion: v1
kind: ConfigMap
metadata:
  name: api-config
data:
  NODE_ENV: production
  LOG_LEVEL: info
---
apiVersion: v1
kind: Secret
metadata:
  name: api-secret
type: Opaque
stringData:                  # plain text here, stored base64 encoded
  DATABASE_URL: postgresql://app:secret@db:5432/appdb
```

### Q10. Ingress and HPA
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: api-ingress
  annotations:
    nginx.ingress.kubernetes.io/ssl-redirect: "true"
spec:
  ingressClassName: nginx
  rules:
    - host: api.example.com
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service: { name: api-service, port: { number: 80 } }
---
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: api-hpa
spec:
  scaleTargetRef: { apiVersion: apps/v1, kind: Deployment, name: api }
  minReplicas: 2
  maxReplicas: 10
  metrics:
    - type: Resource
      resource:
        name: cpu
        target: { type: Utilization, averageUtilization: 60 }
```
HPA needs the metrics server and CPU requests set on the pods.

### Q11. kubectl commands
```bash
kubectl get pods -o wide; kubectl get deploy,svc,ingress; kubectl get all -n staging
kubectl describe pod api-xxxx          # events at the bottom explain most problems
kubectl logs -f api-xxxx; kubectl logs api-xxxx --previous    # logs of the crashed container
kubectl exec -it api-xxxx -- sh
kubectl apply -f deployment.yaml; kubectl delete -f deployment.yaml
kubectl scale deployment api --replicas=5
kubectl rollout status deployment/api; kubectl rollout undo deployment/api; kubectl rollout history deployment/api
kubectl port-forward svc/api-service 8080:80
kubectl top pods; kubectl config get-contexts; kubectl config use-context dev
kubectl create namespace staging
```

### Q12. Pod troubleshooting: common statuses
| Status | Meaning and what to check |
|---|---|
| `Pending` | cannot be scheduled: not enough CPU or memory on nodes, unbound PVC, node selector or taint problems. `kubectl describe pod` |
| `ImagePullBackOff` / `ErrImagePull` | wrong image name or tag, no permission to the registry (missing image pull secret or IAM) |
| `CrashLoopBackOff` | the container starts and crashes repeatedly: read `kubectl logs --previous`, check env variables, config, command and probes |
| `OOMKilled` | memory limit exceeded: raise the limit or fix the memory use |
| `Running` but not working | readiness probe failing, wrong Service selector or port, check endpoints (`kubectl get endpoints`) |

### Q13. Other topics to know by name
**Helm** (package manager for Kubernetes, charts with templated YAML), **RBAC** (roles and bindings controlling who can do what), **NetworkPolicy** (pod-level firewall), **taints and tolerations** and **node affinity** (placement rules), **Kustomize**, **GitOps** with ArgoCD or Flux (Git is the source of truth, the cluster syncs to it), **service mesh** (Istio), **cluster autoscaler** and **Karpenter** (add and remove nodes), **EKS** (managed control plane, node groups or Fargate).

### Q14. Kubernetes vs Docker Swarm vs ECS?
Kubernetes is the industry standard with the biggest ecosystem but more complexity. Docker Swarm is simpler but less used. ECS is AWS's simpler container service, tightly integrated with AWS and good when you only run on AWS.

---

## 15. CI/CD: Jenkins, GitHub Actions, CodeDeploy, Maven, SonarQube

### Q1. What is a CI/CD pipeline? Typical stages?
An automated path from code commit to running software. Stages: **checkout, build, unit tests, code quality scan, package (Docker image), push to registry, deploy to staging, integration tests, approval, deploy to production, smoke tests, monitor**. A failing stage stops the pipeline.

### Q2. Why CI/CD?
Faster feedback, fewer manual errors, repeatable releases, smaller and safer changes, and an audit trail.

### Jenkins
**Q3. Jenkins architecture?**
A **controller** (web UI, schedules jobs, stores config) and **agents** (nodes that run the builds, often containers or EC2 instances). **Plugins** add features (Git, Docker, Pipeline, SonarQube, AWS). Jobs run as **Pipelines** defined in a `Jenkinsfile` stored in the repository (pipeline as code).

**Q4. Declarative vs scripted pipeline?**
Declarative has a fixed, readable structure (`pipeline { stages { ... } }`) and is recommended. Scripted is plain Groovy with more flexibility and more complexity.

**Q5. A Jenkinsfile for a Node.js app (build, test, scan, image, push, deploy)**
```groovy
pipeline {
  agent any

  environment {
    AWS_REGION = 'ap-south-1'
    ECR_REPO   = '123456789012.dkr.ecr.ap-south-1.amazonaws.com/myapp'
    IMAGE_TAG  = "${env.GIT_COMMIT.take(7)}"
  }

  options {
    timeout(time: 30, unit: 'MINUTES')
    disableConcurrentBuilds()
  }

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }
    stage('Install') {
      steps { sh 'npm ci' }
    }
    stage('Test') {
      steps { sh 'npm test' }
      post { always { junit allowEmptyResults: true, testResults: 'reports/junit.xml' } }
    }
    stage('SonarQube analysis') {
      steps {
        withSonarQubeEnv('sonarqube') { sh 'npx sonar-scanner' }
      }
    }
    stage('Quality gate') {
      steps {
        timeout(time: 5, unit: 'MINUTES') { waitForQualityGate abortPipeline: true }
      }
    }
    stage('Build and push image') {
      steps {
        sh '''
          aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin ${ECR_REPO%%/*}
          docker build -t $ECR_REPO:$IMAGE_TAG .
          docker push $ECR_REPO:$IMAGE_TAG
        '''
      }
    }
    stage('Deploy') {
      when { branch 'main' }
      steps {
        sh './scripts/deploy.sh $IMAGE_TAG'
      }
    }
  }

  post {
    success { echo "Deployed ${IMAGE_TAG}" }
    failure { echo 'Pipeline failed' }                // add Slack or email notification here
    always  { cleanWs() }
  }
}
```
**Explain:** stages run in order and stop on failure, the image tag is the commit hash so every build is traceable and rollback means deploying an older tag, credentials come from the Jenkins credential store or the agent's IAM role (never written in the file), and the deploy stage runs only for `main`.

**Q6. How does Jenkins authenticate to AWS?**
Best: the Jenkins agent runs on EC2 with an **IAM instance role**. Otherwise use the Jenkins Credentials store with `withCredentials`, never plain text in the Jenkinsfile.

**Q7. How is a pipeline triggered?**
GitHub webhook (push or pull request), polling (less efficient), scheduled builds (cron syntax), or manually. Multibranch pipelines create a job per branch automatically.

**Q8. How do you speed up Jenkins pipelines?**
Parallel stages (`parallel { ... }`), cache dependencies, use Docker layer caching, run on dedicated agents, split slow tests, and skip unnecessary stages with `when`.

**Q9. Jenkins credentials and security?**
Store secrets in the credentials store, restrict who can configure jobs, use role-based access control, keep plugins updated, and run builds on agents, not on the controller.

### Maven and SonarQube (appear on many Java style pipelines)
**Q10. What is Maven? Build lifecycle?**
A Java build and dependency management tool configured by `pom.xml`. Lifecycle phases: `validate`, `compile`, `test`, `package` (creates the JAR or WAR), `verify`, `install` (to the local repository), `deploy` (to a remote repository). `mvn clean package` removes old output and builds the artifact. `-DskipTests` skips tests.
(For Node.js the equivalents are `npm ci`, `npm test`, `npm run build`, and for Python `pip install`, `pytest`.)

**Q11. What is SonarQube? Quality gate?**
A static analysis platform that scans code for bugs, vulnerabilities, code smells, duplication and test coverage. A **quality gate** is a pass or fail rule (for example "no new critical issues and coverage above 80% on new code"). If the gate fails, the pipeline stops, so poor code is not deployed.

### GitHub Actions
**Q12. What is GitHub Actions? Key terms?**
GitHub's built-in CI/CD. A **workflow** (YAML in `.github/workflows/`) is triggered by **events** (push, pull request, schedule), contains **jobs** that run on **runners**, and each job has **steps** that run commands or reusable **actions**.

**Q13. Workflow: test, build image, push to ECR using OIDC (no stored keys)**
```yaml
name: ci-cd
on:
  push:
    branches: [main]
  pull_request:

permissions:
  id-token: write          # needed for OIDC
  contents: read

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: { node-version: 20, cache: npm }
      - run: npm ci
      - run: npm test

  build-and-push:
    needs: test
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: aws-actions/configure-aws-credentials@v4
        with:
          role-to-assume: arn:aws:iam::123456789012:role/github-actions-deploy
          aws-region: ap-south-1
      - id: ecr
        uses: aws-actions/amazon-ecr-login@v2
      - name: Build and push
        env:
          REGISTRY: ${{ steps.ecr.outputs.registry }}
          TAG: ${{ github.sha }}
        run: |
          docker build -t $REGISTRY/myapp:$TAG .
          docker push $REGISTRY/myapp:$TAG

  deploy:
    needs: build-and-push
    runs-on: ubuntu-latest
    environment: production        # can require manual approval
    steps:
      - run: echo "Trigger deployment here (SSH, CodeDeploy, ECS update or kubectl)"
```
**OIDC** lets the workflow get **temporary AWS credentials** by assuming a role, so no long-lived access keys are stored as secrets. Use repository **secrets** for other sensitive values.

**Q14. Jenkins vs GitHub Actions?**
Jenkins is self-hosted, very flexible with a large plugin ecosystem, but you maintain the server and plugins. GitHub Actions is managed, tightly integrated with GitHub, easier to start, and costs per runner minute for private repositories (self-hosted runners are possible).

### AWS CodeDeploy
**Q15. What is CodeDeploy?**
A service that automates deploying applications to EC2, on-premises servers, Lambda or ECS. It supports in-place and blue-green deployments, health checks and automatic rollback.

**Q16. `appspec.yml` for EC2 and lifecycle hooks**
```yaml
version: 0.0
os: linux
files:
  - source: /
    destination: /var/www/myapp
hooks:
  ApplicationStop:
    - location: scripts/stop.sh
      timeout: 60
      runas: root
  BeforeInstall:
    - location: scripts/before_install.sh
      timeout: 120
      runas: root
  AfterInstall:
    - location: scripts/install_deps.sh
      timeout: 300
      runas: root
  ApplicationStart:
    - location: scripts/start.sh
      timeout: 60
      runas: root
  ValidateService:
    - location: scripts/validate.sh       # curl the health endpoint, exit 1 on failure to trigger rollback
      timeout: 60
      runas: root
```
The CodeDeploy **agent** must be running on the instance, and the instance needs an IAM role. Flow: the pipeline uploads the revision (zip) to S3, then CodeDeploy deploys to a **deployment group** (instances by tag or an Auto Scaling group). With an ALB, instances are taken out of rotation during their update (rolling), which gives no downtime.

### Deployment and release questions
- **How do you do a zero-downtime deployment on EC2?** Several instances behind an ALB, rolling update (CodeDeploy `OneAtATime` or half at a time), health checks and connection draining.
- **How do you roll back?** Redeploy the previous artifact or image tag (this is why tags use the commit hash), CodeDeploy automatic rollback on failed alarms or health checks, `kubectl rollout undo`.
- **How do you handle database migrations in a pipeline?** Run them as a separate, controlled step before the new version starts, keep them backwards compatible (add columns first, remove old ones later), and back up before risky changes.
- **How do you manage secrets in pipelines?** The CI secret store (Jenkins credentials, GitHub secrets), OIDC and IAM roles for cloud access, no secrets in logs (mask them), and no secrets in the repository.
- **What is an artifact repository?** A place to store build outputs (Docker images in ECR, packages in Nexus or Artifactory, zips in S3) so you deploy exactly what you tested.
- **What is GitOps?** The desired state of the system lives in Git. A tool (ArgoCD, Flux) continuously syncs the cluster to match, so deployment means "merge a change to Git", and rollback means "revert a commit".
- **Branch protection?** Require pull requests, reviews and passing CI before merging to `main`.
- **What is a feature flag?** Turning a feature on or off at runtime without redeploying, useful for gradual rollouts and safe releases.

---
## 16. Nginx and web servers

### Q1. What is Nginx? What do you use it for?
A high-performance web server and **reverse proxy**. Uses: serve static files (your React build), reverse proxy to a Node or FastAPI app, load balancing, TLS termination, gzip compression, caching, rate limiting, and redirects.

### Q2. Reverse proxy configuration for an API with WebSocket support
```nginx
server {
  listen 80;
  server_name api.example.com;

  location / {
    proxy_pass http://127.0.0.1:3000;
    proxy_http_version 1.1;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_set_header Upgrade $http_upgrade;             # WebSockets
    proxy_set_header Connection "upgrade";
    proxy_read_timeout 60s;
  }
}
```
Your app must trust the proxy headers (Express: `app.set("trust proxy", 1)`, Uvicorn: `--proxy-headers`) to see the real client IP.

### Q3. Load balancing with Nginx
```nginx
upstream api_servers {
  least_conn;                                  # or ip_hash; default is round robin
  server 10.0.1.10:3000 max_fails=3 fail_timeout=30s;
  server 10.0.1.11:3000;
  server 10.0.1.12:3000 backup;
}

server {
  listen 80;
  location / { proxy_pass http://api_servers; }
}
```

### Q4. Serve a React app and proxy `/api`
```nginx
server {
  listen 80;
  root /var/www/app/dist;
  index index.html;

  gzip on;
  gzip_types text/css application/javascript application/json image/svg+xml;

  location /api/ {
    proxy_pass http://127.0.0.1:3000/;
  }

  location / {
    try_files $uri /index.html;                # single page app fallback
  }

  location ~* \.(js|css|png|jpg|svg|woff2)$ {
    expires 1y;
    add_header Cache-Control "public, immutable";
  }
}
```

### Q5. HTTPS with Let's Encrypt (when not using an ALB)
```bash
sudo dnf install -y certbot python3-certbot-nginx      # or apt on Ubuntu
sudo certbot --nginx -d example.com -d www.example.com
sudo certbot renew --dry-run                           # certificates renew automatically via a timer
```
With an ALB or CloudFront, use **ACM** instead.

### Q6. Security headers and rate limiting
```nginx
http {
  limit_req_zone $binary_remote_addr zone=api_limit:10m rate=10r/s;
  server_tokens off;                                   # hide the Nginx version
}
server {
  add_header X-Content-Type-Options nosniff;
  add_header X-Frame-Options DENY;
  add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;
  location /api/ {
    limit_req zone=api_limit burst=20 nodelay;
    proxy_pass http://127.0.0.1:3000;
  }
}
```

### Q7. Common Nginx commands and troubleshooting
```bash
sudo nginx -t                         # test configuration (always before reloading)
sudo systemctl reload nginx           # apply config without dropping connections
sudo tail -f /var/log/nginx/error.log /var/log/nginx/access.log
```
`502 Bad Gateway`: the upstream app is down or on a different port. Check `ss -tulpn`, the app logs and the `proxy_pass` address. `504`: the app is too slow, raise `proxy_read_timeout` only after checking why. `413 Request Entity Too Large`: raise `client_max_body_size`. `403`: file permissions or an `allow/deny` rule.

### Q8. Nginx vs Apache?
Nginx uses an event-driven, asynchronous model: very good for many concurrent connections and static content, widely used as a reverse proxy. Apache uses processes or threads per connection (with modules) and has `.htaccess` flexibility. For modern apps Nginx is the common choice.

### Q9. Forward proxy vs reverse proxy, and where does PM2 fit?
A reverse proxy sits in front of your servers. **PM2** keeps the Node process alive and runs it in cluster mode, and Nginx sits in front of PM2 for TLS, compression and routing. This is the stack you described for the healthcare app.

---

## 17. Monitoring and logging

### Q1. Why monitor? What is observability?
To detect problems before users do, understand performance, plan capacity and investigate incidents. **Observability** rests on three pillars: **metrics** (numbers over time), **logs** (events), and **traces** (a request's path across services).

### Q2. How does Prometheus work?
Prometheus is a time-series database and monitoring system with a **pull model**: it **scrapes** the `/metrics` HTTP endpoint of your applications and exporters at an interval (for example every 15 seconds) and stores the data. You query with **PromQL**, and **Alertmanager** sends alerts (email, Slack, PagerDuty). **Exporters** expose metrics for things that do not have them (`node_exporter` for Linux hosts, `cadvisor` for containers, `postgres_exporter`).

### Q3. Metric types
- **Counter:** only goes up (total requests, errors). Use `rate()` to get a per-second rate.
- **Gauge:** goes up and down (memory in use, queue length).
- **Histogram:** counts observations in buckets (request durations), used to compute percentiles like p95.
- **Summary:** similar to a histogram but computes quantiles on the client.

### Q4. Prometheus configuration
```yaml
# prometheus.yml
global:
  scrape_interval: 15s

rule_files:
  - alerts.yml

alerting:
  alertmanagers:
    - static_configs:
        - targets: ["alertmanager:9093"]

scrape_configs:
  - job_name: node
    static_configs:
      - targets: ["node-exporter:9100"]
  - job_name: api
    metrics_path: /metrics
    static_configs:
      - targets: ["api:8000"]
```

### Q5. Useful PromQL queries
```
up                                                          # 1 if the target is reachable, 0 if down
rate(http_requests_total[5m])                               # requests per second over 5 minutes
sum(rate(http_requests_total{status=~"5.."}[5m]))
  / sum(rate(http_requests_total[5m]))                      # error ratio (adjust label names to your library)
histogram_quantile(0.95,
  sum(rate(http_request_duration_seconds_bucket[5m])) by (le))   # p95 latency
100 - (avg by (instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)   # CPU usage %
(1 - node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes) * 100            # memory usage %
(1 - node_filesystem_avail_bytes{mountpoint="/"} / node_filesystem_size_bytes{mountpoint="/"}) * 100   # disk %
```

### Q6. Alert rules
```yaml
# alerts.yml
groups:
  - name: api-alerts
    rules:
      - alert: InstanceDown
        expr: up == 0
        for: 2m
        labels: { severity: critical }
        annotations:
          summary: "{{ $labels.instance }} is down"

      - alert: HighErrorRate
        expr: sum(rate(http_requests_total{status=~"5.."}[5m])) / sum(rate(http_requests_total[5m])) > 0.05
        for: 5m
        labels: { severity: critical }
        annotations:
          summary: "More than 5% of requests are failing"

      - alert: HighMemory
        expr: (1 - node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes) > 0.9
        for: 10m
        labels: { severity: warning }
```
The `for:` clause avoids alerts on short spikes.

### Q7. Run Prometheus and Grafana with Docker Compose
```yaml
services:
  prometheus:
    image: prom/prometheus
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
      - ./alerts.yml:/etc/prometheus/alerts.yml
    ports: ["9090:9090"]
  node-exporter:
    image: prom/node-exporter
    ports: ["9100:9100"]
  grafana:
    image: grafana/grafana
    ports: ["3001:3000"]
    volumes: ["grafana-data:/var/lib/grafana"]
volumes:
  grafana-data:
```
In Grafana: add Prometheus as a data source (`http://prometheus:9090`), then import a dashboard (for example the "Node Exporter Full" dashboard) or build panels from PromQL queries.

### Q8. What is Grafana?
A visualization and alerting tool. It connects to data sources (Prometheus, CloudWatch, Loki, Elasticsearch, PostgreSQL) and shows dashboards. A good dashboard shows the **golden signals**: traffic, errors, latency and saturation.

### Q9. How do you add metrics to your app?
- **Node.js:** `prom-client` (default metrics plus custom counters and histograms), expose `GET /metrics`.
- **FastAPI:** `prometheus-fastapi-instrumentator` or `prometheus_client` with `Instrumentator().instrument(app).expose(app)`.
Add business metrics too: payments completed, documents processed, failures by status.

### Q10. CloudWatch vs Prometheus
CloudWatch is AWS-native: automatic metrics for AWS services, logs and alarms, no servers to run, pay per use. Prometheus is open source, strong for application and container metrics, flexible with PromQL, works across clouds and Kubernetes, but you run and scale it. Many teams use both (Grafana can read both).

### Q11. CloudWatch basics
- **Metrics:** CPU, network, disk operations by default. **Memory and disk usage need the CloudWatch agent.**
- **Alarm:** `CPUUtilization > 80% for 5 minutes` triggers an SNS notification or an Auto Scaling action.
- **Logs:** install the agent or use `awslogs` or the Docker `awslogs` driver. **Log groups** hold **log streams**. **Logs Insights** queries logs:
```
fields @timestamp, @message
| filter @message like /ERROR/
| sort @timestamp desc
| limit 50
```
- **Retention:** set it (log groups keep logs forever by default and cost grows).

### Q12. Logging best practices
Write logs to **stdout** (12-factor), use structured JSON logs with a request id, use levels correctly, never log secrets or personal data, centralize logs (CloudWatch Logs, ELK or EFK stack, Grafana Loki), set retention, and alert on error patterns.

### Q13. ELK / EFK stack
**Elasticsearch** stores and searches logs, **Logstash** (or **Fluentd/Fluent Bit**) collects and transforms them, **Kibana** visualizes them. Loki is a lighter alternative that integrates with Grafana.

### Q14. What makes a good alert?
Actionable (someone knows what to do), based on **symptoms users feel** (error rate, latency) rather than only causes (CPU), with a sensible threshold and duration, a severity, a runbook link, and routed to the right team. Too many alerts cause alert fatigue.

### Q15. What is an incident response process?
Detect (alert), triage and assign severity, communicate, mitigate first (rollback, scale, failover), find the root cause, fix, then write a **blameless postmortem** with actions to prevent a repeat.

### Q16. Uptime and synthetic checks
Probe your public endpoints from outside (Route 53 health checks, CloudWatch Synthetics, UptimeRobot, Prometheus `blackbox_exporter`) to check the real user path, including DNS, TLS and the load balancer.

---

## 18. Security in DevOps (DevSecOps)

### Q1. What is DevSecOps?
Building security into every stage of the pipeline ("shift left") instead of checking at the end: secure code review, dependency and image scanning in CI, secret scanning, infrastructure scanning, and runtime monitoring.

### Q2. Common security measures in a pipeline
- **SAST** (static code analysis, SonarQube, CodeQL) and **dependency scanning** (`npm audit`, `pip-audit`, Dependabot, Snyk).
- **Secret scanning** (gitleaks, GitHub secret scanning).
- **Container image scanning** (Trivy, ECR scanning).
- **IaC scanning** (tfsec or `trivy config`, checkov) to catch open security groups and public buckets before they exist.
- **DAST** and penetration tests against staging.
- **Signed images and an SBOM** (software bill of materials) for supply-chain security.

### Q3. How do you manage secrets?
Never in Git, images or logs. Use **AWS Secrets Manager or SSM Parameter Store** (read through IAM roles), **HashiCorp Vault**, or Kubernetes secrets with encryption and external secret operators. Rotate secrets, give each service its own least-privilege credentials, and scan repositories for leaks.

### Q4. A secret leaked in a public repository. What do you do?
1. **Revoke and rotate** the secret immediately (assume it is already stolen).
2. Check logs (CloudTrail for AWS keys) for misuse.
3. Remove it from history (`git filter-repo` or BFG) and from forks and caches where possible.
4. Add secret scanning and pre-commit hooks.
5. Write down what happened and fix the process (use roles instead of keys).

### Q5. Principle of least privilege
Give every user, role and service only the permissions it needs, and no more. For example a service that reads from one S3 bucket gets `s3:GetObject` on that bucket only, not `s3:*` on `*`. Review permissions regularly.

### Q6. How do you harden a Linux server?
Disable root SSH login and password login (use keys), restrict SSH by security group, keep the OS and packages updated, run services as non-root users, close unused ports, use a firewall, enable automatic security updates, install fail2ban for brute force protection, keep logs and monitor them, and use Session Manager instead of open SSH where possible.

### Q7. Container security basics
Minimal base images, non-root user, read-only filesystem where possible, no secrets in images, drop unneeded capabilities, scan and patch images regularly, pin versions, limit resources, and keep the registry private.

### Q8. Kubernetes security basics
RBAC with least privilege, NetworkPolicies, Secrets encryption at rest, pod security standards (no privileged containers), private clusters and endpoints, image scanning, and limiting who can use `kubectl exec`.

### Q9. How do you secure data in transit and at rest?
In transit: TLS everywhere (HTTPS, SSL for databases). At rest: KMS-backed encryption for EBS, S3, RDS and backups.

### Q10. Network security in AWS
Private subnets for apps and databases, security groups with minimal rules (referencing other security groups), NACLs as an extra layer, WAF for web attacks (SQL injection, rate limiting), Shield for DDoS, VPC endpoints, VPC flow logs, and no public database endpoints.

### Q11. What are IAM roles for service accounts and OIDC for CI?
Instead of storing long-lived access keys, the workload or CI job proves its identity with a signed token (OIDC) and **assumes an IAM role** to get temporary credentials. This removes keys that could leak.

### Q12. Common AWS security mistakes to avoid
Public S3 buckets, security groups open to `0.0.0.0/0` on SSH or database ports, root account keys, long-lived access keys in code, overly broad IAM policies (`*:*`), unencrypted storage, no MFA, and no logging (CloudTrail off).

---

## 19. Scripting: Bash and Python

### Bash script habits
```bash
#!/usr/bin/env bash
set -euo pipefail          # stop on errors (-e), unset variables (-u), failed pipes (-o pipefail)
```
Quote variables (`"$VAR"`), check commands succeeded, log with timestamps, and make scripts safe to run twice (idempotent).

### Health check and restart
```bash
#!/usr/bin/env bash
set -euo pipefail

URL="http://localhost:3000/health"
SERVICE="myapp"

if ! curl -fsS --max-time 5 "$URL" > /dev/null; then
  echo "$(date '+%F %T') health check failed, restarting $SERVICE" >> /var/log/healthcheck.log
  systemctl restart "$SERVICE"
fi
```
Run it from cron every minute: `* * * * * /opt/scripts/healthcheck.sh`.

### PostgreSQL backup to S3 with retention
```bash
#!/usr/bin/env bash
set -euo pipefail

DB_NAME="appdb"
BUCKET="s3://my-app-backups/postgres"
STAMP=$(date +%F_%H-%M)
FILE="/tmp/${DB_NAME}_${STAMP}.sql.gz"

pg_dump -h "$DB_HOST" -U "$DB_USER" "$DB_NAME" | gzip > "$FILE"       # password via PGPASSWORD or ~/.pgpass
aws s3 cp "$FILE" "$BUCKET/" --sse AES256
rm -f "$FILE"

echo "Backup $STAMP uploaded"
# keep only 14 days: set an S3 lifecycle rule instead of deleting files in the script
```

### Deploy script (pull a new image, replace the container, verify)
```bash
#!/usr/bin/env bash
set -euo pipefail

TAG="${1:?usage: deploy.sh <image-tag>}"
REPO="123456789012.dkr.ecr.ap-south-1.amazonaws.com/myapp"

aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin "${REPO%%/*}"
docker pull "$REPO:$TAG"

PREVIOUS=$(docker inspect --format '{{.Config.Image}}' api 2>/dev/null || true)

docker rm -f api 2>/dev/null || true
docker run -d --name api --restart unless-stopped -p 3000:3000 --env-file /etc/myapp.env "$REPO:$TAG"

for i in {1..10}; do
  if curl -fsS http://localhost:3000/health > /dev/null; then
    echo "Deployed $TAG"
    exit 0
  fi
  sleep 3
done

echo "Health check failed, rolling back to ${PREVIOUS:-none}"
docker rm -f api
[ -n "$PREVIOUS" ] && docker run -d --name api --restart unless-stopped -p 3000:3000 --env-file /etc/myapp.env "$PREVIOUS"
exit 1
```

### Useful one-liners
```bash
# Top 10 IPs hitting Nginx
awk '{print $1}' /var/log/nginx/access.log | sort | uniq -c | sort -rn | head
# Count 5xx responses
awk '$9 ~ /^5/' /var/log/nginx/access.log | wc -l
# Delete logs older than 7 days
find /var/log/myapp -name "*.log" -mtime +7 -delete
# Loop over servers
for host in web1 web2 web3; do ssh "$host" "uptime"; done
# Check whether a port is open
nc -zv db.internal 5432
```

### Bash basics that get asked
```bash
name="Siva"; echo "Hello $name"
if [ -f file.txt ]; then echo "exists"; fi              # -f file, -d directory, -z empty string
for f in *.log; do gzip "$f"; done
while read -r line; do echo "$line"; done < input.txt
$?   # exit code of the last command (0 = success)
$1 $2 $@ $#   # arguments, all arguments, argument count
```

### Python with boto3 (AWS automation)
```python
import boto3

ec2 = boto3.client("ec2", region_name="ap-south-1")

def running_instances():
    resp = ec2.describe_instances(Filters=[{"Name": "instance-state-name", "Values": ["running"]}])
    for reservation in resp["Reservations"]:
        for inst in reservation["Instances"]:
            name = next((t["Value"] for t in inst.get("Tags", []) if t["Key"] == "Name"), "-")
            print(inst["InstanceId"], inst["InstanceType"], name)

def stop_dev_instances():
    resp = ec2.describe_instances(Filters=[
        {"Name": "tag:Environment", "Values": ["dev"]},
        {"Name": "instance-state-name", "Values": ["running"]},
    ])
    ids = [i["InstanceId"] for r in resp["Reservations"] for i in r["Instances"]]
    if ids:
        ec2.stop_instances(InstanceIds=ids)             # save cost at night
    return ids

s3 = boto3.client("s3")
s3.upload_file("report.pdf", "my-bucket", "reports/report.pdf")
url = s3.generate_presigned_url("get_object", Params={"Bucket": "my-bucket", "Key": "reports/report.pdf"}, ExpiresIn=900)
```
Credentials come from the environment, the instance role or the AWS profile, not from the code. A cost-saving Lambda on a schedule (EventBridge) can run the stop-dev-instances function every evening.

---

## 20. Scenario and troubleshooting questions

For each: **gather facts, narrow down, fix, prevent.**

**S1. The website is down. What do you check?**
1. Is it down for everyone? Check from outside (`curl -I https://site`, an uptime monitor), and check DNS (`dig`).
2. Load balancer: target group health, are targets healthy? ALB access and error metrics.
3. Instances or containers: running? `systemctl status`, `docker ps`, `kubectl get pods`.
4. App logs and recent deployments (did something change? roll back if the cause is a deployment).
5. Dependencies: database reachable, disk full, out of memory, expired certificate.
6. Fix, then write a short postmortem and add monitoring or alerts for the cause.

**S2. A deployment failed in production. What do you do?**
Stop the rollout, **roll back to the last good version** first (previous image tag, `kubectl rollout undo`, CodeDeploy rollback), confirm service health, then investigate with logs and the diff, fix in a branch, test in staging, and redeploy. Improve the pipeline (smoke tests, health checks, canary releases).

**S3. The ALB returns 502 or 504.**
502: the target returned an invalid response or closed the connection: app crashed, wrong port, security group blocks the ALB, or the app closes keep-alive connections earlier than the ALB. 504: the app is too slow (idle timeout exceeded): check slow queries, CPU and memory. Check target health, security group rules (ALB to app port), the health check path, and app logs.

**S4. Target health checks fail but the app works when I open it directly.**
The health check path or port is wrong, the app returns a redirect (`301` or `302`) or `401` on the health path, the security group does not allow the ALB to reach the health check port, or the app is not listening on `0.0.0.0` (only on `127.0.0.1`).

**S5. High CPU on a server.**
`top` or `htop` to find the process, check whether it is a traffic spike, a runaway loop, a memory-swapping problem, or a cron job. Look at logs and metrics for the start time, scale out (Auto Scaling) if it is real load, and fix the root cause (inefficient code, missing cache, bad query).

**S6. The disk is full.**
`df -h`, `du -sh /var/* | sort -h`, clear or rotate logs, `docker system prune`, remove old artifacts, check for deleted files held open (`lsof | grep deleted`). Then add log rotation, a disk alert at 80%, and bigger volumes if needed.

**S7. The application cannot connect to RDS.**
Check in order: the endpoint and port, credentials, **security group** (does the DB group allow the app's security group on the DB port?), subnets and route (same VPC or peered), the DB is `available`, `publicly_accessible` expectation, DNS resolution, max connections reached, and SSL settings. Test with `nc -zv endpoint 5432` and `psql` from the app host.

**S8. I cannot SSH into an EC2 instance.**
Check the instance is running and has passed status checks, the security group allows port 22 from **your current IP**, the instance is in a public subnet with a route to the internet gateway and has a public IP, the key file permissions (`chmod 400`), the right username (`ec2-user` vs `ubuntu`), the network ACL, and whether the OS firewall or `sshd` is blocking. Fall back to Session Manager or the EC2 serial console.

**S9. Terraform apply fails with a state lock error.**
Another run holds the lock or a previous run crashed. Confirm nobody is running `apply`, then `terraform force-unlock <ID>`. Make CI the only place that applies so locks are rare.

**S10. `terraform plan` wants to destroy and recreate the database.**
Stop. Read why (a changed argument that forces replacement, a renamed resource, or state mismatch). Fix the code or use `moved`/`state mv` for renames, add `prevent_destroy` and `deletion_protection`, take a snapshot before any risky change, and never approve a plan you do not understand.

**S11. Someone changed a security group manually and broke an app.**
That is drift. `terraform plan` shows the difference, apply restores the code's version. Fix the process: restrict console write access, use CloudTrail to find who did it, and use AWS Config rules to detect drift.

**S12. A container keeps restarting (CrashLoopBackOff).**
Read `kubectl logs --previous` or `docker logs`, check exit code (137 means OOM or kill), environment variables and secrets, the command, whether it listens on the expected port, and whether the liveness probe is too strict.

**S13. The Docker image is 1.5 GB. How do you reduce it?**
Multi-stage build, a slim or alpine base, `npm ci --omit=dev`, remove build tools and caches, a proper `.dockerignore`, fewer layers, and check what is large with `docker history` or the `dive` tool.

**S14. A traffic spike is expected tomorrow.**
Load test beforehand, check Auto Scaling limits and warm-up, raise the minimum capacity ahead of time, use a CDN and caching, check database connections and read replicas, set up alarms, and have a rollback and an on-call person ready.

**S15. The AWS bill suddenly jumped.**
Use Cost Explorer grouped by service and tags to find the cause (a forgotten large instance, NAT gateway data transfer, unattached EBS volumes, a runaway Lambda, S3 requests, a public bucket download), fix it, then add **budgets and billing alarms** and tagging rules.

**S16. A TLS certificate expired.**
Renew immediately (ACM auto-renews if DNS validation is still in place, Let's Encrypt needs `certbot renew` working). Prevent it with certificate expiry monitoring and alerts at 30 and 7 days.

**S17. How do you move an app from a single server to a scalable setup?**
Make it stateless (sessions to Redis, files to S3), move the database to RDS, put an ALB in front, create an AMI or container image, run an Auto Scaling group across two AZs, add health checks and monitoring, and move infrastructure into Terraform.

**S18. Your pipeline takes 40 minutes. How do you speed it up?**
Cache dependencies and Docker layers, run independent stages in parallel, split and parallelize tests, use faster agents, skip stages that are not needed, and build only what changed.

**S19. How do you handle a database migration with zero downtime?**
Use backwards-compatible steps (add the new column, deploy code that writes both, backfill, switch reads, then remove the old column later), run migrations as a separate controlled step, take a backup first, and test on a copy of production data.

**S20. A developer needs temporary access to production logs.**
Grant a time-limited, read-only IAM role (or SSO permission set) scoped to the log groups, require MFA, log the access through CloudTrail, and remove it after the task. No shared credentials.

**S21. A service in a private subnet cannot reach an external API.**
Check the private route table has `0.0.0.0/0` to a **NAT gateway**, the NAT is in a public subnet with an Elastic IP and a route to the internet gateway, the security group egress rules, the NACL, and DNS. A VPC endpoint is the answer for AWS services.

**S22. Intermittent failures only on some requests.**
Suspect one unhealthy instance behind the load balancer, a stale DNS or cache, a connection limit, a timeout mismatch, or a bad deploy on a subset. Compare per-instance metrics and logs, and use request ids to trace one failing request.

---

## 21. Step-by-step deployments (what interviewers love to ask: "walk me through it")

### 21.1 Deploy a Node.js (MERN) app on EC2 with PM2, Nginx and HTTPS
**Before you start:** launch an Ubuntu EC2 instance, security group allowing 22 (your IP only), 80 and 443, and point your domain's A record to the instance's Elastic IP.
```bash
# 1) Connect and prepare the server
ssh -i key.pem ubuntu@<public-ip>
sudo apt update && sudo apt upgrade -y
sudo apt install -y nginx git curl

# 2) Install Node.js 20 and PM2
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt install -y nodejs
sudo npm install -g pm2

# 3) Get the code and configure it
git clone https://github.com/you/app.git && cd app
npm ci --omit=dev
nano .env                              # PORT, MONGO_URI, JWT_SECRET (never commit this file)

# 4) Run with PM2 (cluster mode) and survive reboots
pm2 start src/server.js --name api -i max
pm2 save
pm2 startup                            # run the command it prints, then: pm2 save
pm2 logs api
```
```nginx
# 5) /etc/nginx/sites-available/app  (then: sudo ln -s to sites-enabled, remove the default site)
server {
  listen 80;
  server_name example.com www.example.com;

  location / {
    proxy_pass http://127.0.0.1:3000;
    proxy_http_version 1.1;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
  }
}
```
```bash
# 6) Enable Nginx and HTTPS
sudo nginx -t && sudo systemctl reload nginx
sudo apt install -y certbot python3-certbot-nginx
sudo certbot --nginx -d example.com -d www.example.com
curl -I https://example.com/health

# 7) Deploy an update later
cd ~/app && git pull && npm ci --omit=dev && pm2 reload api
```
**Why `pm2 reload`?** In cluster mode it restarts workers one by one, so there is no downtime. **If an ALB with ACM is used instead of Certbot,** skip step 6 and terminate TLS on the load balancer.

### 21.2 Deploy a FastAPI app on EC2 with Gunicorn, systemd and Nginx
```bash
sudo apt update && sudo apt install -y python3-venv nginx git
git clone https://github.com/you/api.git && cd api
python3 -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt gunicorn
nano .env                              # DATABASE_URL, SECRET_KEY
```
```ini
# /etc/systemd/system/api.service
[Unit]
Description=FastAPI service
After=network.target

[Service]
User=ubuntu
WorkingDirectory=/home/ubuntu/api
EnvironmentFile=/home/ubuntu/api/.env
ExecStart=/home/ubuntu/api/.venv/bin/gunicorn -k uvicorn.workers.UvicornWorker -w 2 -b 127.0.0.1:8000 app.main:app
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
```
```bash
sudo systemctl daemon-reload
sudo systemctl enable --now api
sudo systemctl status api
journalctl -u api -f
```
Use the same Nginx reverse proxy as above with `proxy_pass http://127.0.0.1:8000;`. Run database migrations before restarting: `alembic upgrade head`. Check `curl http://127.0.0.1:8000/health` first, then through Nginx.

### 21.3 Deploy with Docker on EC2
```bash
sudo apt install -y docker.io               # or install from Docker's official repository
sudo usermod -aG docker ubuntu              # log out and back in
aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 123456789012.dkr.ecr.ap-south-1.amazonaws.com
docker pull 123456789012.dkr.ecr.ap-south-1.amazonaws.com/myapp:1.0
docker run -d --name api --restart unless-stopped -p 127.0.0.1:3000:3000 --env-file /etc/myapp.env \
  123456789012.dkr.ecr.ap-south-1.amazonaws.com/myapp:1.0
```
Binding to `127.0.0.1` keeps the container reachable only through Nginx. The EC2 instance needs an IAM role with ECR pull permissions (no access keys on the server).

### 21.4 Deploy a React app to S3 and CloudFront
```bash
npm ci && npm run build
aws s3 sync dist s3://my-site-bucket --delete                       # upload (bucket stays private)
aws cloudfront create-invalidation --distribution-id E123ABC456 --paths "/*"   # clear the CDN cache
```
Set long cache headers on hashed asset files, and a short cache or invalidation for `index.html`. Route client-side routes (404 and 403) to `index.html` in the CloudFront error settings.

### 21.5 Attach and mount a new EBS volume (a very common task)
```bash
lsblk                                         # find the new disk, for example nvme1n1 (no mount point)
sudo mkfs -t xfs /dev/nvme1n1                 # ONLY for a brand new empty volume, this erases data
sudo mkdir /data
sudo mount /dev/nvme1n1 /data
sudo blkid /dev/nvme1n1                       # copy the UUID
echo 'UUID=<uuid> /data xfs defaults,nofail 0 2' | sudo tee -a /etc/fstab
sudo mount -a && df -h /data                  # verify the fstab entry works before rebooting
```
**Grow a volume later:** modify the size in AWS (no downtime), then on the instance `sudo growpart /dev/nvme0n1 1` and `sudo xfs_growfs /` (or `resize2fs` for ext4).

### 21.6 ECS Fargate in short
Concepts: a **task definition** (what to run: image, CPU, memory, ports, env, logs), a **task** (a running copy), a **service** (keeps N tasks running, integrates with an ALB, rolling deployments), a **cluster** (logical group). Two IAM roles: the **task execution role** (ECS pulls the image from ECR, writes logs, reads secrets) and the **task role** (what your application code can call, for example S3).
```json
{
  "family": "api",
  "networkMode": "awsvpc",
  "requiresCompatibilities": ["FARGATE"],
  "cpu": "256",
  "memory": "512",
  "executionRoleArn": "arn:aws:iam::123456789012:role/ecsTaskExecutionRole",
  "taskRoleArn": "arn:aws:iam::123456789012:role/api-task-role",
  "containerDefinitions": [{
    "name": "api",
    "image": "123456789012.dkr.ecr.ap-south-1.amazonaws.com/myapp:1.0",
    "portMappings": [{ "containerPort": 3000 }],
    "secrets": [{ "name": "DATABASE_URL", "valueFrom": "arn:aws:secretsmanager:ap-south-1:123456789012:secret:app/db-AbCdEf" }],
    "logConfiguration": {
      "logDriver": "awslogs",
      "options": { "awslogs-group": "/ecs/api", "awslogs-region": "ap-south-1", "awslogs-stream-prefix": "api" }
    }
  }]
}
```
Deploy a new version: register a new task definition revision with the new image tag, then update the service (`aws ecs update-service --cluster c --service api --task-definition api:12`). ECS does a rolling deployment and keeps the old tasks until the new ones pass the ALB health check.

### Questions on deployments
- **How do you deploy without downtime on a single server?** `pm2 reload` (cluster mode) or run the new container beside the old one and switch Nginx, but real zero downtime needs at least two instances behind a load balancer.
- **Where do environment variables live in production?** A root-only `.env` or `EnvironmentFile` on a server, Secrets Manager or SSM for ECS and EC2, Kubernetes Secrets in a cluster. Never in Git.
- **How do you roll back this deployment?** Keep the previous release (git tag or image tag), `git checkout <tag>` then reload, or run the previous image, or `kubectl rollout undo`.
- **What do you do right after deploying?** Check health endpoints and logs, watch error rate and latency on the dashboard for a few minutes, and keep the rollback ready.

---

## 22. More Terraform: real-world pieces

### Rename and import without destroying (`moved` and `import` blocks)
```hcl
moved {
  from = aws_instance.web
  to   = aws_instance.app            # Terraform renames it in the state, no destroy and recreate
}

import {
  to = aws_s3_bucket.logs            # bring an existing bucket under Terraform control
  id = "my-existing-logs-bucket"
}
```
Run `terraform plan` to review (`import` blocks can even generate code with `terraform plan -generate-config-out=generated.tf`).

### Use another stack's outputs (remote state) and move state between backends
```hcl
data "terraform_remote_state" "network" {
  backend = "s3"
  config = {
    bucket = "my-tf-state-bucket"
    key    = "myapp/network/terraform.tfstate"
    region = "ap-south-1"
  }
}
# use: data.terraform_remote_state.network.outputs.vpc_id
```
Splitting into stacks (network, database, app) keeps state files small and limits the blast radius of a mistake. Change a backend with `terraform init -migrate-state` (copies the state) or `-reconfigure` (starts without copying).

### Templates and `for` expressions
```hcl
# user_data.tpl
#!/bin/bash
docker run -d -p 80:${app_port} ${image}

# main.tf
user_data = templatefile("${path.module}/user_data.tpl", { app_port = 3000, image = var.image })

locals {
  private_ids = [for s in aws_subnet.private : s.id]
  tags_upper  = { for k, v in var.tags : upper(k) => v }
  prod_only   = var.environment == "prod" ? 1 : 0
}
```

### Multiple regions with a provider alias (CloudFront certificates must be in us-east-1)
```hcl
provider "aws" {
  alias  = "us_east_1"
  region = "us-east-1"
}

resource "aws_acm_certificate" "site" {
  provider          = aws.us_east_1
  domain_name       = "example.com"
  validation_method = "DNS"
}

resource "aws_route53_record" "cert_validation" {
  for_each = {
    for dvo in aws_acm_certificate.site.domain_validation_options : dvo.domain_name => {
      name   = dvo.resource_record_name
      record = dvo.resource_record_value
      type   = dvo.resource_record_type
    }
  }
  zone_id = var.zone_id
  name    = each.value.name
  type    = each.value.type
  records = [each.value.record]
  ttl     = 60
}

resource "aws_acm_certificate_validation" "site" {
  provider                = aws.us_east_1
  certificate_arn         = aws_acm_certificate.site.arn
  validation_record_fqdns = [for r in aws_route53_record.cert_validation : r.fqdn]
}
```

### Static site: private S3 bucket behind CloudFront (Origin Access Control)
```hcl
resource "aws_s3_bucket" "site" {
  bucket = "${local.name}-site"
}

resource "aws_cloudfront_origin_access_control" "site" {
  name                              = "${local.name}-oac"
  origin_access_control_origin_type = "s3"
  signing_behavior                  = "always"
  signing_protocol                  = "sigv4"
}

data "aws_cloudfront_cache_policy" "optimized" {
  name = "Managed-CachingOptimized"
}

resource "aws_cloudfront_distribution" "site" {
  enabled             = true
  default_root_object = "index.html"

  origin {
    domain_name              = aws_s3_bucket.site.bucket_regional_domain_name
    origin_id                = "s3-site"
    origin_access_control_id = aws_cloudfront_origin_access_control.site.id
  }

  default_cache_behavior {
    target_origin_id       = "s3-site"
    viewer_protocol_policy = "redirect-to-https"
    allowed_methods        = ["GET", "HEAD"]
    cached_methods         = ["GET", "HEAD"]
    cache_policy_id        = data.aws_cloudfront_cache_policy.optimized.id
  }

  custom_error_response {                       # single page app routing
    error_code         = 403
    response_code      = 200
    response_page_path = "/index.html"
  }
  custom_error_response {
    error_code         = 404
    response_code      = 200
    response_page_path = "/index.html"
  }

  restrictions {
    geo_restriction { restriction_type = "none" }
  }
  viewer_certificate {
    cloudfront_default_certificate = true       # or the ACM certificate with aliases for your domain
  }
}

data "aws_iam_policy_document" "site" {
  statement {
    actions   = ["s3:GetObject"]
    resources = ["${aws_s3_bucket.site.arn}/*"]
    principals {
      type        = "Service"
      identifiers = ["cloudfront.amazonaws.com"]
    }
    condition {
      test     = "StringEquals"
      variable = "AWS:SourceArn"
      values   = [aws_cloudfront_distribution.site.arn]
    }
  }
}

resource "aws_s3_bucket_policy" "site" {
  bucket = aws_s3_bucket.site.id
  policy = data.aws_iam_policy_document.site.json
}
```

### ECR repository with scanning and a lifecycle rule
```hcl
resource "aws_ecr_repository" "app" {
  name                 = "myapp"
  image_tag_mutability = "IMMUTABLE"            # a tag can never be overwritten
  image_scanning_configuration { scan_on_push = true }
}

resource "aws_ecr_lifecycle_policy" "app" {
  repository = aws_ecr_repository.app.name
  policy = jsonencode({
    rules = [{
      rulePriority = 1
      description  = "Keep the last 20 images"
      selection    = { tagStatus = "any", countType = "imageCountMoreThan", countNumber = 20 }
      action       = { type = "expire" }
    }]
  })
}
```

### GitHub Actions OIDC role (no stored AWS keys)
```hcl
resource "aws_iam_openid_connect_provider" "github" {
  url            = "https://token.actions.githubusercontent.com"
  client_id_list = ["sts.amazonaws.com"]
  # thumbprint_list is optional in recent AWS provider versions
}

data "aws_iam_policy_document" "gha_trust" {
  statement {
    actions = ["sts:AssumeRoleWithWebIdentity"]
    principals {
      type        = "Federated"
      identifiers = [aws_iam_openid_connect_provider.github.arn]
    }
    condition {
      test     = "StringEquals"
      variable = "token.actions.githubusercontent.com:aud"
      values   = ["sts.amazonaws.com"]
    }
    condition {
      test     = "StringLike"
      variable = "token.actions.githubusercontent.com:sub"
      values   = ["repo:YOUR_ORG/YOUR_REPO:ref:refs/heads/main"]      # only this repo and branch
    }
  }
}

resource "aws_iam_role" "gha" {
  name               = "github-actions-deploy"
  assume_role_policy = data.aws_iam_policy_document.gha_trust.json
}
# attach a least-privilege policy for ECR push and the deployment actions
```

### Terraform questions that follow
- **How do you split a large Terraform project?** By lifecycle and blast radius: network, data, and application stacks with separate state files, sharing values with outputs and `terraform_remote_state` (or parameter store).
- **How do you stop people editing the same state at once?** Remote state with locking, and applies only from CI.
- **Where do reusable modules live?** A separate repository or a `modules/` folder, versioned with tags, with documented inputs and outputs.
- **What is `.terraform.lock.hcl`?** Records exact provider versions and checksums so everyone and CI use the same ones. Commit it.
- **How do you pass different values per environment?** `-var-file=envs/prod.tfvars` with a separate backend key per environment.
- **How do you manage cost in Terraform?** Tag everything, use smaller instances in dev, schedule dev shutdowns, and review the plan for expensive resources (NAT gateways, Multi-AZ databases). Tools like Infracost estimate cost in pull requests.

---

## 23. More AWS: reliability, networking and security extras

### Uptime numbers (good to know)
| Availability | Downtime per year | Per month |
|---|---|---|
| 99% | 3.65 days | 7.3 hours |
| 99.9% | 8.76 hours | 43.8 minutes |
| 99.99% | 52.6 minutes | 4.4 minutes |
| 99.999% | 5.3 minutes | 26 seconds |

### Disaster recovery strategies (cheapest to most resilient)
1. **Backup and restore:** back up data to another region, rebuild after a disaster (RTO hours, cheapest).
2. **Pilot light:** the database is replicated and a minimal core is running, scale up the rest on disaster.
3. **Warm standby:** a smaller but fully working copy is always running, scale it up on disaster.
4. **Multi-site active-active:** full capacity in two regions, near-zero downtime, most expensive.
Choose by the **RTO** (how long to recover) and **RPO** (how much data loss is acceptable). Test your restores regularly.

### Networking extras
- **VPC endpoints:** a **gateway endpoint** (S3 and DynamoDB, free, a route table entry) and an **interface endpoint** (PrivateLink, an ENI in your subnet, for most other services, paid). They keep traffic off the internet and can remove the need for a NAT gateway.
- **VPC peering:** a private connection between two VPCs. Not transitive (A to B and B to C does not give A to C), and CIDR ranges must not overlap. **Transit Gateway** is a central hub for many VPCs.
- **Site-to-Site VPN** (encrypted tunnel over the internet) vs **Direct Connect** (dedicated private line, stable latency, higher cost).
- **ALB listener rules:** route by host (`api.example.com`) or path (`/api/*`) to different target groups, which lets one ALB serve several services. **Sticky sessions** bind a user to a target (avoid by keeping apps stateless). **Deregistration delay** (connection draining) lets in-flight requests finish. **Cross-zone** balancing spreads traffic evenly across AZs.
- **Elastic IP** is a static public IPv4 address you can move between instances (it costs money when unattached).

### More storage and database points
- **EBS:** `gp3` (default general purpose, set IOPS and throughput independently), `io2` (high IOPS for databases), `st1` and `sc1` (throughput and cold HDD). Snapshots are incremental, stored in S3, and can be copied across regions or shared. Encrypt volumes (KMS).
- **AMI:** a machine image for launching instances. Build a **golden AMI** (Packer or EC2 Image Builder) with software pre-installed for fast, consistent Auto Scaling launches.
- **S3 extras:** multipart upload for large files, Transfer Acceleration, event notifications (trigger Lambda or SQS on upload), cross-region replication, Object Lock (compliance), and **static website hosting vs CloudFront with OAC** (prefer OAC to keep the bucket private).
- **RDS extras:** parameter groups (engine settings), maintenance windows, Performance Insights (find slow queries), **RDS Proxy** (connection pooling, helps Lambda and many app instances), **Aurora** (MySQL and PostgreSQL compatible, storage that auto-scales, up to 15 read replicas, faster failover).
- **DynamoDB basics:** partition key and optional sort key, access patterns decide the design, global secondary indexes, on-demand vs provisioned capacity, TTL, DynamoDB Streams.

### Security and governance services (know the one-liners)
- **IAM policy evaluation:** everything is denied by default, an explicit **Deny** always wins, then an explicit **Allow** is needed. **Permission boundaries** and **SCPs** (Service Control Policies in AWS Organizations) limit the maximum permissions.
- **Identity-based vs resource-based policies:** identity-based are attached to users and roles. Resource-based are attached to a resource (S3 bucket policy, KMS key policy, SQS queue policy).
- **Cross-account access:** the target account creates a role that trusts the source account, and the source account's principal calls `sts:AssumeRole`.
- **AWS Organizations and Control Tower:** manage many accounts (separate dev, staging, prod accounts for strong isolation) with guardrails.
- **GuardDuty** (threat detection from logs), **Security Hub** (central findings), **Inspector** (vulnerability scanning for EC2, ECR, Lambda), **Macie** (finds sensitive data in S3), **Config** (resource configuration history and compliance rules), **CloudTrail** (API audit log), **WAF** (web application firewall rules), **Shield** (DDoS protection).
- **Systems Manager (SSM):** Session Manager (shell without SSH), Parameter Store, Patch Manager, Run Command.

### More compute and integration services
- **API Gateway:** a managed front door for APIs (REST, HTTP, WebSocket) with authentication, throttling, caching and usage plans, often used with Lambda.
- **EventBridge:** an event bus and scheduler (cron rules) that routes events to targets such as Lambda and SQS.
- **Step Functions:** orchestrate multi-step workflows with retries and branching.
- **Elastic Beanstalk and App Runner:** quick, managed ways to deploy web apps without managing servers.
- **Lambda extras:** cold starts, concurrency limits, environment variables, layers, VPC access (and the NAT cost that comes with it), provisioned concurrency.

### Questions to expect
- **How would you separate dev, staging and production on AWS?** Separate AWS accounts (best isolation and billing), or at least separate VPCs, with the same Terraform code and different variables, and stricter IAM and approvals in production.
- **How do you give a developer access without sharing keys?** IAM Identity Center (SSO) with a role and MFA, or a role assumed through the console, with time-limited sessions.
- **How do you encrypt everything?** KMS keys for EBS, RDS, S3 and Secrets Manager, TLS in transit, and key policies that limit who can decrypt.
- **A bucket was accidentally made public. What do you do?** Turn on Block Public Access, review the policy and ACLs, check CloudTrail and access logs for exposure, rotate anything sensitive, and add an AWS Config rule or SCP so it cannot happen again.

---

## 24. More Linux and networking

### Diagnosing performance and resources
```bash
uptime                               # load average for 1, 5 and 15 minutes
vmstat 1 5; iostat -x 1 5            # CPU, memory, swap and disk activity (iostat is in the sysstat package)
free -m; swapon --show               # memory and swap
df -h; df -i                         # disk space and inodes (full inodes also break writes)
lsof -i :3000                        # which process uses a port
ulimit -n                            # open file limit (Node "EMFILE: too many open files")
dmesg -T | grep -i "killed process"  # the OOM killer ended a process
```
- **Load average:** the average number of runnable and waiting processes. Compare it with the number of CPU cores: a load of 4 on 4 cores is fully busy, 8 means a queue.
- **OOM killer:** when memory runs out the kernel kills a process. Fix by using less memory, adding memory or swap, and setting limits.
- **Zombie process:** a finished process whose parent has not read its exit status. Fix the parent. Zombies use no CPU or memory but occupy process table entries.
- **Inode exhaustion:** the disk has free space but no free inodes (millions of tiny files). Check with `df -i`, then delete the files.
- **Hard link vs soft link:** a hard link is another name for the same data (same inode). A soft (symbolic) link is a pointer to a path (`ln -s target link`).
- **Boot process:** firmware (BIOS or UEFI), bootloader (GRUB), kernel, then `systemd` starts services (targets such as `multi-user.target`).
- **`nohup` and `tmux`:** `nohup cmd &` keeps a command running after you log out, `tmux` keeps terminal sessions alive.
- **`xargs` and `tee`:** `find . -name "*.log" | xargs gzip`, `command | tee output.txt` (show and save).

### Log rotation (stop logs filling the disk)
```
# /etc/logrotate.d/myapp
/var/log/myapp/*.log {
  daily
  rotate 14
  compress
  missingok
  notifempty
  copytruncate
}
```
Test with `sudo logrotate -d /etc/logrotate.d/myapp`.

### Quick checks for a service that "does not work"
```bash
systemctl status myapp                # is it running, what was the last error
journalctl -u myapp -n 100 --no-pager
ss -tulpn | grep 3000                 # is it listening, and on which address (0.0.0.0 vs 127.0.0.1)
curl -v http://localhost:3000/health
sudo nginx -t && sudo tail -f /var/log/nginx/error.log
```

### Subnetting practice
| Prefix | Addresses | Typical use |
|---|---|---|
| /16 | 65,536 | a whole VPC |
| /20 | 4,096 | a large subnet |
| /24 | 256 (251 usable in AWS) | a standard subnet |
| /26 | 64 | a small subnet |
| /28 | 16 (11 usable in AWS) | the smallest AWS subnet |
- **Is 10.0.5.20 inside 10.0.4.0/22?** A /22 covers 1,024 addresses, `10.0.4.0` to `10.0.7.255`, so yes.
- **How many /24 subnets fit in a /16?** 256.
- **Why not use 10.0.0.0/16 in two VPCs you want to peer?** Overlapping ranges cannot be peered.

### Other networking points
- **HTTP/1.1 vs HTTP/2 vs HTTP/3:** HTTP/2 multiplexes many requests over one connection and compresses headers, HTTP/3 runs over QUIC (UDP) for faster connections on bad networks. ALB and CloudFront support HTTP/2.
- **TLS termination vs passthrough:** terminate at the load balancer (it holds the certificate and sees HTTP) or pass TLS through to the backend (end-to-end encryption, the backend holds the certificate).
- **DNS caching and TTL:** changing a DNS record takes up to the old TTL to spread, so lower the TTL before a planned migration.
- **CORS from the infrastructure view:** it is a browser rule enforced through response headers set by the app or gateway. A `CORS error` with a working API usually means missing `Access-Control-Allow-Origin` on the server, or a proxy that dropped the headers or returned an error page without them.
- **Sticky sessions, keep-alive and timeouts:** the ALB idle timeout defaults to 60 seconds, so keep the application's keep-alive timeout longer than the ALB's to avoid random 502 errors.

---
## 25. More Docker, Kubernetes and CI/CD

### Docker extras
- **BuildKit and `buildx`:** the modern builder (faster, parallel stages, build secrets). `docker buildx build --platform linux/amd64,linux/arm64 -t myapp:1.0 .` builds for several CPU types (important when you build on an Apple Silicon laptop and run on x86 servers, or use Graviton).
- **Build secrets without leaking them into layers:**
```dockerfile
# syntax=docker/dockerfile:1
RUN --mount=type=secret,id=npm_token \
    NPM_TOKEN=$(cat /run/secrets/npm_token) npm ci
```
```bash
docker build --secret id=npm_token,src=./npm_token.txt -t myapp .
```
- **Image tagging strategy:** tag with the git SHA (traceable) and a semantic version (`1.4.2`), optionally `latest` for convenience, but deploy by the immutable tag. Use immutable tags in ECR so a tag cannot be overwritten.
- **Compose overrides and profiles:** `docker-compose.override.yml` is merged automatically (dev settings like bind mounts). `docker compose -f docker-compose.yml -f docker-compose.prod.yml up -d` for production. `profiles: ["debug"]` keeps optional services off by default.
- **Logging drivers:** the default `json-file` can fill the disk, so set limits (`--log-opt max-size=10m --log-opt max-file=3`) or use `awslogs` to send logs to CloudWatch.
- **Rootless and read-only containers:** run as a non-root user, add `--read-only` with a `tmpfs` for temporary paths, and `--cap-drop ALL` to remove Linux capabilities you do not need.
- **Scan an image:** `trivy image myapp:1.0` lists known vulnerabilities in the OS packages and libraries. Fail the pipeline on critical findings.
- **Docker Hub rate limits:** anonymous pulls are limited. Use authenticated pulls, a registry mirror, or ECR pull-through cache in CI.
- **Common Docker interview traps:** `EXPOSE` does not publish a port. `docker stop` sends SIGTERM then SIGKILL. A container dies when its main process ends. `COPY . .` before `npm ci` breaks the cache. `latest` is just a tag, not "the newest".

### Kubernetes extras
**Helm (package manager):** a chart is a folder of templates plus `values.yaml`.
```
mychart/
  Chart.yaml
  values.yaml              # defaults (image tag, replicas, resources)
  templates/
    deployment.yaml        # uses {{ .Values.image.tag }}
    service.yaml
```
```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm install my-redis bitnami/redis
helm upgrade --install api ./mychart -f values-prod.yaml --set image.tag=1.4.2
helm list; helm history api; helm rollback api 3
helm template api ./mychart -f values-prod.yaml          # render YAML without installing
```
**Kustomize:** a `base/` of plain YAML and `overlays/dev|prod` that patch it (replicas, image tag), built into `kubectl apply -k overlays/prod`.

**RBAC example (read-only access to pods in one namespace):**
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata: { name: pod-reader, namespace: staging }
rules:
  - apiGroups: [""]
    resources: ["pods", "pods/log"]
    verbs: ["get", "list", "watch"]
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata: { name: read-pods, namespace: staging }
subjects:
  - kind: User
    name: dev-user
    apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```
**NetworkPolicy (default deny, then allow only what is needed):**
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata: { name: default-deny, namespace: prod }
spec:
  podSelector: {}
  policyTypes: [Ingress]
---
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata: { name: allow-api-to-db, namespace: prod }
spec:
  podSelector:
    matchLabels: { app: db }
  ingress:
    - from:
        - podSelector: { matchLabels: { app: api } }
      ports:
        - { protocol: TCP, port: 5432 }
```
(NetworkPolicies need a network plugin that enforces them.)

**CronJob, PVC and PodDisruptionBudget:**
```yaml
apiVersion: batch/v1
kind: CronJob
metadata: { name: nightly-report }
spec:
  schedule: "0 2 * * *"
  jobTemplate:
    spec:
      template:
        spec:
          restartPolicy: OnFailure
          containers:
            - name: report
              image: myrepo/report:1.0
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata: { name: data-pvc }
spec:
  accessModes: ["ReadWriteOnce"]
  resources: { requests: { storage: 10Gi } }
---
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata: { name: api-pdb }
spec:
  minAvailable: 2                  # keep at least 2 pods during node drains and upgrades
  selector:
    matchLabels: { app: api }
```
**More concepts:**
- **Init containers** run before the app container (wait for the database, run migrations). **Sidecars** run alongside it (log shipper, proxy).
- **Deployment vs StatefulSet:** a Deployment's pods are interchangeable. A StatefulSet gives each pod a stable name and its own storage (databases, Kafka).
- **Taints and tolerations** keep pods off certain nodes unless they tolerate them. **Node affinity** and **pod anti-affinity** control placement (spread replicas across nodes and AZs).
- **ResourceQuota and LimitRange** cap what a namespace can use.
- **Cluster autoscaler or Karpenter** add nodes when pods cannot be scheduled. HPA adds pods.
- **EKS basics:** `eksctl create cluster --name demo --region ap-south-1`, then `aws eks update-kubeconfig --name demo --region ap-south-1` to get kubectl access. Worker nodes run in managed node groups or on Fargate. Use IAM roles for service accounts (IRSA) to give pods AWS permissions without keys.
- **Blue-green and canary on Kubernetes:** run two Deployments and switch the Service selector (blue-green), or use two Deployments with a small replica count for the new one, ingress canary annotations, or Argo Rollouts for weighted traffic.

### CI/CD extras
**GitHub Actions features:**
```yaml
strategy:
  matrix:
    node: [18, 20, 22]               # run the job on several versions
steps:
  - uses: actions/setup-node@v4
    with: { node-version: "${{ matrix.node }}", cache: npm }

concurrency:
  group: deploy-prod
  cancel-in-progress: false          # never run two production deploys at once

# reusable workflow: define with "on: workflow_call", use with
# jobs: build: { uses: ./.github/workflows/build.yml }
```
Environments (`environment: production`) can require manual approvals and hold environment-specific secrets. Pin third-party actions to a version or commit SHA for supply-chain safety.

**Jenkins extras:**
```groovy
pipeline {
  agent { docker { image 'node:20' } }          // build inside a container

  stages {
    stage('Checks') {
      parallel {
        stage('Lint') { steps { sh 'npm run lint' } }
        stage('Unit tests') { steps { sh 'npm test' } }
      }
    }
    stage('Approval') {
      when { branch 'main' }
      steps { input message: 'Deploy to production?', ok: 'Deploy' }
    }
    stage('Push image') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'U', passwordVariable: 'P')]) {
          sh 'echo "$P" | docker login -u "$U" --password-stdin'
        }
      }
    }
  }
}
```
**Shared libraries** hold reusable pipeline code across repositories. Credentials bound with `withCredentials` are masked in logs. Use `when { changeset "frontend/**" }` to build only what changed in a monorepo.

**GitOps with Argo CD:**
```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata: { name: api, namespace: argocd }
spec:
  project: default
  source:
    repoURL: https://github.com/you/k8s-manifests.git
    targetRevision: main
    path: apps/api
  destination:
    server: https://kubernetes.default.svc
    namespace: prod
  syncPolicy:
    automated: { prune: true, selfHeal: true }     # revert manual changes, delete removed resources
```
The CI pipeline builds and pushes the image, then updates the image tag in the manifests repository. Argo CD detects the change and syncs the cluster. Rollback is a `git revert`.

**Release strategies on AWS:**
- **Blue-green with an ALB:** two target groups (blue and green), shift the listener's weights (100/0 to 0/100), keep blue until you are sure.
- **Canary:** weighted routing (for example 10% then 50% then 100%) with alarms that roll back automatically (CodeDeploy for ECS and Lambda supports canary and linear configurations).
- **Database changes:** always backwards compatible so old and new versions can run side by side.

**Semantic versioning and release tags:** `MAJOR.MINOR.PATCH` (breaking, feature, fix). Tag releases in Git (`git tag v1.4.2`), and have the pipeline build the image from that tag, so what runs in production maps to an exact commit.

**Monorepo vs polyrepo:** a monorepo keeps all services in one repository (easy refactors, needs path-based pipelines), polyrepo uses one repository per service (clear ownership, harder cross-cutting changes).

---

## 26. Observability and reliability extras

### Tracing
**Distributed tracing** follows one request across services, showing where the time goes. Tools: **OpenTelemetry** (the standard way to instrument code), **Jaeger** or **Tempo** (backends), **AWS X-Ray**. A trace is made of **spans** (one operation each), connected by a **trace id** that is passed in HTTP headers. Use it when a request is slow and you do not know which service or query is responsible.

### Alertmanager routing
```yaml
# alertmanager.yml
route:
  receiver: slack-team
  group_by: [alertname, service]
  group_wait: 30s
  repeat_interval: 4h
  routes:
    - matchers: [severity="critical"]
      receiver: pagerduty
receivers:
  - name: slack-team
    slack_configs:
      - api_url_file: /etc/alertmanager/slack_url      # keep secrets in files, not in Git
        channel: "#alerts"
  - name: pagerduty
    pagerduty_configs:
      - routing_key_file: /etc/alertmanager/pd_key
```
Alertmanager groups, deduplicates, silences and routes alerts. Use **inhibition** to suppress noisy alerts when a bigger one (such as a whole node being down) is already firing.

### Recording rules and burn-rate alerts
Recording rules precompute expensive queries (`record: job:http_error_ratio:rate5m`). **Burn-rate alerts** warn when you consume your SLO error budget too fast (for example "at this rate the monthly budget is gone in 2 hours"), which is better than a fixed error threshold.

### CloudWatch agent configuration (memory, disk and application logs)
```json
{
  "metrics": {
    "append_dimensions": { "InstanceId": "${aws:InstanceId}" },
    "metrics_collected": {
      "mem":  { "measurement": ["mem_used_percent"] },
      "disk": { "measurement": ["used_percent"], "resources": ["/"] }
    }
  },
  "logs": {
    "logs_collected": {
      "files": {
        "collect_list": [
          { "file_path": "/var/log/myapp/*.log", "log_group_name": "/myapp/app", "log_stream_name": "{instance_id}" }
        ]
      }
    }
  }
}
```
Start it with `amazon-cloudwatch-agent-ctl`, give the instance role `CloudWatchAgentServerPolicy`, then alarm on `mem_used_percent` and `used_percent`.

### Reliability practices
- **Runbook:** a short document for each alert: what it means, how to check, how to fix, who to call. Link it in the alert.
- **Postmortem (blameless) template:** summary, impact, timeline, root cause, what went well, what went badly, action items with owners and dates.
- **Capacity planning and load testing:** measure how many requests per second one instance handles (tools: k6, Locust, JMeter), then set Auto Scaling and database limits with headroom.
- **Chaos engineering and game days:** deliberately kill an instance or block a dependency in a safe window to prove the system recovers (AWS Fault Injection Service, Chaos Mesh).
- **Backups you have never restored are not backups:** schedule restore tests.
- **Graceful degradation, timeouts, retries with backoff and circuit breakers** keep one failing dependency from taking everything down.
- **On-call basics:** clear rotation, actionable alerts, escalation path, and time to recover from incidents.
- **Change management:** small frequent changes, reviews, feature flags, deploy during business hours when possible, and a ready rollback.

---

## 27. Hands-on practice plan and rapid-fire questions

### Seven small labs (about 1 to 2 hours each)
Do the ones you can fit into your days. **Always destroy cloud resources afterwards** (`terraform destroy`) and set a billing budget alert first. NAT gateways, load balancers, Multi-AZ databases and large instances cost money even for short tests, and free tier rules change, so check the current AWS pricing page.

| Lab | What to do | What you learn |
|---|---|---|
| 1 | Run a React, API, Postgres and Redis stack locally with Docker Compose and a database healthcheck | Dockerfiles, Compose, networking, volumes |
| 2 | Terraform: one EC2 instance and a security group, output the public IP, then destroy | the Terraform workflow end to end |
| 3 | Manually deploy a Node or FastAPI app on EC2 with PM2 or systemd, Nginx and HTTPS (section 21) | what every deployment tool automates |
| 4 | GitHub Actions workflow: test, build the Docker image and push to ECR (with an OIDC role) | CI/CD and IAM roles |
| 5 | Terraform: VPC with public and private subnets and an RDS instance in the private subnets | networking and security groups |
| 6 | Run Prometheus, Grafana and node exporter with Compose, build one dashboard and one alert | monitoring and PromQL |
| 7 | Install `kind` or `minikube`, deploy a Deployment, Service and Ingress with probes, then do a rolling update and a rollback | Kubernetes fundamentals |

After each lab, explain out loud what you built in 60 seconds. That is the interview answer.

### Rapid-fire one-line answers
1. **Terraform state?** The file mapping your code to real resources.
2. **Why remote state?** Shared, locked and not lost with a laptop.
3. **`plan` vs `apply`?** Preview vs make the changes.
4. **Resource vs data source?** Create and manage vs only read.
5. **Module?** A reusable folder of Terraform code.
6. **`count` vs `for_each`?** Index-based vs key-based, prefer `for_each`.
7. **Drift?** Real infrastructure changed outside Terraform.
8. **Terraform vs Ansible?** Provision infrastructure vs configure servers.
9. **Public subnet?** Has a route to an internet gateway.
10. **NAT gateway?** Lets private subnets reach the internet outbound only.
11. **Security group vs NACL?** Stateful instance level vs stateless subnet level.
12. **IAM role?** Temporary permissions assumed by a service or user, no stored keys.
13. **RDS Multi-AZ vs read replica?** High availability vs read scaling.
14. **ALB vs NLB?** Layer 7 HTTP routing vs layer 4 TCP and static IPs.
15. **Auto Scaling group?** Keeps a number of instances, replaces unhealthy ones, scales on demand.
16. **S3 storage class for archives?** Glacier.
17. **How do you serve a React app on AWS?** S3 private bucket plus CloudFront with OAC, ACM certificate, Route 53.
18. **Image vs container?** Template vs running instance.
19. **`CMD` vs `ENTRYPOINT`?** Default command vs the fixed executable.
20. **Multi-stage build?** Build in one stage, copy only the output into a small final image.
21. **Volume vs bind mount?** Docker-managed persistent data vs a host folder.
22. **Pod vs Deployment vs Service?** Smallest unit vs desired replicas and rollouts vs a stable address.
23. **Readiness vs liveness?** Send traffic or not vs restart or not.
24. **`CrashLoopBackOff`?** The container keeps crashing, read `logs --previous`.
25. **Requests vs limits?** Reserved for scheduling vs the maximum allowed.
26. **CI vs CD?** Merge and test often vs release automatically or on approval.
27. **Blue-green vs canary?** Switch all traffic between two environments vs shift traffic gradually.
28. **How do you roll back?** Deploy the previous image tag or revision.
29. **Prometheus pull model?** It scrapes `/metrics` endpoints on a schedule.
30. **Counter vs gauge vs histogram?** Only goes up vs goes up and down vs buckets for percentiles.
31. **Golden signals?** Latency, traffic, errors, saturation.
32. **502 vs 504?** Bad response from the backend vs the backend was too slow.
33. **`kill` vs `kill -9`?** SIGTERM (graceful) vs SIGKILL (force).
34. **File permission 755?** Owner full access, others read and execute.
35. **A secret was committed. First step?** Rotate it.
36. **Least privilege?** Only the permissions needed, nothing more.
37. **Why not use `latest` tags in production?** You cannot tell what is running or roll back precisely.
38. **Stateless app?** Keeps no local session or files, so any instance can serve any request.
39. **What do you check first when a site is down?** From outside, DNS, load balancer health, instances and logs, recent deployments.
40. **What is IaC's biggest benefit?** Repeatable, reviewable and versioned infrastructure.

---
## 28. Command cheat sheet

| Area | Commands to know |
|---|---|
| **Linux** | `ls -la`, `cd`, `cat`, `tail -f`, `grep -rni`, `find`, `chmod`, `chown`, `ps aux`, `top`, `df -h`, `du -sh`, `free -m`, `systemctl status/restart`, `journalctl -u`, `ss -tulpn`, `curl -I`, `ssh -i`, `scp`, `tar -czvf`, `crontab -e` |
| **Git** | `clone`, `status`, `add`, `commit`, `push`, `pull --rebase`, `switch -c`, `merge`, `rebase`, `stash`, `log --oneline --graph`, `revert`, `reset`, `cherry-pick`, `tag` |
| **Docker** | `build -t`, `run -d -p --name`, `ps -a`, `logs -f`, `exec -it`, `stop`, `rm`, `images`, `system prune`, `compose up -d --build`, `compose down` |
| **Kubernetes** | `get pods -o wide`, `describe`, `logs --previous`, `exec -it`, `apply -f`, `rollout status/undo`, `scale`, `port-forward`, `top`, `get events` |
| **Terraform** | `init`, `fmt`, `validate`, `plan -out`, `apply`, `destroy`, `state list/show/mv`, `import`, `output`, `workspace`, `apply -replace` |
| **AWS CLI** | `sts get-caller-identity`, `s3 ls/cp/sync`, `ec2 describe-instances`, `ecr get-login-password`, `logs tail`, `ssm start-session` |
| **Ansible** | `ansible all -m ping`, `ansible-playbook site.yml --check`, `ansible-playbook -i inventory site.yml`, `ansible-vault encrypt` |
| **Nginx** | `nginx -t`, `systemctl reload nginx`, `tail -f /var/log/nginx/error.log` |
| **Network** | `dig`, `nslookup`, `ping`, `traceroute`, `nc -zv host port`, `curl -v`, `ss -tulpn` |

**Ports:** 22 SSH, 80 HTTP, 443 HTTPS, 3000/8000 app, 5432 Postgres, 3306 MySQL, 27017 MongoDB, 6379 Redis, 9090 Prometheus, 9100 node exporter.

---

## 29. Interview day strategy

### Before the round
- Make sure you can run Docker locally and have an AWS account or a `terraform plan`-ready folder if hands-on is possible. If not, be ready to write HCL or YAML in an editor.
- Re-read your resume DevOps lines and be able to explain each tool in two sentences: what it is, and what you did with it.
- Know the architecture of one deployment you did end to end, and draw it.

### During theory questions
- Short definition, then the "why", then what you did.
- Mention trade-offs: Terraform vs Ansible, ECS vs EKS, rolling vs blue-green.
- For anything you used only lightly, say so. "I used Kubernetes at a fundamentals level: deployments, services and probes" is a strong, honest answer.

### During hands-on tasks
1. Clarify requirements (region, environment, public or private, size).
2. Start with the smallest working piece (a VPC and one EC2, or a Dockerfile that builds).
3. Use variables, tags and least-privilege security groups from the start.
4. Say how you would test (`plan`, `docker run`, `curl /health`).
5. Mention what you would add: remote state, monitoring, CI, backups.

### Common mistakes to avoid
- Opening SSH or database ports to `0.0.0.0/0`.
- Hard-coding secrets, AMI ids or account ids.
- Using `latest` image tags in production.
- Committing `.tfstate`, `.env` or keys to Git.
- Running containers as root, or putting secrets in images.
- Ignoring health checks and rollbacks in a deployment story.
- Overstating experience. If asked a follow-up you cannot answer, say what you know and how you would find out.

### Questions you can ask the interviewer
- What does your AWS setup and deployment pipeline look like today?
- Do you use Terraform for all infrastructure, and how are environments managed?
- How do developers deploy changes, and who handles on-call?
- What would my first three months look like for the DevOps part of this role?

### Rapid answers for tricky "why" questions
- **Why Terraform?** Repeatable, reviewable, version-controlled infrastructure with a plan before changes, and it works across clouds.
- **Why Docker?** The same package runs everywhere, deployments are fast and consistent.
- **Why a load balancer and Auto Scaling?** Availability and capacity: traffic is spread, unhealthy instances are removed, and capacity follows demand.
- **Why private subnets for the database?** The database should never be reachable from the internet. Only the app tier can talk to it.
- **Why CI/CD?** Faster, safer, repeatable releases with automatic testing and a clear history.
- **Why monitoring?** To find problems before users do and to have data when something breaks.

---

## 30. Last day revision checklist

**Be able to write from memory (without looking):**
- [ ] A Terraform file with provider, `aws_instance` and a security group (SSH from your IP, HTTP from anywhere)
- [ ] Variables with `sensitive`, `outputs`, and a `terraform.tfvars`
- [ ] A VPC with public and private subnets, an internet gateway and a route table
- [ ] The S3 backend block and the `init`, `plan`, `apply`, `destroy` flow
- [ ] A multi-stage Dockerfile for a React app (Nginx) and one for Node or FastAPI
- [ ] A `docker-compose.yml` with app, Postgres (healthcheck) and Redis
- [ ] The commands to build, tag and push an image to ECR
- [ ] A Kubernetes Deployment and Service with probes and resource limits
- [ ] A declarative Jenkinsfile (checkout, test, build image, push, deploy)
- [ ] An Nginx reverse proxy config with WebSocket headers
- [ ] A bash health check script and a simple Ansible playbook
- [ ] The step-by-step EC2 deployment: install, clone, env file, PM2 or systemd, Nginx, HTTPS, update and rollback
- [ ] A Terraform `moved` block, an `import` block, and a `for_each` resource
- [ ] The commands to attach, format and mount an EBS volume

**Be able to explain in 30 seconds each:**
- [ ] Terraform state, backend, locking, drift, `count` vs `for_each`, modules
- [ ] Terraform vs Ansible, and Terraform vs CloudFormation
- [ ] VPC, public vs private subnet, internet gateway vs NAT gateway
- [ ] Security group vs NACL, and the security group chain ALB, app, database
- [ ] IAM user vs role, least privilege, and why instances use roles
- [ ] EC2, S3, RDS Multi-AZ vs read replica, ALB vs NLB, Auto Scaling, Route 53, CloudFront, CloudWatch
- [ ] Image vs container, layers and cache, `CMD` vs `ENTRYPOINT`, multi-stage builds, volumes
- [ ] Pod, Deployment, Service, Ingress, ConfigMap, Secret, readiness vs liveness
- [ ] CI vs CD, deployment strategies (rolling, blue-green, canary), rollback
- [ ] Prometheus (pull, scrape, PromQL) and Grafana, the golden signals
- [ ] What you do when a site is down, a deploy fails, or a secret leaks
- [ ] RTO vs RPO and the four DR strategies; what 99.9% availability means
- [ ] VPC gateway vs interface endpoints, peering vs Transit Gateway, VPN vs Direct Connect
- [ ] ECS task role vs task execution role, and how a Fargate deployment works
- [ ] Helm, RBAC and NetworkPolicy in one sentence each; GitOps with Argo CD
- [ ] The rapid-fire answers in section 27 (all 40 out loud)

**Be ready to talk about your projects:**
- [ ] The VPC, RDS and Terraform setup: why private subnets, how state was stored, how secrets were handled
- [ ] The EC2, ALB, Nginx and PM2 deployment of the healthcare app
- [ ] The Jenkins pipeline and what each stage did
- [ ] Docker and Kubernetes at the level you actually used them
- [ ] Prometheus and Grafana: what you monitored and which alerts you set

---

**Final advice:** The role needs full stack engineering with *fundamental* DevOps. Interviewers do not expect a platform engineer, they want to see that you understand how your code gets from Git to a running, monitored, secure production environment, and that you can describe it clearly and honestly. If you can explain a VPC with private database subnets, write a Terraform file for an EC2 instance and security group, containerize an app, and describe a CI/CD pipeline and rollback, you are well prepared. Good luck!
