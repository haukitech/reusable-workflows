# Reusable workflows for GitHub Actions

This repository contains workflows reused across different Kamu
repositories to automate operations with GitHub Actions.


## helmfile-workflow.yml

Runs specified helmfile action on a Kubernetes cluster.


### Inputs

| Variable         | Type   | Required | Default  |
|------------------|--------|----------|----------|
| aws_region       | string | true     |          |
| cluster_name     | string | true     |          |
| environment      | string | true     |          |
| helm_action      | string | true     |          |
| iam_role_arn     | string | true     |          |
| helm_version     | string | false    | v3.13.1  |
| helmfile_version | string | false    | v0.158.0 |
| sops_version     | string | false    | 3.8.1    |


### Outputs

None


### Example

```yaml
jobs:
  verify:
    uses: kamu-data/reusable-workflows/.github/workflows/helmfile-workflow.yml@master
    with:
      aws_region: us-east-1
      cluster_name: my-cluster
      environment: my-helmfile-environment
      helm_action: diff 
      iam_role_arn: arn:aws:::0000000::my-role
```


## reusable-strategy.yml

Allows to specify a GitHub Actions strategy in a JSON-formatted text
file and re-use it in multiple jobs.


### Inputs

| Variable      | Type   | Required | Default                         |
|---------------|--------|----------|---------------------------------|
| strategy_file | string | false    | .github/workflows/strategy.json |


### Outputs

| Variable | Type   | Description             |
|----------|--------|-------------------------|
| strategy | string | JSON-formatted strategy |


### Example

Put the following JSON object to `.github/workflows/strategy.json`:

```json
{
  "fail-fast": false,
  "matrix": {
    "my_variable": [
      "foo",
      "bar"
    ]
  }
}

```

The workflow file will look as follows:

```yaml
jobs:
  strategy:
    uses: kamu-data/reusable-workflows/.github/workflows/reusable-strategy.yml@master
  
  verify:
    needs: strategy
    strategy: ${{ fromJson(needs.strategy.outputs.strategy) }}
    run: echo ${{ matrix.my_variable }}
```
