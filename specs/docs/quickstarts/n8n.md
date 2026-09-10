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

<Note>
  When adding a memory provider to your agent, n8n shows a dedicated **Xata** option. This option is out of date, so don't use it. Instead, use the **Postgres Chat Memory** option and point it at your Xata branch, as described below.
</Note>

### Prerequisites

* A [Xata account](https://console.xata.io) with a project and branch
* An n8n instance (cloud or self-hosted)

### Steps

1. In your n8n AI agent, add a memory provider and choose **Postgres Chat Memory** (not the Xata option).

<img src="https://mintcdn.com/xata/Nj_y4Z5mGeTrrakS/images/n8n-memory-providers.png?fit=max&auto=format&n=Nj_y4Z5mGeTrrakS&q=85&s=4773ad32d392d9836675423b554f2e6e" alt="n8n memory provider list showing Postgres Chat Memory and the out-of-date Xata option" className="rounded-lg" width="812" height="668" data-path="images/n8n-memory-providers.png" />

2. In the [Xata console](https://console.xata.io), open the **Overview** tab of your branch and copy the connection string shown at the top of the page.

<img src="https://mintcdn.com/xata/Nj_y4Z5mGeTrrakS/images/xata-conn-string.png?fit=max&auto=format&n=Nj_y4Z5mGeTrrakS&q=85&s=7ae07fc0c07feb0c5185bb3636bab090" alt="Xata project overview showing the PostgreSQL connection string" className="rounded-lg" width="2364" height="402" data-path="images/xata-conn-string.png" />

A Xata connection string looks like this:

```
postgresql://xata:<password>@<host>.us-east-1.xata.tech:5432/xata?sslmode=require
```

<Note>
  In the Xata UI, you can copy each field (host, poassword, port, etc.) by simply clicking it.
</Note>

3. Create a new PostgreSQL credential in n8n and copy the values from the connection string field by field:

   | n8n field | Value from connection string                |
   | --------- | ------------------------------------------- |
   | Host      | The host, e.g. `<host>.us-east-1.xata.tech` |
   | Database  | `xata`                                      |
   | User      | `xata`                                      |
   | Password  | The password from the connection string     |
   | Port      | `5432`                                      |

<img src="https://mintcdn.com/xata/Nj_y4Z5mGeTrrakS/images/n8n-postgresql-settings.png?fit=max&auto=format&n=Nj_y4Z5mGeTrrakS&q=85&s=ffe839b7611ba5741ef1d074873f88af" alt="n8n PostgreSQL credential form filled in with Xata connection details" className="rounded-lg" width="1714" height="1332" data-path="images/n8n-postgresql-settings.png" />

4. Leave **Ignore SSL Issues (Insecure)** off and set **SSL** to **Allow**.

<img src="https://mintcdn.com/xata/Nj_y4Z5mGeTrrakS/images/n8n-postgresql-settings-ssl.png?fit=max&auto=format&n=Nj_y4Z5mGeTrrakS&q=85&s=e9b9bc597b1560ef4a752b3c49717bc2" alt="n8n PostgreSQL SSL settings with SSL set to Allow and port 5432" className="rounded-lg" width="1678" height="774" data-path="images/n8n-postgresql-settings-ssl.png" />

5. Save the credential and test the connection. It should connect successfully, and your AI agent will now store its chat memory in Xata.

## Next steps

* **Enable scale-to-zero.** In your branch settings, enable scale-to-zero (a suggested inactivity time is 1 hour) so the branch pauses when it's not in use. This way you only incur compute costs while the branch is active. See [Scale to zero](/docs/core-concepts/scale-to-zero) for details.
* **Move beyond the free trial.** To keep using Xata after the free trial, add a credit card in the billing system. Xata charges only for the resources you use. There is no minimum fee.
