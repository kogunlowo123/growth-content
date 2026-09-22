# Reddit Posts

---

## Post 1: r/terraform

**Subreddit:** r/terraform

**Title:** I open-sourced 80+ Terraform modules for AWS, Azure, and GCP -- looking for feedback

**Body:**

Hey r/terraform,

I've been building production Terraform modules for the past year and decided to open-source all of them. The collection is now at 80+ modules across AWS, Azure, and GCP, and I'd appreciate feedback from this community.

**What's in the collection:**

AWS (25+ modules):
- Networking: VPC, Transit Gateway, PrivateLink, Route53, CloudFront
- Compute: EKS, ECS Fargate, EC2, Lambda, Autoscaling
- Security: Security Baseline (GuardDuty/Config/CloudTrail), WAF, KMS, Network Firewall
- Databases: RDS Aurora, DynamoDB, ElastiCache Redis, EFS
- AI/ML: Bedrock, SageMaker Studio, RAG Pipeline

Azure (18+ modules):
- Networking: Hub-Spoke, Virtual Network, Front Door, Application Gateway, Private Endpoint
- Compute: AKS (with Cilium + Workload Identity support), Container Apps, VMs
- Databases: Cosmos DB, SQL Database, PostgreSQL Flexible
- AI/ML: OpenAI Platform, AI Studio

GCP (15+ modules):
- Networking: VPC Network, Cloud Armor
- Compute: GKE, Cloud Run
- Databases: Cloud SQL, Spanner, BigQuery
- AI/ML: Vertex AI, Gemini Platform

**Design principles:**
- Security-first defaults (encryption on, public access blocked, logging enabled)
- Production-ready examples (not hello-world demos)
- Consistent variable naming across modules
- Proper input validation and output exports

**What I'm looking for:**
- Are there modules the community needs that I haven't built?
- Feedback on module structure and conventions
- Suggestions for improving reusability
- Anyone interested in contributing

Full collection: https://github.com/kogunlowo123

Happy to answer questions about any specific module. Not trying to sell anything -- these are all free and MIT licensed.

---

## Post 2: r/aws

**Subreddit:** r/aws

**Title:** Free Terraform module for AWS security baseline -- enables GuardDuty, Config, CloudTrail, and Access Analyzer in one apply

**Body:**

I got tired of setting up the same AWS security services manually in every new account, so I built a Terraform module that handles the entire baseline in one shot.

**What it does:**

One module call enables:
- **CloudTrail** -- multi-region trail with S3 logging, log file validation, KMS encryption
- **AWS Config** -- recorder, delivery channel, and managed rules for encryption/public access/IAM compliance
- **GuardDuty** -- threat detection with findings routed to SNS or EventBridge
- **IAM Access Analyzer** -- flags resources shared outside your trust boundary

Everything is encrypted with a customer-managed KMS key with automatic rotation.

**Usage is straightforward:**

```hcl
module "security_baseline" {
  source = "github.com/kogunlowo123/terraform-aws-security-baseline"

  enable_guardduty       = true
  enable_config          = true
  enable_cloudtrail      = true
  enable_access_analyzer = true
}
```

**Why I built this:**

I've audited too many AWS accounts where basic security services weren't enabled because "we'll get to it later." This module makes "later" take 5 minutes instead of a full sprint.

Module: https://github.com/kogunlowo123/terraform-aws-security-baseline

I also have related modules if you're building out a full security stack:
- WAF with managed rules and rate limiting: https://github.com/kogunlowo123/terraform-aws-waf
- KMS with auto-rotation: https://github.com/kogunlowo123/terraform-aws-kms
- Network Firewall: https://github.com/kogunlowo123/terraform-aws-network-firewall
- VPC with flow logs: https://github.com/kogunlowo123/terraform-aws-vpc-complete

All free, all open source. Would love to hear if there are features or Config rules you'd want added.

---

## Post 3: r/azure

**Subreddit:** r/azure

**Title:** Terraform module for AKS with Cilium CNI and Workload Identity -- open source

**Body:**

Sharing an AKS Terraform module I built that supports the newer Azure features that most existing modules don't handle well yet.

**Key features:**

- **Cilium CNI** -- eBPF-based networking via Azure CNI Overlay with Cilium. Better performance than kubenet, no IP exhaustion issues, native network policies, and Hubble observability.
- **Workload Identity** -- OIDC-based pod authentication to Azure services. No more aad-pod-identity DaemonSet.
- **System/User node pool separation** -- system workloads isolated from application workloads
- **Private cluster support** -- API server with private endpoint
- **Azure Monitor integration** -- Container Insights with Log Analytics

Module: https://github.com/kogunlowo123/terraform-azure-aks

**It pairs well with these other modules I maintain:**

- Hub-spoke networking with Azure Firewall and forced tunneling: https://github.com/kogunlowo123/terraform-azure-hub-spoke-network
- Key Vault with private endpoint and CSI driver integration: https://github.com/kogunlowo123/terraform-azure-key-vault
- Azure Monitor: https://github.com/kogunlowo123/terraform-azure-monitor
- Private Endpoint: https://github.com/kogunlowo123/terraform-azure-private-endpoint

**Why Cilium over the default CNI?**

Azure CNI has the IP exhaustion problem in large clusters. Kubenet is limited to 400 nodes. Cilium on Azure CNI Overlay gives you overlay networking (no IP exhaustion) with eBPF performance and built-in network policy enforcement.

If you're deploying AKS in production, especially in enterprise environments with hub-spoke networking, this module should save you some time.

All open source. Issues and PRs welcome.

---

## Post 4: r/devops

**Subreddit:** r/devops

**Title:** Built MCP servers for connecting AI assistants to AWS, Kubernetes, and Terraform -- here's what I learned

**Body:**

I've been experimenting with MCP (Model Context Protocol) servers for infrastructure automation over the past few months. Built 8 of them and open-sourced everything. Wanted to share what actually works and what's still rough.

**What MCP servers do:**

They give AI assistants structured access to your infrastructure. Instead of pasting error logs into a chat window and hoping for good advice, the AI can directly query your Kubernetes cluster, read CloudWatch metrics, or inspect your Terraform state.

**What I built:**

- **mcp-server-aws** (https://github.com/kogunlowo123/mcp-server-aws) -- read-only access to EC2, S3, Lambda, CloudWatch, IAM
- **mcp-server-kubernetes** (https://github.com/kogunlowo123/mcp-server-kubernetes) -- pod status, logs, events, deployment info
- **mcp-server-terraform** (https://github.com/kogunlowo123/mcp-server-terraform) -- state file parsing, resource dependency mapping
- **mcp-server-azure** (https://github.com/kogunlowo123/mcp-server-azure) -- Azure resource inspection
- **mcp-server-github** (https://github.com/kogunlowo123/mcp-server-github) -- repo, PR, and Actions context
- **mcp-server-devops** (https://github.com/kogunlowo123/mcp-server-devops) -- CI/CD pipeline data
- **mcp-server-database** (https://github.com/kogunlowo123/mcp-server-database) -- schema exploration and queries
- **mcp-server-vector-db** (https://github.com/kogunlowo123/mcp-server-vector-db) -- vector DB and RAG debugging

**What actually works well:**

- Incident triage with the Kubernetes MCP server. "Why is this pod failing?" with full log and event context is genuinely faster than kubectl + dashboard switching.
- Terraform state exploration. Understanding resource dependencies in large state files is much easier when you can ask questions about it.
- Cost analysis with the AWS MCP server.

**What's still rough:**

- Write operations need careful guardrails. I keep most servers read-only by default.
- Context windows fill up fast with large outputs (pod logs, full state files).
- Authentication and credential management needs more thought for team use.

Full platform: https://github.com/kogunlowo123/claude-mcp-platform

Not trying to sell anything -- genuinely interested in how other DevOps teams are approaching AI-assisted operations. What tools are you using?

---

## Post 5: r/kubernetes

**Subreddit:** r/kubernetes

**Title:** Open-source Terraform modules for EKS, AKS, and GKE -- production-ready with security defaults

**Body:**

I maintain Terraform modules for all three managed Kubernetes services and wanted to share them with the community. Each is designed for production deployments, not quick demos.

**EKS module** (https://github.com/kogunlowo123/terraform-aws-eks):
- Managed node groups with autoscaling configuration
- IRSA (IAM Roles for Service Accounts) support
- Private API server endpoint
- EKS add-ons management (CoreDNS, kube-proxy, VPC CNI, EBS CSI)
- Cluster security group configuration

**AKS module** (https://github.com/kogunlowo123/terraform-azure-aks):
- Cilium CNI via Azure CNI Overlay
- Workload Identity (OIDC-based)
- System and user node pool separation
- Private cluster support
- Azure Monitor Container Insights integration

**GKE module** (https://github.com/kogunlowo123/terraform-gcp-gke):
- Autopilot and Standard mode support
- Workload Identity Federation
- Private cluster with authorized networks
- Binary Authorization ready
- GKE-native monitoring

**Common design decisions across all three:**

1. **Node pool isolation** -- system workloads on dedicated node pools, application workloads on separate pools with appropriate taints
2. **Network policy support** -- Cilium on AKS, Calico on EKS, native on GKE
3. **Secret management integration** -- AWS Secrets Manager CSI, Azure Key Vault CSI, GCP Secret Manager
4. **Private by default** -- API servers are private, nodes are in private subnets

**Supporting modules:**

Each K8s module works well with my networking modules:
- AWS VPC: https://github.com/kogunlowo123/terraform-aws-vpc-complete
- Azure Hub-Spoke: https://github.com/kogunlowo123/terraform-azure-hub-spoke-network
- GCP VPC: https://github.com/kogunlowo123/terraform-gcp-vpc-network

And I built an MCP server for Kubernetes that gives AI assistants direct cluster access for troubleshooting: https://github.com/kogunlowo123/mcp-server-kubernetes

All modules are open source. If you're deploying managed Kubernetes and writing your own Terraform from scratch, these might save you some time. Happy to take feedback or PRs.

---

## Post 6: r/devops — Agentic AI for Platform Engineering (2026-09-20)

**Subreddit:** r/devops

**Title:** How we use agentic AI for Kubernetes ops: architecture, code, and lessons from production

**Body:**

We've been running autonomous agents in our platform engineering workflow for a few months and wanted to share what the architecture looks like in practice, since most posts on this topic are either vague or toy examples.

**What the agent actually does:**

Alert fires in Alertmanager → webhook hits our ops agent service → agent executes this sequence:

1. kubectl get/describe on the affected resources
2. Pull pod logs (tries --previous for crash diagnostics)
3. Query Prometheus for correlated metrics (last 15 min)
4. Fetch recent deployment events from CI/CD
5. Reason over all of it → produce a root cause hypothesis with confidence score
6. Post findings + evidence to incident Slack channel

Median time-to-root-cause went from 34 minutes to under 2 minutes for the alert classes we've covered.

**The action envelope (where we landed after a lot of iteration):**

Read operations: immediate, no approval
Write operations: proposed via GitHub PR, requires one approval
Destructive operations (node drain, deployment rollback): requires explicit on-call sign-off

We started with full autonomy on writes and had two incidents in the first week where the agent's reasoning was correct but the action had unintended side effects. Moved everything behind PR gates and haven't had a problem since.

**Stack:**

- LangChain AgentExecutor
- Claude Opus 5 for reasoning (Haiku for high-frequency classification tasks)
- FastAPI webhook handler
- DynamoDB for audit log
- Redis for action rate limiting

**Code and full architecture writeup:** https://www.citadelcloudmanagement.com/blog/agentic-ai-platform-engineering

Happy to answer questions about the implementation — particularly the security considerations and how we handle prompt injection via alert payloads, which turned out to be a real concern we underestimated initially.

---

## Post 7: r/kubernetes — Platform Engineering IDP (2026-09-22)

**Subreddit:** r/kubernetes

**Title:** How we structured our Internal Developer Platform: Crossplane + Backstage + ArgoCD — architecture and hard lessons

**Body:**

We've been running a platform engineering team for 14 months. Wanted to share the current architecture and specifically the mistakes we made, since most writeups skip that part.

**The stack:**

- **Crossplane** for infrastructure abstraction (CompositeResourceDefinitions that expose developer-friendly APIs over cloud primitives)
- **Backstage** for developer portal + software templates
- **ArgoCD with ApplicationSets** for GitOps deployment
- **Kyverno** for policy enforcement at admission
- **Prometheus Operator + ServiceMonitors** for auto-discovered observability

**Mistake 1: Building everything at once**

We tried to launch all four layers simultaneously. After four months, we had nothing production-ready. Developers were still opening tickets.

What worked: we shipped one Backstage template for our most common service type (a Go gRPC service) and got one team using it. That one template, when it worked reliably, was more persuasive than any architecture diagram we had made.

**Mistake 2: Crossplane XRDs without versioning**

We shipped v1alpha1 CompositeResourceDefinitions and made breaking changes without notice. Teams had Crossplane-provisioned resources fail silently during upgrades. We now treat XRDs like external APIs: versioned, with deprecation periods and migration docs.

**Mistake 3: Treating Backstage as a documentation site**

Our initial Backstage deployment was basically a Confluence replacement with nicer UI. The value unlock came when we wired software templates to actually do things — create repos, trigger CI, register resources. If your Backstage templates don't provision infrastructure end-to-end, they're under-delivering.

**What's worked really well:**

Kyverno policies that enforce resource limits, block privileged containers, and require specific labels — shipped once by the platform team, inherited by every onboarded service automatically. No per-team action required.

ArgoCD ApplicationSets with a Git directory generator. Teams add a directory to the platform-manifests repo; ArgoCD creates the Application automatically in the right namespace with the right project. We've onboarded 30 teams in 6 months without a single manual ArgoCD config step.

**Current state:**

- 47 teams onboarded to the platform
- Average time-to-first-deployment for a new service: 18 minutes (was 4 days before)
- Infrastructure tickets from dev teams: down 80% since launch
- Platform NPS from engineering: 42 (up from -8 at launch)

**Full architecture writeup with code examples:** https://www.citadelcloudmanagement.com/blog/platform-engineering-internal-developer-platform-2026

Happy to answer questions about the Crossplane setup specifically — that's where we've seen the most complexity and have the most detailed learnings.
