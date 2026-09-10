> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Projects

> Create and manage projects that contain your database branches

## Overview

A project is part of an organization that has a base branch and child branches. Projects serve as containers for your database environments, allowing you to organize and manage multiple development and production environments under a single project umbrella.

<img src="https://mintcdn.com/xata/PDDxPY9xptrEGBCP/images/platform/project.png?fit=max&auto=format&n=PDDxPY9xptrEGBCP&q=85&s=5966fe09b0f11da19e97e7e3530cc008" alt="Project overview interface" className="rounded-lg" width="2880" height="1556" data-path="images/platform/project.png" />

## Create Project

Create a new project to start managing your database environments and branches.

<img src="https://mintcdn.com/xata/PDDxPY9xptrEGBCP/images/platform/project-create.png?fit=max&auto=format&n=PDDxPY9xptrEGBCP&q=85&s=542f2c3a6f723bcf86e4d0120c87c40f" alt="Create project interface" className="rounded-lg" width="2880" height="686" data-path="images/platform/project-create.png" />

### From the CLI

Create a project using the Xata CLI:

```bash theme={null}
# Create a new project
xata project create --name "my-app" --organization "org-id"
```

For more CLI commands, see the [CLI Reference](/docs/cli/project).

## Configure Compute

Configure compute resources for your project including scale-to-zero capabilities and manual hibernation options.

For detailed information about compute configuration, scaling, and resource management, see [Compute Resources](/docs/platform/compute).

## Default Database

Set a default database for your project to streamline access and operations. This feature allows you to select which database should be automatically selected within each branch. If the database does not exist in the branch, it will default to `xata`.

<img src="https://mintcdn.com/xata/PDDxPY9xptrEGBCP/images/platform/default-database.png?fit=max&auto=format&n=PDDxPY9xptrEGBCP&q=85&s=1370fc7f8afca2f46fe8800cbee65a36" alt="Default database selection interface" className="rounded-lg" width="1288" height="472" data-path="images/platform/default-database.png" />

## GitHub Repository

Link a GitHub repository to a project to create Xata preview branches from pull requests. The Xata GitHub App creates child branches from the root branch you choose and cleans them up when pull requests close.

For setup instructions and behavior details, see the [GitHub App guide](/docs/automations/github-app).

## Delete Project

Delete a project and all its associated resources. **Important**: All branches must be deleted before a project can be deleted.

### From the CLI

Delete a project using the Xata CLI:

```bash theme={null}
# Delete a project (requires confirmation)
xata project delete --project "project-id"
```

For more CLI commands, see the [CLI Reference](/docs/cli/project).
