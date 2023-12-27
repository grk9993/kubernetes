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

Bash
awk '{print}' employee.txt

2. Print Selected Fields:

Bash
awk '{print $1, $4}' employee.txt
awk -F: '{print $1, $5}' /etc/passwd  # Use a colon as the field separator

3. Filter Lines Based on a Pattern:

Bash
awk '/manager/ {print}' employee.txt
awk '$3 > 50 {print $1, $3}' data.csv

4. Perform Calculations:

Bash
awk '{print $1, $2 * 10}' numbers.txt
awk '{sum += $2; print $1, sum}' data.csv

5. Restructure Data:

Bash
awk '{print $2, $1}' names.txt  # Swap first and second fields
awk -F, '{print $1, $3}' addresses.csv  # Print first and third fields, comma-separated

6. Generate Reports:

Bash
awk '{count[$1]++} END {for (name in count) print name, count[name]}' words.txt  # Word frequency count
awk '{sales += $4} END {print "Total sales:", sales}' sales_data.csv

7. Combine Patterns and Actions:

Bash
awk 'NR > 5 && $2 ~ /error/ {print}' logfile.txt  # Print lines after line 5 containing "error" in the second field

8. Use Built-in Variables and Functions:

Bash
awk '{print NR, NF, $0}' data.txt  # Print line number, number of fields, and line
awk '{print toupper($1), $2}' names.txt  # Capitalize first field

9. Create User-Defined Functions:

Bash
awk 'function square(x) {return x * x} {print $1, square($2)}' numbers.txt
