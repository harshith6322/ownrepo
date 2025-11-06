## Question  
How do you remove the first and last line of a file using `sed`?

### 📝 Short Explanation  
This task checks your understanding of line-addressing in `sed`, which is a powerful stream editor used in shell scripting for text manipulation.

## ✅ Answer  

### 🖥️ Command:

```bash
sed '1d; $d' filename.txt
```

---

### 📘 Detailed Explanation

#### 🔍 Breakdown:
- `sed`: Stream editor used to process text line-by-line.
- `'1d'`: Deletes the **first line** (`1` = line number).
- `'$d'`: Deletes the **last line** (`$` = end of file).
- `filename.txt`: The file to be processed.

Together, the command says:
> "Delete the first line **and** the last line from `filename.txt`."

---

### ✅ Example:

If `file.txt` contains:
```
Line 1
Line 2
Line 3
Line 4
Line 5
```

Command:
```bash
sed '1d; $d' file.txt
```

Output:
```
Line 2
Line 3
Line 4
```

---

### 🧠 Bonus Tip:
If you want to **save the result to a new file**:
```bash
sed '1d; $d' file.txt > trimmed.txt
```

> Summary:  
> The `sed '1d; $d'` command is an efficient way to remove both the first and last lines of a text file using line number and end-of-file markers.

---


Here’s a **simple and short list** of the **most-used `sed` commands** you’ll need for text editing 👇

---

### 🧱 Basics

```bash
sed 'command' filename
```

Or to edit in place:

```bash
sed -i 'command' filename
```

---

### 🔍 1. **View / Print Lines**

| Task                | Command              | Example                     |
| ------------------- | -------------------- | --------------------------- |
| Print specific line | `sed -n '3p' file`   | Print 3rd line              |
| Print range         | `sed -n '2,5p' file` | Lines 2–5                   |
| Exclude line        | `sed '3d' file`      | Delete 3rd line from output |

---

### ✂️ 2. **Delete**

| Task                 | Command               | Example                         |
| -------------------- | --------------------- | ------------------------------- |
| Delete line(s)       | `sed '2d' file`       | Delete 2nd line                 |
| Delete range         | `sed '5,10d' file`    | Delete lines 5–10               |
| Delete matching text | `sed '/error/d' file` | Remove lines containing "error" |

---

### 🔁 3. **Find & Replace**

| Task                           | Command                     | Example                      |
| ------------------------------ | --------------------------- | ---------------------------- |
| Replace first match            | `sed 's/old/new/' file`     | Replace first "old" per line |
| Replace all matches            | `sed 's/old/new/g' file`    | Replace all occurrences      |
| Case-insensitive replace       | `sed 's/old/new/gi' file`   | Ignore case                  |
| Replace in file (save changes) | `sed -i 's/old/new/g' file` | Edit file directly           |

---

### ➕ 4. **Insert / Append**

| Task               | Command              | Example             |
| ------------------ | -------------------- | ------------------- |
| Insert before line | `sed '3i text' file` | Add before line 3   |
| Append after line  | `sed '3a text' file` | Add after line 3    |
| Add at end         | `sed '$a text' file` | Append to last line |

---

### ✍️ 5. **Modify Specific Line**

| Task                 | Command                          | Example                     |
| -------------------- | -------------------------------- | --------------------------- |
| Replace whole line   | `sed '4c new text' file`         | Replace 4th line completely |
| Replace pattern line | `sed '/pattern/c new text' file` | Replace line with "pattern" |

---

### 🧹 6. **Remove Blank / Extra Spaces**

| Task                   | Command                  | Example            |
| ---------------------- | ------------------------ | ------------------ |
| Delete blank lines     | `sed '/^$/d' file`       | Remove empty lines |
| Remove leading spaces  | `sed 's/^[ \t]*//' file` | Trim start         |
| Remove trailing spaces | `sed 's/[ \t]*$//' file` | Trim end           |

---

### 💡 7. **Combine with `pipe`**

```bash
cat file | sed 's/error/warning/g'
grep "nginx" log.txt | sed 's/INFO/DEBUG/'
```

---

Would you like me to make a **small cheat sheet table (for quick reference)** — like “20 most useful `sed` one-liners” (for printing, replacing, deleting, inserting)?

