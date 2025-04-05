# Bitbucket to GitHub Repository Synchronization
## Project Overview

This documentation outlines the automated synchronization process between Bitbucket and GitHub repositories using GitHub Actions. The system enables automatic code synchronization, ensuring both repositories remain consistent through both manual and scheduled triggers.

## Repository Setup

### Prerequisites
- Bitbucket repository access credentials
- GitHub Personal Access Token with appropriate permissions
- Admin access to both repositories

### Required Secrets
Configure the following secrets in your GitHub repository settings:
- `BITBUCKET_USERNAME`: Your Bitbucket username
- `BITBUCKET_APP_PASSWORD`: Bitbucket App Password with repository access
- `GH_PERSONAL_ACCESS_TOKEN`: GitHub Personal Access Token with repo scope

## GitHub Actions Workflow

### Workflow Triggers
The synchronization process can be initiated through multiple triggers:
1. Push events to `main` or `migration-sync` branches
2. Pull requests to `main` branch
3. Scheduled runs every 5 minutes
4. Manual trigger via workflow dispatch

### Synchronization Process

#### 1. Repository Checkout
- Checks out the GitHub repository
- Specifically targets the `migration-sync` branch
- Maintains git history with `fetch-depth: 0`

```yaml
- name: Checkout GitHub repository
  uses: actions/checkout@v3
  with:
    ref: migration-sync
    persist-credentials: true
    fetch-depth: 0
```

#### 2. Bitbucket Remote Configuration
- Configures git credentials
- Adds Bitbucket repository as remote
- Fetches latest changes from Bitbucket

```yaml
- name: Add Bitbucket remote with git credential helper
  env:
    BITBUCKET_USERNAME: ${{ secrets.BITBUCKET_USERNAME }}
    BITBUCKET_APP_PASSWORD: ${{ secrets.BITBUCKET_APP_PASSWORD }}
  run: |
    git config --global credential.helper store
    echo "https://$BITBUCKET_USERNAME:$BITBUCKET_APP_PASSWORD@bitbucket.org" > ~/.git-credentials
    git remote add bitbucket https://bitbucket.org/solavisetech-migration/aws-etl-tf-python-project.git
    git fetch bitbucket
    git reset --hard bitbucket/master
```

#### 3. GitHub Authentication
- Sets up git configuration with commit author details

#### 4. Synchronization
- Pushes changes from Bitbucket to GitHub
- Forces push to ensure exact synchronization
- Updates the `migration-sync` branch

## Flowchart Visualization

![image](https://github.com/user-attachments/assets/49555b57-37fb-465e-9d82-00b7bfcf59eb)

## Troubleshooting

### Scheduled Triggers (Cron Jobs)
If scheduled triggers are not firing:

1. Verify cron syntax
   - Current configuration: `*/5 * * * *` (runs every 5 minutes)
   - Ensure no syntax errors in the workflow YAML

2. Check branch configuration
   - Confirm `migration-sync` branch exists
   - Verify workflow file is present in the branch

3. Common Issues
   - GitHub Actions may skip scheduled runs if there's no recent repository activity
   - Scheduled workflows only run on the default branch unless explicitly configured

### General Troubleshooting
1. Check Action Logs
   - Review workflow run history in GitHub Actions tab
   - Look for error messages in individual step outputs

2. Credential Issues
   - Verify secret configuration in repository settings
   - Ensure tokens haven't expired
   - Check token permissions match required scopes

3. Repository Access
   - Confirm Bitbucket repository URL is correct
   - Verify access permissions on both platforms

## Monitoring and Maintenance

### Health Checks
1. Regular monitoring of workflow runs
2. Verification of successful synchronization
3. Review of any failed attempts

### Best Practices
1. Regular token rotation
2. Periodic permission audits
3. Monitoring of workflow execution times
4. Regular validation of synchronized content

## Future Improvements
1. Implementation of error notifications
2. Addition of content validation steps
3. Enhanced logging and monitoring
4. Conflict resolution documentation

## Support and Contact
Contributors:
- Akwa N: Team Lead
- Emmanuel: Secretary
- Felix M.  : Member
- Osenat A.: Member
- Mark:   Member
- Darrell: Member
- Neil:  Member
