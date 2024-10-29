# GitHub ref_protected false for tags

This repo demonstrates the (at least documentation) bug due to which github.ref_protected and GITHUB_REF_PROTECTED are always `false` in GitHub Actions jobs, even for tags that are protected. The bug was [raised in GitHub Community disussions here](https://github.com/orgs/community/discussions/142985)

## Docs

Both the [docs for `github.ref_protected`](https://docs.github.com/en/actions/writing-workflows/choosing-what-your-workflow-does/accessing-contextual-information-about-workflow-runs#github-context) and [for `GITHUB_REF_PROTECTED`](https://docs.github.com/en/actions/writing-workflows/choosing-what-your-workflow-does/store-information-in-variables#default-environment-variables) describe the value as:

> `true` if branch protections or [rulesets](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/managing-rulesets-for-a-repository) are configured for the ref that triggered the workflow run.

The language is ambiguous as to whether this means "`true` if branch protections or _branch_ rulesets" or "`true` if branch protections or _any_ rulesets." This compounded by the current state where rulesets may be configured for both tags and branches, while protections are only available for branches (since tag protections are deprecated), a possible explanation for the choice of language specifying "_branch_ protections" and just "rulesets," on the assumption that the variable is intended to be `true` for a protected tag ref.

The variable name also implies that it should be `true` for protected tags, as if it were only for branches the name should be `github.branch_protected`.

## Settings

Settings for this repo are tracked as code via the [Probot settings App](https://probot.github.io/apps/settings/) and defined in the `.github/settings.yml` file. For convenience, the following is the result of these settings:

<img width="802" alt="Screen Shot 2024-10-29 at 07 23 08" src="https://github.com/user-attachments/assets/32eb55bc-c801-47af-9f3f-aaed315cf90d">

<img width="802" alt="Screen Shot 2024-10-29 at 07 23 32" src="https://github.com/user-attachments/assets/2ccae7a7-9209-4afb-bd2b-10d9b9e403f5">

<img width="802" alt="Screen Shot 2024-10-29 at 07 23 45" src="https://github.com/user-attachments/assets/758ddd15-00d4-4e30-98fc-081d19635609">

## Workflow

The workflow definition is a simple `env` and `echo "github.ref_protected: ${{ github.ref_protected }}"` call, defined in the [.github/workflows/workflow.yaml](https://github.com/rcwbr/GitHub-ref_protected-false-for-tags/blob/main/.github/workflows/workflow.yaml) file.

## Variable results

With the settings applied, the [0.1.0 release](https://github.com/rcwbr/GitHub-ref_protected-false-for-tags/releases/tag/0.1.0) was created, thus generating the [0.1.0 tag](https://github.com/rcwbr/GitHub-ref_protected-false-for-tags/tree/0.1.0). The [Actions run for this event](https://github.com/rcwbr/GitHub-ref_protected-false-for-tags/actions/runs/11575406064/job/32221918719) produced the following logs (truncated for convenience):

```bash
SELENIUM_JAR_PATH=/usr/share/java/selenium-server.jar
CONDA=/usr/share/miniconda
...
GITHUB_REF_PROTECTED=false
...
AGENT_TOOLSDIRECTORY=/opt/hostedtoolcache
_=/usr/bin/env
github.ref_protected: false
```

The value for `ref_protected` appears as false, despite the matching rule as can be seen from the ruleset page:

<img width="802" alt="Screen Shot 2024-10-29 at 07 29 41" src="https://github.com/user-attachments/assets/f3b97f57-5b52-41bf-b813-13e0180d954a">

## History

This behavior seems to date back at least several years, as it was raised in a [comment on a public beta in 2022](https://github.com/orgs/community/discussions/10906#discussioncomment-3915816) and the subject of [a discussions post in 2023](https://github.com/orgs/community/discussions/66588). After creating this repo, it was shared to [a community bug report discussion](https://github.com/orgs/community/discussions/142985).
