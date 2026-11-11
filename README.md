# 3-tier-highly-available-web-architecture-on-AWS
This is a foundational project for a company migrating from an on-premises environment to the AWS cloud

The Scenario:

You've joined a small CompanyUIO.Co as a junior cloud engineer. They currently run everything on a single on-premises server in a back office: one web app, no redundancy, no real access controls, no monitoring beyond someone noticing the site is down. Leadership wants to move to AWS before their lease is up — the given reasons are "get rid of the physical server" and "stop losing the site every time the power blips."
You've been asked to build and document a first production-shaped environment: not a proof of concept, something that could actually hold customer traffic. You're expected to deliver a working environment, an architecture diagram, and a short handover document explaining what you built and why — because someone else will eventually need to maintain this.

---

The business problem: everything sits on one flat network. Divide the network to prevent public access to private data.

What you're expected to solve:
- Design a VPC with public and private subnets, spread across two Availability Zones(for redundancy).
- Public subnets can reach the internet directly; private subnets can only reach out through a NAT gateway.
- Route tables and an Internet Gateway configured explicitly 

Deliverable: A VPC, built via CLI or CloudFormation, with a diagram showing subnet layout across both AZs.
To reflect: why does the private subnet have no direct route to the internet, and what would break if you removed the NAT gateway?

---

The business problem: On the old server, everything ran as root with no firewall rules between processes, heavily compromising security in case of malicious access

What you're expected to solve:
- Security groups scoped tightly: the web tier only accepts 80/443 from the internet and from the web tier's security group, not an open IP range
- NACLs as a second layer of defense at the subnet level
- Remote access to instances via SSM Session Manager, to avoid managing access keys altogether while still allowing remote access

Deliverable: Security groups, NACLs, and test the security groups by attempting to access the blocked paths

---

The business problem: The old setup was one server and if it went down, the business went down with it. They've mentioned a marketing push next quarter that could spike traffic.

What you're expected to solve:
- EC2 instances in an Auto Scaling Group, spread across both AZs, sitting in the private subnets with no public IPs
- An Application Load Balancer in the public subnets as the only public-facing entry point
- Health checks configured so a failed instance is automatically replaced, not manually noticed and restarted

Deliverable: A working ALB + ASG serving a simple app, test by manually terminating an instance confirm the ASG replaces it without you intervening. 

---

The business problem: Nobody currently notices problems until a customer complains; the old server ran with full admin rights everywhere because "it was easier." 

What you're expected to solve:
- A purpose-built IAM instance role for the EC2 instances with only the specific permissions they need not a broad managed policy.
- CloudWatch alarms on CPU and instance health, feeding into a basic dashboard
- A written handover document: what was built, why each decision was made, and what to check first if something breaks.

Deliverable: IAM policy JSON (custom, not managed), a CloudWatch dashboard, and the handover doc itself.

Success criteria for handover doc: Someone unfamiliar with the project could read your handover doc and understand the architecture without asking you a single question.

