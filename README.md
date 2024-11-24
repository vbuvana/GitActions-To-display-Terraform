# GitActions-To-display-Terraform

This project automates the creation of AWS infrastructure using Terraform and integrates the workflow into a CI/CD pipeline with GitHub Actions.

## Project Overview

This repository contains:

* A Terraform configuration file (`main.tf`) that provisions an AWS infrastructure including:
  * A VPC
  * A public subnet
  * An internet gateway
  * A route table with internet access
  * A security group allowing SSH and HTTP access
  * An EC2 instance
* A GitHub Actions workflow (`.github/workflows/terraform.yml`) to deploy this infrastructure automatically.

---

## Prerequisites

Before running the project, ensure you have:

* **AWS Account**: Access keys with sufficient permissions to create resources.
* **Terraform**: Version 1.0.1 or later installed locally.
* **GitHub Repository Secrets**:
  * `AWS_ACCESS_KEY_ID`: Your AWS access key ID.
  * `AWS_SECRET_ACCESS_KEY`: Your AWS secret access key.
  * `AWS_SESSION_TOKEN`: Your AWS session token (if applicable).

---

## Repository Structure

```bash
bashCopy code.
├── .github/
│   └── workflows/
│       └── terraform.yml    # CI/CD pipeline definition
├── src/
│   └── main.tf              # Terraform configuration
└── README.md                # Project documentation
```

* **`terraform.yml`**: Defines the GitHub Actions workflow to automate Terraform deployment.
* **`main.tf`**: Contains the Terraform script for AWS infrastructure provisioning.

---

## Workflow: GitHub Actions

The GitHub Actions workflow automates the deployment process. It:

1. Pulls the repository code.
2. Sets up Terraform CLI.
3. Initializes Terraform.
4. Plans and applies the infrastructure changes.

### GitHub Workflow YAML

The workflow is defined in `.github/workflows/terraform.yml`:

```yaml
yamlCopy codename: "Terraform"

on:
    push:
        branches:
            - main

jobs:
    terraform:
        name: "Terraform"
        runs-on: ubuntu-latest

        env:
            AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
            AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
            AWS_SESSION_TOKEN: ${{ secrets.AWS_SESSION_TOKEN }}

        defaults:
            run:
                working-directory: src

        steps:
            - name: Checkout
              uses: actions/checkout@v2

            - name: Setup Terraform
              uses: hashicorp/setup-terraform@v1
              with:
                terraform_version: 1.0.1
                terraform_wrapper: false

            - name: Terraform initialization
              id: init
              run: terraform init

            - name: Terraform planning
              id: plan
              run: terraform plan

            - name: Terraform apply infrastructure
              run: terraform apply -auto-approve
```

---

## Steps to Execute

### 1\. Clone the Repository

Clone this repository to your local machine:

```bash
bashCopy codegit clone https://github.com/yourusername/your-repo-name.git
cd your-repo-name
```

### 2\. Set Up GitHub Secrets

Add the following secrets in your GitHub repository under **Settings > Secrets and variables > Actions**:

* `AWS_ACCESS_KEY_ID`
* `AWS_SECRET_ACCESS_KEY`
* `AWS_SESSION_TOKEN`

### 3\. Push Changes

Ensure your `main.tf` is complete, and push changes to the `main` branch:

```bash
bashCopy codegit add .
git commit -m "Added Terraform infrastructure and GitHub Actions workflow"
git push origin main
```

### 4\. Monitor GitHub Actions

Go to the **Actions** tab in your GitHub repository to monitor the workflow execution.

---

## Terraform Configuration Details

### AWS Resources Provisioned

1. **VPC**: Custom Virtual Private Cloud.
2. **Public Subnet**: A subnet with public IP mapping enabled.
3. **Internet Gateway**: Allows internet connectivity.
4. **Route Table**: Configured to direct internet traffic.
5. **Security Group**: Permits SSH (port 22) and HTTP (port 80) access.
6. **EC2 Instance**:
   * AMI: `ami-04dd23e62ed049936`
   * Instance Type: `t2.micro`

### Backend Configuration

The Terraform backend is configured to use an S3 bucket:

```hcl
hclCopy codeterraform {
  backend "s3" {
    bucket = "buvana-bucket"
    key    = "terraform.tfstate"
    region = "us-west-2"
  }
}
```

---

## Notes

* **Destroy Infrastructure**: Uncomment the `terraform destroy` step in `terraform.yml` to automatically clean up resources.
* **Accessing the EC2 Instance**: Use the public IP assigned to the instance. Ensure you have an SSH keypair configured for access.
* **IAM Permissions**: Ensure your AWS credentials have sufficient permissions to manage VPCs, subnets, route tables, EC2, and S3 resources.

---

## Troubleshooting

* Check the **Actions** tab for detailed logs if the workflow fails.
* Ensure the Terraform state file in S3 is accessible and correctly configured.

---


