> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Use Xata with Railway

> Learn how to connect your Railway project with a Xata PostgreSQL database.

## What is Railway

[Railway](https://railway.com?utm_medium=integration\&utm_source=docs\&utm_campaign=xata) is Platform as a Service (PaaS) that supports almost any application stack or open-source framework. Railway will host your infrastructure so you don't have to deal with configuration, while allowing you to vertically and horizontally scale it.

While Railway offers built-in PostgreSQL templates, this page shows you how to use Xata as a full-managed PostgreSQL service for Railway.

## Prerequisites

* Xata account
* Railway account

## Getting started

1. Create a project and branch in the [Xata console](https://console.xata.io).
2. In the **Overview** tab of the branch, copy the connection string.

<img src="https://mintcdn.com/xata/PDDxPY9xptrEGBCP/images/guides/railway-branch-overview.png?fit=max&auto=format&n=PDDxPY9xptrEGBCP&q=85&s=c871b185aca450dd92f0f81713563924" alt="Branch overview showing connection string and database details" className="rounded-lg" width="2116" height="1928" data-path="images/guides/railway-branch-overview.png" />

3. In the Railway Marketplace you can find the following Xata specific templates:

* [Xata PostgreSQL for Node.js and Drizzle](https://railway.com/deploy/xata-postgresql-nodejs-drizzle?utm_medium=integration\&utm_source=docs\&utm_campaign=xata)
* [Xata PostgreSQL for Django](https://railway.com/deploy/xata-postgresql-django?utm_medium=integration\&utm_source=docs\&utm_campaign=xata)

4. Click **Deploy Now** in the template page.
5. In the creation modal, use the connection string from step 2 as the `DATABASE_URL` setting.

<img src="https://mintcdn.com/xata/PDDxPY9xptrEGBCP/images/guides/railway-template-install.png?fit=max&auto=format&n=PDDxPY9xptrEGBCP&q=85&s=60e926d3297218142dd497e5f8b67bb4" alt="Railway template installation modal showing database URL configuration" className="rounded-lg" width="2428" height="2146" data-path="images/guides/railway-template-install.png" />

6. Click **Deploy**. Once the deployment is done, visit the resulting page at the assign domain. On success, it should show the PostgreSQL version.

<img src="https://mintcdn.com/xata/PDDxPY9xptrEGBCP/images/guides/railway-template-success.png?fit=max&auto=format&n=PDDxPY9xptrEGBCP&q=85&s=4178f3de20371646120d873104ae1788" alt="Railway template success page showing PostgreSQL version and deployment confirmation" className="rounded-lg" width="2228" height="880" data-path="images/guides/railway-template-success.png" />

<Tip>
  The above screenshot is from the Node.js template. The other templates have a different success screen.
</Tip>

## Add Xata PostgreSQL to your existing Railway project

You can also add Xata to your existing project. To do this, you would typically replace the PostgreSQL service from the Railway canvas. To do this:

1. Create a `DATABASE_URL` variable and set it to the PostgreSQL connection string, which you can copy from the Xata branch **Overview** page.
2. Use `DATABASE_URL` as your PostgreSQL connection string in your application. See the various framework and programming language guides on how to do this.
3. If you had PostgreSQL on your Railway canvas, you can remove it now.
