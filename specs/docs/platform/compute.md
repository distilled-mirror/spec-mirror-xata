> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Compute

> Configure scale-to-zero and hibernation options for projects and branches

## Overview

With scale to zero, branches automatically hibernate after periods of inactivity. You can also manually hibernate branches that won't be used for extended periods. Learn more about the [benefits of scale to zero](/docs/core-concepts/scale-to-zero).

## Project-Level Settings

Configure scale to zero defaults for all branches in your project. These settings apply to both base branches and child branches, providing a foundation for cost optimization across your entire project.

<img src="https://mintcdn.com/xata/PDDxPY9xptrEGBCP/images/platform/compute-project-settings.png?fit=max&auto=format&n=PDDxPY9xptrEGBCP&q=85&s=4db9f195bcedb39012ae8bf574ad3d2d" alt="Project-level compute settings showing scale to zero configuration options" className="rounded-lg" width="2240" height="1060" data-path="images/platform/compute-project-settings.png" />

Set up automatic hibernation for inactive branches across the project.

* **Enable for base branch**: Enable scale to zero for the base branch in the project
* **Enable for child branches**: Enable scale to zero for all child branches in the project
* **Inactivity period**: Define the duration of inactivity before hibernating the branch (base or child)

## Branch-Level Settings

Override project defaults with branch-specific compute settings. Each branch can have its own scale-to-zero configuration or be manually hibernated for extended periods.

<img src="https://mintcdn.com/xata/PDDxPY9xptrEGBCP/images/platform/compute-branch-settings.png?fit=max&auto=format&n=PDDxPY9xptrEGBCP&q=85&s=78997bfa0c584a9018014abcc9c32732" alt="Branch-level compute settings showing scale to zero and manual hibernation options" className="rounded-lg" width="2240" height="852" data-path="images/platform/compute-branch-settings.png" />

Customize compute settings for individual branches.

* **Scale to zero**: Enable or disable scale to zero for this branch
* **Inactivity period**: Define the duration of inactivity before hibernating the branch
* **Hibernate branch**: Instead of enabling scale to zero, manually hibernate the branch
