> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Deployment Models

> The various ways you can deploy Xata, whether for staging/dev or production, hosted in our cloud or in your cloud account.

You can use Xata in two major ways:

* **For staging and development use cases**, where you use Xata to enhance other Postgres DBaaS offerings by adding features like instant database branching and data anonymization.
* **For production use cases**, where you run your production Postgres as well as any development branches on our platform.

In either case, you can you can deploy PostgreSQL instances on our cloud, or in your cloud account or even on-prem (BYOC).

## Production clone and development branches on Xata

In this deployment model, we create a "production clone" of your production Postgres on the Xata platform. This works with
any Postgres service (AWS RDS, Amazon Aurora, GCP Cloud SQL, Azure Database, etc.). The production clone is updated with
the production dataset periodically, for example every night.  We call this process “cloning,” and it is accomplished using our
open-source CLI (`xata clone`). You can automate the cloning process using GitHub Actions or any other CI/CD tooling.

Anonymization can be done during the cloning phase. Depending on your requirements, you can set anonymization in `strict`
or `relaxed` mode. In `strict` mode, any new table and column needs to be explicitly present in the configuration,
minimizing the chance of accidental leaks.

<img src="https://mintcdn.com/xata/zunLvnbMMVhTIsZq/images/deployment-models/xata-deployment-staging.png?fit=max&auto=format&n=zunLvnbMMVhTIsZq&q=85&s=19ef06a16fb3d83a6d6bff838395b5b1" alt="Xata for staging and dev use-cases" className="rounded-lg" width="1084" height="1270" data-path="images/deployment-models/xata-deployment-staging.png" />

Because `xata clone` is executed in the context of your environment (e.g. you cloud account), there are two important benefits:

* The tool has access to your production database without requiring changes to your external firewall rules.
* Sensitive data is anonymized before it ever leaves your cloud account.

From the "production clone", you can create instant branches with all the data, for your development previews, ephemeral environments,
or ad-hoc testing needs.

## Production and development branches on Xata

In this deployment model, you run both production and any staging or development branches you require on the Xata platform. Your production
branch is typically configured for high availability (with one or multiple read replicas). Development branches generally use a single
instance and are configured with lower CPU and memory resources to save costs.

If you'd like to set up anonymization between your production and development branches, as well as have stricter controls on who can access production, you can use two Xata organizations. In the production organization you run only the production branch. You can configure `xata clone` to copy and anonymize the data between prod and staging.

<img src="https://mintcdn.com/xata/PDDxPY9xptrEGBCP/images/deployment-models/xata-prod-branches-diagram.png?fit=max&auto=format&n=PDDxPY9xptrEGBCP&q=85&s=1e26d43a7395275eaa34781e6a0b4574" alt="Production and staging/dev branches on the Xata platform" className="rounded-lg" width="1130" height="1304" data-path="images/deployment-models/xata-prod-branches-diagram.png" />

### High availability and replicas

Each branch has one primary PostgreSQL instance and can have zero or more replicas. Replicas can serve read-only traffic and act as standby promotion targets.

Configure at least 1 replica for production workloads. If the primary becomes unavailable, Xata can automatically promote a healthy replica. During maintenance or an upgrade that requires replacing the primary, Xata can first switch over to a replica to minimize disruption. Failovers and planned switchovers can briefly interrupt existing connections, so applications must reconnect and retry transient failures as appropriate.

<Warning>
  **A branch with 0 replicas is not suitable for production workloads.** It has no standby promotion target and cannot automatically fail over. A primary failure, or an update that requires the primary to restart or be replaced, makes the database unavailable until the primary is available again.
</Warning>

#### Connection endpoints

Choose an endpoint based on where each connection should go:

* **Primary only (`rw`)**: Connects to the primary for reads and writes.
* **Primary or replica (`r`)**: Connects to either the primary or a replica. Use it only for read-only workloads; writes are unreliable because the connection can land on a read-only replica.
* **Replica only (`ro`)**: Connects only to read-only replicas and requires at least 1 replica.
* **Pooler (`pooled_rw`)**: Provides pooled connections to the primary.

For connection string options, see [Branch Endpoint Types](/docs/platform/branch#endpoint-types).

#### Configuring replicas

* **During branch creation**: Select the replica count when creating a base branch in the Xata console, or pass `--replicas` to [`xata branch create`](/docs/cli/branch#create).
* **On existing branches**: Open **Settings** → **Instance Details** and adjust **Replicas**.

## Bring Your Own Cloud (BYOC)

In this deployment model, we install the whole dataplane part of Xata in a Kubernetes setup that you control. This can be in your
own cloud account (AWS, Azure, GCP, Hetzner, etc.) or on-premises. The minimal setup requires three Kubernetes nodes, and there are multiple
installation models (disaggregated, hyper-converged, or hybrid). Our engineers will help you choose the best setup and configure it for you.

This model brings the following benefits:

* **Security, compliance, and data governance.** This setup ensure that all Postgres databases are started in your usual production environment.
* **Better use of your cloud provider credits.** Since you pay for the Kubernetes nodes directly to your cloud provider, you can take advantage fully of your cloud credits, discounts, and negotiated private agreements.
* **Throughput and latency.** Depending on your hardware and networking setup, this deployment model can achieve levels of performance not possible when using a separate cloud account.

<img src="https://mintcdn.com/xata/PDDxPY9xptrEGBCP/images/deployment-models/byoc-architecture.png?fit=max&auto=format&n=PDDxPY9xptrEGBCP&q=85&s=f89e157ddc85ef95ac6dda6b13526051" alt="BYOC deployment model" className="rounded-lg" width="1132" height="992" data-path="images/deployment-models/byoc-architecture.png" />

The Xata control plane—responsible for managing organizations, users, regions, and instances—continues to run in our AWS account. Latency between
the control and data planes is not critical, as it involves only control messages (e.g., start/stop instance). By keeping the control plane in
our account (and optionally also the monitoring/observability data), we can provide you with a managed service similar to what you'd get
when deploying on our platform.

This setup is especially convenient if you want to offer an internal “Postgres as a service” or if you’re looking to white-label Xata for your customers.

If you have any question regarding BYOC, do not hesitate to write us [an email](mailto:info@xata.io).
