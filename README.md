# 🛠️ Ansible AWS Lambda Info Fetch (with AWS SSO)

This project demonstrates how to use **Ansible** with AWS credentials (including AWS SSO) to fetch and list AWS Lambda functions.

---

## 📁 Files

.
├── ansible.cfg
├── lambda_list.yml
├── inventory.ini (optional)
└── venv/

---

## ✅ Prerequisites

- Python 3.8+
- AWS CLI v2 with SSO configured
- Active AWS SSO session
- Ansible (installed in a virtualenv)
- `boto3`, `botocore`, `amazon.aws` collection

---

## 🧱 Setup

### 1. Create and activate a virtualenv

    python3 -m venv venv
    source venv/bin/activate

### 2. Install required packages

    pip install ansible boto3 botocore
    ansible-galaxy collection install amazon.aws

---

## ⚙️ ansible.cfg

    [defaults]
    interpreter_python = ./venv/bin/python
    deprecation_warnings = False

---

## 🔐 AWS Authentication

### 1. Make sure SSO is configured for your AWS profile

    aws configure sso --profile 88HoursDevOps

### 2. Log in to refresh token

    aws sso login --profile 88HoursDevOps

---

## 📜 lambda_list.yml

    - name: List Lambda functions
      hosts: localhost
      connection: local
      gather_facts: false

      tasks:
        - name: Fetch Lambda list
          amazon.aws.lambda_info:
            region: ap-south-1
            profile: 88HoursDevOps
          register: lambda_info

        - name: Show function names
          debug:
            msg: "{{ item.function_name }}"
          loop: "{{ lambda_info.functions }}"

---

## 🚀 Run the Playbook

    ansible-playbook lambda_list.yml

---

## 🧠 Gotchas

- ✅ `region: ap-south-1` → don’t use invalid regions like `ap-south-2`
- ✅ `register:` must be at task level, not inside the module
- ✅ `profile:` is required when using AWS SSO (env vars won't work)
- ✅ Ensure `boto3` and `ansible` are installed **in the same venv**
- ✅ Use Ansible binary from the venv: `which ansible` should point to `./venv/bin/ansible`
- ✅ Use `interpreter_python` in `ansible.cfg` to bind it to your venv

---

## ✅ Validation

To test your profile works:

    python3 -c 'import boto3; print(boto3.Session(profile_name="88HoursDevOps").client("sts").get_caller_identity())'

---

## 🧪 Next Steps

- Filter Lambda by name or tag
- Use dynamic inventory for AWS EC2
- Extend playbook to deploy or update functions
