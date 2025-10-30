Prepare a presentation for KubeCon conference in Atlanta (research what it is before).

I want to have a minimalist presentation highlight 1-2 ideas on a slide for 25 mins sessions. Suggest the content via Marp's speaker notes feature (websearch what it is)


---

Abstract:
They told us, “Don’t run databases on Kubernetes.” We heard, “Challenge accepted.” This is the story of how we went from handcrafted Postgres chaos to a stable, CNCF-aligned DBaaS using CloudNativePG over the years — and lived to tell the tale.
We started with containers, then pinned nodes in Kubernetes (spoiler: not scalable). Zalando’s operator with Patroni got us partway, but the real leap came with CloudNativePG: rebuilt from scratch, capable of autopilot.
You’ll hear real incidents (yes, including “the one with the wrong PVC”), lessons learned, and how we’ve moved from tickets to self-service via GitOps. So what happens when YAML becomes your new DBA?
This is a story of maturity, simplicity, real benefits CNCF brings to your project. Not just from a single project, but from the whole ecosystem evolving over the years in networking, deployment, and much more.
Whether you're building a platform or escaping vendor lock-in, come laugh, learn, and reconsider running DBs in Kubernetes.

Benefits to the ecosystem:
This session offers a practical, vendor-neutral case study of running PostgreSQL on Kubernetes using CNCF-native tooling and shifting mindset along the way. Attendees will gain real-world insights into using CloudNativePG for production workloads, including lessons from failures, operational wins, and architecture trade-offs. By showing how a DIY journey evolved into a sustainable, operator-managed DBaaS, the talk empowers platform teams to adopt stateful workloads more confidently. It also helps drive awareness and adoption of CNCF projects, promotes GitOps best practices, and encourages open-source contributions to ecosystem tooling.

Format: Solo presentation 25 mins
Conference: CNCF-hosted Co-located Events North America 2025 in 31 days

---
marp: true
theme: gaia
class: [gaia]
#paginate: true
lang: en-US
transition: none
style: |
  section {
    align-content: start;
  }
  section::after {
    content: attr(data-marpit-pagination) ' / ' attr(data-marpit-pagination-total);
  }

---

TODO: nektere slidy precuhuji

# YAML Is My DBA Now: Our Postgres Journey from DIY to Autopilot Self-Service

---

# David Pech

TODO: ksicht, format
![Wrike](./wrike.png)
![Sestra Emmy](./emmy.png)
![Golden Kubestronaut](./golden-kubestronaut.png)

---

Question: Do you consider running your databases in Kubernetes boring?

---

??? (image of heavily underutilized envs - VM, aurora)

-> comments very large slack typically, peaks max at 50-60% of capacity

---

Question: Is PostgreSQL suited to run in a container?

---

PostgreSQL is not designed to be run in an dynamic env much (by design)

- some start-up config can't be changed (`max_connections`, `shared_buffers`)
- `extension_control_path` (PG18)

Example: you can promote to leader online, but demotion needs restart

---

Question: DB Uptime at least 99.999? (26s downtime per month)

---

A candidate's resume:

(quote)
 ... after finishing project we achieved 0% of DB failures on infra level ...
(/quote)

Reality check: typically no more >20s of downtime when doing a DB switchover.

---

Question: Does a typical developer understands his DB well? (What he/she thinks?)

---

???

---

Question: How many DBs require (any) DB-level tuning?

FIXME: any research on this?

---

Question: How many DBs can a DBA handle?
https://www.forrester.com/blogs/10-09-30-how_many_dbas_do_you_need_support_databases/
40:1 (2010)

one DBA per 40 database instances or per 4 terabytes of data
https://www.reddit.com/r/SQLServer/comments/d2w5f6/dbas_how_many_environments_do_you_support/
VERIFY
(2019)


DBA-to-developer ratio should not be less than 1:200
https://www.bytebase.com/blog/how-many-dbas-should-a-company-hire/
(2024)

FIXME: any research on this?

---

Natural motivation:
- align PostgreSQL runtime with all other applications


---

Gaininig confidence

Can you run mixed deployments of Postreses?

---

Docker time!

Dockerized vs. VMs

Libc problems
- tooling not included, DIY

---

PostgreSQL containerized?

- visible benefit with nodes pinned to disk/node?

- Docker Swarm app era
-> no, Patroni

---

PostgreSQL management

- predefined users? (Script)
- backup and restore? (Scripts)
- monitoring? Supported!

---

Typical use-cases:

- refresh QA environment DBs
- verify DisasterRecovery
- semi-manually manage accounts and permissions

---

Core PostgreSQL vs. "Product"

-> Highlight all other benefits of operator (backups included, leader-replication automatic setup, lifecycle control for 2nd day operations)

---

Let's bring PostgreSQL into container, 2nd attempt

Explain complexity of Spilo (too much envs knobs, multiple PG versions later, ...)

Benefit - questionable for non-expert

Image: Swiss army nife building a wooden cabin

---

First K8s operators

-> lift-n-shift Patroni
-> at first covered that PG was alive and in cluster


---

Problem: Users and DB declarative management

Fixed since Day1  TODO: when

---

Problem: lost block device-level backups
(the main DR method)
Fixed: VolumeSnapshot 
[Beta in 1.17] (2019)

---

Problem: snapshotting multiple volumes at once lost 
Fixed: VolumeGroupSnapshot
[Beta in 1.32]

---

Problem: managing replication slots
TODO: ???

---

Problem: restart DB without reconnecting users
TODO: ??? done? with pgbouncer?

---

Problem: changing the most configs on the fly without restarting leader
Not fixed

---

2nd generation of operators

CloudNativePG

---

resiliency bug - 

---

Why is it for us acceptable?

TODO:

---

Do some "typical PG problems" still matter?

CHECKPOINT before restarting

TODO: surprising

---

Problem: basic DB tuning based on Pod size not being done

- `shared_buffers`
- `work_mem`

Not fixed - WHY???

TODO: surprising

---

Summing up:

Originally: A seasoned DBA was running on a VM with lot of scripts.


We've simplified it: now you can have this YAML

Now under behind the scenes, you ran in dynamic environment these technologies and you probably need at least 2 SREs.

Job well done!







