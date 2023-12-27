
# CI/CD


Continuous Integration(CI):
The practice of frequently building, testing and merging code changes into a shared repo.
**Allows developers to detect integration issues early and maintain a consistent codebase.
Conitnuous Delivery (CD): Unsures that code changes can be deployed to prod. at any time but requeires manual approval.
Continuous Deployment (CD): Automatically deploys code changes to production without manual intervention.

## CI/CD in ML

Data dependency: Data versioning and management strategies.
Experimentation: Automating hyperparameter tuning.
Model Versioning: Improving collaoration.
Testing Paradigm: Goes beyond traditional functional and unit testing.
Continuous Deployment Challenges: Complexities in model serving, monitroing and updates.

# GitHub Actions (GHA)

Checkout -> Env. Setup -> LINT & TEST -> Build -> Deploy

*Event*: is a specific acitivity in a repository that triggers a workflow run.
Push, Pull request, opening a issue
*Workflow*: automated process that will run one or more jobs.
Defined in YAML files, triggered automatically by event (manual run possible)

housed in .github/workflows, multiple workflows can be defined.

*Steps*: individual units of work.
Executed in order, depends on previous step
Run on the same machine, so data can be shared
Unit of work examples:
Compiled code application, shell script.

complex but frequently repeated tasks can combine
(Checkout repo, comment on PR)

*Job*: set of steps.
Each job is independent.
Parallel execeution is possible.
Executed on the compute machine called runners.

## Intermediate YAML 

Represents multiline strings or blocks of text
- Code snippets (shell commands)
- Paragraphs (logs)
- Configuration files
  
### Style indicators - letral

*Literal style* (|) preserves line break and identation
Useful for writing shell commands
commands: |
  echo "Running script"
  R script.R

*Fold style* (>) removes line breaks
Useful for writing wrapped messages like paragraphs
message: >
  lorem ipsum lorem 
  ipsum lorem.

### Chomping indicators
Control the behaviour of newlines at he end of the string
added after the style indicators

*clip* default mode single line at the end
No additional symbol needed for representation

*strip*(-) removes all newlines at the end
*keep* (+) retains all newlines at the end

### Dynamic value injection

Expressions allow parser to dynamically substitute values
 ```
Usage:
  ENV variables
  Ref other parts of YAML
${{ ... }}
database:
  host: ${{ config.database.host }}
 ```
Multi-document YAML
 ```
---
- name: Ugur
----
- name: ...
 ```
### Example
 ```
config:
  slack:
    channels:
      - workflow-orchestration
      - builds-911
  workflow:
   #Use the correct block style indicator 
   #while writing commands to run
  run: |
    echo "Running script.py"
    python3 script.py
  notify:
    - slack:
        # Reference the Slack channels using 
        # placeholder defined in config block
        channels: ${{ config.slack.channels }}
        # Use the correct block style indicator 
        # to send the message without newlines
        message: >-
          It appears that your run has failed.
          To gain more insights into the failure,
          it is recommended to examine the CI logs.
          In case further assistance is required,
          feel free to contact the Engineer on call.
      if: run.state == "failed"
       ```

# Setting a basic CI pipeline

# Environment Variables and Secrets
- Access information about predefined variables and data
- workflow runs, variables, runner environments, jobs and steps
  ${{ context.XXXX }}
  ## Variables
  Store non-sensitive information in plain text: compiler flags, usernames, file paths
  ```
  name: Greeting on variable day
  #Global Env
  env:
    Greeting: Hello
  jobs:
    greeting_job:
      runs-on: ubuntu-latest
      # Local enc scoped to greeting_job
      env:
        Frist_Name: Ravi
      steps:
        - run: |
              echo "${{ env.Greeting }} \
              ${{ env.First_Name }}."
  ```

## Secrets
Store senstive information in encrrpyted manner: password API keys
$[[ secrets.SuperSecrets }}
```
steps:
 -name: Hello world action
  env: # set the secret as an env var
      super_secret: ${{ secrets.SuperSecret }}
  with: # Or as an input
      super_secret: ${{ secrets.SuperSecret }}
```

Repository -> Settings -> Security -> Secrets and Variables -> Actions -> 
New RepoSecret -> Name and Secret.
GITHUB_TOKEN secret ${{ secrets.GITHUB_TOKEN }} automatically created.

Write comment in PR
```
permission: 
  pull-request: write
steps:
  - name Commenct PR
    uses: thollander/actions-comment-pull-request@v2
    with:
      GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN}}
      message: |
        Hello world ! :wave:
```

```
name: Testing Environment variables

on:
  pull_request:
    # Write the target branch
    branches: main

env:
  # Set the value of global environment variable
  GLOBAL_VARIABLE: global_value

jobs:
  
  # Job block
  print_env_and_secrets:
    runs-on: ubuntu-latest
    
    env:
      # Set the value of local environment variable
      JOB_VARIABLE: job_value

    steps:
      - name: Print Variables
        # Write the environment variable whose value is set
        run: |
          echo "Global Variable: ${{ env.GLOBAL_VARIABLE }}"
          echo "Set job Variable: ${{ env.JOB_VARIABLE }}"
```

```
name: Testing Secrets

on:
  # Write the event triggering the workflow
  pull_request:
    branches: master

jobs:
  print_secrets:
    runs-on: ubuntu-latest
    
    permissions: 
      # Grant the write permissions
      pull-requests: write

    steps:
      - name: Comment on Pull Request
        uses: thollander/actions-comment-pull-request@v2
        with:
          # Access the GITHUB_TOKEN token from secrets
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          message: |
            Hello world ! :wave:
```
    
  
  

