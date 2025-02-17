## preview

Note that when using the preview version of AL-Go for GitHub, you need to Update your AL-Go system files, as soon as possible when told to do so.

### Issues
- Issue #143 Commit Message for **Increment Version Number** workflow
- Issue #160 Create local DevEnv aith appDependencyProbingPaths
- Issue #156 Versioningstrategy 2 doesn't use 24h format
- Issue #155 Initial Add existing app fails with "Cannot find path"
- Issue #152 Error when loading dependencies from releases
- Issue #168 Regression in preview fixed

### Settings
- New Repo setting: CICDPushBranches can be specified as an array of branches, which triggers a CI/CD workflow on commit. Default is main', release/\*, feature/\*
- New Repo setting: CICDPullRequestBranches can be specified as an array of branches, which triggers a CI/CD workflow on pull request. Default is main
- New Repo setting: CICDSchedule can specify a CRONTab on when you want to run CI/CD on a schedule. Note that this will disable Push and Pull Request triggers unless specified specifically using CICDPushBranches or CICDPullRequestBranches
- New Repo setting: UpdateGitHubGoSystemFilesSchedule can specify a CRONTab on when you want to Update AL-Go System Files. Note that when running on a schedule, update AL-Go system files will perfom a direct commit and not create a pull request.

### All workflows
- Support 2 folder levels projects (apps\w1, apps\dk etc.)
- Better error messages for if an error occurs within an action
- Special characters are now supported in secrets
- Initial support for agents running inside containers on a host

### Update AL-Go System Files Workflow
- workflow now displays the currently used template URL when selecting the Run Workflow action

### CI/CD workflow
- Better detection of changed projects
- appDependencyProbingPaths did not support multiple projects in the same repository for latestBuild dependencies
- appDependencyProbingPaths with release=latestBuild only considered the last 30 artifacts
- Use mutex around ReadSecrets to ensure that multiple agents on the same host doesn't clash

### CI/CD and Publish To New Environment
- Base functionality for selecting a specific GitHub runner for an environment

### localDevEnv.ps1 and cloudDevEnv.ps1
- Display clear error message if something goes wrong

## v1.5

### Issues
- Issue #100 - Add more resilience to localDevEnv.ps1 and cloudDevEnv.ps1
- Issue #131 - Special characters are not allowed in secrets

### All workflows
- During initialize, all AL-Go settings files are now checked for validity and reported correctly
- During initialize, the version number of AL-Go for GitHub is printed in large letters (incl. preview or dev.)

### New workflow: Create new Performance Test App
- Create BCPT Test app and add to bcptTestFolders to run bcpt Tests in workflows (set doNotRunBcptTests in workflow settings for workflows where you do NOT want this)

### Update AL-Go System Files Workflow
- Include release notes of new version in the description of the PR (and in the workflow output)

### CI/CD workflow
- Apps are not signed when the workflow is running as a Pull Request validation
- if a secret called applicationInsightsConnectionString exists, then the value of that will be used as ApplicationInsightsConnectionString for the app

### Increment Version Number Workflow
- Bugfix: increment all apps using f.ex. +0.1 would fail.

### Environments
- Add suport for EnvironmentName redirection by adding an Environment Secret under the environment or a repo secret called \<environmentName\>_EnvironmentName with the actual environment name.
- No default environment name on Publish To Environment

### Settings
- New setting: **runs-on** to allow modifying runs-on for all jobs (requires Update AL-Go System files after changing the setting)
- New setting: **DoNotSignApps** - setting this to true causes signing of the app to be skipped
- New setting: **DoNotPublishApps** - setting this to true causes the workflow to skip publishing, upgrading and testing the app to improve performance.
- New setting: **ConditionalSettings** to allow to use different settings for specific branches. Example:
```
    "ConditionalSettings": [
        {
            "branches": [
                "feature/*"
            ],
            "settings": {
                "doNotPublishApps":  true,
                "doNotSignApps":  true
            }
        }
    ]
```
- Default **BcContainerHelperVersion** is now based on AL-Go version. Preview AL-Go selects preview bcContainerHelper, normal selects latest.
- New Setting: **bcptTestFolders** contains folders with BCPT tests, which will run in all build workflows
- New Setting: set **doNotRunBcptTest** to true (in workflow specific settings file?) to avoid running BCPT tests
- New Setting: set **obsoleteTagMinAllowedMajorMinor** to enable appsource cop to validate your app against future changes (AS0105). This setting will become auto-calculated in Test Current, Test Next Minor and Test Next Major later.

## v1.4

### All workflows
- Add requested permissions to avoid dependency on user/org defaults being too permissive

### Update AL-Go System Files Workflow
- Default host to https://github.com/ (you can enter **myaccount/AL-Go-PTE@main** to change template)
- Support for "just" changing branch (ex. **\@Preview**) to shift to the preview version

### CI/CD Workflow
- Support for feature branches (naming **feature/\***) - CI/CD workflow will run, but not generate artifacts nor deploy to QA

### Create Release Workflow
- Support for release branches
- Force Semver format on release tags
- Add support for creating release branches on release (naming release/\*)
- Add support for incrementing main branch after release

### Increment version number workflow
- Add support for incremental (and absolute) version number change

### Environments
- Support environmentName redirection in CI/CD and Publish To Environments workflows
- If the name in Environments or environments settings doesn't match the actual environment name,
- You can add a secret called EnvironmentName under the environment (or \<environmentname\>_ENVIRONMENTNAME globally)


## v1.3

### Issues
- Issue #90 - Environments did not work. Secrets for environments specified in settings can now be **\<environmentname\>_AUTHCONTEXT**

### CI/CD Workflow
- Give warning instead of error If no artifacts are found in **appDependencyProbingPaths**

## v1.2

### Issues
- Issue #90 - Environments did not work. Environments (even if only defined in the settings file) did not work for private repositories if you didn't have a premium subscription.

### Local scripts
- **LocalDevEnv.ps1** and ***CloudDevEnv.ps1** will now spawn a new PowerShell window as admin instead of running inside VS Code. Normally people doesn't run VS Code as administrator, and they shouldn't have to. Furthermore, I have seen a some people having problems when running these scripts inside VS Code.


## v1.1

### Settings
- New Repo Setting: **GenerateDependencyArtifact** (default **false**). When true, CI/CD pipeline generates an artifact with the external dependencies used for building the apps in this repo.
- New Repo Setting: **UpdateDependencies** (default **false**). When true, the default artifact for building the apps in this repo is not the latest available artifacts for this country, but instead the first compatible version (after calculating application dependencies). It is recommended to run Test Current, Test NextMinor and Test NextMajor in order to test your app against current and future builds.

### CI/CD Workflow
- New Artifact: BuildOutput.txt. All compiler warnings and errors are emitted to this file to make it easier to investigate compiler errors and build a better UI for build errors and test results going forward.
- TestResults artifact name to include repo version number and workflow name (for Current, NextMinor and NextMajor)
- Default dependency version in appDependencyProbingPaths setting used is now latest Release instead of LatestBuild
