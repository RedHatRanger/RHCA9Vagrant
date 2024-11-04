
# Lesson 3: Collecting Information for Red Hat Support

## Overview

Red Hat Enterprise Linux provides powerful tools for collecting diagnostic data and interacting with Red Hat Support:

- **sos report** tool: Collects system information and diagnostic data for technical support.
- **redhat-support-tool**: Allows direct interaction with Red Hat's subscription services to access documentation, search for solutions, and manage support cases.

### 1. Using `sos report`

- The `sos report` command gathers a comprehensive set of configuration files, logs, and other system information into a tarball, which can be attached to a Red Hat support case.
- Requires **root privileges**.
- Example command to run without options:
  ```bash
  sos report
  ```
  - The tool will prompt for a **case number** and generate the tarball.
  - To view all available plug-ins and those enabled by default, run:
    ```bash
    sos report -l
    ```
  - Enable additional plug-ins using:
    ```bash
    sos report -e PLUGIN_NAME
    ```
  - Use the `sos clean` subcommand to **obfuscate sensitive data**, such as IP addresses or hostnames, that are not removed by the default postprocessing.

- You can also generate encrypted reports:
  ```bash
  sos report --encrypt-key <KEY> --encrypt-pass <PASSWORD>
  ```

- In the event `sos report` fails, refer to Red Hat Knowledgebase article **68996** for manual data collection steps.

### 2. Using `redhat-support-tool`

- The **redhat-support-tool** is a command-line utility for interacting with Red Hat's subscription services, requiring **internet access**.
- Run the tool in interactive shell mode:
  ```bash
  redhat-support-tool
  ```
  - You will be prompted for Red Hat Access credentials, which can be stored locally for future use.
  - You can use the `search` subcommand to locate solutions in the Knowledgebase:
    ```bash
    redhat-support-tool search "How to manage system entitlements with subscription-manager"
    ```

- You can also access a Knowledgebase article directly using its document ID:
  ```bash
  redhat-support-tool kb 253273 | less
  ```
  - Use `less` to paginate the output.

### 3. Managing Support Cases

- The `redhat-support-tool` can be used to **create, modify, or close support cases**.
- To open a support case:
  ```bash
  redhat-support-tool opencase --product="Red Hat Enterprise Linux" --version="9.4"
  ```
  - You will be prompted for details like **summary, description**, and **severity** level.
  
- Severity levels:
  1. **Urgent (Severity 1)**: System is not functioning; major business impact.
  2. **High (Severity 2)**: Production environment severely impacted.
  3. **Medium (Severity 3)**: Partial, non-critical impact.
  4. **Low (Severity 4)**: General questions, minor issues, or documentation requests.

- To attach a previously generated `sos report`:
  ```bash
  redhat-support-tool addattachment -c CASE_NUMBER <path_to_sosreport>
  ```

### 4. Red Hat Insights

**Red Hat Insights** is a proactive management service for Red Hat systems:

- **Insights-client** uploads system data to Red Hat, which is then analyzed to provide **tailored recommendations**.
- Registering a system with Red Hat Insights:
  1. Ensure the `insights-client` package is installed:
     ```bash
     yum install insights-client
     ```
  2. Register the system:
     ```bash
     insights-client --register
     ```

- Insights provides various services, such as:
  - **Advisor**: Identifies configuration risks.
  - **Vulnerability**: Assesses CVEs in your environment.
  - **Compliance**: Analyzes compliance with security policies.
  - **Patch**: Guides on applying patches.
  - **Drift**: Compares system configurations to baselines.

### Key Takeaways

- The **sos report** tool is crucial for gathering diagnostic data to support Red Hat Support cases.
- The **redhat-support-tool** allows you to interact directly with Red Hat's Knowledgebase, create support cases, and manage technical support queries from the terminal.
- **Red Hat Insights** provides proactive monitoring and management of registered systems, offering insights into risks, compliance, and vulnerabilities.
