# Experiment 201: Process Creation using fork() in QNX

## Aim

Write a program to implement **process creation using fork() in QNX/Linux** and observe the behavior of **parent and child processes** when the parent terminates.

---

## Objective

* To understand **process creation using fork()**.
* To observe the **difference between parent and child processes**.
* To demonstrate how **child processes continue execution after the parent exits**.

---

## Problem Statement

Write a program that **creates multiple child processes using fork()**.
Each child process should print its **PID and parent PID**.
The parent process should **terminate after 5 seconds**, and the child processes should print their **PID and new parent PID after the parent exits**.

---

# Algorithm

1. Start the program.
2. Print the **Parent Process ID (PID)**.
3. Define the number of child processes to create.
4. Use a **for loop** to create multiple child processes.
5. Inside the loop call `fork()` to create a new process.
6. If `fork()` returns a negative value:

   * Print an error message.
   * Terminate the program.
7. If `fork()` returns **0**, it is the child process:

   * Print the child PID and parent PID.
   * Wait for **6 seconds** to allow the parent to terminate.
   * Print the child PID and new parent PID.
   * Exit the child process.
8. If `fork()` returns a positive value:

   * Continue the loop to create the next child process.
9. After creating all children, the parent process:

   * Sleeps for **5 seconds**.
10. Print a message indicating the parent is exiting.
11. Terminate the parent process.
12. End the program.

---

# Program

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/types.h>

#define NUM_CHILDREN 3

int main(void)
{
    pid_t pid;
    int i;

    printf("Parent started. PID = %d\n", getpid());

    for (i = 0; i < NUM_CHILDREN; i++) {
        pid = fork();

        if (pid < 0) {
            perror("fork failed");
            exit(EXIT_FAILURE);
        }

        if (pid == 0) {
            // ---- CHILD PROCESS ----
            printf("Child %d created. PID = %d, Parent PID = %d\n",
                   i, getpid(), getppid());

            // Delay longer than parent
            sleep(15);

            printf("Child %d running AFTER parent exit\n", i);
            printf("Child %d: My PID = %d | Parent PID = %d\n",
                   i, getpid(), getppid());

            exit(EXIT_SUCCESS);
        }

        // ---- PARENT continues to create other children ----
    }

    // ---- PARENT PROCESS ----
    printf("Parent (PID=%d) will exit in 5 seconds...\n", getpid());
    sleep(5);
    printf("Parent exiting now.\n");

    return 0;   // Parent terminates without waiting for children
}


```

---

# Expected Output

```text
Parent started. PID = 1200
Child 1 started. PID = 1201, Parent PID = 1200
Child 2 started. PID = 1202, Parent PID = 1200
Child 3 started. PID = 1203, Parent PID = 1200
Parent sleeping for 5 seconds...
Parent exiting now. PID = 1200
Child 1 running after parent exit. My PID = 1201, New Parent PID = 1
Child 2 running after parent exit. My PID = 1202, New Parent PID = 1
Child 3 running after parent exit. My PID = 1203, New Parent PID = 1
```

*(After the parent exits, the child processes are adopted by the **init process (PID 1)**.)*

---

# Ouput
<img width="1895" height="327" alt="image" src="https://github.com/user-attachments/assets/9abe6fd0-0b9d-4dce-97d6-a49f9892f839" />

---

# Result

Thus, the program successfully demonstrated **process creation using fork()** and showed that **child processes continue executing even after the parent process terminates**.
