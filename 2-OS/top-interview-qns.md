Perfect 👌
Here are **important OS + Linux + Build-focused interview Q&A** tailored for your Build Integration role.

Focus on understanding — not memorizing.

---

# 🔥 1️⃣ What is a Process?

**Answer:**

A process is a program in execution.
It has:

- Its own memory space
- PID (Process ID)
- Stack, Heap, Code, Data segments
- Resources like file descriptors

Each process runs independently.

---

# 🔥 2️⃣ What is a Thread?

**Answer:**

A thread is a lightweight execution unit inside a process.

- Threads share same memory space
- Faster context switching
- Used for parallel tasks

Key Difference:

Process → Separate memory
Thread → Shared memory

---

# 🔥 3️⃣ What happens when you run a program in Linux?

**Answer (Very Important One):**

1. You execute the binary
2. OS creates a new process (fork/exec)
3. Loader loads the program into memory
4. Code segment, stack, heap initialized
5. Program counter starts execution from `main()`
6. When finished, process terminates and memory freed

---

# 🔥 4️⃣ What is fork() and exec()?

**Answer:**

- `fork()` creates a child process (copy of parent)
- `exec()` replaces current process image with new program

Used together to run new programs in Linux.

---

# 🔥 5️⃣ What is a Zombie Process?

**Answer:**

A process that has completed execution but still has an entry in process table because parent hasn’t read its exit status.

Fix:
Parent must call `wait()`.

---

# 🔥 6️⃣ What is an Orphan Process?

**Answer:**

If parent process dies before child, child becomes orphan.

It is adopted by `init` (PID 1).

---

# 🔥 7️⃣ What is Virtual Memory?

**Answer:**

Virtual memory allows each process to think it has its own large memory space.

OS maps virtual address → physical address using paging.

Advantage:

- Isolation
- Efficient memory usage
- Security

---

# 🔥 8️⃣ Difference Between Heap and Stack?

| Stack                  | Heap                                |
| ---------------------- | ----------------------------------- |
| Stores local variables | Stores dynamically allocated memory |
| Fast                   | Slower                              |
| Fixed size             | Grows dynamically                   |
| Automatically managed  | Manually managed (malloc/free)      |

---

# 🔥 9️⃣ What is a Segmentation Fault?

**Answer:**

Occurs when program tries to access memory it is not allowed to access.

Common reasons:

- Null pointer
- Accessing freed memory
- Array out of bounds

---

# 🔥 1️⃣0️⃣ What is Context Switching?

**Answer:**

When CPU switches from one process/thread to another.

OS saves current state and loads new process state.

---

# 🔥 1️⃣1️⃣ What is Deadlock?

**Answer:**

Deadlock occurs when processes are waiting for each other’s resources indefinitely.

4 conditions:

- Mutual exclusion
- Hold and wait
- No preemption
- Circular wait

---

# 🔥 1️⃣2️⃣ What is Inode?

**Answer:**

Inode is a data structure that stores metadata about a file:

- Owner
- Permissions
- Size
- Location in disk

File name is stored separately.

---

# 🔥 1️⃣3️⃣ Hard Link vs Soft Link?

Hard Link:

- Same inode
- Cannot cross file systems

Soft Link:

- Shortcut to another file
- Can cross file systems

---

# 🔥 1️⃣4️⃣ What is a System Call?

**Answer:**

System call is an interface between user space and kernel.

Examples:

- open()
- read()
- write()
- fork()

---

# 🔥 1️⃣5️⃣ Static vs Dynamic Linking?

Static Linking:

- Libraries copied into binary
- Larger size
- No runtime dependency

Dynamic Linking:

- Libraries loaded at runtime
- Smaller size
- Shared libraries (.so files)

Important for build roles.

---

# 🔥 1️⃣6️⃣ What happens if build fails in CI?

**Answer (Structured Way):**

1. Check logs
2. Identify compilation error / dependency error
3. Verify environment variables
4. Check recent commit
5. Reproduce locally
6. Fix and re-run pipeline

---

# 🔥 1️⃣7️⃣ What is Build Artifact?

**Answer:**

Build artifact is the output of build process.

Examples:

- .exe
- .jar
- Docker image
- Binary file

---

# 🔥 1️⃣8️⃣ What is Process Scheduling?

**Answer:**

OS decides which process runs on CPU and for how long.

Modern systems use preemptive scheduling.

---

# 🔥 1️⃣9️⃣ What is Race Condition?

**Answer:**

Occurs when multiple threads access shared resource and outcome depends on execution order.

Solution:

- Mutex
- Locks
- Synchronization

---

# 🔥 2️⃣0️⃣ Signals in Linux?

Common signals:

- SIGKILL → Force kill
- SIGTERM → Graceful termination
- SIGINT → Ctrl+C

Used to control processes.

---

# 🎯 For Your Role — Most Important Ones

⭐ Process vs Thread
⭐ fork/exec
⭐ Zombie process
⭐ Virtual memory
⭐ Static vs Dynamic linking
⭐ Build failure debugging
⭐ Git merge conflict

If you explain calmly with structure — you’re safe.

---

Now tell me:

Do you want Git Q&A next
OR
CI/CD Q&A next?
