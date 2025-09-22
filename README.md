## Setup and local testing

### Prerequisites
- **Python**: 3.9+ (macOS ships with `python3`)
- **Git**: optional, for version control

### 1) Create and activate the virtual environment `.venv`
```bash
cd scdemo
python3 -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
pip install -r requirements.txt
```

Notes:
- After activation, your shell prompt should show `(.venv)`. Use `deactivate` to exit.

### 2) Install Ansible Collections from `collections/requirements.yml`
Option A — user-level (installs under your home directory):
```bash
ansible-galaxy collection install -r collections/requirements.yml
```

Option B — project-local (recommended for reproducibility):
```bash
ansible-galaxy collection install -r collections/requirements.yml -p ./collections
export ANSIBLE_COLLECTIONS_PATHS="$PWD/collections:${ANSIBLE_COLLECTIONS_PATHS}"
```

Verify the collection is installed:
```bash
ansible-galaxy collection list | grep ansible.eda || true
```

### 3) Test ansible-rulebook locally
First, confirm it’s installed:
```bash
ansible-rulebook --version
```

Set `ANSIBLE_COLLECTIONS_PATHS` if you used project-local installs:
```bash
export ANSIBLE_COLLECTIONS_PATHS="$PWD/collections:${ANSIBLE_COLLECTIONS_PATHS}"
```

Run the debug rulebook (uses Kafka, prints incoming events):
```bash
ansible-rulebook -r rulebooks/kakfa_debug_rulebook.yml -vv
```

Run the SELinux remediation rulebook (triggers a Controller job template):
```bash
# If using Ansible Controller/AWX, set credentials first
export CONTROLLER_HOST="https://your-controller.example.com"
export CONTROLLER_OAUTH_TOKEN="<token>"
# (legacy env names also supported by many setups: TOWER_HOST/TOWER_OAUTH_TOKEN)

ansible-rulebook -r rulebooks/kafka_selinux_rulebook.yml -vv
```

Both rulebooks expect a reachable Kafka broker and topic as configured in the files under `rulebooks/`. Adjust `host`, `port`, and `topic` values to match your environment.

### Tips
- Reactivate the venv in new shells with `source .venv/bin/activate`.
- If you see "Collection not found" errors, ensure `ANSIBLE_COLLECTIONS_PATHS` includes `./collections` or install collections at the user level (Option A above).


