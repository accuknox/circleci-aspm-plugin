### **AccuKnox CircleCI Plugin**

This CircleCI Plugin is built to run AccuKnox’s robust security scans, enabling teams to secure their applications and infrastructure effectively. It supports:

* **DAST** (Dynamic Application Security Testing)  
* **IaC** (Infrastructure as Code) Security Checks  
* **SAST** (Static Application Security Testing)  
* **Container Scans**
* **Secret Scans**

With this plugin, you can automate security testing and integrate it seamlessly into your CI/CD pipelines for improved DevSecOps practices.

### **Required Environment Variables**

The following environment variables are required for running AccuKnox scans in CircleCI pipelines:

#### **Common Environment Variables (Applicable to All Scans)**

| Variable | Description | Default Value |
| :---- | :---- | :---- |
| **ACCUKNOX_TENANT** | The ID of the tenant associated with the CSPM panel. | N/A (Required) |
| **ACCUKNOX_ENDPOINT** | The URL of the CSPM panel to push the scan results to. | N/A (Required) |
| **ACCUKNOX_LABEL** | The label created in AccuKnox SaaS for associating scan results. | N/A (Required) |
| **ACCUKNOX_TOKEN** | The token for authenticating with the CSPM panel. | N/A (Required) |

### **Container Scanning**

The container scanning section of the CI/CD pipeline is designed to integrate with AccuKnox to scan Docker images for security vulnerabilities.

Here’s the table that outlines the inputs and their descriptions, along with default values:

| Input         | Description                                                                                     | Default Value                               |
|---------------|-------------------------------------------------------------------------------------------------|---------------------------------------------|
| **IMAGE_NAME** | Name of the Docker image to be scanned.                                                        | *(Required)*                                |
| **TAG**        | Tag of the Docker image to be scanned.                                                         | *(Required)*                                |
| **SEVERITY**   | Comma-separated list of severities to include in the scan. Options: `UNKNOWN, LOW, MEDIUM, HIGH, CRITICAL`. | `UNKNOWN,LOW,MEDIUM,HIGH,CRITICAL` |
| **SOFT_FAIL**  | Do not return an error code if there are failed checks.                                        | `true`                                      |

#### **Example Pipeline Configuration**

```
version: 2.1
orbs:
  accuknox-scan: accuknox-scan/accuknox-test-scan@dev:1.125
workflows:
  accuknox:
    jobs:
      - accuknox-scan/container:
            IMAGE_NAME: circleci
            TAG: test
            SOFT_FAIL: true
```

### **Infrastructure as Code (IaC) scanning**

The Infrastructure as Code (IaC) scanning section of the CI/CD pipeline is designed to integrate with AccuKnox for scanning infrastructure code files (e.g., Terraform) for security vulnerabilities.

Here is the table that outlines the inputs and their descriptions, along with default values:

| Input | Description | Default Value |
| :---- | :---- | :---- |
| **FILE** | Specify a file for scanning (e.g., “.tf” for Terraform). Cannot be used with directory input. | `""` (empty, optional) |
| **DIRECTORY** | Directory with infrastructure code and/or package manager files to scan | `"."` (current directory) |
| **COMPACT** | Do not display code blocks in the output | `true` (boolean) |
| **QUIET** | Display only failed checks | `true` (boolean) |
| **FRAMEWORK** | Run only on a specific infrastructure (e.g., Kubernetes or Terraform) | `""` (empty, optional) |
| **SOFT_FAIL** | Do not return an error code if there are failed checks | `true` (boolean) |

#### **Example Pipeline Configuration**

```
version: 2.1
orbs:
  accuknox-scan: accuknox-scan/accuknox-test-scan@dev:1.125
workflows:
  accuknox:
    jobs:
      - accuknox-scan/iac:
          SOFT_FAIL: true
```

### **Dynamic Application Security Testing (DAST) Scanning**

The Dynamic Application Security Testing (DAST) scanning section of the  CI/CD pipeline integrates with AccuKnox for scanning live web applications for security vulnerabilities.

Here is the table that outlines the inputs and their descriptions, along with default values:

| Input | Description | Default Value |
| :---- | :---- | :---- |
| **TARGET_URL** | The URL of the web application to scan. | N/A (Required) |
| **SEVERITY_THRESHOLD** | The minimum severity level (e.g., High, Medium, Low) that will cause the pipeline to fail if present in the report. | `High` |
| **DAST_SCAN_TYPE** | Type of ZAP scan to run: ‘baseline’ or ‘full-scan’. | `baseline` |
| **SOFT_FAIL** | Do not return an error code if there are failed checks. | `true` (boolean) |

#### **Example**

```
version: 2.1
orbs:
  accuknox-scan: accuknox-scan/accuknox-test-scan@dev:1.125
workflows:
  accuknox:
    jobs:
      - accuknox-scan/dast:
          INPUT_SOFT_FAIL: false
          TARGET_URL: "http://testhtml5.vulnweb.com"
```

This example shows a  CI configuration for a DAST scan job. It defines the necessary inputs such as the stage, target URL, severity threshold, scan type, and credentials for authentication. The results are generated and uploaded to the AccuKnox platform.

### **Static Application Security Testing (SAST) Scanning**

The **Static Application Security Testing (SAST)** scanning section of the  CI/CD pipeline

| Input           | Description                                             | Default Value |
|----------------|---------------------------------------------------------|---------------|
| **SOFT_FAIL**   | Do not return an error code if there are failed checks. | `true`        |

#### **Example**

```
version: 2.1
orbs:
  accuknox-scan: accuknox-scan/accuknox-test-scan@dev:1.125
workflows:
  accuknox:
    jobs:
      - accuknox-scan/sast:
          SOFT_FAIL: false
```

Here’s the **SonarQube SAST** section in the same style as your **Static Application Security Testing (SAST)** section, tailored for the `scan sq-sast` command:

---

### **SonarQube Static Application Security Testing (SQ-SAST)**

The **SonarQube SAST** scanning command enables static code analysis using SonarQube or SonarCloud, and can optionally be skipped during pipeline execution.

| Input                 | Description                                             | Default Value |
| --------------------- | ------------------------------------------------------- | ------------- |
| **SOFT_FAIL**        | Do not return an error code if there are failed checks. | `true`        |
| **SKIP_SONAR_SCAN** | If `true`, skips the SonarQube scan entirely.           | `false`       |

#### **Required Environment Variables**

Make sure the following environment variables are set at the project level:

| Variable | Description | Default Value |
| :---- | :---- | :---- |
| **SONAR_TOKEN** | Token for authenticating with SonarQube. | N/A (Required) |
| **SONAR_HOST_URL** | The SonarQube host URL. | N/A (Required) |
| **SONAR_PROJECT_KEY** | The project key in SonarQube. | N/A (Required) |
| **SONAR_ORG_ID** | Required in case of sonarcloud enterprise. | Optional |

#### **Example**

```yaml
version: 2.1
orbs:
  accuknox-scan: accuknox-scan/accuknox-test-scan@dev:1.125

workflows:
  accuknox:
    jobs:
      - accuknox-scan/sq-sast:
          SKIP_SONAR_SCAN: true
          SOFT_FAIL: false
```

### Secret Scanning

The secret scanning section of the GitLab CI/CD pipeline is designed to integrate with AccuKnox to scan for hardcoded secrets and sensitive information in the git repositories.

Here's the table that outlines the inputs and their descriptions, along with default values:

| **Input Value**         | **Description**                                                                                     | **Default Value**                  |
| ------------------------ | --------------------------------------------------------------------------------------------------- | ---------------------------------- |
| **RESULTS**             | Specifies which type(s) of results to output: `verified`, `unknown`, `unverified`, `filtered_unverified`. Defaults to all types. | `""`                               |
| **BRANCH**              | The branch to scan. Use `all-branches` to scan all branches.                                        | `""`                               |
| **EXCLUDE_PATHS**       | Paths to exclude from the scan.                                                                     | `""`                               |
| **ADDITIONAL_ARGUMENTS**| Extra parameters for secret scanning.                                                               | `""`                               |
| **SOFT_FAIL**     | Do not return an error code if secrets are found.                                                   | `true`                             |

#### Example

```yaml
version: 2.1
orbs:
  accuknox-scan: accuknox-scan/accuknox-test-scan@dev:1.125
workflows:
  accuknox:
    jobs:
      - accuknox-scan/secret:
          SOFT_FAIL: false
```

This example defines a job for secret scanning and then uploads scan results to the AccuKnox platform