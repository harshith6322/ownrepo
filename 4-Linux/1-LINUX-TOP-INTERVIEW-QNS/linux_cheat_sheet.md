# Linux Cheat Sheet (converted)

## Sheet1

| COMMANDS  | DESCRIPTION |
|---|---|
| SYSTEM COMMANDS |  |
| uname | used to get OS  |
| uname -r | Displays Linux kerner version |
| uname -a  | Displays all information about Linux system information |
| uptime | Displays since how system has been running |
| uptime -p | Shows uptime in pretty format |
| uptime -s | Shows uptime in pretty format |
| hostname | Displays the Hostname |
| hostname -i | Displays IP addresses for the host name |
| hostname -I | Displays IP addresses for the host name |
| last reboot | Shows system reboot history |
| ip addr | Shows addresses assigned to all network interfaces |
| ip route | Show table routes |
| ifconfig | Displays the IP address of the system |
| date | Shows system date and timestamp |
| date +”%d” | Prints day of the month (01-31) |
| date +”%m” | Prints the month of the year 01-12 |
| date +”%y” | Prints only the last two digits of Year |
| date +”%H” | Prints the hour 00-23 |
| date +”%M” | Prints the Minute of the hour 00-59 |
| date +”%S” | Prints the current seconds count in the minute (00-60) |
| date +"%D" | Prints Date in MM/DD/YY |
| date +”%F” | Prints only the Full date as YYYY-MM-DD |
| date +”%A” | Prints the Day of the Week Saturday-Sunday |
| date +”%B” | Prints the month between January-December |
| who | Prints information about default user in our server |
| whoami | Prints information about all users who are currently logged in |
| top | List out the running processors in our system |
| ps | Displays information about a selection of the active processes |
|  |  |
| HARDWARE COMMANDS |  |
| lscpu | Displays information about the CPU architecture |
| lsblk -a | Lists the information about all the block devices attached to the system |
| free | Displays system memory(RAM) details in KB |
| free -m | Displays system memory(RAM) details in MB |
| df | Report file system disk space usage |
| df -h | Report file system disk space usage in human readable languages |
| du filename | Summarize disk usage of each FILE, recursively for directories |
| du -sh filename | Summarize disk usage in human readable format |
| cat /proc/cpuinfo | Displays information about the CPU architecture |
| cat /proc/meminfo | Displays system memory(RAM) details |
| fdisk -l | List the partition tables for the specified devices |
| fdisk -s <partition>  | Displays partition size(s) in blocks (to convert block into MB : blocksize*1024/1000000) |
|  |  |
| FILE COMMANDS |  |
| touch file-name | used to crete a single file |
| touch f1 f2 f3 | used to create multiple files |
| touch file{1..5} | create 5 files at a time |
| rm file | used to remove single file |
| rm f1 f2 f3 | used to remove multiple files |
| rm file{1..5} | used to remove 5 files  |
| rm -f filename | used to remove a file without our permission |
| rm -f * | used to remove all files at a time |
| mkdir folder1 | used to create a single folder |
| mkdir f1 f2 f3 | used to create multiple folders |
| mkdir folder{1..7} | used to create 7 folders |
| touch foldername/filename | used to create a file inside the folder |
| mkdir foldername/foldername | used to create a folder inside a folder |
| mkdir -p foldername/foldername/foldername | used to create folders inside a folder  |
| cd foldername | used to change the directory |
| cd .. | used to back to one step back |
| cd - | used to go back to the previous directory |
| cd  | used to go to root directory at a time |
| cd / | To change the pwd to root directory which is the topmost/outermost parent directory   |
| pwd | present working directory |
| rmdir folder | used to remove empty directory |
| rmdir * | used to remove all empty directories |
| rm -rf * | used to remove all files and folders at a time |
| ll | used to see all the files along with the data |
| ls | used to see only file names |
| ls folder1 | used to see the list of files present in folder1 |
| ll -a | used to see hidden files |
| ll -r | used to see the files in reverse order |
| ll -t | used to see the latest files in top |
| ll -ltr | To list the files in long listing format with sort by modification time, newest first and then in reverse order |
| cat>filename | used to overwrite the data in a file |
| cat>>filename | used to append the data into a file |
| cat filename | used to read the data into a file |
| cat -n filename | used to read the data along the line numbers  |
| tac filename | Displays the file1 content in reverse ie last line first |
| rev filename | used to reverse the content in a file |
| cat f1 f2 f3 | used to see all the files data at a time |
| more f1 f2 f3 | used to see all the files data at a time with percentages |
| head filename | used to print first 10 lines of a file |
| tail filename | used to print last 10 lines of a file |
| sed -n '5,9p' filename | used to print the lines between 5 to 9 |
| sed -n '7p' filename | used to print the 7th line |
| head -n 8 filename | prints 8 lines in a file |
| tail -n 4 filename | used to print last 4 lines in a file |
| wc filename | used to get the no of lines, words, letters in a file |
| wc -l filename | used to get only line numbers of a file |
| wc -w filename | used to get no of words in a file |
| wc -c filename | used to get no of characters in a file |
| cp file1 file2 | used to copy the data from file1 file2 |
| cat file1 >> file2 | used to append the data from file1 file2 |
| cat file1 | tee file2 file3 file4 | used to copy the data from file1 to file2 file3 file4 |
| cat file1 | tee -a file2 file3 file4 | used to append the data from file1 to file2 file3 file4 |
| cp file1 folder1 | used to copy file1 to folder1 |
| mv file1 file2 | used to move the data from file1 to file2 |
| mv file1 folder1 | used to move file1 to folder1 |
|  echo folder{2..7} | xargs -n 1 cp -v folder1/* | copy files from folder1 to folder2 to folder7 at a time |
| cmp file1 file2  | used to compare the 2 files |
| diff file1 file2  | used to get the differences of a file b/w 2 files |
|  |  |
|  |  |
| SEARCH COMMANDS |  |
| find . -name file | used to find a file in current directory |
| find /proc/ -name filename | used to find a file in proc directory |
| find . -type d -name folder | used to find a folder in current directory |
| find . -type f -name <file1.txt> | used to find a file in current directory |
| find . -type f -perm 777 | Finds all the files whose permissions are 777 in the current directory   |
| find . -type f ! -perm 777 | Finds all the files whose permissions are NOT 777 in the current directory |
| find . -perm /u=r | Finds all Read-Only files in the current directory |
| find . -perm /a=x | Finds all executables files in the current directory |
| find . -perm /a=w | Finds all writable files in the current directory |
| find . -type f -empty | Find all Empty Files in the current directory |
| find . -type d -empty | Find all Empty directories in the current directory |
| find / -user <username> | Finds all the files specific user owned in / directory |
| find / -group groupname | Finds all the files specific group owned in / directory |
| find . -mtime 10 | Finds all the files which are modified 10 days back in current folder |
| find / -atime 100 | Finds all the files which are accessed 10 days back in current folder |
| find . -cmin -60  | Finds all the files which are changed in the last 1 hour in current directory |
|  find . -mmin -60  | Finds all the files which are modified in the last 1 hour in current directory |
| find . -amin -60 | Finds all the files which are accessed in the last 1 hour in current directory |
| find . -size 1k | Finds all 1KB files in current directory |
| find / -size +50M -size -100M | Finds all the files which are greater than 50MB and less than 100MB in / directory |
| locate filename  | Used to locate a word in linux (by default it will not locate, we need update db every time) |
| sudo updatedb | used to update linux db |
| locate -i filename  | used to search for a file in case sensitive |
| locate -n 5 "*.txt" | used to search top 5 text files  |
| locate -c aws* | used to count no of aws files present in server |
| grep "word" file | Used to search for a word in a file |
| grep "word" file1 file2 file3 | Used to search for a word in multiple files |
| grep -l "word" file1 file2 file3  | Prints the filename which contains the word |
| grep -n "word" file | Used to search for a word in a file with line number |
| grep -i "word" file | Searches the word in file with case-insensitive  |
| grep -c "word" file | Gives the count of words in a file |
| grep -e <pattern1> -e <pattern2> <file1> | To search multiple patterns in file1 |
|  |  |
|  |  |
| USER COMMANDS |  |
|  |  |
| useradd | To add the user |
| useradd -e 2024-01-31 username |  Set Expiration date of the user. After the date the user will be no longer available |
| useradd -U username  |  Create a group with the same name as the user and added the user into the group |
| useradd -M username | Created username without hoem directory |
| useradd -D | Prints the default home directory, default shell, default expiration date, and other settings. |
| userdel | To delete the user |
| userdel -f username | Forcefully deleted |
| userdel -r username | Deletes the user along with the directory |
| chage -l userName | Used to get user expiry details  |
| su - useradd | Login into the user |
| passwd username | Used to set a password |
| groupadd | Used to add a group |
| groups | Displays the group where current user belongs to |
| groupmod -n newgroup oldgroup | used to change the group name |
| groupdel | Used to delete the group |
| groupdel -f | Used to delete the group forcefully |
|  |  |
| PERMISSION COMMANDS |  |
|  chown username file/foldername | To change the user of a file/folder |
| chown -R username foldername  | To change the user of folder along with files |
| chown username foldername/* | To change the user of all files that are present in folder |
| chgrp groupname file/foldername  | To change the group of a file/folder |
| chgrp -R groupname foldername  | To change the group of folder along wiht files |
| chgrp username foldername/* | To change the group of all files that are present in folder |
| chown username:groupname file/foldername | To change the user and group of a file/folder |
| chown -R  username:groupname foldername | To change the user group of folder along wiht files  |
| chown username:groupname foldername/* | To change the user group of all files that are present in folder |
| chmod 777 file/foldername | To change the permissions of a file/folder |
| chmod  -R 777 foldername | To change the permissions of folder along wiht files |
| chmod 567 foldername/* | To change the permissions of all files that are present in folder |
| sudo gpasswd -d username groupname
 | To delete a user from group |



## Sheet2

| COMMANDS  | DESCRIPTION |
|---|---|
| touch foldername/filename | used to create a file inside the folder |
| mkdir foldername/foldername | used to create a folder inside a folder |
| mkdir -p foldername/foldername/foldername | used to create folders inside a folder  |
| cd foldername | used to change the directory |
| cd .. | used to back to one step back |
| cd - | used to go back to the previous directory |
| cd  | used to go to root directory at a time |
| cd / | To change the pwd to root directory which is the topmost/outermost parent directory   |
| pwd | present working directory |
| rmdir folder | used to remove empty directory |
| rmdir * | used to remove all empty directories |
| rm -rf * | used to remove all files and folders at a time |
| ll | used to see all the files along with the data |
| ls | used to see only file names |
| ls folder1 | used to see the list of files present in folder1 |
| ll -a | used to see hidden files |
| ll -r | used to see the files in reverse order |
| ll -t | used to see the latest files in top |
| ll -ltr | To list the files in long listing format with sort by modification time, newest first and then in reverse order |
| cat>filename | used to overwrite the data in a file |
| cat>>filename | used to append the data into a file |
| cat filename | used to read the data into a file |
| cat -n filename | used to read the data along the line numbers  |
| tac filename | Displays the file1 content in reverse ie last line first |
| rev filename | used to reverse the content in a file |
| head filename | used to print first 10 lines of a file |
| tail filename | used to print last 10 lines of a file |
| sed -n '5,9p' filename | used to print the lines between 5 to 9 |
| sed -n '7p' filename | used to print the 7th line |
| head -n 8 filename | prints 8 lines in a file |
| tail -n 4 filename | used to print last 4 lines in a file |
| wc filename | used to get the no of lines, words, letters in a file |
| wc -l filename | used to get only line numbers of a file |
| wc -w filename | used to get no of words in a file |
| wc -c filename | used to get no of characters in a file |
| cp file1 file2 | used to copy the data from file1 file2 |
| cat file1 >> file2 | used to append the data from file1 file2 |
| cp file1 folder1 | used to copy file1 to folder1 |
| mv file1 file2 | used to move the data from file1 to file2 |
| mv file1 folder1 | used to move file1 to folder1 |




## Additional Important Linux Commands (organized)

### File & Directory
- `ls -la` — list files (including hidden) with details.
- `cd /path/to/dir` — change directory.
- `pwd` — print working directory.
- `mkdir -p dir/subdir` — create directory (parent as needed).
- `rm -rf dir` — remove directory recursively (dangerous).
- `cp -r src dst` — copy files/directories recursively.
- `mv src dst` — move/rename files.

### File Viewing & Editing
- `cat file` — show file contents.
- `less file` — view file with paging (use `q` to quit).
- `head -n 20 file` — show first 20 lines.
- `tail -f file` — follow file (live updates).
- `nl file` — show file with line numbers.

### Permissions & Ownership
- `chmod 755 file` — change permissions (rwxr-xr-x).
- `chmod u+x file` — add execute to user.
- `chown user:group file` — change owner and group.
- `umask` — set default file creation permissions.

### Process Management
- `ps aux` — show all running processes.
- `top` / `htop` — interactive process viewer (`htop` may need install).
- `kill <PID>` — send SIGTERM to PID.
- `kill -9 <PID>` — send SIGKILL (force).
- `pkill -f name` — kill processes matching name.
- `nohup mycmd &` — run command immune to hangups.
- `nice -n 10 cmd` — set lower priority.
- `renice -n -5 -p <PID>` — change priority of running process.

### Systemctl & Services
- `systemctl status nginx` — check service status.
- `systemctl start|stop|restart|reload <service>` — manage services.
- `systemctl enable <service>` — enable at boot.
- `journalctl -u <service>` — show logs for a service.
- `journalctl -f` — follow system journal.

### Networking
- `ip a` — show interfaces and addresses.
- `ip route` — show routing table.
- `ss -tuln` — show listening sockets (tcp/udp).
- `netstat -tulpn` — similar (may need `net-tools`).
- `ping -c 4 host` — ping host 4 times.
- `traceroute host` — show route to host (may need install).
- `curl -I http://example.com` — fetch headers.
- `wget url` — download file.

### Disk & Filesystems
- `df -h` — disk usage by filesystem.
- `du -sh /path` — disk usage of a directory.
- `lsblk` — list block devices.
- `mount` / `umount` — mount/unmount filesystems.
- `fdisk -l` — partition table (requires root).
- `mkfs.ext4 /dev/sdXn` — create filesystem on partition.

### Searching & Text Processing
- `grep -R "pattern" .` — recursive search.
- `egrep` / `fgrep` — extended or fixed-string grep.
- `find /path -name '*.log' -type f` — find files by name.
- `xargs -I {} cmd {}` — build and execute command lines from input.
- `awk '{print $1}' file` — powerful field processing.
- `sed -n '1,20p' file` — stream editor (print lines 1-20).

### Archiving & Compression
- `tar -czvf archive.tar.gz dir/` — create gzipped tar.
- `tar -xzvf archive.tar.gz` — extract tar.gz.
- `zip -r archive.zip dir/` — create zip.
- `unzip archive.zip` — extract zip.

### Users & Groups
- `useradd -m username` — add user and create home.
- `passwd username` — set/change password.
- `usermod -aG sudo username` — add user to sudoers group.
- `id username` — show user id and groups.
- `groups username` — show groups.

### SSH & Remote
- `ssh user@host` — SSH into remote host.
- `ssh -i ~/.ssh/key.pem user@host` — use private key.
- `scp file user@host:/path/` — secure copy.
- `rsync -avz src/ user@host:/dest/` — fast sync (useful for backups).

### Package Management (Debian/Ubuntu)
- `apt update` — update package lists.
- `apt upgrade` — upgrade packages.
- `apt install pkg` — install package.
- `apt remove pkg` — remove.

### Logs & Troubleshooting
- `dmesg | tail` — kernel messages.
- `tail -n 200 /var/log/syslog` — system log (path varies).
- `ss -s` — socket statistics.

### Disk Quotas & LVM (short)
- `pvdisplay`, `vgdisplay`, `lvdisplay` — LVM info.
- `resize2fs /dev/mapper/vg-lv` — resize filesystem (after lvresize).

### Bash Scripting Tips
- `set -euo pipefail` — fail-fast and safer scripts.
- Use `"$@"` when forwarding all positional args.
- Quote variables: `"$var"` to prevent word-splitting.
- Use functions, and `trap 'cleanup' EXIT` to cleanup.

### Useful Shortcuts
- `Ctrl + C` — kill foreground process.
- `Ctrl + Z` — suspend foreground process.
- `fg` / `bg` — resume in foreground/background.
- `!!` — repeat last command.
- `!$` — last argument of previous command.

---

**Examples & Snippets**

- Create an archive of a directory, copy to remote, and extract:
```bash
tar -czvf site.tar.gz /var/www/site
scp site.tar.gz user@remote:/tmp/
ssh user@remote "tar -xzvf /tmp/site.tar.gz -C /var/www/"
```

- Find large files older than 30 days:
```bash
find / -type f -mtime +30 -size +100M -exec ls -lh {} \;
```

- Monitor logs with grep:
```bash
tail -f /var/log/nginx/access.log | grep --line-buffered "500"
```

