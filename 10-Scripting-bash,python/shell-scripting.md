# Shell Scripting Notes (Markdown) — conditions, loops, functions, examples

Below are compact, practical **bash** scripting notes with multiple examples you can copy & run. I assume `bash` (#!/usr/bin/env bash). If you use `sh` on some systems, some bash-specific features (arrays, `[[`, `==`, etc.) may not work.

---

# Shebang & execution

```bash
#!/usr/bin/env bash
# Make executable: chmod +x script.sh
# Run: ./script.sh or bash script.sh
```

---

# Safety & boilerplate

Start scripts with safer options:

```bash
#!/usr/bin/env bash
set -euo pipefail
IFS=$'\n\t'   # safer IFS (prevents word-splitting on spaces)
```

* `-e` exit on first error
* `-u` treat unset variables as error
* `-o pipefail` fail if any command in pipeline fails

Helpful debug/tracing flags:

```bash
set -x   # print commands as executed (useful for debugging)
set +x   # turn it off
```

---

# Variables

```bash
name="Harshith"
greeting='Hello'        # single quotes: literal
count=5                 # no spaces around =
echo "$greeting, $name" # always quote expansions
```

* Access: `$var`, `${var}`, `${var:-default}`, `${var:=default}`

Examples:

```bash
# default value if empty/unset
dir="${1:-/tmp}"  # use $1 if provided, else /tmp

# assign if unset
: "${LOG_DIR:=/var/log/myapp}"  # sets LOG_DIR if not set
```

---

# Quoting & expansions

* Double quotes `"` allow expansions; single quotes `'` are literal.
* Command substitution: `$(command)` preferred over backticks.
* Brace expansion: `file{1..5}.txt` -> file1.txt ... file5.txt
* Parameter expansion: `${var#prefix}`, `${var%suffix}`, `${var,,}` (lowercase), `${#var}` length.

Example:

```bash
files=(file{1..3}.txt)
echo "${files[@]}"  # prints file1.txt file2.txt file3.txt
```

---

# Conditionals

## if / elif / else

```bash
if [[ -f "$file" && -r "$file" ]]; then
  echo "File exists and readable"
elif [[ -d "$file" ]]; then
  echo "It's a directory"
else
  echo "Nope"
fi
```

**Notes**:

* Prefer `[[ ... ]]` for bash (safer, supports `&&`, `||`, `=~` regex).
* `[` or `test` is POSIX-compatible but requires careful quoting and spacing.

### Examples

1. Check numeric comparison:

```bash
n=10
if (( n > 5 )); then
  echo "greater than 5"
fi
```

2. String test:

```bash
s="hello"
if [[ "$s" == h* ]]; then
  echo "starts with h"
fi
```

3. Using `-z` / `-n`:

```bash
if [[ -z "$1" ]]; then
  echo "need argument"
fi
```

## Case

Use `case` for multi-way string matching:

```bash
case "$1" in
  start) echo "Starting" ;;
  stop)  echo "Stopping" ;;
  *)     echo "Usage: $0 {start|stop}" ;;
esac
```

---

# Loops

## for (list)

```bash
for file in /var/log/*.log; do
  echo "Processing $file"
done
```

## classic for C-style (bash only)

```bash
for ((i=0; i<5; i++)); do
  echo "$i"
done
```

## while

```bash
count=0
while (( count < 5 )); do
  echo "count=$count"
  ((count++))
done
```

## until

Runs until condition is true:

```bash
n=5
until (( n <= 0 )); do
  echo "$n"
  ((n--))
done
```

## read loop (line-by-line)

```bash
while IFS= read -r line; do
  echo "Line: $line"
done < file.txt
```

---

# Functions

```bash
# define
myfunc() {
  local name="$1"
  echo "hello $name"
  return 0
}

# call
myfunc "Harshith"
```

* Use `local` inside functions to avoid polluting global scope.
* Return values are exit codes (0 success, non-zero failure). For richer return use stdout.

### Examples

1. Function that computes sum and echoes result:

```bash
sum() {
  local a="$1" b="$2"
  echo $(( a + b ))
}
res=$(sum 3 4)   # res="7"
```

2. Function that uses trap to cleanup:

```bash
cleanup() {
  echo "cleaning up"
  rm -f /tmp/mytempfile
}
trap cleanup EXIT
```

---

# Parameters & argument handling

* Positional params: `$1`, `$2`, ..., `$@` (all quoted), `$*` (all as single string)
* Shift:

```bash
while (( $# )); do
  case "$1" in
    -v|--verbose) verbose=1; shift ;;
    -o) file="$2"; shift 2 ;;
    --) shift; break ;;
    *) echo "Unknown $1"; exit 1 ;;
  esac
done
```

### Using `getopts` (short options)

```bash
while getopts ":ab:c" opt; do
  case $opt in
    a) echo "flag a" ;;
    b) echo "option b with $OPTARG" ;;
    c) echo "flag c" ;;
    \?) echo "Invalid option: -$OPTARG" ;;
  esac
done
```

---

# Arrays (bash)

```bash
arr=(one two three)
echo "${arr[0]}"     # one
echo "${arr[@]}"     # all elements
arr+=("four")        # append
len=${#arr[@]}       # length
```

Associative arrays (bash >= 4):

```bash
declare -A map
map[apple]="red"
map[banana]="yellow"
for k in "${!map[@]}"; do echo "$k -> ${map[$k]}"; done
```

---

# File tests (common)

* `-e file` exists
* `-f file` regular file
* `-d dir` directory
* `-r file` readable
* `-w file` writable
* `-x file` executable
* `-s file` size > 0

Example:

```bash
if [[ -f "$f" && -s "$f" ]]; then
  echo "non-empty file"
fi
```

---

# String operations

```bash
s="hello world"
echo "${s:0:5}"    # hello
echo "${s#* }"     # remove shortest prefix up to space => world
echo "${s##* }"    # remove longest prefix up to last space
echo "${s/hello/hi}" # hi world
echo "${#s}"       # length
```

---

# Arithmetic

* `(( expr ))` for integer arithmetic (no `$` needed inside).
* `$(( expr ))` for command substitution of arithmetic result.

Examples:

```bash
a=5
b=3
((c = a + b))
echo $c   # 8

x=$(( a * b + 2 ))
```

Floating point: use `bc` or `awk`:

```bash
result=$(awk "BEGIN {print 3.14 * 2.5}")
echo "$result"
```

---

# Command substitution & pipelines

```bash
files_count=$(ls -1 | wc -l)
today=$(date +%F)
```

Prefer `$(...)` over backticks. Quote expansions when used.

---

# I/O redirection & here-documents

* Redirect stdout: `> file` (overwrite), `>> file` (append)
* Redirect stderr: `2> error.log`
* Both: `&> all.log` or `> file 2>&1`
* Here-doc:

```bash
cat <<'EOF' > file.txt
This is multi-line text.
Variables are NOT expanded because of single-quoted EOF.
EOF
```

Use quoted `EOF` to avoid variable expansion.

---

# Subshells vs grouping

* Subshell `( ... )` runs in child shell (variable changes don't persist)
* Group `{ ... ; }` runs in current shell (note spaces and semicolon)

Example:

```bash
( cd /tmp; ls )   # current shell cwd unchanged
{ cd /tmp; ls; }  # cd affects current shell
```

---

# Traps & cleanup

```bash
on_exit() {
  echo "Exiting with code $?"
}
trap on_exit EXIT
trap 'echo "SIGINT caught"; exit 2' INT
```

Use traps to clean temp files and tidy up on interrupts or errors.

---

# Debugging & logging

* `set -x` to trace
* Use `echo` or a `log()` function:

```bash
log() { echo "[$(date +%T)] $*"; }
log "Starting backup"
```

---

# Examples (complete small scripts)

## 1) Backup dir to tar.gz with rotation

```bash
#!/usr/bin/env bash
set -euo pipefail

SRC="${1:-/var/www}"
DEST="${2:-/backup}"
mkdir -p "$DEST"
ts=$(date +%F_%H%M)
archive="$DEST/backup-${ts}.tar.gz"

tar -czf "$archive" -C "$(dirname "$SRC")" "$(basename "$SRC")"
# keep last 7 backups
ls -1t "$DEST"/backup-*.tar.gz | awk 'NR>7' | xargs -r rm --
```

## 2) Simple log-watcher alert (tail + grep)

```bash
#!/usr/bin/env bash
set -euo pipefail

LOG="/var/log/myapp.log"
tail -F "$LOG" | while IFS= read -r line; do
  if [[ "$line" =~ "ERROR" ]]; then
    echo "ALERT: $line"  # or call notify/alert
  fi
done
```

## 3) User creation wrapper (with argument checks)

```bash
#!/usr/bin/env bash
set -euo pipefail

if [[ $# -lt 1 ]]; then
  echo "Usage: $0 username [shell]"
  exit 1
fi

user="$1"
shell="${2:-/bin/bash}"

# check user doesn't exist
if id "$user" &>/dev/null; then
  echo "User exists"
  exit 2
fi

sudo useradd -m -s "$shell" "$user"
sudo passwd "$user"
echo "Created $user with shell $shell"
```

## 4) Parallel worker with background jobs and wait

```bash
#!/usr/bin/env bash
set -euo pipefail

process_file() {
  local f="$1"
  echo "Processing $f..."
  sleep 2
  echo "Done $f"
}

for f in "$@"; do
  process_file "$f" &   # run in background
  # limit concurrency to 4
  while (( $(jobs -rp | wc -l) >= 4 )); do
    sleep 0.2
  done
done

wait
echo "All done"
```

---

# Good practices / style

* Use `set -euo pipefail` and `IFS` like above.
* Quote variables: `"$var"`
* Use `local` in functions.
* Prefer `$(...)` for command substitution.
* Use meaningful exit codes (0 success, >0 error).
* Validate inputs early (fail fast).
* Use `mktemp` for temporary files and remove them in `trap`.
* Avoid parsing `ls` output; use shell globbing or find.
* Add comments for non-obvious logic and a usage/help block.

---

# Quick reference cheatsheet (common idioms)

* Check if file exists:

```bash
if [[ -f "$file" ]]; then echo yes; fi
```

* Check command success:

```bash
if command; then echo ok; else echo fail; fi
```

* Read lines safely:

```bash
while IFS= read -r line; do ...; done < file
```

* Replace spaces-safe array loop:

```bash
for f in "${arr[@]}"; do echo "$f"; done
```

* Test integer equality:

```bash
if (( a == b )); then ...
```

* Run command and capture output:

```bash
out=$(command) || { echo "fail"; exit 1; }
```

* Redirect stderr to file:

```bash
command 2>error.log
```

---

If you'd like, I can:

* Convert this to a `.md` file and save it for download, or
* Produce a printable one-page summary, or
* Create a collection of 10 beginner-to-intermediate exercises with solutions to practice these concepts.

Which would you like next?
