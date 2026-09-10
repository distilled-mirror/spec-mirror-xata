> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Scale to Zero

> Minimize costs by hibernating inactive database branches

## Overview

Xata's Scale to Zero feature allows you to minimize costs by hibernating database branches during periods of inactivity. This is particularly useful for development, testing and staging environments that don't need to be active 24/7.

When a branch is hibernated:

* Compute is paused and no active database connections are allowed
* Your data remains safe and accessible
* You only pay for storage, not compute costs
* Branches can be reactivated immediately

Xata offers two different ways of implementing scale to zero for your branches.

* Manual hibernation
* Automatic scale to zero

At the moment, you can enable either manual hibernation or scale to zero, but not both simultaneously.

## Manual Hibernation

Currently, you can manually hibernate branches through the Xata Console:

### How to Hibernate a Branch

1. Go to the Compute section under Branch Settings in the Xata Console
2. Toggle "Hibernate branch" to enabled
3. Click "Save compute settings"

### Hibernation Status

You can monitor hibernation status in the Xata Console:

* Active branches are running and accepting connections
* Hibernated branches are paused and not accepting connections

### Waking Up a Manually Hibernated Branch

To reactivate a hibernated branch:

1. Go to the Compute section under Branch Settings in the Xata Console
2. Toggle "Hibernate branch" to "off"
3. Click "Save compute settings"
4. The branch will immediately start accepting connections

Alternatively you can click the "Wake up branch" button in the branch overview to wake up a Hibernated Branch. You can also use the "Wake up branch" button available in the Queries, Extensions and Schemas tabs.

## Automatic Scale to Zero

Automatic scale to zero allows you to hibernate a branch after a configurable period of inactivity and automatically reactivate it when you attempt to connect to it.

### How to Enable Scale to Zero for a Branch

You can configure your scale to zero settings at the project level:

1. Go to the Compute section under Project Settings in the Xata Console
2. Toggle "Enable for base branch" if you want to enable scale to zero for your base branch by default
3. Toggle "Enable for child branches" if you want to enable scale to zero for child branches by default
4. Configure the relevant inactivity period for base/child branches
5. Click "Save settings"

The project scale to zero settings will be the default when creating new branches within the project. However, you will also be prompted at branch creation time in case you want to update those settings on a per branch basis. By default, these project level settings are only applied to new branches. If you would like existing branches to also be affected by these settings, you can check the "Apply these settings to existing branches" box.

Alternatively, you can also update the scale to zero settings once the branch has been created:

1. Go to the Compute section under Branch Settings in the Xata Console
2. Toggle "Scale to Zero" to enabled
3. Configure the inactivity period for the branch (default to 30min)
4. Click "Save compute settings"
5. The branch will now start tracking the open connections, and will automatically scale to zero once the configured period of inactivity has passed.

### Waking Up a Scaled to Zero Branch

A branch that has been automatically scaled to zero can be reactivated:

* By opening a connection to the branch (ex. connecting to the branch via psql)
* Manually, by clicking on the "Wake up branch" icon that appears on the branch overview or any other branch tabs that allow it.

<Note>
  When attempting to connect to a hibernated branch via the Serverless Proxy (HTTP or WebSocket), you'll receive a `409 Conflict` error with code `BRANCH_HIBERNATED` and the message "branch is hibernated, reactivate it to continue". The branch will need to be manually reactivated before connections can be established.
</Note>

## Branching off a Hibernated Branch

It is possible to create a child branch from a hibernated base branch (regardless of it being manually hibernated or scaled to zero). The base branch will not be woken up, keeping the costs to a minimum during the branching process.

## When to use Scale to Zero

### Development Branches

Perfect for feature branches that are only used during active development:

* You can hibernate branches when not actively developing
* Branches wake up instantly when you start working
* This reduces costs for long-running feature development

### Staging Environments

Ideal for staging databases that mirror production:

* You can hibernate staging environments when not testing
* Branches wake up for deployment testing
* This maintains production-like data without 24/7 costs

### Testing Environments

Great for automated testing databases:

* You can hibernate between test runs
* Branches wake up for CI/CD pipelines
* This optimizes costs for comprehensive testing
