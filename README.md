# ELK
Adanced ELK Queries -  Install, configure, and integrate the ELK stack with Wazuh alerts and advanced KQL queries for comprehensive log analysis.

By Ramyar Daneshgar 

Efficient log analysis is critical for incident detection and response within a Security Operations Center (SOC). Leveraging advanced querying techniques in Kibana, a key component of the Elastic Stack (ELK), enables driving intelligence from vast datasets. This write-up outlines the steps I take to construct advanced Kibana queries to enhance log analysis, threat hunting, and forensic investigations.

---

### **1. Escaping Reserved Characters**

Special characters, such as `&`, `=`, and `!`, are reserved in ELK query syntax and must be escaped to avoid errors or unintended query behavior. Prepending reserved characters with a backslash (`\`) ensures queries execute as intended. For instance, the query to search for the string `password:Me&Try=Hack!` would look like this:
```bash
password:Me\&Try\=Hack!
```
Understanding reserved character handling ensures reliable queries, avoiding missed logs due to malformed syntax.

---

### **2. Using Nested Queries for Structured Data**

Modern log formats, such as JSON, often store data in nested structures, requiring targeted queries for effective analysis. For example:
- Using a wildcard search to retrieve all values in a nested field:
  ```kql
  comments.author:*
  ```
- Filtering logs with combined field and value conditions:
  ```kql
  comments.author:"Alice" AND comments.text:attack
  ```

These techniques are invaluable for investigating user activity or identifying malicious inputs linked to specific entities. Nested queries enable precise analysis of structured datasets, a common requirement in threat investigations.

---

### **3. Leveraging Range Queries for Temporal and Severity Analysis**

Cybersecurity incidents often require filtering logs by numeric values (severity levels) or timestamps. Range queries streamline this process, for instance:
- Querying numeric ranges to identify incidents meeting specific thresholds:
  ```kql
  severity >= 9
  ```
- Using temporal filtering to analyze logs within defined time periods:
  ```kql
  @timestamp < "2022-12-01T00:00:00Z"
  ```

This method prioritizes high-severity alerts, such as **52 "Data Leak" incidents with severity ≥ 9**, or investigates specific timeframes, like **63 incidents before December 1, 2022, tied to specific systems**. Temporal and range-based filtering optimizes log triage, focusing analysis on high-priority events.

---

### **4. Fuzzy Searches for Handling Data Inconsistencies**

Fuzzy searching accounts for variations, such as typos or inconsistent field values. For example:
- Performing fuzzy searches with controlled fuzziness thresholds:
  ```lucene
  host_name:server~1
  ```
- Expanding fuzziness for broader matches:
  ```lucene
  host_name:server~2
  ```

This technique identified **110 incidents** where "true" was misspelled, demonstrating its utility in error-prone datasets. Fuzzy searches enhance data coverage, minimizing false negatives during threat hunting and log parsing.

---

### **5. Proximity Searches for Contextual Log Analysis**

Proximity searches detect terms appearing near each other within log messages. For example:
- Defining the maximum distance between terms:
  ```lucene
  log_message:"server error"~2
  ```

This approach pinpointed **33 incidents** involving "data leak" and "true negative" within three words of each other, aiding in forensic log correlation. Proximity searches facilitate contextual analysis, critical for understanding incident narratives and lateral movement.

---

### **6. Regular Expressions for Advanced Pattern Matching**

Regex-based queries allow for precise pattern matching, especially in unstructured text fields. For example:
- Matching patterns efficiently using regex syntax:
  ```lucene
  Event_Type:/(S|M).*/
  ```
- Combining regex with logical operators for complex filtering:
  ```lucene
  Description:/(s|m).*/ AND /user.*/
  ```

Regex queries identified **70 ransomware incidents** involving "client_list" files and flagged patterns in descriptions containing specific terms (e.g., **project**). Regular expressions provide unmatched flexibility for parsing complex logs, enabling deep threat intelligence extraction.

---

### Lessons Learned
- Proficiency in KQL and Lucene is indispensable for constructing precise, context-aware queries.
- Strategically combine AND/OR conditions to refine search results and filter complex log data.
- Save commonly used queries and leverage dashboards for faster and consistent execution.
- Continuously evaluate and fine-tune queries to adapt to evolving datasets and organizational threat landscapes.
- Validate query performance using realistic datasets to ensure relevance and accuracy during critical incidents.
