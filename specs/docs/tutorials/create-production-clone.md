> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Create a production clone

> Use Xata's clone and branching features to create a safe, anonymized production clone.

This guide shows you how to create a realistic production clone in Xata by cloning your production database, optionally anonymizing sensitive data.

<img src="https://mintcdn.com/xata/zunLvnbMMVhTIsZq/images/deployment-models/xata-deployment-staging.png?fit=max&auto=format&n=zunLvnbMMVhTIsZq&q=85&s=19ef06a16fb3d83a6d6bff838395b5b1" alt="Creating a production clone of your database" className="rounded-lg" width="1084" height="1270" data-path="images/deployment-models/xata-deployment-staging.png" />

## 1. Prerequisites

* A Xata account ([sign up here](https://console.xata.io))
* The [Xata CLI](/docs/cli) installed:
  ```bash theme={null}
  curl -fsSL https://xata.io/install.sh | bash
  ```
* Access to your production PostgreSQL database

## 2. Create a Xata project and branch

In the Console, create a new project and then click the **Create main branch** button to create the PostgreSQL instance that will hold the production clone.

<img src="https://mintcdn.com/xata/PDDxPY9xptrEGBCP/images/create-new-branch.png?fit=max&auto=format&n=PDDxPY9xptrEGBCP&q=85&s=cbb47662022af582812118fea4057b47" alt="Create new branch" className="rounded-lg" width="3010" height="2050" data-path="images/create-new-branch.png" />

Since this is a non-production setup, you can typically select 0 replicas. It is a good idea to start with a similar
instance size as you use for production, but you can also use a smaller instance size if the production clone traffic will be much smaller. You can adjust the size later.

<Tip>
  If you're using Xata for production, we recommend creating a separate organization for your production clone. This allows you to control access to production data more effectively by only granting production access to team members who need it. More granular role-based access control (RBAC) is on our roadmap.
</Tip>

## 3. Configure the Xata CLI

Authenticate the CLI by running:

```sh theme={null}
xata auth login
```

Initialize the project by running:

```sh theme={null}
xata init
```

You would typically run `xata init` command in your application source directory, or in any directory/repository where you'd like to keep the configuration. It will create a `.xata` folder with several configuration files.

Follow the instructions from the prompt to select the project and branch that you created at the previous step. Leave the database name set to `xata`.

## 4. Get the connection string of your current provider

This step depends on your Postgres provider, but keep the following recommendations in mind:

* If your provider offers a read-only endpoint, it is recommended to use that one in order to avoid extra load to your primary instance.
* If your provider offers a "pooler" or "pgbouncer" endpoint, avoid that one, because `xata clone` uses a long running transaction.
* The Postgres connection string generally has the form `postgres://user:pass@host:5432/dbname`.

## 5. Configure cloning

First, generate a configuration for the Xata clone process:

```bash theme={null}
xata clone config --source-url $CONN_STRING --mode=prompt
```

Where the `CONN_STRING` variable is set to the production connection string from the previous step.

<Tip>
  if your Postgres hosting provider uses self-signed certificates, you can disable the strict certificate verification like this:
</Tip>

```bash theme={null}
NODE_TLS_REJECT_UNAUTHORIZED=0 xata clone config --source-url $CONN_STRING --mode=prompt
```

Follow the prompts to specify whether you want anonymization enabled, which tables and columns to anonymize, and which transformers to use. For more details about transformers, see the [anonymization](/docs/core-concepts/anonymization) page.

<Tip>
  To simplify the process, the interactive prompt suggests which columns require anonymization. This is based on simple heuristics (name, email, etc.)
</Tip>

The result is a YAML configuration file written in `.xata/clone.yaml`. You can edit this file to further configure your anonymization process.

### 6. Start the clone

Now, run the clone command to snapshot your production database into Xata:

```bash theme={null}
xata clone start --source-url $CONN_STRING
```

* The clone process will copy data and apply your anonymization rules to the current production clone branch.

***

## 7. Create a branch from the production clone

Now, list your branches to get the production clone branch ID:

```bash theme={null}
xata branch list
```

Grab the production clone branch ID from the list and create a new `dev` branch from it:

```bash theme={null}
xata branch create --name dev --parent-branch <production-clone-branch-id>
```

Wait for the branch to become ready:

```bash theme={null}
xata branch wait-ready
```

Connect to the newly created dev branch:

```sh theme={null}
psql `xata branch url`
```

***

## Summary

* You now have a realistic, anonymized production clone in Xata.
* You can safely test schema migrations, new indexes, or any other database changes using instant Copy-on-Write branches.

For more details on how to set up an advanced schema changes workflow, see the [schema changes](/docs/core-concepts/schema-changes) section.

To automate this process and keep your production clone up-to-date, see the [scheduled clone](/docs/automations/kubernetes-clone) documentation.
