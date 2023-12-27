# AWK Command Examples: Learn to Manipulate Text Data Efficiently

**AWK is a powerful and versatile scripting language designed for text processing and data manipulation.** It excels at tasks like filtering, transforming, formatting, and analyzing text files or streams of data. Through concise commands and pattern-matching capabilities, AWK empowers you to extract meaningful insights and automate tasks with ease.

**This guide introduces you to AWK's core concepts through practical examples.** You'll learn how to:

- Filter and transform data
- Perform calculations
- Restructure content
- Generate reports
- And more!

**Let's dive in!**

## Essential AWK Concepts

**Key Building Blocks:**

- **Patterns:** Specify conditions for matching lines or fields.
- **Actions:** Instructions executed on matching lines.
- **Blocks:** Code segments enclosed in curly braces.
- **Variables:** Store and manipulate data.
- **Built-in functions:** Perform common operations (e.g., string manipulation, mathematical calculations).

## Examples for Mastering AWK

**1. Print All Lines:**

- `awk '{print}' employee.txt`

**2. Print Selected Fields:**

- `awk '{print $1, $4}' employee.txt`
- `awk -F: '{print $1, $5}' /etc/passwd`  # Use a colon as the field separator

**3. Filter Lines Based on a Pattern:**

- `awk '/manager/ {print}' employee.txt`
- `awk '$3 > 50 {print $1, $3}' data.csv`

**4. Perform Calculations:**

- `awk '{print $1, $2 * 10}' numbers.txt`
- `awk '{sum += $2; print $1, sum}' data.csv`

**5. Restructure Data:**

- `awk '{print $2, $1}' names.txt`  # Swap first and second fields
- `awk -F, '{print $1, $3}' addresses.csv`  # Print first and third fields, comma-separated

**6. Generate Reports:**

- `awk '{count[$1]++} END {for (name in count) print name, count[name]}' words.txt`  # Word frequency count
- `awk '{sales += $4} END {print "Total sales:", sales}' sales_data.csv`

**7. Combine Patterns and Actions:**

- `awk 'NR > 5 && $2 ~ /error/ {print}' logfile.txt`  # Print lines after line 5 containing "error" in the second field

**8. Use Built-in Variables and Functions:**

- `awk '{print NR, NF, $0}' data.txt`  # Print line number, number of fields, and line
- `awk '{print toupper($1), $2}' names.txt`  # Capitalize first field

**9. Create User-Defined Functions:**

- `awk 'function square(x) {return x * x} {print $1, square($2)}' numbers.txt`

**10. Get the names and labels of all pods in a namespace:**

- `kubectl get pods -n my-namespace -o awk '{print $1, $NF}'`

**11. Get the CPU requests and limits of all containers in a pod:**

- `kubectl describe pod my-pod -n my-namespace | awk '/Requests/ {print $2} /Limits/ {print $2}'`

**12. Get the logs of a specific container in a pod:**

- `kubectl logs my-pod -n my-namespace -c my-container | awk '/ERROR/ {print}'`

**13. Get the node name and IP address of all nodes in the cluster:**

- `kubectl get nodes -o json | jq -r '.items[] | .metadata.name + " " + .status.addresses[] | select(.type == "InternalIP") | .address' | awk '{print $1, $2}'`

**14. Get the pod name and status of all pods that are not running:**

- `kubectl get pods --all-namespaces --field-selector=status.phase!=Running -o json | jq -r '.items[] | .metadata.name + " " + .status.phase' | awk '{print $1, $2}'`

**15. Get the name and image of all containers in a deployment:**

- `kubectl get deployment my-deployment -n my-namespace -o json | jq -r '.spec.template.spec.containers[] | .name + " " + .image' | awk '{print $1, $2}'`