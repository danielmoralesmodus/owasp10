# alerts-to-owasp10 GitHub Action

This GitHub Action helps you manage security risks by filtering alerts from your repositories and mapping them to the [OWASP Top 10](https://owasp.org/www-project-top-ten/).

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![GitHub Marketplace](https://img.shields.io/badge/Marketplace-alerts--to--owasp10-blue.svg?colorA=24292e&colorB=0366d6&style=flat&longCache=true&logo=github)](https://github.com/marketplace/actions/alerts-to-owasp10)

## Why Use This Action?

This tool is particularly useful for:

* **Security Officers:** To quickly assess an organization's exposure to the most critical web application security risks and initiate a response.
* **Engineering Managers:** To pinpoint weaknesses in application security, which can help in prioritizing team training and educational resources.

## How It Works

The action scans for GitHub security alerts (specifically from CodeQL code scanning) within your organization. It then filters these alerts based on their associated Common Weakness Enumerations (CWEs). The action compares the CWEs found in your open alerts against the list of CWEs associated with the OWASP Top 10 2021 revision. If there are any matches, it signifies potential exposure to these critical risks.

## Outputs

Upon completion, the action produces two artifact files:

1.  **`mapping.csv`**: A CSV file that contains a list of all security alerts that have been successfully mapped to a specific OWASP Top 10 risk.
    *Example `mapping.csv` output:*
    ```csv
    repo_name,alert_no,risk,cwe_id
    webapp-1,12,A03:2021 – Injection,cwe-089
    api-gateway,5,A01:2021 – Broken Access Control,cwe-284
    ```

2.  **`alerts.json`**: A JSON file that contains a comprehensive, unfiltered list of all `open` security alerts across the specified organization.
    *Example `alerts.json` output:*
    ```json
    {
      "webapp-1": {
        "12": [ "cwe-089", "cwe-090" ],
        "14": [ "cwe-022" ]
      },
      "api-gateway": {
        "5": [ "cwe-284", "cwe-285" ]
      }
    }
    ```

## Configuration

To integrate this action into your workflow, you must provide the following as environment variables:

| Variable | Required | Description |
| :--- | :--- | :--- |
| `ORGANISATION` | **Yes** | The name of the GitHub organization you want to scan. |
| `GITHUB_TOKEN` | **Yes** | A GitHub token with `security_events` (read-only) permissions. This is necessary to fetch security alerts. |

## Basic Usage Example

You can add the following steps to your GitHub Actions workflow file (e.g., `.github/workflows/security-scan.yml`) to use this action. This example runs the scan and then uploads the resulting `mapping.csv` as a build artifact.

```yaml
steps:
  # 1. Check out the repository's code
  - name: Checkout
    id: checkout
    uses: actions/checkout@v4

  # 2. Run the OWASP Top 10 mapping action
  - name: OWASP Top 10 Mapping
    uses: KittyChiu/alerts-to-owasp10@v0.1.2
    env:
      ORGANISATION: ${{ github.repository_owner }}
      GITHUB_TOKEN: ${{ secrets.SECURITY_ALERTS_TOKEN }}

  # 3. Upload the mapping file as an artifact
  - name: Upload Mapping Artifact
    id: upload
    uses: actions/upload-artifact@v4
    with:
      name: owasp-mapping-report
      path: mapping.csv
```

## Customization and Advanced Usage

For more advanced use cases, such as customizing the output format or including alerts with different statuses (e.g., `closed`, `dismissed`), you can fork the [original repository](https://github.com/KittyChiu/alerts-to-owasp10) and modify the action's source code to fit your specific needs.

## License

This project is distributed under the **MIT License**.
