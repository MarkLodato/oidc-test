# GitHub OIDC Security Demo

**Dummy update for the pull request.**

This repo demonstrates that, when verifying a
[GitHub OIDC token](https://docs.github.com/en/actions/deployment/security-hardening-your-deployments/about-security-hardening-with-openid-connect),
it is critical to verify the full `sub`, includin both repo and ref names.

The workflow [oidc-test.yml](.github/workflows/oidc-test.yml) is only supposed
to run on the `main` branch:

```yaml
on:
  push:
    branches:
      - main
  workflow_dispatch:
```

However, anyone with write access to the branch could create a new branch and
trigger this workflow, either by updating the yaml or by executing
`workflow_dispatch` on the new branch.

Here is a JWT payload on `main`:

```json
{
  "jti": "614cdcda-82a1-429a-b877-1a5c575324e8",
  "sub": "repo:MarkLodato/oidc-test:ref:refs/heads/main",
  "aud": "https://github.com/MarkLodato",
  "ref": "refs/heads/main",
  "sha": "51d8e23d23ab48ee0d1c539dc5c4788369ce2bb9",
  "repository": "MarkLodato/oidc-test",
  "repository_owner": "MarkLodato",
  "run_id": "1409438232",
  "run_number": "2",
  "run_attempt": "1",
  "actor": "MarkLodato",
  "workflow": "Dump OIDC to logs",
  "head_ref": "",
  "base_ref": "",
  "event_name": "push",
  "ref_type": "branch",
  "job_workflow_ref": "MarkLodato/oidc-test/.github/workflows/oidc-test.yml@refs/heads/main",
  "iss": "https://token.actions.githubusercontent.com",
  "nbf": 1635795550,
  "exp": 1635796450,
  "iat": 1635796150
}
```

And here is a payload on `evil-branch`:

```json
{
  "jti": "3d98dd63-0dd6-496c-a856-fb847f125171",
  "sub": "repo:MarkLodato/oidc-test:ref:refs/heads/evil-branch",
  "aud": "https://github.com/MarkLodato",
  "ref": "refs/heads/evil-branch",
  "sha": "333722a3be65214ecf66cf98e31ad0cde8240e1c",
  "repository": "MarkLodato/oidc-test",
  "repository_owner": "MarkLodato",
  "run_id": "1409448609",
  "run_number": "3",
  "run_attempt": "1",
  "actor": "MarkLodato",
  "workflow": "Dump OIDC to logs",
  "head_ref": "",
  "base_ref": "",
  "event_name": "push",
  "ref_type": "branch",
  "job_workflow_ref": "MarkLodato/oidc-test/.github/workflows/oidc-test.yml@refs/heads/evil-branch",
  "iss": "https://token.actions.githubusercontent.com",
  "nbf": 1635795766,
  "exp": 1635796666,
  "iat": 1635796366
}
```
