<div align="center">

<img src="https://capsule-render.vercel.app/api?type=waving&color=0:0A2540,60:1A3A5C,100:E85D26&height=200&section=header&text=Vaibhav%20Singh&fontSize=52&fontColor=ffffff&animation=fadeIn&fontAlignY=36&desc=Site%20Reliability%20%7C%20DevOps%20%7C%20Platform%20Engineering&descAlignY=56&descSize=18" width="100%" />

<a href="https://git.io/typing-svg">
  <img src="https://readme-typing-svg.demolab.com?font=JetBrains+Mono&weight=500&size=20&pause=1200&color=E85D26&center=true&vCenter=true&width=650&lines=terraform+apply+--auto-approve;kubectl+get+pods+-A+--watch;99.9%25+uptime+is+a+design+decision%2C+not+luck;alerts+should+wake+you+for+a+reason" alt="Typing SVG" />
</a>

<br /><br />

<a href="https://www.linkedin.com/in/vaibhavsingh79/">
  <img src="https://img.shields.io/badge/LinkedIn-1A3A5C?style=for-the-badge&logo=linkedin&logoColor=6CB6FF&labelColor=0A2540" alt="LinkedIn" />
</a>
<a href="mailto:vaibhavsingh8829@gmail.com">
  <img src="https://img.shields.io/badge/Email-1A3A5C?style=for-the-badge&logo=gmail&logoColor=E85D26&labelColor=0A2540" alt="Email" />
</a>
<a href="https://leetcode.com/u/YOUR_LEETCODE_HANDLE/">
  <img src="https://img.shields.io/badge/LeetCode-1A3A5C?style=for-the-badge&logo=leetcode&logoColor=FFA116&labelColor=0A2540" alt="LeetCode" />
</a>
<a href="https://stackoverflow.com/users/17320810/vaibhav-singh">
  <img src="https://img.shields.io/badge/Stack%20Overflow-1A3A5C?style=for-the-badge&logo=stackoverflow&logoColor=F58025&labelColor=0A2540" alt="Stack Overflow" />
</a>
<img src="https://komarev.com/ghpvc/?username=vaibhavsingh79&label=Profile%20views&color=E85D26&style=for-the-badge" alt="Profile views" />

<br /><br />

<a href="#whoami"><b>whoami</b></a> &nbsp;·&nbsp;
<a href="#how-i-think-about-delivery"><b>Delivery</b></a> &nbsp;·&nbsp;
<a href="#a-topology-i-shipped"><b>Topology</b></a> &nbsp;·&nbsp;
<a href="#things-ive-built"><b>Projects</b></a> &nbsp;·&nbsp;
<a href="#reliability-work-in-production"><b>Production</b></a> &nbsp;·&nbsp;
<a href="#toolchain"><b>Toolchain</b></a>

<br />

<img src="https://capsule-render.vercel.app/api?type=rect&color=0:0A2540,50:E85D26,100:0A2540&height=3" width="100%" />

</div>

## `whoami`

```yaml
role:      Associate Cloud Engineer — Network & Security @ Minfy Technologies
focus:     production infrastructure, reliability, and the paths code takes to get there
domains:
  - CI/CD & GitOps          # GitHub Actions (OIDC), ArgoCD, Jenkins
  - Observability & SLOs    # Prometheus, Grafana, CloudWatch Metric Math
  - Kubernetes on AWS       # EKS, Helm, Network Policies, IRSA
  - Infrastructure as Code  # Terraform, CloudFormation
  - Zero-trust networking   # VPC Links, private ingress, least-privilege IAM
automates_with: [Python, Go, Bash, HCL]
certified:      [AWS Solutions Architect Associate, Azure Administrator AZ-104]
currently:      going deeper on distributed systems + platform engineering
```

> I care about the boring parts that keep things up: failover that doesn't page you at 3 AM,
> pipelines that fail loudly and early, and infrastructure you can delete and rebuild from a repo.

<img src="https://capsule-render.vercel.app/api?type=rect&color=0:0A2540,50:E85D26,100:0A2540&height=3" width="100%" />

## How I Think About Delivery

```mermaid
%%{init: {'theme':'base','themeVariables':{'primaryColor':'#1A3A5C','primaryTextColor':'#ffffff','primaryBorderColor':'#E85D26','lineColor':'#E85D26','secondaryColor':'#0A2540','tertiaryColor':'#0A2540','fontFamily':'ui-monospace, monospace'}}}%%
flowchart LR
    subgraph SRC ["Source"]
        A["git push"]
    end

    subgraph CI ["CI — fail fast, fail loud"]
        B["Build &<br/>Unit tests"]
        C["Trivy<br/>image scan"]
        D["SonarQube<br/>SAST"]
    end

    subgraph AUTH ["Identity"]
        E["OIDC token<br/>exchange"]
        F["Scoped<br/>temp IAM role"]
    end

    subgraph CD ["GitOps Delivery"]
        G["ArgoCD<br/>sync"]
        H["EKS<br/>rolling deploy"]
    end

    subgraph OPS ["Feedback loop"]
        I["Prometheus<br/>golden signals"]
        J["SLO burn rate"]
        K{"Within<br/>error budget?"}
    end

    A --> B --> C --> D --> E
    E --> F --> G --> H --> I --> J --> K
    K -- "yes" --> L["Keep shipping"]
    K -- "no" --> M["Freeze &<br/>rollback"]
    M -.-> A

    style A fill:#0A2540,stroke:#E85D26,color:#fff
    style K fill:#E85D26,stroke:#E85D26,color:#fff
    style L fill:#1A3A5C,stroke:#3fb950,color:#fff
    style M fill:#1A3A5C,stroke:#f85149,color:#fff
```

<img src="https://capsule-render.vercel.app/api?type=rect&color=0:0A2540,50:E85D26,100:0A2540&height=3" width="100%" />

## A Topology I Shipped

Private API ingress at Minfy — no public endpoints, no redundant load balancers,
and health checks that don't page on an expected VPN failover.

```mermaid
%%{init: {'theme':'base','themeVariables':{'primaryColor':'#1A3A5C','primaryTextColor':'#ffffff','primaryBorderColor':'#E85D26','lineColor':'#E85D26','secondaryColor':'#0A2540','tertiaryColor':'#0A2540','fontFamily':'ui-monospace, monospace'}}}%%
flowchart TB
    subgraph CALLERS ["Allow-listed sources"]
        direction LR
        C1["Corporate<br/>IP range"]
        C2["GCP<br/>egress range"]
        C3["NAT gateway<br/>range"]
    end

    POL["IAM resource policy<br/>rate limit · burst cap · IP allow-list"]

    subgraph AWS ["AWS account"]
        GW["API Gateway<br/>(private)"]
        VL(["VPC Link"])
        subgraph VPC ["VPC — private subnets only"]
            NLB["Internal NLB<br/>shared, EKS-provisioned"]
            S1["EKS service"]
            S2["EKS service"]
        end
        OBS["CloudWatch + EventBridge<br/>Metric Math group health"]
        SNS["Buffered SNS<br/>alerts"]
    end

    C1 --> POL
    C2 --> POL
    C3 --> POL
    POL --> GW --> VL --> NLB
    NLB --> S1
    NLB --> S2
    NLB -.->|"metrics"| OBS
    OBS -.->|"suppresses failover<br/>false positives"| SNS

    style POL fill:#E85D26,stroke:#E85D26,color:#fff
    style NLB fill:#0A2540,stroke:#E85D26,color:#fff
    style OBS fill:#1A3A5C,stroke:#E85D26,color:#fff
```

<img src="https://capsule-render.vercel.app/api?type=rect&color=0:0A2540,50:E85D26,100:0A2540&height=3" width="100%" />

## Things I've Built

<table>
<tr>
<td width="50%" valign="top">

### Zero-Trust IaC Generation Platform
`Python` `FastAPI` `Pydantic` `Jinja2` `Terraform` `Cognito` `OIDC`

Replaced static AWS credentials for **40+ engineers** with GitHub Actions OIDC — short-lived signed tokens exchanged for scoped temporary IAM roles. No long-lived keys, no rotation overhead.

<details>
<summary><b>How it works</b></summary>

<br />

- A workflow requests an OIDC token from GitHub's identity provider; an IAM trust policy scoped to the repo and branch exchanges it for a temporary role. Nothing durable is ever stored.
- Pydantic models validate every input — malformed CIDRs, overlapping subnets, bad tags — and reject them *before* Terraform touches an account.
- Jinja2 renders the validated model into VPC / subnet / IAM HCL. What used to be an afternoon of copy-paste is now a form submission.
- Stateless FastAPI backend behind Cognito (SRP), React UI, so multiple engineers can generate concurrently without stepping on each other.

</details>

</td>
<td width="50%" valign="top">

### Secure Microservices Delivery Pipeline
`Kubernetes` `ArgoCD` `Trivy` `SonarQube` `Prometheus` `Grafana`

Shift-left DevSecOps: Trivy image scanning and SonarQube SAST gate the build on critical/high CVEs. ArgoCD drives GitOps delivery with drift detection and zero-downtime rollouts.

<details>
<summary><b>How it works</b></summary>

<br />

- The build fails on critical and high-severity CVEs rather than warning about them — a scanner nobody blocks on is just a log file.
- ArgoCD reconciles cluster state against Git continuously, so manual `kubectl edit` drift gets reverted instead of silently persisting.
- Grafana dashboards track the four golden signals (latency, traffic, errors, saturation) against explicit SLOs.
- Cluster hardening: zero-trust Network Policies (default deny), least-privilege IRSA per workload, liveness/readiness probes so the cluster self-heals before a human notices.

</details>

</td>
</tr>
<tr>
<td width="50%" valign="top">

### Mini Vercel — Serverless Container Orchestration
`Python` `ECS Fargate` `Terraform` `ECR` `CloudWatch`

A small PaaS modeled on Vercel. A Python CLI triggers Docker builds, semantic versioning, and immutable ECR pushes; modular Terraform provisions an isolated VPC, subnets, and ALB **per deployment** for multi-tenant isolation.

<details>
<summary><b>How it works</b></summary>

<br />

- Immutable ECR tags mean a deploy is always reproducible — no `:latest` ambiguity when you need to know what's actually running.
- Each tenant gets its own network boundary rather than sharing a VPC with security-group gymnastics.
- CPU/memory target-tracking autoscaling plus Spot capacity cut non-production compute **~40%**.
- Structured CloudWatch JSON logging so failures are queryable instead of grep-able.

</details>

</td>
<td width="50%" valign="top">

### Serverless Data-Ingestion Pipeline
`Step Functions` `Lambda` `Spot Fargate` `AWS`

Replaced a manual multi-script workflow. Fargate handles **1 GB+ payloads** that blow past Lambda's 15-minute ceiling, while Step Functions own orchestration, retries, and error paths.

<details>
<summary><b>How it works</b></summary>

<br />

- Step Functions make the state machine explicit — retries, backoff, and failure branches live in the definition, not in someone's memory of the run order.
- Lambda handles the small/fast stages; work that exceeds the 15-minute limit is handed to Fargate tasks instead of being awkwardly chunked.
- Spot capacity for the heavy stages cut compute cost **~70%**, with the state machine handling interruption retries.

</details>

</td>
</tr>
</table>

<img src="https://capsule-render.vercel.app/api?type=rect&color=0:0A2540,50:E85D26,100:0A2540&height=3" width="100%" />

## Reliability Work in Production

<table>
<tr><th align="left" width="26%">Area</th><th align="left">What I did</th><th align="left" width="22%">Outcome</th></tr>
<tr>
<td><b>Private API ingress</b></td>
<td>Designed API Gateway ingress over VPC Links to a shared, EKS-provisioned internal NLB, with a senior cloud architect.</td>
<td>Removed redundant load balancers; lower infra spend</td>
</tr>
<tr>
<td><b>Traffic controls</b></td>
<td>Rate limiting, burst capacity, and IP allow-listing via IAM resource policies scoped to corporate, GCP, and NAT-gateway ranges.</td>
<td>Granular, auditable access control</td>
</tr>
<tr>
<td><b>Alerting that doesn't cry wolf</b></td>
<td>CloudWatch + EventBridge monitoring for active-passive VPNs and the AWS Health stream. Metric Math group-health checks suppress failover false positives; SNS alerts are buffered.</td>
<td>Materially less alert noise</td>
</tr>
<tr>
<td><b>Network-layer support</b></td>
<td>Resolved 20+ production tickets across EC2/VPC connectivity; audited security groups and NACLs.</td>
<td>Closed unused ports, tighter attack surface</td>
</tr>
<tr>
<td><b>Consensus components (DRDO)</b></td>
<td>Python and C++ contributions to a secure blockchain system under a Ministry of Defence project.</td>
<td>Documented reliability improvements</td>
</tr>
</table>

<img src="https://capsule-render.vercel.app/api?type=rect&color=0:0A2540,50:E85D26,100:0A2540&height=3" width="100%" />

## Toolchain

<table>
<tr>
<td valign="top" width="50%">

**Cloud & Compute**

![AWS](https://img.shields.io/badge/AWS-1A3A5C?style=flat-square&logo=amazonwebservices&logoColor=FF9900)
![Azure](https://img.shields.io/badge/Azure-1A3A5C?style=flat-square&logo=microsoftazure&logoColor=48B0F7)
![ECS Fargate](https://img.shields.io/badge/ECS%20Fargate-1A3A5C?style=flat-square&logo=amazonecs&logoColor=FF9900)
![Lambda](https://img.shields.io/badge/Lambda-1A3A5C?style=flat-square&logo=awslambda&logoColor=FF9900)
![Step Functions](https://img.shields.io/badge/Step%20Functions-1A3A5C?style=flat-square&logo=amazonwebservices&logoColor=FF6F91)
![API Gateway](https://img.shields.io/badge/API%20Gateway-1A3A5C?style=flat-square&logo=amazonapigateway&logoColor=FF6F91)
![VPC](https://img.shields.io/badge/VPC%20%2F%20NLB%20%2F%20ALB-1A3A5C?style=flat-square&logo=amazonvpc&logoColor=A88BFF)

**Containers & Orchestration**

![Docker](https://img.shields.io/badge/Docker-1A3A5C?style=flat-square&logo=docker&logoColor=4FA8F5)
![Kubernetes](https://img.shields.io/badge/Kubernetes-1A3A5C?style=flat-square&logo=kubernetes&logoColor=6D9BF5)
![Helm](https://img.shields.io/badge/Helm-1A3A5C?style=flat-square&logo=helm&logoColor=8FA8FF)
![ArgoCD](https://img.shields.io/badge/ArgoCD-1A3A5C?style=flat-square&logo=argo&logoColor=EF7B4D)

**Infrastructure as Code**

![Terraform](https://img.shields.io/badge/Terraform-1A3A5C?style=flat-square&logo=terraform&logoColor=A970FF)
![CloudFormation](https://img.shields.io/badge/CloudFormation-1A3A5C?style=flat-square&logo=amazonwebservices&logoColor=FF6F91)

</td>
<td valign="top" width="50%">

**CI/CD**

![GitHub Actions](https://img.shields.io/badge/GitHub%20Actions-1A3A5C?style=flat-square&logo=githubactions&logoColor=6CB6FF)
![Jenkins](https://img.shields.io/badge/Jenkins-1A3A5C?style=flat-square&logo=jenkins&logoColor=E8654A)
![Git](https://img.shields.io/badge/Git-1A3A5C?style=flat-square&logo=git&logoColor=F05032)

**Observability & Security**

![Prometheus](https://img.shields.io/badge/Prometheus-1A3A5C?style=flat-square&logo=prometheus&logoColor=E6522C)
![Grafana](https://img.shields.io/badge/Grafana-1A3A5C?style=flat-square&logo=grafana&logoColor=F46800)
![CloudWatch](https://img.shields.io/badge/CloudWatch-1A3A5C?style=flat-square&logo=amazoncloudwatch&logoColor=FF6F91)
![Trivy](https://img.shields.io/badge/Trivy-1A3A5C?style=flat-square&logo=aqua&logoColor=4FA8F5)
![SonarQube](https://img.shields.io/badge/SonarQube-1A3A5C?style=flat-square&logo=sonarqube&logoColor=6CC0E5)

**Languages & Runtime**

![Python](https://img.shields.io/badge/Python-1A3A5C?style=flat-square&logo=python&logoColor=FFD343)
![Go](https://img.shields.io/badge/Go-1A3A5C?style=flat-square&logo=go&logoColor=4FD3E8)
![Bash](https://img.shields.io/badge/Bash-1A3A5C?style=flat-square&logo=gnubash&logoColor=7BD44B)
![C++](https://img.shields.io/badge/C%2B%2B-1A3A5C?style=flat-square&logo=cplusplus&logoColor=6CA9E8)
![FastAPI](https://img.shields.io/badge/FastAPI-1A3A5C?style=flat-square&logo=fastapi&logoColor=3ECFB2)
![Linux](https://img.shields.io/badge/Linux-1A3A5C?style=flat-square&logo=linux&logoColor=FCC624)

</td>
</tr>
</table>

<div align="center">

<img src="https://img.shields.io/badge/AWS%20Certified-Solutions%20Architect%20Associate-E85D26?style=for-the-badge&logo=amazonwebservices&logoColor=FF9900&labelColor=0A2540" alt="AWS SAA" />
<img src="https://img.shields.io/badge/Microsoft%20Certified-Azure%20Administrator%20AZ--104-E85D26?style=for-the-badge&logo=microsoftazure&logoColor=48B0F7&labelColor=0A2540" alt="AZ-104" />
<img src="https://img.shields.io/badge/OverTheWire-Bandit%20All%20Levels-E85D26?style=for-the-badge&logo=linux&logoColor=FCC624&labelColor=0A2540" alt="OverTheWire Bandit" />

<br /><br />

<img src="https://capsule-render.vercel.app/api?type=rect&color=0:0A2540,50:E85D26,100:0A2540&height=3" width="100%" />

</div>

## GitHub Activity

<div align="center">

<img src="https://raw.githubusercontent.com/VaibhavSingh79/VaibhavSingh79/main/github-metrics.svg" alt="GitHub metrics" width="72%" />

<br /><br />

<img src="https://raw.githubusercontent.com/VaibhavSingh79/VaibhavSingh79/main/github-habits.svg" alt="Coding habits" width="54%" />

<br /><br />

<img src="https://raw.githubusercontent.com/VaibhavSingh79/VaibhavSingh79/output/snake.svg" alt="Contribution snake" width="98%" />

</div>

<img src="https://capsule-render.vercel.app/api?type=rect&color=0:0A2540,50:E85D26,100:0A2540&height=3" width="100%" />

## Currently

```console
$ kubectl get roadmap -o wide

NAME                       STATUS        NOTES
distributed-systems        In Progress   consensus, partitioning, failure modes
platform-engineering       In Progress   golden paths, developer self-service
dsa-cpp                    Ongoing       100+ LeetCode
cka                        Planned       next certification target
```

<br />

<div align="center">

**Reach me:** [vaibhavsingh8829@gmail.com](mailto:vaibhavsingh8829@gmail.com) · [LinkedIn](https://www.linkedin.com/in/vaibhavsingh79/)

<sub>Night owl. Most of these commits have timestamps that prove it.</sub>

<img src="https://capsule-render.vercel.app/api?type=waving&color=0:E85D26,40:1A3A5C,100:0A2540&height=120&section=footer" width="100%" />

</div>
