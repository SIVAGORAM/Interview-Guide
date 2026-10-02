# DevOps Interview Guide for Full Stack Engineers
### AWS • Terraform • Docker • Kubernetes • Jenkins CI/CD • Linux | Questions + Simple Answers + Examples

> **How to use this document**
> - **Part 1–3:** DevOps basics, Linux, Git and networking (asked in every interview)
> - **Part 4–6:** Docker, Kubernetes, Jenkins CI/CD (the core of DevOps rounds)
> - **Part 7–8:** Terraform and AWS
> - **Part 9:** Monitoring, logging, security, Ansible
> - **Part 10:** Real full-stack deployment examples (Dockerfiles, YAML, Jenkinsfile, Terraform)
> - **Part 11–12:** Troubleshooting scenarios, HR questions, cheat sheet
> - Answer format that impresses: **Definition → Why we use it → Small example → "In my project I used..."**
> - For a full stack role, the interviewer wants to know: *"Can you build, containerize, deploy and troubleshoot your own app?"* Keep that in mind in every answer.
> - Where you haven't used something: *"I haven't used it in production, but my understanding is..."*

---

## Table of Contents
1. [DevOps Fundamentals](#part-1--devops-fundamentals)
2. [Linux](#part-2--linux)
3. [Git and Networking Basics](#part-3--git-and-networking-basics)
4. [Docker](#part-4--docker)
5. [Kubernetes](#part-5--kubernetes)
6. [Jenkins and CI/CD](#part-6--jenkins-and-cicd)
7. [Terraform](#part-7--terraform)
8. [AWS](#part-8--aws)
9. [Monitoring, Logging, Security and Ansible](#part-9--monitoring-logging-security-and-ansible)
10. [Full-Stack Deployment Examples](#part-10--full-stack-deployment-examples)
11. [Troubleshooting Scenarios and HR Questions](#part-11--troubleshooting-scenarios-and-hr-questions)
12. [Last-Minute Cheat Sheet](#part-12--last-minute-cheat-sheet)

---

# Part 1 — DevOps Fundamentals

### Q1. What is DevOps?
DevOps is a **culture and set of practices** that brings **Development and Operations teams together** to build, test, release and run software **faster, more reliably and continuously.** It uses **automation (CI/CD), Infrastructure as Code, monitoring and collaboration.** It is not a tool or a job title only.

### Q2. Why do companies use DevOps? What are the benefits?
- **Faster releases** (multiple deployments per day)
- **Fewer failures and quick recovery**
- **Automation** reduces manual errors
- Better **collaboration** between dev, QA and ops
- **Consistent environments** (dev = staging = production)
- Faster feedback and better quality

### Q3. Explain the DevOps lifecycle.
**Plan → Code → Build → Test → Release → Deploy → Operate → Monitor** (then feedback goes back to Plan). Example tools: Jira (plan), Git (code), Maven/npm/Docker (build), Jest/Selenium (test), Jenkins (CI/CD), Terraform/Ansible (infra), Kubernetes (deploy/operate), Prometheus/Grafana (monitor).

### Q4. What is CI/CD?
- **CI (Continuous Integration):** developers merge code often; each push **automatically builds and tests** the code to find problems early.
- **CD (Continuous Delivery):** every successful build is **ready to deploy** (a manual approval before production).
- **CD (Continuous Deployment):** every successful build is **automatically deployed to production** (no manual step).

### Q5. What is a CI/CD pipeline? Typical stages?
An automated workflow from code commit to deployment:
**Checkout → Install dependencies → Lint → Unit tests → Build → Security scan → Build Docker image → Push to registry → Deploy to staging → Integration/E2E tests → Approval → Deploy to production → Smoke test/Monitor.**

### Q6. What is Infrastructure as Code (IaC)?
Managing and creating infrastructure (servers, networks, databases) **using code files** instead of clicking in a console. Benefits: **repeatable, version-controlled, reviewable, consistent, faster.** Tools: **Terraform, CloudFormation, Pulumi, Ansible.**

### Q7. Declarative vs imperative IaC?
**Declarative:** you describe the **desired end state** and the tool figures out how (Terraform, CloudFormation, Kubernetes YAML). **Imperative:** you write **step-by-step commands** (shell scripts, some Ansible tasks).

### Q8. What is configuration management?
Automatically setting up and keeping servers in the desired state (install packages, config files, users, services). Tools: **Ansible, Chef, Puppet.** (Terraform *creates* infrastructure; Ansible *configures* it.)

### Q9. What is the difference between Docker, Kubernetes, Terraform, Ansible and Jenkins? (very common)
| Tool | Purpose |
|---|---|
| **Docker** | Package the app into a container |
| **Kubernetes** | Run and manage many containers (scale, heal, deploy) |
| **Terraform** | Create cloud infrastructure with code |
| **Ansible** | Configure servers / deploy apps with playbooks |
| **Jenkins** | Automate build, test, deploy (CI/CD) |
| **Git** | Version control of code |

### Q10. Deployment strategies?
- **Recreate:** stop old, start new (has downtime)
- **Rolling update:** replace instances **gradually** (default in Kubernetes)
- **Blue-Green:** two identical environments; **switch traffic** from blue (old) to green (new); instant rollback
- **Canary:** release to a **small % of users** first, then increase
- **A/B testing:** different versions for different user groups to compare behavior
- **Shadow:** copy live traffic to the new version without affecting users

### Q11. What is rollback? How do you do it?
Going back to the previous working version. Ways: **redeploy the previous Docker image tag,** `kubectl rollout undo`, switch back in blue-green, revert the Git commit, `terraform apply` of a previous version. **Keep every build as an immutable, versioned artifact** so rollback is quick.

### Q12. What is GitOps?
Using **Git as the single source of truth** for infrastructure and app deployment. A tool (**ArgoCD, Flux**) watches the Git repo and automatically syncs the cluster to match it. Changes happen via **pull requests**, which gives audit history and easy rollback.

### Q13. What is immutable infrastructure?
Never change a running server. To update, **build a new image/instance and replace the old one** (containers, AMIs). It avoids "configuration drift" and "it works on my machine."

### Q14. What is configuration drift?
When the real infrastructure becomes **different from what the code says** (someone changed it manually). Prevent with IaC, no manual changes, and drift detection (`terraform plan`).

### Q15. What are the DORA metrics? (awareness)
Four metrics for delivery performance: **Deployment frequency, Lead time for changes, Change failure rate, Mean time to recovery (MTTR).**

### Q16. What is Agile vs DevOps?
Agile is a **way of developing software** in short iterations (sprints). DevOps extends it to **delivery and operations**: building, deploying and running software continuously. They complement each other.

### Q17. What is "shift left"?
Doing testing and security checks **earlier** in the pipeline (at code/commit stage), because bugs are cheaper to fix early.

### Q18. What is the 12-factor app? (key points for full stack devs)
Best practices for cloud apps: config in **environment variables**, **stateless** processes, logs as **streams to stdout**, **one codebase** in Git, explicit **dependencies**, backing services as attached resources, **dev/prod parity**, disposable processes (fast start/graceful shutdown).

### Q19. What is the role of a full stack engineer in DevOps?
Own the app from code to production: write code, **write Dockerfiles, CI/CD pipelines, basic Terraform/Kubernetes configs,** set environment variables/secrets, handle logs and monitoring, and **debug production issues** with the team.

### Q20. What is SRE? SLI, SLO, SLA?
**Site Reliability Engineering:** applying software engineering to operations for reliability. **SLI** = a measurement (e.g., 99.9% requests successful). **SLO** = the target for it. **SLA** = a contract with customers (with penalties).

---

# Part 2 — Linux

### Q21. Why is Linux important for DevOps?
Most servers, containers and cloud VMs run Linux. You need it to **deploy apps, troubleshoot servers, read logs, manage services, automate with shell scripts,** and work inside Docker containers.

### Q22. Explain the Linux directory structure.
```
/         root of everything
/home     user home directories
/root     home of the root user
/etc      configuration files (nginx, ssh, passwd)
/var      variable data: /var/log (logs), /var/lib, /var/www
/usr      user programs and libraries (/usr/bin)
/bin, /sbin   essential commands
/tmp      temporary files
/opt      optional/third-party software
/dev      device files
/proc     virtual files: process and kernel info
/mnt, /media   mount points
```

### Q23. Basic commands you must know?
```bash
pwd; ls -la; cd /var/log; mkdir -p a/b; touch f.txt
cp a b; mv a b; rm -rf folder        # careful with rm -rf
cat f; less f; head -n 20 f; tail -n 50 f; tail -f app.log   # follow logs live
ln -s /path/target linkname          # symbolic link
which node; whoami; hostname; uname -a; uptime; date
history; clear; man ls; echo $PATH
```

### Q24. Explain file permissions (`chmod`, `chown`).
`ls -l` shows e.g. `-rwxr-xr--` = **type | owner | group | others.** Each has **r=4, w=2, x=1.**
```bash
chmod 755 script.sh     # owner rwx(7), group r-x(5), others r-x(5)
chmod 644 file.txt      # owner rw-, group r--, others r--
chmod +x deploy.sh      # add execute permission
chown user:group file   # change owner and group
chmod -R 750 folder
```
`777` (everyone can do everything) is **dangerous**; avoid it. Special: **SUID, SGID, sticky bit** (e.g., /tmp).

### Q25. What are users, groups and `sudo`?
```bash
useradd -m ravi; passwd ravi; usermod -aG docker ravi   # add to group
userdel ravi; id ravi; groups
sudo command        # run as root (privileges controlled by /etc/sudoers, edit with visudo)
su - ravi           # switch user
```
Files: `/etc/passwd` (users), `/etc/shadow` (hashed passwords), `/etc/group` (groups).

### Q26. How do you search for text and files?
```bash
grep "error" app.log                  # search text
grep -rni "timeout" /var/log/        # recursive, line numbers, ignore case
grep -v "debug" app.log               # exclude lines
grep -c "ERROR" app.log               # count
find / -name "*.log"                  # find files by name
find /var -type f -size +100M         # large files
find . -mtime -1                      # modified in last 1 day
```

### Q27. What are pipes and redirection?
```bash
ls | grep ".js"             # pipe: output of one → input of next
echo "hi" > file.txt        # overwrite file
echo "hi" >> file.txt       # append
command 2> err.log          # redirect errors (stderr)
command > out.log 2>&1      # both stdout and stderr to a file
command &> all.log
cat file | sort | uniq -c | sort -nr | head     # top repeated lines
```

### Q28. Useful text-processing commands (`awk`, `sed`, `cut`, `sort`, `uniq`, `wc`)?
```bash
awk '{print $1}' access.log                 # print first column
awk -F: '{print $1}' /etc/passwd            # custom separator
sed 's/old/new/g' file.txt                  # replace text
sed -i 's/8080/9090/' config.conf           # edit file in place
cut -d',' -f2 data.csv                      # 2nd column
sort file | uniq -c                         # count duplicates
wc -l file                                  # number of lines
# Top 5 IPs in an access log:
awk '{print $1}' access.log | sort | uniq -c | sort -nr | head -5
```

### Q29. How do you check and manage processes?
```bash
ps aux | grep node        # list processes
top / htop                # live view of CPU/memory
kill 1234                 # ask to stop (SIGTERM, 15)
kill -9 1234              # force kill (SIGKILL)
pkill -f "node app.js"
nohup node app.js &       # run in background, survive logout
jobs; bg; fg
lsof -i :3000             # which process uses port 3000
```

### Q30. What is a zombie process? What is load average?
**Zombie:** a finished process whose parent hasn't read its exit status (shows as `Z`; uses no CPU, but a table entry). **Load average** (`uptime`) shows average number of processes **running or waiting** over 1, 5, 15 minutes. If it's **higher than the number of CPU cores**, the system is overloaded.

### Q31. How do you check disk, memory and CPU?
```bash
df -h               # disk space of filesystems
du -sh *            # size of folders in current dir
du -h --max-depth=1 /var | sort -h
free -h             # memory (look at "available")
lscpu; nproc        # CPU info
vmstat 1; iostat    # system / disk stats
top; htop
```

### Q32. What are inodes? "Disk full" but `df -h` shows free space — why?
An **inode** stores metadata of a file (permissions, owner, location). A filesystem has a **fixed number of inodes.** If you have millions of tiny files, you can run **out of inodes** even with free space. Check with **`df -i`**.

### Q33. Hard link vs soft (symbolic) link?
**Hard link:** another name for the **same inode/data** (works even if the original name is deleted; same filesystem only). **Soft link:** a **shortcut/pointer to a path** (breaks if the target is deleted). `ln file hard` vs `ln -s file soft`.

### Q34. How do you manage services? (`systemd`)
```bash
systemctl status nginx
systemctl start|stop|restart|reload nginx
systemctl enable nginx         # start on boot
systemctl disable nginx
systemctl list-units --type=service
journalctl -u nginx -f         # live logs of a service
journalctl -xe                 # recent errors
journalctl --since "1 hour ago"
```
**Create a service for a Node app:** `/etc/systemd/system/myapp.service`
```ini
[Unit]
Description=My Node App
After=network.target

[Service]
User=ubuntu
WorkingDirectory=/home/ubuntu/myapp
ExecStart=/usr/bin/node server.js
Restart=always
Environment=NODE_ENV=production

[Install]
WantedBy=multi-user.target
```
Then `systemctl daemon-reload && systemctl enable --now myapp`.

### Q35. Networking commands?
```bash
ip a                         # IP addresses (ifconfig older)
ping google.com
curl -I https://site.com     # response headers
curl -X POST -H "Content-Type: application/json" -d '{"a":1}' http://localhost:3000/api
wget URL
ss -tulnp                    # listening ports and processes (netstat -tulnp older)
nslookup site.com / dig site.com     # DNS lookup
traceroute site.com
telnet host 5432 / nc -zv host 5432  # test if a port is reachable
```

### Q36. SSH basics? How to connect securely?
```bash
ssh -i key.pem ubuntu@1.2.3.4
chmod 400 key.pem                       # key must not be public
ssh-keygen -t ed25519                   # generate key pair
ssh-copy-id user@host                   # install public key on server
scp file user@host:/path/               # copy file
rsync -avz ./dist/ user@host:/var/www/  # sync folder
```
Security: **disable password login and root login** in `/etc/ssh/sshd_config` (`PasswordAuthentication no`, `PermitRootLogin no`), use keys, change/limit access with firewall/security groups.

### Q37. What are package managers?
Debian/Ubuntu: `apt update && apt install nginx`. RHEL/CentOS/Amazon Linux: `yum install` / `dnf install`. Alpine (Docker): `apk add`.

### Q38. What is a cron job?
Schedules tasks. `crontab -e`, format: `minute hour day month weekday command`.
```
0 2 * * *   /home/ubuntu/backup.sh        # every day at 2:00 AM
*/5 * * * * /usr/bin/health-check.sh      # every 5 minutes
```

### Q39. What is a firewall in Linux?
Controls incoming/outgoing traffic: **ufw** (simple: `ufw allow 22`, `ufw enable`), **iptables/nftables**, **firewalld.** In cloud, security groups act as the first firewall.

### Q40. Basic shell scripting example?
```bash
#!/bin/bash
set -euo pipefail          # exit on error, undefined var, pipe failure

APP="myapp"
LOG="/var/log/${APP}_deploy.log"

echo "Deploy started at $(date)" | tee -a "$LOG"

if ! systemctl is-active --quiet "$APP"; then
  echo "App is down, restarting..." | tee -a "$LOG"
  systemctl restart "$APP"
fi

for i in 1 2 3; do
  if curl -sf http://localhost:3000/health > /dev/null; then
    echo "Healthy" ; exit 0
  fi
  sleep 5
done
echo "Health check failed" ; exit 1
```
Know: variables, `if/else`, `for/while`, functions, arguments (`$1`, `$@`, `$#`), exit codes (`$?`, 0 = success), `&&` and `||`.

### Q41. What is the Linux boot process (short)?
**BIOS/UEFI → bootloader (GRUB) → kernel → init/systemd → services/targets → login.**

### Q42. What are environment variables? How to set them?
```bash
export DB_URL="postgres://..."     # for current session
echo $DB_URL; env | grep DB
# Permanent: add to ~/.bashrc or /etc/environment; for services, use Environment= in systemd or a .env file
```

### Q43. How do you find which process is using a port / how to free it?
`ss -tulnp | grep 3000` or `lsof -i :3000` → get PID → `kill PID` (or `kill -9 PID`).

### Q44. What is `tar` and how to compress/extract?
```bash
tar -czvf backup.tar.gz folder/     # create gzip archive
tar -xzvf backup.tar.gz             # extract
tar -xzvf a.tar.gz -C /target/dir
zip -r a.zip folder; unzip a.zip
```

### Q45. Common Linux troubleshooting checks (memorize this flow)
1. **Is the service running?** `systemctl status`
2. **Logs:** `journalctl -u`, `tail -f /var/log/...`
3. **Resources:** `df -h`, `free -h`, `top`, `df -i`
4. **Network/port:** `ss -tulnp`, `curl localhost:port`, firewall/security group
5. **Permissions:** `ls -l`, `id`
6. **Recent changes:** deployments, config, `history`

---

# Part 3 — Git and Networking Basics

### Q46. Essential Git commands?
```bash
git init; git clone URL
git status; git add .; git commit -m "msg"
git branch; git checkout -b feature/x     # or: git switch -c feature/x
git pull; git push origin feature/x
git merge main; git rebase main
git log --oneline --graph
git stash; git stash pop
git diff; git reset --hard HEAD~1; git revert <commit>
git tag v1.0.0
```

### Q47. Merge vs rebase? Reset vs revert?
- **Merge** keeps history and creates a merge commit; **rebase** replays your commits on top of another branch (**linear history**; don't rebase shared/public branches).
- **Reset** moves the branch pointer (rewrites history; avoid on shared branches). **Revert** creates a **new commit that undoes** an earlier one (safe for shared branches).

### Q48. Common branching strategies?
**Git Flow** (main, develop, feature, release, hotfix), **GitHub Flow** (main + short feature branches + PRs), **Trunk-based** (very short-lived branches, frequent merges to main, with feature flags). CI/CD teams often prefer trunk-based or GitHub flow.

### Q49. What are Git hooks and webhooks?
**Hooks** are scripts that run on Git events locally (pre-commit lint). **Webhooks** are HTTP calls from GitHub/GitLab to a URL (Jenkins) when events happen (push, PR), triggering the pipeline.

### Q50. How do you resolve merge conflicts?
Open the conflicted file, edit between `<<<<<<<`, `=======`, `>>>>>>>`, keep the correct code, `git add`, then `git commit` (or `git rebase --continue`).

### Q51. What does `.gitignore` do? What should never be committed?
Lists files Git should ignore: `node_modules/`, `.env`, build output, logs, `*.tfstate`. **Never commit secrets, keys, .env, terraform state.** If a secret was committed, **rotate it immediately** (removing it from history isn't enough).

### Q52. Explain what happens when you type a URL in the browser.
Browser checks cache → **DNS** resolves domain to IP → **TCP** connection (3-way handshake) → **TLS** handshake (HTTPS) → **HTTP request** sent → (possibly through CDN/load balancer/reverse proxy) → server processes and responds → browser renders HTML/CSS/JS.

### Q53. Important ports?
`22` SSH, `80` HTTP, `443` HTTPS, `3306` MySQL, `5432` PostgreSQL, `27017` MongoDB, `6379` Redis, `3000/8080` common app ports, `53` DNS, `25` SMTP.

### Q54. TCP vs UDP?
TCP: connection-based, reliable, ordered (web, SSH). UDP: connectionless, faster, no guarantee (DNS queries, streaming, gaming).

### Q55. What is DNS? Record types?
Translates domain names to IP addresses. Records: **A** (name → IPv4), **AAAA** (IPv6), **CNAME** (alias to another name), **MX** (mail), **TXT** (text/verification), **NS** (name servers). **TTL** controls caching time.

### Q56. HTTP vs HTTPS? What is SSL/TLS?
HTTPS = HTTP over **TLS** (encryption + server identity via certificates). Certificates come from a CA (Let's Encrypt, AWS ACM). In production, **TLS terminates at the load balancer/Nginx.**

### Q57. What is a reverse proxy vs forward proxy? What is a load balancer?
**Reverse proxy** (Nginx) sits in front of servers and forwards client requests (SSL, caching, routing). **Forward proxy** sits in front of clients. **Load balancer** distributes traffic across multiple servers (round robin, least connections) and checks health.

### Q58. Basic Nginx config for a Node app + React static?
```nginx
server {
  listen 80;
  server_name example.com;

  root /var/www/react-app/build;
  index index.html;
  location / { try_files $uri /index.html; }      # SPA routing

  location /api/ {
    proxy_pass http://localhost:3000;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  }
}
```
Test and reload: `nginx -t && systemctl reload nginx`.

### Q59. What are HTTP status codes DevOps engineers watch?
`200` OK, `301/302` redirect, `401/403` auth, `404` not found, `429` rate limited, `500` app error, **`502` bad gateway** (upstream app down/crashed), **`503`** unavailable/overloaded, **`504`** gateway timeout (upstream too slow).

### Q60. What are CIDR and subnets? 
CIDR notation shows an IP range: `10.0.0.0/16` = 65,536 addresses; `/24` = 256 addresses (251 usable in AWS subnets). A **subnet** is a smaller network inside a bigger one. **Private IP ranges:** `10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16`.

---

# Part 4 — Docker

### Q61. What is Docker? Why use it?
Docker is a platform to **package an application with all its dependencies into a container** that runs the same everywhere (laptop, server, cloud). It solves **"it works on my machine."** Benefits: consistency, lightweight, fast startup, easy scaling, isolation.

### Q62. Container vs Virtual Machine?
| Container | Virtual Machine |
|---|---|
| Shares the **host OS kernel** | Has its **own full OS** |
| Lightweight (MBs), starts in seconds | Heavy (GBs), starts in minutes |
| Process-level isolation | Hardware-level (stronger) isolation |
| Many per host | Fewer per host |

### Q63. Image vs Container?
An **image** is a read-only template (blueprint) containing app code, runtime and libraries. A **container** is a **running instance** of an image. (Like class vs object.) One image can run many containers.

### Q64. What is a Dockerfile? Important instructions?
A text file with steps to build an image.
- `FROM` base image
- `WORKDIR` set working directory
- `COPY` / `ADD` copy files
- `RUN` run a command **at build time** (install packages)
- `ENV` environment variable
- `ARG` build-time variable
- `EXPOSE` document the port
- `USER` run as a non-root user
- `CMD` default command **at run time**
- `ENTRYPOINT` fixed command that always runs
- `HEALTHCHECK`, `VOLUME`, `LABEL`

### Q65. CMD vs ENTRYPOINT?
`CMD` provides the **default command/arguments** and is **easily overridden** by `docker run image <command>`. `ENTRYPOINT` sets the **main executable** that always runs; `docker run` arguments are **appended** to it. Often used together: `ENTRYPOINT ["node"]` + `CMD ["server.js"]`.

### Q66. COPY vs ADD? RUN vs CMD? ENV vs ARG?
- `COPY` simply copies files; `ADD` also **extracts tar files and downloads URLs** (prefer `COPY`).
- `RUN` executes during **build** (creates a layer); `CMD` runs when the **container starts.**
- `ARG` exists only at **build time**; `ENV` persists into the **running container.**

### Q67. What are image layers? Why does order matter?
Each Dockerfile instruction creates a **layer.** Docker **caches layers**; if a layer changes, all layers after it are rebuilt. So put things that **change rarely first** (install dependencies) and things that **change often last** (copy source code).
```dockerfile
COPY package*.json ./     # changes rarely → cached
RUN npm ci
COPY . .                  # changes often → last
```

### Q68. Write a Dockerfile for a Node.js app.
```dockerfile
FROM node:20-alpine
WORKDIR /app
COPY package*.json ./
RUN npm ci --omit=dev
COPY . .
ENV NODE_ENV=production
EXPOSE 3000
USER node
CMD ["node", "server.js"]
```

### Q69. What is a multi-stage build? Example for React?
Uses **multiple FROM stages** so the final image contains only what is needed (smaller and safer: no build tools/source).
```dockerfile
# Stage 1: build
FROM node:20-alpine AS build
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# Stage 2: serve with nginx
FROM nginx:alpine
COPY --from=build /app/dist /usr/share/nginx/html     # CRA uses /app/build
COPY nginx.conf /etc/nginx/conf.d/default.conf
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

### Q70. Essential Docker commands?
```bash
docker build -t myapp:1.0 .
docker run -d -p 8080:3000 --name web -e NODE_ENV=production myapp:1.0
docker ps            # running containers;  docker ps -a  (all)
docker images
docker logs -f web
docker exec -it web sh          # shell inside container
docker stop web; docker start web; docker restart web; docker rm web; docker rmi myapp:1.0
docker inspect web
docker stats                     # live resource usage
docker pull nginx; docker tag myapp:1.0 user/myapp:1.0; docker push user/myapp:1.0
docker login
docker system prune -a           # remove unused data (careful)
docker cp web:/app/log.txt .
```

### Q71. What does `-p 8080:3000` mean? EXPOSE vs `-p`?
`-p HOST_PORT:CONTAINER_PORT` publishes the container's port 3000 on the host's port 8080. `EXPOSE` is only **documentation** inside the image; it does **not** publish the port. You need `-p` (or `-P`).

### Q72. What is a Docker volume? Types of storage?
Containers' filesystem is **temporary** (data is lost when the container is removed). To persist data:
- **Volume:** managed by Docker (`docker volume create`, `-v mydata:/var/lib/postgresql/data`) — **recommended**
- **Bind mount:** maps a **host folder** (`-v $(pwd):/app`) — good for development
- **tmpfs:** in memory only

### Q73. Docker networking types?
- **bridge** (default): containers on the same host talk via a private network; user-defined bridges give **DNS by container name**
- **host:** shares the host's network (no isolation)
- **none:** no network
- **overlay:** multi-host networking (Swarm/Kubernetes-like)
- **macvlan:** gives containers their own MAC/IP on the network
Containers on a custom network reach each other by **service/container name** (`mongodb://mongo:27017`).

### Q74. What is Docker Compose? Example for a MERN app.
A tool to define and run **multi-container** applications with one YAML file.
```yaml
services:
  frontend:
    build: ./client
    ports: ["80:80"]
    depends_on: [backend]

  backend:
    build: ./server
    ports: ["3000:3000"]
    environment:
      - MONGO_URI=mongodb://mongo:27017/mydb
      - NODE_ENV=production
    depends_on: [mongo]

  mongo:
    image: mongo:7
    volumes:
      - mongo_data:/data/db

volumes:
  mongo_data:
```
Commands: `docker compose up -d`, `docker compose down`, `docker compose logs -f`, `docker compose build`, `docker compose ps`.

### Q75. What does `depends_on` do (and not do)?
It controls **start order**, but does **not wait until the service is ready.** Use **healthchecks** (`condition: service_healthy`) or retry logic in the app.

### Q76. What is a Docker registry? Docker Hub? ECR?
A place to **store and distribute images.** Docker Hub (public/private), **AWS ECR**, GitHub Container Registry, GitLab Registry, Harbor (self-hosted). Flow: build → tag → push → pull on server/Kubernetes.

### Q77. How to reduce Docker image size?
Use **small base images** (`alpine`, `slim`, `distroless`), **multi-stage builds**, combine `RUN` commands, `--omit=dev`/`--no-cache`, use **`.dockerignore`** (node_modules, .git, .env), remove temp files in the same layer.

### Q78. Docker best practices (security + performance)?
- **Don't run as root** (`USER node`)
- **Pin image versions** (`node:20-alpine`, not `latest`)
- Use **`.dockerignore`**
- **No secrets in images or Dockerfile** (use env vars/secret managers)
- **Scan images** (Trivy, Docker Scout)
- One process per container; **log to stdout/stderr**
- Add **HEALTHCHECK**
- Use multi-stage builds, **minimal base images**
- Set **resource limits** (`--memory`, `--cpus`)

### Q79. What is `.dockerignore`?
Lists files excluded from the build context (like `.gitignore`): `node_modules`, `.git`, `.env`, `Dockerfile`, `*.log`. Makes builds **faster and safer.**

### Q80. How do you debug a container that keeps exiting?
`docker ps -a` (see status/exit code) → `docker logs <container>` → `docker inspect` (exit code, OOMKilled) → run with a shell: `docker run -it --entrypoint sh image` → check the CMD, environment variables, missing files, port conflicts, memory limits.

### Q81. Docker architecture?
**Docker client** (CLI) → talks to **Docker daemon (dockerd)** → which manages images, containers, networks, volumes, using **containerd/runc** to run containers; images are pulled from a **registry.**

### Q82. What is a healthcheck in Docker?
```dockerfile
HEALTHCHECK --interval=30s --timeout=5s --retries=3 \
  CMD wget -qO- http://localhost:3000/health || exit 1
```
Docker marks the container `healthy` or `unhealthy`.

### Q83. How do you pass environment variables/secrets to a container?
`-e KEY=value`, `--env-file .env`, `environment:` in Compose, or Kubernetes ConfigMap/Secret. **Never bake secrets into the image.** For sensitive data use Docker secrets, AWS Secrets Manager/SSM, or Vault.

### Q84. What is Docker Swarm vs Kubernetes?
Swarm is Docker's simple built-in orchestrator. **Kubernetes is the industry standard** with a larger ecosystem and features. Interviews focus on Kubernetes.

### Q85. Docker `restart` policies?
`--restart no | on-failure | always | unless-stopped`. For servers, `unless-stopped` or `always` keeps containers running after crashes/reboots.

---

# Part 5 — Kubernetes

### Q86. What is Kubernetes (K8s)? Why use it?
An open-source **container orchestration platform** that automatically **deploys, scales, heals and manages containers** across a cluster of machines. Features: **auto-scaling, self-healing, rolling updates/rollbacks, load balancing/service discovery, configuration and secret management, storage orchestration.**

### Q87. Docker vs Kubernetes?
Docker **creates and runs containers** (on one machine). Kubernetes **manages many containers across many machines** (scaling, restarting, networking, deployments).

### Q88. Explain Kubernetes architecture. (VERY IMPORTANT)
**Control Plane (master):**
- **kube-apiserver:** the front door; all commands/components talk through it
- **etcd:** key-value store holding the **entire cluster state**
- **kube-scheduler:** decides **which node** runs a new pod
- **kube-controller-manager:** runs controllers (node, replication, deployment) that keep **actual state = desired state**
- **cloud-controller-manager:** talks to cloud APIs (load balancers, disks)

**Worker Node:**
- **kubelet:** agent that makes sure the pod's containers are running
- **kube-proxy:** handles networking/service routing rules
- **Container runtime:** containerd/CRI-O runs the containers

### Q89. What is a Pod?
The **smallest deployable unit** in Kubernetes: one or more containers that **share the same network (IP) and storage**, and are scheduled together. Usually **one main container per pod** (sometimes with sidecars). Pods are **temporary** (can be replaced anytime).

### Q90. Pod vs Deployment vs ReplicaSet?
- **Pod:** runs containers.
- **ReplicaSet:** ensures a **fixed number of identical pods** are running.
- **Deployment:** manages ReplicaSets and gives **rolling updates, rollbacks, scaling** declaratively. **You almost always create a Deployment**, not bare pods.

### Q91. Write a Deployment YAML.
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-app
  labels: { app: web-app }
spec:
  replicas: 3
  selector:
    matchLabels: { app: web-app }
  strategy:
    type: RollingUpdate
    rollingUpdate: { maxSurge: 1, maxUnavailable: 0 }
  template:
    metadata:
      labels: { app: web-app }
    spec:
      containers:
        - name: web
          image: myrepo/web-app:1.0.0
          ports: [{ containerPort: 3000 }]
          envFrom:
            - configMapRef: { name: web-config }
            - secretRef: { name: web-secret }
          resources:
            requests: { cpu: "100m", memory: "128Mi" }
            limits:   { cpu: "500m", memory: "256Mi" }
          readinessProbe:
            httpGet: { path: /health, port: 3000 }
            initialDelaySeconds: 5
            periodSeconds: 10
          livenessProbe:
            httpGet: { path: /health, port: 3000 }
            initialDelaySeconds: 15
            periodSeconds: 20
```

### Q92. What is a Service? Types?
A Service gives pods a **stable IP/DNS name and load-balances** across them (because pod IPs change). Types:
- **ClusterIP** (default): reachable **only inside the cluster**
- **NodePort:** exposes on each node's IP at a port (30000–32767)
- **LoadBalancer:** creates a **cloud load balancer** (AWS ELB) for external access
- **ExternalName:** maps to an external DNS name
```yaml
apiVersion: v1
kind: Service
metadata: { name: web-svc }
spec:
  type: ClusterIP
  selector: { app: web-app }
  ports:
    - port: 80
      targetPort: 3000
```

### Q93. What is Ingress? Ingress controller?
**Ingress** defines **HTTP/HTTPS routing rules** (host/path → service) with TLS, so one load balancer serves many services. An **Ingress Controller** (NGINX Ingress, AWS Load Balancer Controller, Traefik) is the component that actually implements those rules.
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata: { name: app-ingress }
spec:
  ingressClassName: nginx
  rules:
    - host: app.example.com
      http:
        paths:
          - path: /api
            pathType: Prefix
            backend: { service: { name: api-svc, port: { number: 80 } } }
          - path: /
            pathType: Prefix
            backend: { service: { name: web-svc, port: { number: 80 } } }
```

### Q94. ConfigMap vs Secret?
- **ConfigMap:** non-sensitive configuration (URLs, flags).
- **Secret:** sensitive data (passwords, tokens), stored **base64-encoded, which is NOT encryption.** To really protect: enable **encryption at rest**, strict **RBAC**, and use external secret managers (**AWS Secrets Manager, Vault, External Secrets Operator**).
Use as env vars or mounted files.
```bash
kubectl create configmap web-config --from-literal=API_URL=http://api
kubectl create secret generic web-secret --from-literal=DB_PASSWORD=pass123
```

### Q95. Liveness vs readiness vs startup probes?
- **Liveness:** is the app alive? If it fails → **container is restarted.**
- **Readiness:** is the app ready for traffic? If it fails → pod is **removed from the Service endpoints** (not restarted).
- **Startup:** for slow-starting apps; disables other probes until startup succeeds.

### Q96. Requests vs limits?
**Requests** = the guaranteed minimum resources; the **scheduler** uses them to place the pod. **Limits** = the maximum allowed. Exceeding the **memory limit → OOMKilled**; exceeding CPU limit → **throttled.**

### Q97. What is a Namespace?
A way to **divide a cluster** into virtual sections (dev, staging, prod, team-a) for isolation, access control and resource quotas. Default namespaces: `default`, `kube-system`, `kube-public`.

### Q98. Persistent storage: PV, PVC, StorageClass?
- **PersistentVolume (PV):** actual storage (EBS disk, EFS).
- **PersistentVolumeClaim (PVC):** a **request for storage** by a pod.
- **StorageClass:** defines the storage type and enables **dynamic provisioning.**
Pods are temporary, but data in a PV **survives pod restarts.**

### Q99. StatefulSet vs Deployment? DaemonSet? Job/CronJob?
- **StatefulSet:** for **stateful apps** (databases): stable pod names (`db-0`, `db-1`), stable storage, ordered start/stop.
- **Deployment:** for **stateless** apps.
- **DaemonSet:** runs **one pod on every node** (log collectors, monitoring agents).
- **Job:** run to completion once. **CronJob:** Jobs on a schedule.

### Q100. How does auto-scaling work in Kubernetes?
- **HPA (Horizontal Pod Autoscaler):** changes the **number of pods** based on CPU/memory/custom metrics (needs metrics-server).
- **VPA:** adjusts pod resource requests/limits.
- **Cluster Autoscaler / Karpenter:** adds/removes **nodes.**
```bash
kubectl autoscale deployment web-app --cpu-percent=70 --min=2 --max=10
```

### Q101. Rolling update and rollback commands?
```bash
kubectl set image deployment/web-app web=myrepo/web-app:1.1.0
kubectl rollout status deployment/web-app
kubectl rollout history deployment/web-app
kubectl rollout undo deployment/web-app                  # back to previous
kubectl rollout undo deployment/web-app --to-revision=2
kubectl scale deployment web-app --replicas=5
kubectl rollout restart deployment/web-app
```

### Q102. Must-know `kubectl` commands?
```bash
kubectl get pods -o wide -n mynamespace
kubectl get all; kubectl get nodes; kubectl get svc,ing,deploy
kubectl describe pod <pod>          # events, reasons for failures
kubectl logs <pod> -f [-c container] [--previous]
kubectl exec -it <pod> -- sh
kubectl apply -f file.yaml; kubectl delete -f file.yaml
kubectl port-forward svc/web-svc 8080:80
kubectl top pods; kubectl top nodes
kubectl config get-contexts; kubectl config use-context prod
kubectl get events --sort-by=.metadata.creationTimestamp
kubectl explain deployment.spec
kubectl run test --image=busybox -it --rm -- sh
```

### Q103. `kubectl apply` vs `create`?
`create` makes a new resource (errors if it exists). **`apply` creates or updates declaratively** (recommended; used in CI/CD and GitOps).

### Q104. Pod lifecycle and common statuses?
Pending → Running → Succeeded/Failed. Common problem statuses:
- **Pending:** no node can run it (insufficient resources, taints, PVC not bound)
- **ImagePullBackOff / ErrImagePull:** wrong image name/tag or no registry credentials
- **CrashLoopBackOff:** container starts and **keeps crashing** (app error, bad config, failed liveness probe)
- **OOMKilled:** exceeded memory limit
- **CreateContainerConfigError:** missing ConfigMap/Secret
- **Evicted:** node out of resources

### Q105. How do you troubleshoot a pod in CrashLoopBackOff? (VERY COMMON)
1. `kubectl get pods` → see status/restarts.
2. `kubectl describe pod <pod>` → check **Events** and exit code/reason (OOMKilled?).
3. `kubectl logs <pod> --previous` → logs of the **crashed** container.
4. Check **env vars, ConfigMaps/Secrets, DB connection, command/args, probes** (liveness too aggressive?), **resource limits.**
5. Fix, redeploy, verify with `rollout status`.

### Q106. A pod is stuck in Pending. Why?
`kubectl describe pod` → Events: **insufficient CPU/memory**, node selector/affinity mismatch, **taints** without tolerations, **PVC not bound**, quota exceeded. Fix by scaling nodes, adjusting requests, or tolerations.

### Q107. How do pods communicate with each other?
Every pod gets its **own IP**; all pods can reach each other (flat network via CNI plugins like Calico, Flannel, AWS VPC CNI). Use **Service DNS names:** `service-name.namespace.svc.cluster.local` (or just `service-name` in the same namespace).

### Q108. What is a Network Policy?
Rules that **control which pods can talk to which** (like a firewall inside the cluster). By default, all pods can talk to all pods.

### Q109. What is RBAC?
**Role-Based Access Control:** controls who can do what. **Role/ClusterRole** (permissions) + **RoleBinding/ClusterRoleBinding** (assign to a user/group/**ServiceAccount**). Follow **least privilege.**

### Q110. What are taints, tolerations, node selectors, affinity?
- **Taint** on a node: "don't schedule pods here unless they tolerate it."
- **Toleration** on a pod: "I can run on a tainted node."
- **nodeSelector / nodeAffinity:** "schedule me on nodes with this label."
- **podAntiAffinity:** "don't put my replicas on the same node" (high availability).

### Q111. What are init containers and sidecars?
**Init container:** runs **before** the main container and must finish (wait for DB, run migrations). **Sidecar:** runs **alongside** the main container (log shipper, proxy such as Envoy in a service mesh).

### Q112. What is Helm?
The **package manager for Kubernetes.** A **chart** bundles templated YAML; **values.yaml** customizes it. Commands: `helm install myapp ./chart`, `helm upgrade --install`, `helm rollback myapp 1`, `helm list`, `helm repo add`. Great for deploying the same app to different environments.

### Q113. What is Kustomize? Helm vs Kustomize?
Kustomize customizes plain YAML using **overlays/patches** (no templating). Helm uses **templates + values + releases.** Both manage environment differences.

### Q114. Managed Kubernetes services?
**AWS EKS**, Azure AKS, Google GKE. The cloud runs the **control plane**; you manage worker nodes (or use **Fargate**/managed node groups). Local: **minikube, kind, k3s, Docker Desktop.**

### Q115. How do you deploy a full stack app to Kubernetes?
Build and push images (frontend, backend) to a registry (ECR) → create **Deployments** for each → **Services** (ClusterIP) → **Ingress** for external access with TLS → **ConfigMaps/Secrets** for config → DB as **managed service (RDS/Atlas)** or StatefulSet with PVC → **HPA** for scaling → **probes** and **resource limits** → monitoring/logging → CI/CD (Jenkins/GitHub Actions/ArgoCD) to update the image tag.

### Q116. What happens when you run `kubectl apply -f deployment.yaml`? 
kubectl sends the request to the **API server** → validated and saved in **etcd** → **Deployment controller** creates a **ReplicaSet** → ReplicaSet creates **Pods** → **scheduler** assigns pods to nodes → **kubelet** on the node pulls the image and starts containers → status is reported back.

### Q117. How does Kubernetes self-heal?
Controllers constantly compare **desired vs actual state.** If a pod crashes, a node dies or the liveness probe fails, Kubernetes **recreates/reschedules** pods automatically.

### Q118. What is a Service Mesh? (awareness)
A layer (Istio, Linkerd) that handles **service-to-service communication**: mTLS encryption, traffic splitting (canary), retries, observability, using sidecar proxies. Used in large microservice systems.

### Q119. How do you handle zero-downtime deployments in Kubernetes?
**Rolling update** with `maxUnavailable: 0`, **readiness probes** (traffic only to ready pods), **graceful shutdown** (handle SIGTERM, `terminationGracePeriodSeconds`, preStop hook), **multiple replicas**, and **PodDisruptionBudget.**

### Q120. How do you secure a Kubernetes cluster?
RBAC with least privilege, **don't run containers as root**, Network Policies, **encrypt secrets** / external secret manager, **scan images**, keep Kubernetes updated, restrict API server access, **Pod Security Standards**, use private registries, audit logging.

---

# Part 6 — Jenkins and CI/CD

### Q121. What is Jenkins?
An **open-source automation server** used to build **CI/CD pipelines.** It automatically builds, tests and deploys code whenever changes are pushed. It has **1800+ plugins** (Git, Docker, Kubernetes, AWS, SonarQube, Slack).

### Q122. Jenkins architecture?
- **Controller (formerly master):** the brain; manages jobs, scheduling, UI, configuration, plugins.
- **Agents (formerly slaves/nodes):** machines or containers that **actually run the builds.**
Use **many agents** (static VMs or dynamic Docker/Kubernetes agents) so the controller isn't overloaded and builds run in parallel. Don't run heavy builds on the controller.

### Q123. What is a Jenkins pipeline? Declarative vs Scripted?
A pipeline is **CI/CD defined as code in a `Jenkinsfile`** stored in Git.
- **Declarative:** structured, simple syntax (`pipeline { agent... stages {...} }`); **recommended.**
- **Scripted:** uses Groovy more freely (`node { ... }`); more flexible but more complex.

### Q124. Freestyle job vs Pipeline job?
**Freestyle:** configured through the UI; simple, not version-controlled. **Pipeline:** defined in a **Jenkinsfile** (versioned, reviewable, reusable, supports complex flows). Prefer pipelines.

### Q125. Write a complete declarative Jenkinsfile for a Node.js app → Docker → deploy.
```groovy
pipeline {
  agent any

  environment {
    IMAGE_NAME = 'myrepo/web-app'
    IMAGE_TAG  = "${env.BUILD_NUMBER}"
  }

  options {
    timestamps()
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

    stage('Lint & Test') {
      parallel {
        stage('Lint') { steps { sh 'npm run lint' } }
        stage('Unit Tests') { steps { sh 'npm test' } }
      }
    }

    stage('Build Docker Image') {
      steps { sh 'docker build -t $IMAGE_NAME:$IMAGE_TAG .' }
    }

    stage('Push Image') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub-creds',
                         usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
          sh '''
            echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
            docker push $IMAGE_NAME:$IMAGE_TAG
          '''
        }
      }
    }

    stage('Deploy to Staging') {
      steps {
        sh 'kubectl set image deployment/web-app web=$IMAGE_NAME:$IMAGE_TAG -n staging'
        sh 'kubectl rollout status deployment/web-app -n staging'
      }
    }

    stage('Approval') {
      when { branch 'main' }
      steps { input message: 'Deploy to production?' }
    }

    stage('Deploy to Production') {
      when { branch 'main' }
      steps {
        sh 'kubectl set image deployment/web-app web=$IMAGE_NAME:$IMAGE_TAG -n production'
        sh 'kubectl rollout status deployment/web-app -n production'
      }
    }
  }

  post {
    success { echo 'Pipeline succeeded' }
    failure { echo 'Pipeline failed' /* e.g., slackSend(...) */ }
    always  { cleanWs() }
  }
}
```

### Q126. Explain the sections of a declarative pipeline.
- `pipeline {}` root block
- `agent` where to run (`any`, `label 'linux'`, `docker { image 'node:20' }`, `kubernetes {}`)
- `environment` variables
- `options` (timeout, retry, timestamps)
- `parameters` user inputs (string, choice, boolean)
- `triggers` (cron, pollSCM, upstream)
- `stages` → `stage` → `steps`
- `when` conditions, `parallel`, `input` (manual approval)
- `post` actions after the build (`always`, `success`, `failure`, `unstable`, `cleanup`)
- `tools` (JDK, Maven, NodeJS configured in Jenkins)

### Q127. How can a Jenkins job be triggered?
- **Webhook** from GitHub/GitLab on push/PR (best; instant)
- **Poll SCM** (`H/5 * * * *`: Jenkins checks Git periodically; less efficient)
- **Build periodically** (cron, e.g., nightly)
- **Upstream job** finished, **manual**, or **remote API call** with a token

### Q128. How do you manage secrets/credentials in Jenkins?
Store them in **Jenkins Credentials** (username/password, secret text, SSH key, files) and use them with **`credentials()`** in `environment` or **`withCredentials`.** Jenkins **masks** secrets in logs. Never hardcode secrets in the Jenkinsfile. Also integrate **Vault/AWS Secrets Manager** for advanced setups.

### Q129. What is a multibranch pipeline?
Jenkins **automatically discovers branches and pull requests** in a repo that contain a Jenkinsfile and creates a pipeline for each. Great for feature-branch CI and PR validation.

### Q130. What are Jenkins shared libraries?
Reusable pipeline code (Groovy) stored in a separate Git repo and imported into many Jenkinsfiles (`@Library('my-lib') _`). Avoids copy-paste across projects.

### Q131. How do you run Jenkins agents on Docker/Kubernetes?
Use the **Docker** plugin (agent as a container) or **Kubernetes plugin**: Jenkins creates a **pod per build** and deletes it after, which gives clean, scalable, on-demand agents.
```groovy
agent { docker { image 'node:20-alpine' } }
```

### Q132. How do you pass parameters to a pipeline?
```groovy
parameters {
  choice(name: 'ENV', choices: ['dev','staging','prod'], description: 'Target')
  string(name: 'VERSION', defaultValue: 'latest')
  booleanParam(name: 'RUN_TESTS', defaultValue: true)
}
// use: ${params.ENV}
```

### Q133. How do you handle build artifacts and test reports?
```groovy
post { always { junit 'reports/**/*.xml'; archiveArtifacts artifacts: 'dist/**', fingerprint: true } }
```
Store large artifacts in **Nexus/Artifactory/S3** and images in a **registry.**

### Q134. How do you secure Jenkins?
Enable **authentication** (LDAP/SSO/GitHub OAuth) and **role-based authorization** (Role Strategy plugin), **don't run builds on the controller**, keep Jenkins and plugins **updated**, use **HTTPS**, manage secrets in Credentials, restrict script approval/sandbox, limit plugin installs, and **back up `JENKINS_HOME`.**

### Q135. How do you back up Jenkins?
Back up the **`JENKINS_HOME`** directory (jobs, config, credentials) or use the ThinBackup plugin. Better: keep **pipelines in Git** and **Jenkins configuration as code (JCasC)** so Jenkins can be rebuilt quickly.

### Q136. How do you speed up a slow Jenkins pipeline?
**Run stages in parallel**, use **caching** (npm/Docker layers), use **more agents**, run only needed stages (`when`), avoid unnecessary checkouts, use lightweight agents, split big test suites, and use **incremental builds.**

### Q137. A Jenkins build is failing. How do you troubleshoot?
Open **Console Output** → find the first error → check the stage that failed → reproduce the command locally on the agent → check **credentials, tool versions, disk space, network access, Docker daemon permissions** (Jenkins user must be in the `docker` group) → check recent code/config changes → fix and re-run.

### Q138. Common Jenkins issues?
- **"permission denied" for Docker:** add the jenkins user to the docker group
- **Disk full:** old builds/workspaces/Docker images (`docker system prune`, discard old builds)
- **Agent offline:** network/SSH/agent process issues
- **Plugin conflicts after upgrade**
- **Credentials not found:** wrong credential ID/scope
- **Queue stuck:** no available executors/agent label mismatch

### Q139. Jenkins vs GitHub Actions vs GitLab CI?
| Jenkins | GitHub Actions / GitLab CI |
|---|---|
| Self-hosted, you manage the server | Hosted, integrated with the repo |
| Very flexible, huge plugin ecosystem | Simpler setup, YAML-based |
| More maintenance | Less maintenance |
Many companies still use Jenkins for complex, on-prem pipelines; know at least one alternative.

### Q140. Example: GitHub Actions workflow (awareness)
```yaml
name: CI
on: { push: { branches: [main] }, pull_request: {} }
jobs:
  build-test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: { node-version: 20, cache: npm }
      - run: npm ci
      - run: npm test
      - run: docker build -t myapp:${{ github.sha }} .
```

### Q141. What is a build artifact? Artifact repository?
The output of a build (JAR, Docker image, ZIP, npm package). Stored in a repository (**Nexus, JFrog Artifactory, ECR, S3, npm registry**) so the **same tested artifact** is promoted through staging to production (**"build once, deploy many"**).

### Q142. How do you version Docker images in CI/CD?
Tag with **build number, Git commit SHA, or semantic version** (`1.4.2`, `abc1234`). **Avoid deploying `latest` to production** (not traceable, hard to roll back).

### Q143. What is the difference between CI/CD for frontend vs backend?
Frontend: lint, test, **build static files**, deploy to **S3 + CloudFront** or Nginx container, **cache-bust**/invalidate CDN. Backend: test, build image, deploy containers, run **DB migrations**, health checks, rolling update.

### Q144. How do you handle database migrations in CI/CD?
Run migrations as a **pipeline step or Kubernetes Job/init container** before/with deploy; make changes **backward compatible** (expand → deploy code → contract), keep migrations in Git, **take a backup** before risky changes, and have a rollback plan.

### Q145. What are quality gates? SonarQube?
Rules a build must pass before moving on (tests pass, **coverage ≥ X%**, no critical vulnerabilities/code smells). **SonarQube** analyzes code quality and security; the pipeline can **fail** if the quality gate fails.

---

# Part 7 — Terraform

### Q146. What is Terraform?
An open-source **Infrastructure as Code tool by HashiCorp** to **create, change and version cloud infrastructure** (AWS, Azure, GCP, Kubernetes, etc.) using **declarative code in HCL** (HashiCorp Configuration Language). It is **cloud-agnostic** (uses providers).

### Q147. Why use Terraform? Terraform vs CloudFormation vs Ansible?
| Terraform | CloudFormation | Ansible |
|---|---|---|
| Multi-cloud, HCL | AWS-only, JSON/YAML | Configuration management (mostly) |
| Keeps a **state file** | State managed by AWS | Agentless, push-based, YAML playbooks |
| Large provider ecosystem | Deep AWS integration | Installs software/configures servers |
**Terraform provisions infrastructure; Ansible configures what runs on it.**

### Q148. Terraform workflow? (VERY IMPORTANT)
`terraform init` → `terraform validate` / `fmt` → **`terraform plan`** → **`terraform apply`** → (later) **`terraform destroy`**.
- `init`: downloads providers/modules, sets up the backend
- `plan`: **preview** of changes (create/update/destroy)
- `apply`: makes the changes (creates/updates the **state**)
- `destroy`: deletes everything managed

### Q149. Main Terraform concepts?
**Provider** (plugin to talk to AWS etc.), **Resource** (infra object), **Data source** (read existing info), **Variable** (input), **Output** (exported values), **Locals** (internal named values), **Module** (reusable group of resources), **State** (record of what exists), **Backend** (where state is stored).

### Q150. What is the Terraform state file? Why is it important?
`terraform.tfstate` is a **JSON file mapping your code to real resources** (IDs, attributes). Terraform compares **code ↔ state ↔ real infrastructure** to decide changes. It may contain **sensitive data**, so **never commit it to Git**; protect it.

### Q151. What is a remote backend? State locking?
Store state **remotely** (S3, Terraform Cloud, Azure Blob, GCS) so a **team shares it** safely. **State locking** prevents two people from running `apply` at the same time and corrupting state. With **S3**, the classic setup used a **DynamoDB table for locking**; newer Terraform versions can also lock using S3 itself (`use_lockfile`). Also enable **versioning and encryption** on the bucket.
```hcl
terraform {
  backend "s3" {
    bucket         = "my-company-tf-state"
    key            = "prod/app/terraform.tfstate"
    region         = "ap-south-1"
    dynamodb_table = "tf-state-lock"
    encrypt        = true
  }
}
```

### Q152. Write a basic Terraform config (provider, variables, resources, outputs).
```hcl
terraform {
  required_version = ">= 1.5.0"
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

provider "aws" {
  region = var.region
}

variable "region" {
  type    = string
  default = "ap-south-1"
}

variable "instance_type" {
  type    = string
  default = "t3.micro"
}

data "aws_ami" "al2023" {
  most_recent = true
  owners      = ["amazon"]
  filter {
    name   = "name"
    values = ["al2023-ami-*-x86_64"]
  }
}

resource "aws_security_group" "web_sg" {
  name        = "web-sg"
  description = "Allow HTTP and SSH"

  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["203.0.113.10/32"]   # only your IP
  }
  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}

resource "aws_instance" "web" {
  ami                    = data.aws_ami.al2023.id
  instance_type          = var.instance_type
  vpc_security_group_ids = [aws_security_group.web_sg.id]

  tags = {
    Name        = "web-server"
    Environment = "dev"
  }
}

output "public_ip" {
  value = aws_instance.web.public_ip
}
```

### Q153. Variables: how do you provide values?
Priority (low → high): **default** in the variable → `terraform.tfvars` / `*.auto.tfvars` → `-var-file=prod.tfvars` → **`-var "name=value"`** → environment variable `TF_VAR_name`. Mark secrets with **`sensitive = true`.** Add **validation** blocks for input checks.

### Q154. `count` vs `for_each`?
Both create **multiple resources.** `count` uses a number/index (removing an item in the middle can shift and recreate others). **`for_each`** uses a **map/set with stable keys** (safer, preferred).
```hcl
resource "aws_s3_bucket" "b" {
  for_each = toset(["logs", "assets", "backups"])
  bucket   = "myco-${each.key}"
}
```

### Q155. What is a Terraform module? Why use it?
A **reusable, self-contained package of Terraform code** (e.g., `vpc`, `ec2`, `rds`) called with inputs and returning outputs. Benefits: **DRY, consistency, easier maintenance,** shared across environments. Use the public **Terraform Registry** or your own modules.
```hcl
module "vpc" {
  source = "terraform-aws-modules/vpc/aws"
  name   = "main-vpc"
  cidr   = "10.0.0.0/16"
}
```

### Q156. What are Terraform workspaces?
Multiple **named state instances** for the same configuration (`dev`, `staging`, `prod`). Commands: `terraform workspace new dev`, `select`, `list`. Many teams prefer **separate folders/state files (or Terragrunt)** per environment for stronger separation.

### Q157. How do you structure Terraform for multiple environments?
```
terraform/
 ├─ modules/ (vpc, ec2, rds, eks)
 └─ envs/
     ├─ dev/   (main.tf, variables.tf, dev.tfvars, backend config)
     ├─ staging/
     └─ prod/
```
Each environment has its **own state**, calls shared modules with different variables.

### Q158. What are `terraform import`, `taint`/`replace`, `state` commands?
- `terraform import` brings an **existing resource** under Terraform management (then write matching code).
- `terraform apply -replace=aws_instance.web` forces **recreation** of a resource (replaces the older `taint`).
- `terraform state list | show | mv | rm` inspect/modify the state.
- `terraform output`, `terraform refresh` (now part of plan/apply), `terraform graph`, `terraform fmt`, `terraform validate`.

### Q159. What is drift? How do you detect and fix it?
Real infrastructure differs from the state/code (manual console changes). **`terraform plan`** shows the drift. Fix by either **applying** (to restore the code's state) or **updating the code** to match. Prevent by restricting console access.

### Q160. `depends_on`, implicit vs explicit dependencies?
Terraform builds a dependency graph automatically from **references** (`aws_security_group.web_sg.id` → implicit). Use **`depends_on`** only when the dependency isn't visible through references.

### Q161. What are lifecycle rules?
```hcl
lifecycle {
  create_before_destroy = true     # make new before deleting old (zero downtime)
  prevent_destroy       = true     # block accidental deletion (databases)
  ignore_changes        = [tags]   # ignore external changes to these attributes
}
```

### Q162. What are provisioners? Should you use them?
`local-exec` / `remote-exec` run scripts after creation. HashiCorp recommends them as a **last resort** (not declarative). Prefer **user_data, cloud-init, Packer images, or Ansible.**

### Q163. How do you handle secrets in Terraform?
Never hardcode them. Use **sensitive variables, environment variables (`TF_VAR_...`), AWS Secrets Manager/SSM Parameter Store, or Vault.** Remember secrets can still appear in the **state file**, so **encrypt and restrict access to state.**

### Q164. What is `terraform plan -out` and why use it?
Saves the exact plan: `terraform plan -out=tfplan` then `terraform apply tfplan`, so what you **reviewed is exactly what gets applied** (good for CI/CD with approval).

### Q165. How do you use Terraform in a CI/CD pipeline?
On a pull request: `fmt -check`, `validate`, `plan` (post the plan as a comment). After merge/approval: `apply` using **remote state with locking** and **short-lived credentials (IAM role/OIDC)**. Tools: Jenkins, GitHub Actions, Atlantis, Terraform Cloud. Add policy checks (**OPA/Sentinel, tfsec, Checkov**).

### Q166. What is `.terraform.lock.hcl`?
Locks **provider versions** (and hashes) for consistent runs across machines. **Commit it to Git.** (Do not commit `.terraform/` folder or state.)

### Q167. What are Terraform functions, conditionals, dynamic blocks (awareness)?
```hcl
instance_type = var.env == "prod" ? "t3.large" : "t3.micro"      # conditional
name = "app-${var.env}"                                           # interpolation
cidr = cidrsubnet("10.0.0.0/16", 8, 1)                            # function
dynamic "ingress" {                       # generate repeated blocks
  for_each = var.ports
  content {
    from_port   = ingress.value
    to_port     = ingress.value
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
}
```
Others: `length, merge, lookup, join, file, templatefile, toset, tolist`.

### Q168. What happens if two people run `terraform apply` at the same time?
Without locking, the state could be **corrupted.** With a **remote backend that supports locking**, the second run fails/waits until the lock is released. If a lock gets stuck: `terraform force-unlock <ID>` (use carefully).

### Q169. How do you recover if the state file is lost/corrupted?
Restore from **S3 versioning/backup.** If lost entirely, **re-import** resources with `terraform import` (painful), which is why remote state with versioning and backups is essential.

### Q170. What is OpenTofu? (awareness)
An open-source fork of Terraform (community-driven, under the Linux Foundation) created after Terraform's license change. It's largely compatible with Terraform syntax and workflow.

---

# Part 8 — AWS

### Q171. What is AWS? What is cloud computing? Service models?
**Cloud computing** = renting computing resources (servers, storage, databases) over the internet with **pay-as-you-go** pricing. **AWS** is Amazon's cloud platform. Models: **IaaS** (EC2: you manage OS and up), **PaaS** (Elastic Beanstalk, RDS: AWS manages more), **SaaS** (ready software).

### Q172. Region, Availability Zone, Edge Location?
- **Region:** a geographic area with multiple data centers (e.g., `ap-south-1` Mumbai, `us-east-1` N. Virginia).
- **Availability Zone (AZ):** one or more **isolated data centers** inside a region (e.g., `ap-south-1a`). Deploy across **2+ AZs for high availability.**
- **Edge location:** CDN points of presence (CloudFront) close to users.

### Q173. What is the AWS Shared Responsibility Model?
**AWS is responsible for security *of* the cloud** (hardware, data centers, network, managed-service infrastructure). **You are responsible for security *in* the cloud** (IAM, data, OS patching on EC2, security groups, encryption, app security).

### Q174. What is IAM? Users, groups, roles, policies?
**Identity and Access Management** controls **who can access what** in AWS.
- **User:** a person/app with long-term credentials
- **Group:** collection of users sharing permissions
- **Role:** an identity with **temporary credentials** assumed by AWS services (EC2, Lambda), other accounts, or federated users — **preferred over access keys**
- **Policy:** a JSON document defining permissions (Allow/Deny on actions and resources)
```json
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Action": ["s3:GetObject", "s3:PutObject"],
    "Resource": "arn:aws:s3:::my-bucket/*"
  }]
}
```

### Q175. IAM best practices?
**Least privilege**, enable **MFA**, **don't use the root account** for daily work (lock it with MFA), use **roles instead of long-term access keys**, rotate credentials, use groups, **no credentials in code/Git**, review with IAM Access Analyzer, use **CloudTrail** for auditing, SSO/Identity Center for people.

### Q176. IAM role vs IAM user? How does an EC2 instance access S3 safely?
Attach an **IAM role (instance profile)** to the EC2 instance with an S3 policy. The app gets **temporary credentials automatically** from the instance metadata, with **no access keys stored** on the server.

### Q177. Explicit deny vs allow?
By default everything is **denied.** An **explicit Deny always overrides any Allow.**

### Q178. What is EC2?
**Elastic Compute Cloud:** virtual servers in the cloud. Key concepts: **AMI** (machine image/template), **instance type** (CPU/RAM: t3, m5, c5, r5), **EBS volumes** (disk), **key pair** (SSH), **security groups**, **user data** (startup script), **Elastic IP** (static public IP), **tags**.

### Q179. EC2 purchasing options?
- **On-Demand:** pay per second/hour, no commitment
- **Reserved Instances / Savings Plans:** 1–3 year commitment, big discount (steady workloads)
- **Spot:** spare capacity at up to ~90% off, **can be interrupted** (batch jobs, stateless)
- **Dedicated Hosts:** physical server for compliance/licensing

### Q180. EBS vs Instance Store vs EFS vs S3?
| Storage | Type | Notes |
|---|---|---|
| **EBS** | Block storage (a disk) | Persistent, attached to one EC2 (same AZ), snapshots |
| **Instance store** | Block storage on host | **Temporary** (lost on stop/terminate), very fast |
| **EFS** | Shared file system (NFS) | Many instances at once, multi-AZ |
| **S3** | Object storage | Unlimited, durable (99.999999999%), accessed via API/HTTP |

### Q181. Security Group vs NACL? (VERY COMMON)
| Security Group | Network ACL |
|---|---|
| **Instance/ENI level** | **Subnet level** |
| **Stateful** (return traffic automatically allowed) | **Stateless** (need rules for both directions) |
| **Allow rules only** | Allow **and deny** rules |
| All rules evaluated | Rules evaluated **in number order** |

### Q182. What is a VPC? Its components?
**Virtual Private Cloud:** your **isolated private network** in AWS.
- **Subnets:** public (route to internet gateway) and private (no direct internet)
- **Route tables:** decide where traffic goes
- **Internet Gateway (IGW):** internet access for the VPC
- **NAT Gateway:** lets **private subnet instances reach the internet outbound** (updates, APIs) without being reachable from the internet (placed in a public subnet)
- **Security groups and NACLs**
- **VPC peering / Transit Gateway:** connect VPCs; **VPC endpoints:** private access to AWS services (S3, DynamoDB) without internet

### Q183. Public vs private subnet?
**Public subnet:** route table has a route to the **Internet Gateway** (`0.0.0.0/0 → igw`); hosts load balancers, bastion hosts. **Private subnet:** no direct route to IGW; hosts **app servers and databases** (outbound via NAT).

### Q184. Design a standard 3-tier architecture on AWS. (VERY COMMON)
```
Users → Route 53 (DNS) → CloudFront (CDN, optional) → ALB (public subnets, 2 AZs)
      → App servers: EC2 Auto Scaling Group / ECS / EKS (private subnets)
      → Database: RDS Multi-AZ (private DB subnets)
Extras: S3 for static files, ElastiCache (Redis), NAT Gateway for outbound,
        Security groups per tier, CloudWatch monitoring, IAM roles, ACM certificate on ALB.
```
Why: **high availability (multi-AZ), scalability (auto scaling), security (private subnets, least exposure).**

### Q185. What is S3? Storage classes?
**Simple Storage Service:** scalable **object storage** (files = objects in buckets). Use for static websites, backups, logs, media, data lakes. Classes: **Standard, Intelligent-Tiering, Standard-IA, One Zone-IA, Glacier (Instant/Flexible/Deep Archive).** Use **lifecycle rules** to move/delete old data.

### Q186. S3 features and security?
**Versioning**, **lifecycle policies**, **encryption** (SSE-S3, SSE-KMS), **bucket policies/ACLs**, **Block Public Access** (enable by default!), **pre-signed URLs** (temporary access), **cross-region replication**, **static website hosting**, event notifications (to Lambda/SQS). S3 is **not a file system** and has strong read-after-write consistency.

### Q187. How do you host a React app on AWS?
Build static files → upload to **S3** → serve via **CloudFront** (HTTPS with **ACM** certificate, caching) → domain in **Route 53.** Use an **Origin Access Control** so the bucket stays private. For SPA routing, configure custom error response to `index.html`. Invalidate CloudFront cache on deploy. (Alternative: **AWS Amplify**, or a container on ECS/EKS with Nginx.)

### Q188. What is RDS? Multi-AZ vs Read Replica?
**Relational Database Service:** managed MySQL/PostgreSQL/MariaDB/Oracle/SQL Server/Aurora (AWS handles backups, patching, failover).
- **Multi-AZ:** a **standby copy in another AZ** for **high availability/automatic failover** (not for reads).
- **Read Replica:** **extra copy for scaling reads** (asynchronous), can be promoted; can be cross-region.
Also: automated backups + point-in-time restore, snapshots, **Aurora** (AWS-built, faster, more scalable).

### Q189. What is DynamoDB?
AWS's managed **NoSQL key-value/document database** with single-digit millisecond latency and automatic scaling. Concepts: **partition key (and sort key), RCU/WCU or on-demand capacity, GSI/LSI, TTL, streams.** MongoDB alternative on AWS: **DocumentDB** or MongoDB Atlas.

### Q190. What is a Load Balancer? ALB vs NLB vs CLB?
**Elastic Load Balancing** distributes traffic across targets and checks their health.
- **ALB (Application, Layer 7):** HTTP/HTTPS, **path/host-based routing**, WebSockets, works with ECS/EKS/Lambda
- **NLB (Network, Layer 4):** TCP/UDP, **very high performance**, static IPs
- **GWLB:** for network appliances; **CLB:** legacy

### Q191. What is Auto Scaling?
Automatically **adds/removes EC2 instances** based on demand (**Auto Scaling Group** with launch template, min/max/desired capacity, scaling policies like **target tracking on CPU 60%**) and replaces unhealthy instances. Combined with ELB across multiple AZs → **scalable and self-healing.**

### Q192. Vertical vs horizontal scaling?
**Vertical:** bigger instance (more CPU/RAM). **Horizontal:** **more instances** (preferred in cloud; needs stateless apps).

### Q193. What is Route 53?
AWS **DNS service** (also domain registration and health checks). **Routing policies:** simple, weighted (canary), latency-based, failover (active-passive), geolocation. **Alias record** points a domain to AWS resources (ALB, CloudFront, S3) and works at the root domain (unlike CNAME).

### Q194. What is CloudFront?
AWS's **CDN**: caches content at edge locations for **lower latency**, supports HTTPS, DDoS protection with Shield, and WAF integration.

### Q195. What is CloudWatch? CloudTrail? Difference?
- **CloudWatch:** **monitoring** — metrics (CPU), **logs**, alarms, dashboards, events (EventBridge).
- **CloudTrail:** **audit log of API calls** (who did what, when) for security/compliance.
**CloudWatch = performance/health; CloudTrail = who changed what.**

### Q196. What is AWS Lambda? Serverless?
Run code **without managing servers**; pay only for execution time. Triggered by events (API Gateway, S3, SQS, schedule). Limits: **15-minute max duration**, cold starts. Great for APIs, event processing, cron jobs. Related: **API Gateway, DynamoDB, Step Functions.**

### Q197. SQS vs SNS vs EventBridge?
- **SQS:** message **queue** (pull-based, decouples services; standard and FIFO)
- **SNS:** **pub/sub notifications** (push to many subscribers: email, SMS, SQS, Lambda)
- **EventBridge:** event bus with rules for routing events between services/SaaS
Common pattern: **SNS → multiple SQS queues** (fan-out).

### Q198. ECS vs EKS vs Fargate vs EC2? ECR?
- **ECS:** AWS's own container orchestrator (simpler)
- **EKS:** managed **Kubernetes**
- **Fargate:** **serverless compute for containers** (no servers to manage) — works with ECS and EKS
- **ECR:** private **Docker image registry**
Pick ECS/Fargate for simplicity; EKS for Kubernetes standardization/portability.

### Q199. Elastic Beanstalk, CloudFormation, CodePipeline — what are they?
- **Elastic Beanstalk:** PaaS; upload code and AWS provisions EC2/ELB/scaling for you.
- **CloudFormation:** AWS-native IaC (JSON/YAML templates, stacks).
- **CodeCommit/CodeBuild/CodeDeploy/CodePipeline:** AWS's CI/CD suite (note: CodeCommit is no longer open to new customers; most teams use GitHub/GitLab + CodeBuild/CodePipeline or Jenkins).

### Q200. Secrets Manager vs SSM Parameter Store vs KMS?
- **Secrets Manager:** stores secrets (DB passwords) with **automatic rotation**; paid per secret.
- **SSM Parameter Store:** stores config/secrets (**cheaper**, simple; SecureString uses KMS).
- **KMS:** manages **encryption keys** used by other services (S3, EBS, RDS, Secrets).

### Q201. What is AWS Certificate Manager (ACM)?
Provides **free SSL/TLS certificates** for use with ALB, CloudFront, API Gateway, with **automatic renewal.**

### Q202. How do you secure an AWS environment?
IAM least privilege + MFA, **private subnets** for apps/DBs, tight **security groups**, **encryption** at rest (KMS) and in transit (TLS), **S3 Block Public Access**, **CloudTrail + GuardDuty + Security Hub + Config**, **WAF/Shield** for web protection, regular patching, secrets in Secrets Manager, **no public SSH** (use **SSM Session Manager** or a bastion with restricted IP).

### Q203. What is a bastion host? What is SSM Session Manager?
A **bastion (jump) host** is a hardened EC2 in a public subnet used to SSH into private instances. **SSM Session Manager** lets you open a shell on instances **without opening port 22 or managing keys**, with logging; it is the more secure modern approach.

### Q204. What is high availability vs fault tolerance vs disaster recovery?
- **High availability:** minimal downtime (multi-AZ, load balancing, auto scaling).
- **Fault tolerance:** keeps running **with no interruption** even if components fail.
- **Disaster Recovery:** recover from major failure (region outage). Strategies from cheapest to fastest: **Backup & Restore → Pilot Light → Warm Standby → Multi-site active-active.** Terms: **RTO** (how long to recover) and **RPO** (how much data loss is acceptable).

### Q205. How do you reduce AWS costs?
**Right-size** instances, **Savings Plans/Reserved** for steady usage, **Spot** for flexible workloads, **auto scaling**, **stop dev/test resources** at night, delete unused EBS volumes/snapshots/Elastic IPs/load balancers, **S3 lifecycle rules**, use **VPC endpoints/NAT optimization**, monitor with **Cost Explorer, Budgets, Trusted Advisor,** and **tag resources.**

### Q206. AWS Well-Architected Framework pillars?
**Operational Excellence, Security, Reliability, Performance Efficiency, Cost Optimization, Sustainability.**

### Q207. How do you deploy a Node.js backend on AWS? (several ways)
1. **EC2 + Nginx + PM2/systemd** (simple)
2. **Elastic Beanstalk** (managed)
3. **ECS/Fargate** or **EKS** with Docker image from **ECR** (modern, scalable)
4. **Lambda + API Gateway** (serverless)
Put it behind an **ALB**, DB in **RDS/DocumentDB/Atlas** in private subnets, config in **SSM/Secrets Manager**, logs in **CloudWatch**, CI/CD via Jenkins/GitHub Actions.

### Q208. What are IAM roles for services, e.g., "Task role" and "Execution role" in ECS (awareness)?
**Execution role:** lets ECS **pull images and write logs.** **Task role:** gives the **application code** permissions (e.g., read S3).

### Q209. How can two VPCs/accounts communicate?
**VPC Peering** (one-to-one, no transitive routing), **Transit Gateway** (hub-and-spoke for many VPCs), **PrivateLink** (private access to a service), **VPN / Direct Connect** to on-premises.

### Q210. What is the difference between Elastic IP and public IP?
A normal **public IP changes** when an instance stops/starts. An **Elastic IP is a static public IP** that stays with your account until released (charged if unused/unattached).

---

# Part 9 — Monitoring, Logging, Security and Ansible

### Q211. Why monitoring and logging? Three pillars of observability?
To know the **health of the system, detect problems early, and troubleshoot quickly.** The pillars: **Metrics** (numbers over time), **Logs** (event records), **Traces** (request journey across services).

### Q212. What is Prometheus? How does it work?
An open-source **metrics monitoring system.** It **pulls (scrapes)** metrics from targets (apps, **exporters** like node_exporter) at intervals, stores them as **time series**, and is queried with **PromQL.** **Alertmanager** sends alerts (Slack, email, PagerDuty).
```
rate(http_requests_total[5m])                       # requests per second
sum(rate(http_requests_total{status=~"5.."}[5m]))   # 5xx rate
```

### Q213. What is Grafana?
A **visualization/dashboard tool** that reads data from Prometheus, CloudWatch, Loki, Elasticsearch, etc. and shows graphs and alerts.

### Q214. What is the ELK/EFK stack? Loki?
**Elasticsearch** (store/search logs), **Logstash/Fluentd/Fluent Bit** (collect/process), **Kibana** (visualize). **Loki** is a lightweight alternative that works with Grafana. In Kubernetes, a **DaemonSet** (Fluent Bit) ships container logs from each node.

### Q215. The four golden signals?
**Latency, Traffic, Errors, Saturation** (how full the system is). Also **RED** (Rate, Errors, Duration) for services and **USE** (Utilization, Saturation, Errors) for resources.

### Q216. What is the difference between monitoring and observability?
Monitoring tells you **that** something is wrong (known problems with dashboards/alerts). Observability helps you understand **why** (explore unknown problems using metrics, logs and traces).

### Q217. How do you design good alerts?
Alert on **symptoms that affect users** (error rate, latency), not every small cause; set sensible thresholds and durations to avoid **alert fatigue**; each alert should be **actionable** with a runbook; use severity levels and on-call rotation.

### Q218. What is distributed tracing? OpenTelemetry? 
Tracking one request across many services to find where time is spent. Tools: **Jaeger, Zipkin, AWS X-Ray, OpenTelemetry** (vendor-neutral standard for metrics/logs/traces).

### Q219. What is DevSecOps?
Building **security into every stage** of the pipeline: **SAST** (static code analysis, SonarQube), **dependency scanning** (`npm audit`, Snyk, Dependabot), **secret scanning** (gitleaks), **container image scanning** (Trivy), **IaC scanning** (tfsec, Checkov), **DAST** (OWASP ZAP), and runtime monitoring.

### Q220. How do you manage secrets across the DevOps lifecycle?
Never in Git or images. Use **Jenkins Credentials, AWS Secrets Manager/SSM, HashiCorp Vault, Kubernetes Secrets (with encryption + External Secrets)**, inject at runtime, **rotate regularly**, and apply least privilege. Add pre-commit/CI **secret scanning.**

### Q221. What is Ansible? Key concepts?
**Agentless** configuration management and automation tool using **SSH** and **YAML playbooks.** Concepts: **inventory** (list of hosts), **playbook**, **tasks**, **modules** (apt, copy, service, template), **roles**, **variables**, **handlers**, **idempotency** (running again doesn't change things already correct), **Ansible Vault** (encrypt secrets).
```yaml
- name: Setup web server
  hosts: web
  become: yes
  tasks:
    - name: Install nginx
      apt: { name: nginx, state: present, update_cache: yes }
    - name: Start nginx
      service: { name: nginx, state: started, enabled: yes }
```
Run: `ansible-playbook -i inventory.ini site.yml`.

### Q222. What is Packer? (awareness)
A HashiCorp tool to **build machine images (AMIs)** from code, supporting **immutable infrastructure** (bake the app/config into an image, then launch it with Terraform/Auto Scaling).

### Q223. What is a Bastion/VPN/Zero Trust? (awareness)
Ways to access private resources securely. Modern approach: **no direct public access, strong identity (SSO + MFA), short-lived access, audit logs.**

### Q224. What is Blue-Green deployment on AWS/Kubernetes?
Run two environments: **blue (live)** and **green (new).** Test green, then **switch traffic** (Route 53 weighted/ALB target group switch/Kubernetes Service selector change). Rollback = switch back. Cost: double resources temporarily.

### Q225. What is a canary release?
Send a **small percentage (e.g., 5%)** of traffic to the new version, watch metrics (errors/latency), and **gradually increase** if healthy or roll back. Tools: **Argo Rollouts, Istio, ALB weighted target groups, Route 53 weighted records.**

---

# Part 10 — Full-Stack Deployment Examples

### Q226. Explain the end-to-end CI/CD flow for a MERN/React + Node app. (YOUR BEST ANSWER)
1. Developer pushes code to **Git** (feature branch → pull request).
2. **Webhook triggers Jenkins** (or GitHub Actions).
3. Pipeline: **checkout → `npm ci` → lint → unit tests → SonarQube quality gate.**
4. **Build Docker images** (frontend with multi-stage + Nginx, backend Node image) tagged with **build number/commit SHA.**
5. **Scan images** (Trivy), **push to ECR/Docker Hub.**
6. **Deploy to staging** (Kubernetes `kubectl set image` or Helm upgrade / ArgoCD updates the Git manifest).
7. Run **smoke/integration tests.**
8. **Manual approval** → **deploy to production** with a **rolling update.**
9. **Monitor** with Prometheus/Grafana/CloudWatch; **rollback** automatically/manually if health checks or alerts fail.
10. Infrastructure (VPC, EKS, RDS, ALB) is created and versioned with **Terraform.**

### Q227. Architecture diagram (draw this on a whiteboard)
```
Developer → Git (GitHub) → Jenkins ──► Build & Test ──► Docker image ──► ECR
                                              │                             │
                                  Terraform ──┴─► AWS (VPC, EKS, RDS, ALB)  │
                                                                            ▼
Users → Route53 → CloudFront/ALB → Ingress → [Frontend pods] [Backend pods] → RDS/MongoDB
                                                   │
                          Prometheus/Grafana + CloudWatch + EFK for monitoring/logs
```

### Q228. Dockerfiles for a MERN project (summary)
- **Backend:** Node Alpine image, `npm ci --omit=dev`, non-root user, `CMD ["node","server.js"]` (see Q68).
- **Frontend:** multi-stage build → Nginx serving static files with SPA fallback (see Q69, Q58).
- **Compose for local dev:** frontend + backend + mongo/postgres with a volume (see Q74).

### Q229. Kubernetes manifests for the app (checklist)
`Namespace` → `ConfigMap` + `Secret` → `Deployment` (backend, frontend, with probes + resources) → `Service` (ClusterIP) → `Ingress` (TLS, `/api` → backend, `/` → frontend) → `HPA` → (optional) `PodDisruptionBudget`, `NetworkPolicy`. (See Q91–Q100.)

### Q230. HPA manifest example
```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata: { name: web-app-hpa }
spec:
  scaleTargetRef: { apiVersion: apps/v1, kind: Deployment, name: web-app }
  minReplicas: 2
  maxReplicas: 10
  metrics:
    - type: Resource
      resource:
        name: cpu
        target: { type: Utilization, averageUtilization: 70 }
```

### Q231. Terraform for the AWS side (what you'd create)
`VPC (public + private subnets, IGW, NAT)` → `Security groups` → `EKS cluster + node group` (or ECS/EC2 ASG) → `RDS (Multi-AZ, private subnets)` → `ECR repos` → `ALB` → `S3 + CloudFront (frontend, optional)` → `IAM roles` → `Route53 + ACM` → `CloudWatch alarms`. Use **modules**, **remote S3 state with locking**, separate **envs.**

### Q232. Deploying a Node.js app on a plain EC2 (classic way)
1. Launch **EC2** (Ubuntu/Amazon Linux), security group allowing **80/443 (and 22 from your IP).**
2. SSH in, install **Node.js, Nginx, Git.**
3. `git clone` the repo, `npm ci --omit=dev`, set **environment variables.**
4. Run the app with **PM2** (`pm2 start server.js -i max`, `pm2 startup`, `pm2 save`) or a **systemd** service.
5. Configure **Nginx reverse proxy** to port 3000 (see Q58).
6. Add **HTTPS** with **Certbot/Let's Encrypt** (or ALB + ACM).
7. Automate with a **CI/CD pipeline** (SSH/rsync or pull latest + restart).

### Q233. A simple Jenkins deploy-to-EC2 stage (SSH)
```groovy
stage('Deploy to EC2') {
  steps {
    sshagent(credentials: ['ec2-ssh-key']) {
      sh '''
        ssh -o StrictHostKeyChecking=no ubuntu@$EC2_HOST "
          cd /home/ubuntu/app &&
          git pull origin main &&
          npm ci --omit=dev &&
          pm2 reload all
        "
      '''
    }
  }
}
```

### Q234. How would you do zero-downtime deployment for a Node API on AWS?
Behind an **ALB** with **health checks**; **rolling update** in ECS/EKS (new tasks become healthy before old ones are removed) or **blue-green** with CodeDeploy; graceful shutdown on `SIGTERM` (finish requests, close DB connections); **backward-compatible DB migrations.**

### Q235. How do you manage different environments (dev/staging/prod)?
Same code/image, **different configuration** (ConfigMaps, env vars, Terraform `tfvars`, separate AWS accounts or namespaces, separate state files). **Promote the same image** through environments (**build once, deploy many**). Separate credentials and data per environment.

---

# Part 11 — Troubleshooting Scenarios and HR Questions

### Q236. The website is down. How do you troubleshoot? (VERY COMMON)
Work **from the user toward the server**, step by step:
1. **Confirm and scope:** is it down for everyone? Which URL/region? Check monitoring/alerts and **recent deployments or changes.**
2. **DNS:** `dig site.com` → correct IP?
3. **Load balancer / CDN:** ALB healthy targets? 5xx errors? Certificate expired?
4. **Network:** security groups/NACLs/firewall, ports open? `curl -I` from outside.
5. **Server/app:** is the service running? (`systemctl status`, `docker ps`, `kubectl get pods`) → check **logs.**
6. **Resources:** CPU, memory, disk (`df -h`, `df -i`), connection limits.
7. **Dependencies:** database, cache, third-party APIs reachable?
8. **Fix or roll back** (fastest safe option), then verify, communicate, and write a **post-mortem** (root cause + prevention).

### Q237. You get a 502 Bad Gateway. What does it mean and what do you check?
The proxy/load balancer got a **bad or no response from the upstream app.** Check: is the app **running/crashed?** correct **port** in the Nginx/ALB target group? app **health check** failing? **timeouts** (504 if slow)? security group between ALB and app? recent deployment? app logs for exceptions or out-of-memory kills.

### Q238. Disk is 100% full on a Linux server. What do you do?
`df -h` (which filesystem?) → `du -h --max-depth=1 / | sort -h` to find large folders → usually **logs** (`/var/log`), Docker data (`/var/lib/docker`), temp files. Clean safely: rotate/compress/delete old logs (`logrotate`, `journalctl --vacuum-time=7d`), `docker system prune`, check **deleted-but-open files** (`lsof | grep deleted`), check **inodes** (`df -i`). Then prevent: log rotation, monitoring alerts at 80%, bigger volume.

### Q239. CPU usage is 100%. How do you find the cause?
`top`/`htop` → find the process → check what it's doing (logs, `strace`, app profiler). Possible causes: infinite loop, heavy request, traffic spike, memory pressure causing swapping, cryptominer/malware. Fix: restart/scale out, optimize code, rate limit, investigate the root cause.

### Q240. Memory is running out / the app gets OOMKilled. What do you check?
`free -h`, `top` (sort by memory), `dmesg | grep -i oom`, container memory **limits**, **memory leak** (growing over time), too many workers/connections. Fix: set proper limits/requests, fix leaks (heap snapshot), scale out, add swap/memory as a stopgap.

### Q241. A deployment went wrong in production. What do you do?
**Rollback first** to restore service (`kubectl rollout undo`, previous image tag, switch blue-green), then investigate in staging/logs, fix, and redeploy with tests. Communicate to stakeholders. Afterwards: **blameless post-mortem,** add tests/health checks/canary to prevent a repeat.

### Q242. Pods are running, but the application is not reachable. What do you check?
`kubectl get svc,endpoints` (does the Service have **endpoints?** if empty → **selector/labels mismatch** or readiness failing) → `kubectl describe ingress` → Ingress controller running? → correct **ports/targetPort** → test inside the cluster with `kubectl port-forward` or a debug pod (`curl svc-name`) → NetworkPolicy blocking? → cloud load balancer/security group/DNS.

### Q243. Terraform apply fails with "state lock" or "resource already exists". What do you do?
- **State lock:** someone else's run (or a crashed run) holds the lock. Wait, or verify no run is active and `terraform force-unlock <ID>`.
- **Already exists:** the resource exists outside Terraform → **`terraform import`**, or rename/remove the manual resource, then `plan` again.
- **Drift/permission errors:** read the error, check IAM permissions, provider versions, and run `terraform plan` first.

### Q244. Your CI pipeline is slow (30 minutes). How do you improve it?
Profile stages, **parallelize** independent steps, **cache** dependencies/Docker layers, use **smaller images**, run **only affected tests**, add more/faster agents, avoid rebuilding unchanged parts, skip expensive stages on non-main branches, split test suites.

### Q245. A secret (API key) was committed to Git. What do you do?
**Revoke/rotate the secret immediately** (assume it's compromised), remove it from the code and use a secret manager, clean history if needed (git filter-repo/BFG) — but rotation is the real fix, check logs for misuse, and add **secret scanning (gitleaks) in pre-commit/CI.**

### Q246. How do you ensure a service has high availability?
Multiple **replicas across multiple AZs**, load balancer with **health checks**, **auto scaling**, redundant DB (Multi-AZ/replicas), stateless apps, **graceful degradation**, no single points of failure, regular **backup/restore tests**, and **DR plan.**

### Q247. Tell me about your DevOps experience / project. (template)
"In my project, I built a **React + Node.js + MongoDB/PostgreSQL** application. I **containerized** the frontend and backend with **Docker** (multi-stage builds), wrote a **docker-compose** for local development, and created a **Jenkins pipeline** that runs install, lint, tests, builds and pushes images, and deploys to **AWS** (EC2/ECS/EKS). I used **Terraform** to create the **VPC, security groups, EC2/EKS and RDS,** stored state in **S3.** I monitored the app with **CloudWatch/Prometheus-Grafana** and used **Linux** commands daily to check logs, services and resources."
*(Say only what you really did; be ready to explain each step in detail.)*

### Q248. Describe a production issue you solved.
Use this structure: **Situation → Symptom → Investigation steps → Root cause → Fix → Prevention.** Example: *"After a deployment, the API returned 502 errors. I checked the ALB target health and pod logs, found the new image had a wrong env variable causing a crash loop. I rolled back with `kubectl rollout undo`, fixed the config, and added a readiness probe and a staging smoke test."*

### Q249. What would you do in your first 30 days in a DevOps/full-stack role?
Learn the architecture and deployment flow, read pipelines/IaC, get access and run the app locally, fix small issues, **improve documentation or a pipeline step,** understand monitoring/on-call, and ask questions.

### Q250. How do you keep learning DevOps?
Hands-on labs (free-tier AWS, kind/minikube, local Jenkins), official docs, building a **personal project end to end** (code → Docker → Jenkins → Terraform → AWS/K8s), certifications (**AWS Cloud Practitioner/Solutions Architect Associate, CKA, Terraform Associate**), and following blogs/communities.

### Q251. Strengths / weakness / why should we hire you?
- **Strength:** "I understand the full path from code to production and can automate repetitive work; I debug methodically using logs and metrics."
- **Weakness:** "I'm still gaining production-scale experience with Kubernetes and Terraform modules; I practice with real projects and labs."
- **Why hire me:** "As a full stack developer with DevOps skills, I can build features and also containerize, deploy and troubleshoot them, which shortens delivery time and reduces hand-offs."

### Q252. Questions to ask the interviewer
- "What does your CI/CD pipeline and deployment process look like?"
- "Which cloud services and tools (Kubernetes/Terraform) does the team use?"
- "How does on-call and incident response work here?"

---

# Part 12 — Last-Minute Cheat Sheet

### One-line answers (memorize)
| Topic | One-line answer |
|---|---|
| DevOps | Culture + automation joining dev and ops for fast, reliable delivery |
| CI / CD | Auto build+test on every push / auto-deliver or deploy after tests |
| IaC | Manage infrastructure with version-controlled code |
| Docker | Package app + dependencies into a portable container |
| Image vs container | Blueprint vs running instance |
| Container vs VM | Shares host kernel (light) vs full OS (heavy) |
| CMD vs ENTRYPOINT | Default overridable command vs fixed main executable |
| Multi-stage build | Build in one stage, copy only output to a small final image |
| Volume | Persistent storage outside the container lifecycle |
| Docker Compose | Run multi-container apps from one YAML |
| Kubernetes | Orchestrator: scaling, self-healing, rolling updates |
| Pod | Smallest unit; one or more containers sharing network/storage |
| Deployment | Manages ReplicaSets/pods; rolling update + rollback |
| Service types | ClusterIP (internal), NodePort, LoadBalancer (external), ExternalName |
| Ingress | HTTP/HTTPS routing rules by host/path |
| ConfigMap / Secret | Non-sensitive config / sensitive data (base64, not encrypted) |
| Liveness / readiness | Restart if dead / send traffic only when ready |
| Requests / limits | Guaranteed minimum / maximum resources |
| HPA | Scales pod count by CPU/memory/metrics |
| StatefulSet / DaemonSet | Stateful apps with stable identity / one pod per node |
| Helm | Package manager for Kubernetes |
| CrashLoopBackOff | Container keeps crashing → `logs --previous`, `describe` |
| Jenkins | CI/CD automation server; Jenkinsfile = pipeline as code |
| Controller / agent | Brain & scheduler / machine that runs builds |
| Terraform | Declarative multi-cloud IaC with state |
| Terraform flow | init → plan → apply → destroy |
| State file | Maps code to real resources; store remotely with locking; never in Git |
| Module | Reusable Terraform package |
| count vs for_each | Index-based vs key-based (preferred) |
| Ansible | Agentless configuration management with YAML playbooks |
| IAM | Who can do what; use roles + least privilege + MFA |
| Security Group vs NACL | Stateful, instance-level, allow only / stateless, subnet-level, allow+deny |
| Public vs private subnet | Route to IGW / no direct internet (NAT for outbound) |
| S3 / EBS / EFS | Object / block (one EC2) / shared file system |
| Multi-AZ vs read replica | Failover (HA) / read scaling |
| ALB vs NLB | Layer 7 HTTP routing / Layer 4 high performance |
| CloudWatch vs CloudTrail | Metrics, logs, alarms / API audit trail |
| Lambda | Serverless functions, pay per execution (max 15 min) |
| ECS / EKS / Fargate | AWS orchestrator / managed Kubernetes / serverless containers |
| Blue-green / canary | Switch whole traffic / gradually shift small % |
| Prometheus / Grafana | Metrics collection (pull) / dashboards |

### Top 20 most-asked DevOps interview questions
1. What is DevOps / CI/CD / IaC?
2. Explain your CI/CD pipeline end to end
3. Docker: image vs container, Dockerfile, multi-stage builds, volumes, networking
4. Docker vs VM; Docker Compose
5. Kubernetes architecture and components
6. Pod / Deployment / Service / Ingress; ConfigMap vs Secret
7. Liveness vs readiness probes; HPA; rolling update and rollback
8. Troubleshooting CrashLoopBackOff / Pending / ImagePullBackOff
9. Jenkins architecture, Jenkinsfile (declarative), triggers, credentials
10. Terraform workflow, state, remote backend, modules, `count` vs `for_each`
11. Terraform vs CloudFormation vs Ansible
12. AWS: IAM roles, EC2, S3, VPC, public/private subnets, security group vs NACL
13. Design a highly available 3-tier architecture on AWS
14. RDS Multi-AZ vs read replica; ALB; Auto Scaling
15. Linux commands: logs, processes, disk, permissions, systemd
16. Linux troubleshooting: disk full, high CPU, port in use
17. Deployment strategies: rolling, blue-green, canary; how to rollback
18. Monitoring: Prometheus/Grafana/CloudWatch; golden signals
19. Securing pipelines, secrets, containers, and AWS
20. "Website is down; what do you do?" (systematic troubleshooting)

### Command quick-recall
```bash
# Linux
tail -f app.log | grep ERROR     df -h     free -h     ss -tulnp     systemctl status app     journalctl -u app -f
# Docker
docker build -t app:1 .   docker run -d -p 80:3000 app:1   docker logs -f c   docker exec -it c sh   docker compose up -d
# Kubernetes
kubectl get pods -A   kubectl describe pod p   kubectl logs p --previous   kubectl apply -f x.yaml   kubectl rollout undo deploy/x
# Terraform
terraform init   terraform fmt   terraform validate   terraform plan -out=tfplan   terraform apply tfplan   terraform destroy
# Git
git checkout -b feat   git add . && git commit -m "msg"   git push origin feat   git rebase main
```

### Interview day tips
- **Draw diagrams** (CI/CD flow, 3-tier AWS architecture, Kubernetes components). Visual answers impress.
- Always explain **why,** not just what: *"I used multi-stage builds to keep the image small and secure."*
- For troubleshooting questions, **think aloud and go step by step** (logs → resources → network → recent changes).
- Mention **security and cost** habits: least privilege, no secrets in Git, right-sizing, tagging.
- Be honest about depth: *"I've used X in projects and practiced Y in labs."*
- Practice with **free tools:** Docker Desktop + kind/minikube, a local Jenkins in Docker, AWS Free Tier, `terraform` with a small EC2/S3 example. **Never leave cloud resources running;** run `terraform destroy` after practice.
- Stay calm and confident. **You've prepared well.** 💪

---
**Best of luck with your interview! 🚀**
