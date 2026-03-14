### Step 1. Create IAM user. Give ec2 full access permission and create access keys for that user.
### Step 2. Setup Vault
#### Create a password for vault
```
openssl rand -base64 2048 > vault.pass
```

#### Add your AWS credentials using the below vault command
```
ansible-vault create group_vars/all/pass.yml --vault-password-file vault.pass
```

### Step 3. Create ec2_create.yaml
### Set passwordless Authentication
### Shutdown the instances using Ansible Conditionals



