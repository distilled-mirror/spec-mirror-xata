> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Use Xata with n8n

> Learn how to use Xata as the memory provider for AI agents in n8n.

## What is n8n

[n8n](https://n8n.io) is a workflow automation platform that lets you connect apps, APIs, and AI models to build automated workflows. It's commonly used to build AI agents, where a large language model is given tools and memory to carry out multi-step tasks.

This page shows you how to use Xata with n8n. Since Xata is fully PostgreSQL-compatible, you can use it anywhere n8n supports PostgreSQL.

## Use Xata as memory for AI agents

The most common way to use Xata with n8n is as the memory store for an AI agent, so that the agent can persist and recall chat history across runs.

> [!NOTE]
>
> When adding a memory provider to your agent, n8n shows a dedicated **Xata** option. This option is out of date, so don't use it. Instead, use the **Postgres Chat Memory** option and point it at your Xata branch, as described below.

### Prerequisites

* A [Xata account](https://console.xata.io) with a project and branch
* An n8n instance (cloud or self-hosted)

### Steps

1. In your n8n AI agent, add a memory provider and choose **Postgres Chat Memory** (not the Xata option).

![n8n memory provider list showing Postgres Chat Memory and the out-of-date Xata option](https://mintcdn.com/xata/Nj_y4Z5mGeTrrakS/images/n8n-memory-providers.png?fit=max\&auto=format\&n=Nj_y4Z5mGeTrrakS\&q=85\&s=4773ad32d392d9836675423b554f2e6e)

2. In the [Xata console](https://console.xata.io), open the **Overview** tab of your branch and copy the connection string shown at the top of the page.

![Xata project overview showing the PostgreSQL connection string](https://mintcdn.com/xata/Nj_y4Z5mGeTrrakS/images/xata-conn-string.png?fit=max\&auto=format\&n=Nj_y4Z5mGeTrrakS\&q=85\&s=7ae07fc0c07feb0c5185bb3636bab090)

A Xata connection string looks like this:

```
postgresql://xata:<password>@<host>.us-east-1.xata.tech:5432/xata?sslmode=require
```

> [!NOTE]
>
> In the Xata UI, you can copy each field (host, poassword, port, etc.) by simply clicking it.

3. Create a new PostgreSQL credential in n8n and copy the values from the connection string field by field:

   | n8n field | Value from connection string |
   | - | - |
   | Host | The host, e.g. `<host>.us-east-1.xata.tech` |
   | Database | `xata` |
   | User | `xata` |
   | Password | The password from the connection string |
   | Port | `5432` |

![n8n PostgreSQL credential form filled in with Xata connection details](https://mintcdn.com/xata/Nj_y4Z5mGeTrrakS/images/n8n-postgresql-settings.png?fit=max\&auto=format\&n=Nj_y4Z5mGeTrrakS\&q=85\&s=ffe839b7611ba5741ef1d074873f88af)

4. Leave **Ignore SSL Issues (Insecure)** off and set **SSL** to **Allow**.

![n8n PostgreSQL SSL settings with SSL set to Allow and port 5432](https://mintcdn.com/xata/Nj_y4Z5mGeTrrakS/images/n8n-postgresql-settings-ssl.png?fit=max\&auto=format\&n=Nj_y4Z5mGeTrrakS\&q=85\&s=e9b9bc597b1560ef4a752b3c49717bc2)

5. Save the credential and test the connection. It should connect successfully, and your AI agent will now store its chat memory in Xata.

## Next steps

* **Enable scale-to-zero.** In your branch settings, enable scale-to-zero (a suggested inactivity time is 1 hour) so the branch pauses when it's not in use. This way you only incur compute costs while the branch is active. See [Scale to zero](https://xata.io/docs/core-concepts/scale-to-zero.md) for details.
* **Move beyond the free trial.** To keep using Xata after the free trial, add a credit card in the billing system. Xata charges only for the resources you use. There is no minimum fee.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
