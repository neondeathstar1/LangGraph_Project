# LangGraph_Project
# LangGraph Alert Triage Agent

## Project Overview

This project is a cybersecurity alert-triage agent built using Python and LangGraph. The goal is to demonstrate how an AI-assisted workflow can evaluate security alerts and determine whether they should be escalated for further investigation or closed as routine activity.

The agent uses keyword-based classification, asset criticality checks, and conditional routing to process alerts. It also demonstrates how LangGraph can organize a workflow into separate nodes and preserve state using memory.

## Project Objectives

- Build an alert-triage workflow using LangGraph.
- Classify cybersecurity alerts as high or low risk.
- Check the criticality of affected assets.
- Route suspicious alerts to an investigation process.
- Route lower-risk alerts to a quick-close process.
- Evaluate the agent using 12 manually written cybersecurity alerts.
- Test different keyword configurations and compare their results.
- Demonstrate LangGraph memory using a thread ID.

## How the Agent Works

The workflow contains four main nodes:

1. **Classify:** Examines the alert text and searches for configured keywords to determine its initial risk level.
2. **Check Asset:** Checks the affected hostname against a sample asset inventory to determine its criticality.
3. **Investigate:** Processes high-risk alerts and performs a simulated CVE lookup.
4. **Quick Close:** Produces a closure report for alerts that do not meet the escalation criteria.

### Workflow

The graph follows this sequence:

```text
             START
               |
               v
           CLASSIFY
               |
               v
          CHECK ASSET
               |
               v
         ROUTE BY RISK
          /         \
         /           \
        v             v
   INVESTIGATE     QUICK CLOSE
        |             |
        v             v
       END           END
```

The routing decision uses the alert's risk level and the criticality of the affected asset. Alerts with high risk or high asset criticality are sent to investigation. Other alerts are sent to quick close.

## Alert Testing

The project uses 12 custom cybersecurity alerts:

- 6 alerts labeled **escalate**.
- 6 alerts labeled **close**.

The test cases include examples of suspicious PowerShell activity, failed network connections, unusual account activity, authentication failures, data transfers, routine scans, software updates, and authorized security simulations.

Two special test cases are included:

- **Hard Escalate:** An alert describing suspicious Microsoft 365 account activity that does not contain any of the original classification keywords.
- **Hard Close:** An authorized ransomware awareness simulation that contains a keyword that could incorrectly trigger escalation.

These cases help demonstrate the limitations of relying only on keyword matching.

## Keyword Tuning and Evaluation

The original classification keywords are:

```python
KEYWORDS = ["rce", "ransomware", "exfil", "critical", "admin"]
```

Different keyword configurations are tested to measure how changes affect classification performance.

The evaluation tracks three measurements:

- **Caught:** Correctly escalated alerts out of the six alerts labeled escalate.
- **Missed:** Alerts labeled escalate that the agent incorrectly closes.
- **Over-escalated:** Alerts labeled close that the agent incorrectly escalates.

The results are recorded in `results_mine.csv` so that different configurations can be compared.

## LangGraph Memory Demonstration

The project also includes a memory demonstration using `MemorySaver()` and a thread ID.

The thread ID identifies a conversation or workflow thread, allowing LangGraph to maintain state between interactions within that thread.

The demonstration uses:

```python
MemorySaver()
```

and:

```python
thread_id = "incident-42"
```

This is an in-memory demonstration. Stored state is not permanently saved after the notebook runtime is restarted.

## Technologies Used

- **Python:** Main programming language.
- **LangGraph:** Builds and manages the alert-triage workflow.
- **Google Colab:** Development and execution environment.
- **CSV:** Stores experimental evaluation results.
- **MemorySaver:** Demonstrates state persistence within a running session.

## How to Run the Project

1. Open the project notebook in Google Colab.
2. Run the installation and import cells.
3. Execute the notebook cells in order to initialize the classification rules, asset inventory, and graph.
4. Run the 12 cybersecurity alert test cases.
5. Execute the keyword-tuning experiments.
6. Review the evaluation results and generated CSV file.
7. Run the memory demonstration.

**Important:** Run the notebook from the beginning after restarting the Colab runtime so that all variables, functions, and graph configurations are initialized correctly.

## Limitations

This project is a prototype designed to demonstrate a basic cybersecurity alert-triage workflow.

- Keyword matching can miss genuine security incidents that do not contain configured keywords.
- Routine activity can be incorrectly escalated when it contains suspicious keywords.
- The sample asset inventory does not include every hostname used in the custom test alerts.
- The CVE lookup is simulated using a small predefined dictionary and does not connect to a live vulnerability database.
- The evaluation uses only 12 alerts, which is not enough to establish production-level accuracy.
- MemorySaver stores information in memory and does not provide permanent storage.

## Conclusion

This project demonstrates how LangGraph can organize a cybersecurity alert-triage process into individual tasks and conditional decision paths. The experiments show how keyword selection affects the agent's ability to identify suspicious activity and avoid unnecessary escalations.

Although the prototype has limitations, it provides a foundation for a more advanced system incorporating threat intelligence, larger asset inventories, external security tools, and more sophisticated classification methods.
