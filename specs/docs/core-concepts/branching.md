> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Branching

> Create instant, isolated PostgreSQL environments with Copy-on-Write database branches.

Branches on Xata are isolated PostgreSQL databases that give you separate environments for development, testing, and previews. Xata uses Copy-on-Write (CoW) at the storage layer to create branches instantly: a child branch contains the exact schema and data of its parent at the moment of creation, regardless of database size.

After creation, branches are independent PostgreSQL instances. Changes made in one branch — to the schema or the data — do not affect any other branch. There is no replication between branches, so writing to one branch never shows up in another.

Typical use cases include development branches for pull requests, preview databases, ephemeral CI environments, ad-hoc testing against production-like data, and "freezing" data at a point in time.

## Base and child branches

Every branch in a project is either a base branch or a child branch:

| Term             | Meaning                                                                                                                                                                                   |
| ---------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Base branch**  | A branch without a parent. It starts as an empty PostgreSQL database, and you choose its region, instance type, and configuration at creation.                                            |
| **Child branch** | A branch created from a parent branch. It starts with the parent's exact schema and data at the moment of creation, inherits the parent's configuration, and runs in the parent's region. |

When you create your first branch in a project, the console suggests the name `main`. This is a naming convention only — `main` is not a special or default branch, and any branch can be the parent of a child branch. Branches can be nested: a child branch can itself have children, forming a branch tree.

<Note>
  Each branch runs on its own compute instance, billed by instance type for the time it runs. [Scale-to-zero](/docs/core-concepts/scale-to-zero) pauses compute on idle branches, which makes development branches cheap to keep around. See [Costs and limits](#costs-and-limits).
</Note>

<Warning>
  **A branch's region is fixed at creation.** Child branches always run in the same region as their parent. To move to a different region, create a new base branch in the target region and copy your data over with [`xata clone`](/docs/cli/clone).
</Warning>

## Create a branch

### Create a child branch

Creating a child branch copies the parent's schema and data using a Copy-on-Write storage snapshot, so it completes in seconds even for terabyte-scale databases.

<Tabs>
  <Tab title="Console">
    <Steps>
      <Step>Navigate to the **Branches** page in your project</Step>
      <Step>Click **New branch**</Step>
      <Step>Select the source (parent) branch</Step>
      <Step>Give the branch a name</Step>
      <Step>Optionally adjust the [scale-to-zero](/docs/core-concepts/scale-to-zero) settings</Step>
      <Step>Click **Create branch**</Step>
    </Steps>
  </Tab>

  <Tab title="CLI">
    ```bash theme={null}
    xata branch create --name "my-dev-branch" --parent-branch "main"
    ```

    See the [`xata branch create`](/docs/cli/branch#create) reference for all options.
  </Tab>

  <Tab title="API">
    ```bash theme={null}
    curl -X POST "https://api.xata.tech/organizations/{organizationId}/projects/{projectId}/branches" \
      -H "Authorization: Bearer YOUR_API_KEY" \
      -H "Content-Type: application/json" \
      -d '{
        "name": "my-dev-branch",
        "mode": "inherit",
        "parentID": "PARENT_BRANCH_ID"
      }'
    ```

    With `mode: "inherit"`, the new branch inherits the parent branch's configuration and data. See the API Reference for the full request schema.
  </Tab>
</Tabs>

A few things to know about child branches:

* The branch starts with a single primary instance and no read replicas, regardless of how many replicas the parent has. You can add replicas later in the branch settings.
* The branch is a point-in-time copy. Changes made to the parent after creation do not appear in the child, and vice versa.
* If the parent contains sensitive production data, so does the child. For development environments, consider branching from an [anonymized staging replica](/docs/tutorials/create-production-clone) instead of production.

### Create a base branch

A base branch has no parent and starts empty — no schema, no data. Use it for a new production database, a staging environment in a specific region, or as the root of a new branch tree.

<Tabs>
  <Tab title="Console">
    <Steps>
      <Step>Navigate to the **Branches** page and click **New branch**</Step>
      <Step>Give the branch a name</Step>
      <Step>Select a region</Step>
      <Step>Choose the PostgreSQL image, instance type, and number of replicas</Step>
      <Step>Optionally adjust the [scale-to-zero](/docs/core-concepts/scale-to-zero) settings</Step>
      <Step>Click **Create branch**</Step>
    </Steps>
  </Tab>

  <Tab title="CLI">
    ```bash theme={null}
    xata branch create --name "staging" --no-parent \
      --region "us-east-1" --instance-type "xata.small" --replicas 0
    ```
  </Tab>
</Tabs>

<Warning>
  **A branch with 0 replicas is not suitable for production workloads.** It has no automatic failover target. Configure at least 1 replica for production. Learn more in [High availability and replicas](/docs/core-concepts/deployment#high-availability-and-replicas).
</Warning>

## Connect to a branch

Each branch has its own connection string and credentials. When connecting, you choose an endpoint type that controls how traffic is routed within the branch:

| Endpoint type                | Access                         | Use it for                                                       |
| ---------------------------- | ------------------------------ | ---------------------------------------------------------------- |
| **Primary** (`rw`)           | Read/write on the primary      | Default for most applications                                    |
| **Primary or replica** (`r`) | Routed to primary or a replica | Read-only workloads that can use either; do not use for writes   |
| **Replica only** (`ro`)      | Read-only, replicas only       | Keeping read load off the primary; requires at least one replica |
| **Pooler** (`pooled_rw`)     | Pooled access to the primary   | Serverless and high-concurrency workloads                        |

To get a connection string, open the branch's **Connect** panel in the console, or use the CLI:

```bash theme={null}
# Default (primary) connection string
xata branch url my-dev-branch

# Pooled connection for a serverless app
xata branch url my-dev-branch --type pooler
```

Store connection strings as secrets — each one contains the branch's credentials. For endpoint details and connection examples for `psql`, Prisma, Drizzle, and other clients, see the [branch reference](/docs/platform/branch#endpoint-types).

## View branches

The **Branches** page in the console shows all branches in your project, either as a list or as a graph visualizing the parent/child hierarchy, along with each branch's region and creation time.

From the CLI:

```bash theme={null}
# List all branches
xata branch list

# Show the branch hierarchy as a tree
xata branch tree

# Switch the CLI context to another branch
xata checkout my-dev-branch
```

## Manage a branch

Each branch has its own **Settings** page where you can rename the branch, change the instance type, add or remove read replicas, increase storage, upgrade the PostgreSQL minor version, tune PostgreSQL parameters, and rotate credentials. See the [branch reference](/docs/platform/branch) for details on each setting.

### Scale development branches to zero

Branches that only see occasional use — dev branches, PR previews, staging — don't need to run around the clock. With [scale-to-zero](/docs/core-concepts/scale-to-zero), a branch automatically hibernates after a configurable inactivity period (15 minutes to 3 hours) and wakes automatically when a new connection arrives. While hibernated, the branch's data is retained but no compute is billed.

Scale-to-zero is configured per branch, and your project defines separate defaults for base branches and child branches, so development branches can default to aggressive hibernation while production stays always-on.

### Delete a branch

Deleting a branch permanently removes the branch and its data. This cannot be undone.

<Tabs>
  <Tab title="Console">
    <Steps>
      <Step>Navigate to the **Branches** page</Step>
      <Step>Open the actions menu ("...") next to the branch</Step>
      <Step>Select **Delete**</Step>
      <Step>Type the branch name to confirm the deletion</Step>
    </Steps>
  </Tab>

  <Tab title="CLI">
    ```bash theme={null}
    xata branch delete my-dev-branch
    ```
  </Tab>
</Tabs>

Development branches are billed for the time they run, so delete branches you no longer need — or let [scale-to-zero](/docs/core-concepts/scale-to-zero) pause them automatically.

## Branching workflows

<Columns cols={2}>
  <Card title="Dev branches for pull requests" icon="code-pull-request" iconType="solid" href="/docs/automations/github-app">
    Connect the Xata GitHub App to create a dedicated dev branch for every pull request, and clean it up when the PR closes.
  </Card>

  <Card title="Branch automation in CI" icon="gears" iconType="solid" href="/docs/automations/ga-pr">
    Use GitHub Actions to create branches, apply migrations, and wire preview environments to a branch connection string.
  </Card>

  <Card title="Anonymized production clones" icon="user-shield" iconType="solid" href="/docs/tutorials/create-production-clone">
    Clone production into a staging branch with PII anonymized on the fly, then create instant dev branches from it.
  </Card>

  <Card title="Test schema changes safely" icon="code-merge" iconType="solid" href="/docs/core-concepts/schema-changes">
    Try a migration on a child branch first, then apply the same migration to production with zero downtime using pgroll.
  </Card>
</Columns>

Branches do not merge or synchronize schema changes with each other. The workflow is: test the migration on a child branch, then apply the same migration separately to the target branch — manually or through CI. See [schema changes](/docs/core-concepts/schema-changes).

## How Copy-on-Write branching works

When a branching operation starts, Xata creates a Copy-on-Write snapshot of the underlying logical volume at the storage level, and starts a new PostgreSQL instance on a volume initialized from that snapshot.

Because the mechanism lives entirely below PostgreSQL, Xata doesn't require any modifications to the PostgreSQL source code — branches run vanilla Postgres.

In generic terms, [Copy-on-Write](https://en.wikipedia.org/wiki/Copy-on-write) is a mechanism used to share data efficiently between processes. Instead of copying data right away when multiple programs use it, the same data is shared between programs until one tries to modify it.

When stored on disk, the database volume is split into *blocks*. In the illustration below, the blocks are numbered from 1 to 8. Because we're using a distributed storage system, the blocks can be on different disks and nodes. A metadata index is used to keep track of where the data blocks are stored.

<img src="https://mintcdn.com/xata/PDDxPY9xptrEGBCP/images/core-concepts/copy-on-write-diagram-1.png?fit=max&auto=format&n=PDDxPY9xptrEGBCP&q=85&s=76e697227752a113273067e3b5adf840" alt="Copy-on-Write branching — initial state" className="rounded-lg" width="882" height="450" data-path="images/core-concepts/copy-on-write-diagram-1.png" />

When the Copy-on-Write volume snapshot is created, and the child database branch started from this snapshot, a new index is created. However, no data blocks are copied at this point; the index simply points to the original data blocks. This makes the snapshot instant, regardless of how large the database is.

<img src="https://mintcdn.com/xata/PDDxPY9xptrEGBCP/images/core-concepts/copy-on-write-branching-2.png?fit=max&auto=format&n=PDDxPY9xptrEGBCP&q=85&s=d8f94c67678a2a38fd5cde505220a85c" alt="Copy-on-Write branching — new branch created" className="rounded-lg" width="880" height="446" data-path="images/core-concepts/copy-on-write-branching-2.png" />

After the branch is created, whenever writes are received by a data block — either by the child or the parent branch — that particular block is first copied. In the illustration below, this has happened for blocks 3 and 6. When a block diverges like this, the index from each branch references its own private copy of it.

<img src="https://mintcdn.com/xata/PDDxPY9xptrEGBCP/images/core-concepts/copy-on-write-branching-3.png?fit=max&auto=format&n=PDDxPY9xptrEGBCP&q=85&s=6a4052b79ef9c9fdf3a63496721b1a30" alt="Copy-on-Write branching — blocks diverging after writes" className="rounded-lg" width="868" height="448" data-path="images/core-concepts/copy-on-write-branching-3.png" />

Only the blocks that are modified need to be copied, which results in significant I/O and storage savings compared to a full copy.

## Costs and limits

* Each running branch has its own compute instance, billed hourly by instance type. [Scale-to-zero](/docs/core-concepts/scale-to-zero) eliminates compute costs while a branch is idle.
* Copy-on-Write shares unchanged storage blocks between a parent and its children, so creating a branch does not duplicate the parent's storage.
* The number of branches per project and the branch creation rate depend on your plan. If you hit a limit, [contact support](https://xata.io/contact-support).

## Behavior and limitations

* **Do changes in the parent appear in a child branch?** No. A child is a point-in-time copy; there is no replication between branches after creation.
* **Can I merge branches?** No. Schema changes are promoted by applying the same [migration](/docs/core-concepts/schema-changes) to each branch, manually or via CI.
* **Can I reset a child branch from its parent?** Not in place. Delete the branch and create a fresh one from the parent to get a new point-in-time copy.
* **Is `main` special?** No. It's the conventional name for the first branch, but Xata has no default-branch or promotion concept — any branch can be a parent.
* **Can I change a branch's region?** No. Region is fixed at creation, and child branches always live in the parent's region. Create a new base branch and [clone](/docs/cli/clone) your data to move regions.
