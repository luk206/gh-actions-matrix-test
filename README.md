# GitHub Actions Matrix Deployment

A workflow for deploying multiple projects to different environments with manual approval.

## What it does

This workflow lets you run deployments across several projects and environments. Each deployment has two stages:
- **Plan**: Runs automatically to check what will change
- **Apply**: Requires manual approval and only runs if the plan succeeded

## How to use it

1. Go to Actions → Projects Matrix PoC → Run workflow
2. Set your parameters:
   - `projects`: which projects to deploy (comma-separated)
   - `environments`: which environments (comma-separated)
   - `exclude`: skip specific combinations (format: `project:env`)
3. Wait for plans to complete
4. Review and approve the deployments you want to apply

## Parameters

**Default values:**
- Projects: `1000-5,1000-6,1000-7,1000-8,1000-9`
- Environments: `dev,mlops,prd`
- Exclude: `1000-5:mlops,1000-6:dev`

## Examples

Deploy one project to all environments:
```
projects: 1000-5
environments: dev,mlops,prd
exclude: 
```

Deploy everything to production:
```
projects: 1000-5,1000-6,1000-7,1000-8,1000-9
environments: prd
exclude: 
```

Deploy with exclusions:
```
projects: 1000-5,1000-6
environments: dev,prd
exclude: 1000-5:prd,1000-6:dev
```

## Setting up approvals

You need to create GitHub Environments for manual approval:

1. Settings → Environments → New environment
2. Name it `approve-{project}-{env}` (example: `approve-1000-5-dev`)
3. Add required reviewers
4. Save

Do this for each project-environment combination you want to protect.

## Adding new projects

Copy an existing job pair and change the project/environment names. Each combination needs:
- One `plan_{project}_{env}` job
- One `apply_{project}_{env}` job with an `environment` setting

## How it works

Each deployment runs independently. If one plan fails, it doesn't affect the others. You only get asked to approve the ones that succeeded.

The workflow uses simple filtering: it checks if your project is in the `projects` list, your environment is in the `environments` list, and the combination is not in the `exclude` list.

## Notes

- Plan `1000-5:prd` is set to fail on purpose to show how failures work
- All plans run in parallel
- Be careful with project names that overlap (like `1000-1` and `1000-10`)
