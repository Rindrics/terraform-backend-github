# terraform-backend-github

This action downloads and uploads Terraform state file from a specified GitHub repository.

## Inputs

### Required

- `token`: GitHub Token
- `state_repo`: Repository containing Terraform state file
- `action`: Action to perform, 'download' or 'upload'
- `sha`: 'SHA of the state file to be overwritten by upload action (required for upload)'

### Optional (defaults)

- `state_path_remote`: Path to the Terraform state file in `state_repo` (`terraform.tfstate`)
- `state_path_local`: Path to the Terraform state file in GitHub Actions environment  (`terraform.tfstate`)
- `commit_message`: Commit message for the upload action (`'Update state file'`)
- `committer_name`: Committer name for the upload action (`'github-actions[bot]'`)
- `committer_email`: Committer email for the upload action (`'github-actions[bot]@users.noreply.github.com'`)

## Outputs

- `sha`: SHA of the state file

## Example usage

```yaml
jobs:
  terraform:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Setup Terraform
        uses: hashicorp/setup-terraform@v3

      - name: Initialize Terraform workdir
        run: terraform init

      - name: Download Terraform state file
        id: terraform
        uses: your-username/terraform-backend-action@v1
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
          state_repo: your-repo/tfstates

      - name: Execute `terraform apply`
        run: |
          terraform plan -no-color -out .terraform/terraform.plan
          terraform apply -no-color .terraform/terraform.plan

      - name: Upload Terraform state file
        uses: your-username/terraform-backend-action@v1
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
          state_repo: your-repo/tfstates
          sha: ${{ steps.terraform.outputs.sha }}
```
