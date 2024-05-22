# terraform-backend-github

This action downloads and uploads Terraform state file from a specified GitHub repository.

## Required inputs

- `token`: GitHub Token
- `state_repo`: Repository containing Terraform state file
- `action`: Action to perform, 'download' or 'upload'

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
