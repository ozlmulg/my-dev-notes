# List GitHub Actions Jobs and Steps

This script scans a given GitHub Actions workflow file or an entire folder containing multiple workflow files, and lists
all jobs and their steps.

---

## Requirements

Install dependencies before running the script:

```bash
   python3 -m venv .venv
   source .venv/bin/activate
   pip install pyyaml
```

---

## 1. Single File Usage

If you want to list jobs and steps from a single GitHub Actions workflow file:

### **Script**

<details>
<summary>list_github_actions.py</summary>

```python
import argparse
import yaml


def main(filepath):
    with open(filepath, 'r') as file:
        workflow = yaml.safe_load(file)

    # List jobs and steps
    for job_name, job in workflow['jobs'].items():
        print(f"Job: {job_name}")
        for step in job['steps']:
            print(f"  Step: {step['name']}")


if __name__ == "__main__":
    parser = argparse.ArgumentParser(description='List GitHub Actions jobs and steps')
    parser.add_argument('filepath', type=str, help='Path to the GitHub Actions workflow file')
    args = parser.parse_args()
    main(args.filepath)
```

</details>

### **Run**

```bash
python3 list_github_actions.py .github/workflows/test-workflow.yaml
```

---

## 2. Folder Usage

If you want to scan all workflow files inside a folder:

### **Script**

<details>
<summary>list_github_actions.py</summary>

```python
import argparse
import os
import yaml


def list_jobs_and_steps(filepath):
    with open(filepath, 'r') as file:
        workflow = yaml.safe_load(file)

    # List jobs and steps
    for job_name, job in workflow['jobs'].items():
        print(f"Job: {job_name}")
        for step in job['steps']:
            print(f"  Step: {step['name']}")


def main(folder_path):
    for filename in os.listdir(folder_path):
        if filename.endswith('.yml') or filename.endswith('.yaml'):
            filepath = os.path.join(folder_path, filename)
            print(f"\nProcessing file: {filepath}")
            list_jobs_and_steps(filepath)


if __name__ == "__main__":
    parser = argparse.ArgumentParser(description='List GitHub Actions jobs and steps from a folder')
    parser.add_argument('folderpath', type=str, help='Path to the folder containing GitHub Actions workflow files')
    args = parser.parse_args()
    main(args.folderpath)
```

</details>

### **Run**

```bash
python3 list_github_actions.py .github/workflows/
```

### **Example Output**

```text
Processing file: .github/workflows/test-workflow.yaml
Job: build
  Step: Checkout code
  Step: Set up Python
  Step: Install dependencies
  Step: Run tests
```

---

## Notes

- Works with both .yml and .yaml files.
- Ignores files without jobs in the workflow.
- Helpful for quickly auditing workflow configurations.
