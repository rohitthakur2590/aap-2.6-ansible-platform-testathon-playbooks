# aap-2.6-ansible-platform-testathon-playbooks
This repository contains playbooks for validating the **`ansible.platform`** collection on **AAP 2.6** across Gateway, Controller, EDA, and Automation Hub.  
The test set covers organizations, teams, users, role definitions, and role→team assignments, verifying propagation, idempotency, and RBAC behavior.

---
## 1) Setup & Preparation

### Step 1: Fork/Clone the Repo
- Fork or clone this repository:  
   [aap-2.6-ansible-platform-testathon-playbooks](https://github.com/rohitthakur2590/aap-2.6-ansible-platform-testathon-playbooks)

 

### Step 2: Update `vars/aap.yml`
Edit [`vars/aap.yml`](https://github.com/rohitthakur2590/aap-2.6-ansible-platform-testathon-playbooks/blob/main/vars/aap.yml) in your fork:

```yaml
aap_hostname: "http://<AAP_HOST>"
aap_username: "admin"
aap_password: "REPLACE_ME"
aap_validate_certs: false
```

### Step 3: Update `vars/user.yml`
Edit [`vars/user.yml`](https://github.com/rohitthakur2590/aap-2.6-ansible-platform-testathon-playbooks/blob/main/vars/user.yml) to set your username:

```yaml
rh_username: "<your_official_redhat_username>"
```

## 2) Register a **EE** (AAP-GUI)

- **Controller UI** → **Automation Execution** →  **Infrastructure** → **Execution Environments** → **Create execution environment**
  - **Name:** `ansible-platform-ee`
  - **Image:** `quay.io/rothakur18/platform-ee01`
  - **Pull:** `Always pull container before running`
  - **Description:** `EE for platform testathon`
  - **Organization:** `default`
  - **Save execution environment**

---

## 3) Create a **Project** (GUI)
- **Controller UI** → **Automation Execution** →  **Projects** → **Create project** 
  - **Name:** `Platform collection testing`
  - **Description:** `Contains jobs to test platform collection`
  - **Execution Enviornment:** `ansible-platform-ee`
  - **Organization:** `default`
  - **Source Control Type** `git`
  - **Source Control URL:** `https://github.com/<yourgithubusername>/aap-2.6-ansible-platform-testathon-playbooks`
  - **Source Control Credential:** `validated_nat_scm_cred`
  - **Save project**

## 4) Create a **Job Template** (Create a Role Definition) (AAP-GUI)
- **Controller UI** → **Automation Execution** → **Templates** → **Create template**
  - **Name:** `1487-1-1-TC-RDEF`
  - **Description:** `Define role`
  - **Job Type:** `Run`
  - **Inventory:** `Demo Inventory`
  - **Project:** `Platform collection testing`
  - **Execution Environment:** `ansible-platform-ee`
  - **Playbook:** `1487-1-1-TC-RDEF.yaml`
  - **Save job template**

- Similarly, you can create other job templates as per the tests mentioned in the testing documentation.



## 5) Running **Locally** (No AAP UI / No Provided EE)

If you’re not using AAP’s GUI or the pre-built EE, you can run everything from your local machine using the `devel` branch of **ansible.platform**.

### 5.1 Prerequisites
- Python 
- Git  
- Network access to your AAP 2.6 instance (the `aap_hostname` you set in `vars/aap.yml`)  
- Ansible (core)

```bash
# optional but recommended
python -m venv .venv && source .venv/bin/activate

# install ansible-core (or the meta 'ansible' pkg)
pip install --upgrade pip
pip install "ansible-core=2.18"  # or `pip install ansible`
```

### 5.2 Get `ansible.platform` (devel) and install it
```bash
# clone the collection source
git clone https://github.com/ansible/ansible.platform.git -b devel
cd ansible.platform
```
### 5.3  Get playbooks and Configure Vars (same as GUI path)
- Clone this repo for playbooks (https://github.com/rohitthakur2590/aap-2.6-ansible-platform-testathon-playbooks)
- Update `vars/aap.yml` with your **AAP URL**, **admin username**, **password**, and `aap_validate_certs` as appropriate (set `false` for self-signed).
- Update `vars/user.yml` with your **username** (`rh_username`) and a default test **password**.

### 5.4 Run a test
```bash
ansible-playbook 1487-1-1-TC-RDEF.yaml -i inventory
```
Happy testing 🚀
