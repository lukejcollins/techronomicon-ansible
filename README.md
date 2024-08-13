# Ansible Deployment for Techronomicon EC2 Instances

This repository contains Ansible playbooks and GitHub Actions workflow to automate the setup and deployment of services on EC2 instances for both pre-production and production environments for Techronomicon.

## Repository Structure

- **.github/workflows/ansible.yml**:
  GitHub Actions workflow file that automates the deployment process based on pull request events. It triggers the appropriate Ansible playbooks depending on whether the pull request is opened, synchronized, or merged.

- **preprod_deploy.yml**:
  Ansible playbook to configure EC2 instances for the pre-production environment. This includes setting up ECS, Nginx, CloudWatch Agent, and obtaining SSL certificates.

- **prod_deploy.yml**:
  Ansible playbook to configure EC2 instances for the production environment. Similar to the pre-production playbook but configured for the production environment.

- **files/**:
  Directory containing configuration files for Nginx and CloudWatch Agent, which are copied to the EC2 instances during the playbook execution.

## GitHub Actions Workflow

The GitHub Actions workflow is defined in `.github/workflows/ansible.yml` and has two main jobs:

- **ansible_preprod**:
  This job runs when a pull request is opened or updated (but not closed). It applies the `preprod_deploy.yml` playbook to the EC2 instances in the pre-production environment.

- **ansible_prod**:
  This job runs only when a pull request is closed and merged. It applies the `prod_deploy.yml` playbook to the EC2 instances in the production environment.

### Workflow Triggers

- The workflow is triggered by pull requests that modify `.yml` files.
- The `ansible_preprod` job runs only if the pull request is not closed.
- The `ansible_prod` job runs only if the pull request is both closed and merged.

### AWS Integration

The workflow uses AWS Systems Manager (SSM) to send commands to the EC2 instances. It retrieves instance IDs from SSM Parameter Store and assumes an IAM role using OIDC federation to access AWS resources.

## Ansible Playbooks

### preprod_deploy.yml

This playbook sets up an EC2 instance in the pre-production environment by:

1. Creating the `/etc/ecs` directory and configuring the ECS cluster.
2. Installing necessary packages like `ecs-init`, `nginx`, `amazon-cloudwatch-agent`, and `collectd`.
3. Configuring Nginx and SSL certificates for the pre-production domain.
4. Setting up and starting the CloudWatch Agent.

### prod_deploy.yml

This playbook sets up an EC2 instance in the production environment by:

1. Creating the `/etc/ecs` directory and configuring the ECS cluster.
2. Installing necessary packages like `ecs-init`, `nginx`, `amazon-cloudwatch-agent`, and `collectd`.
3. Configuring Nginx and SSL certificates for the production domain.
4. Setting up and starting the CloudWatch Agent.

## Usage

### Running the Workflow

1. **Pre-production deployment**:
   - Open or update a pull request that modifies `.yml` files.
   - The `ansible_preprod` job will run automatically, applying the `preprod_deploy.yml` playbook to the pre-production EC2 instance.

2. **Production deployment**:
   - Merge the pull request after review.
   - The `ansible_prod` job will run automatically, applying the `prod_deploy.yml` playbook to the production EC2 instance.

### Manual Execution

To run the playbooks manually, use the following commands:

```sh
ansible-playbook preprod_deploy.yml -i <inventory_file> --ask-become-pass
ansible-playbook prod_deploy.yml -i <inventory_file> --ask-become-pass
```

Replace `<inventory_file>` with your Ansible inventory file.

## Contributing

Feel free to open issues or submit pull requests if you have any suggestions for improvements or if you encounter any bugs.

## License

This project is licensed under the MIT License. See the [LICENSE](https://github.com/lukejcollins/techronomicon-ansible/blob/main/LICENSE) file for more details.
