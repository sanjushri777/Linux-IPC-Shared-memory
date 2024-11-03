# Name: SANJUSHRI A
# Register No:212223040187
# Linux-IPC-Shared-memory
Ex06-Linux IPC-Shared-memory

# AIM:
To Write a C program that illustrates two processes communicating using shared memory.

# DESIGN STEPS:

### Step 1:

Navigate to any Linux environment installed on the system or installed inside a virtual environment like virtual box/vmware or online linux JSLinux (https://bellard.org/jslinux/vm.html?url=alpine-x86.cfg&mem=192) or docker.

### Step 2:

Write the C Program using Linux Process API - Shared Memory

### Step 3:

Execute the C Program for the desired output. 

# PROGRAM:

## Write a C program that illustrates two processes communicating using shared memory.
```
Writer Program (s2.c)
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/ipc.h>
#include <sys/shm.h>
#include <sys/types.h>
#include <unistd.h>

#define SHM_SIZE 1024  // Size of shared memory segment

int main() {
    int shmid;
    key_t key = 1234;  // Shared memory key
    char *shm;

    // Create the shared memory segment
    shmid = shmget(key, SHM_SIZE, IPC_CREAT | 0666);
    if (shmid < 0) {
        perror("shmget");
        exit(1);
    }

    // Attach to the shared memory segment
    shm = shmat(shmid, NULL, 0);
    if (shm == (char *)(-1)) {
        perror("shmat");
        exit(1);
    }

    printf("Shared memory id = %d\n", shmid);
    printf("Memory Attached at %p\n", shm);

    // Write to shared memory
    while (1) {
        char input[SHM_SIZE];
        printf("Enter Some Text: ");
        fgets(input, SHM_SIZE, stdin);
        
        // Remove the newline character from input
        input[strcspn(input, "\n")] = 0;
        
        if (strcmp(input, "end") == 0) {
            break;  // Exit the loop if "end" is entered
        }

        strcpy(shm, input);  // Write input to shared memory
        printf("waiting for client.\n");
        sleep(1);  // Simulate some waiting time
    }

    // Detach from the shared memory segment
    shmdt(shm);
    return 0;
}
```
Reader Program (s1.c)
```
#include <stdio.h>
#include <stdlib.h>
#include <sys/ipc.h>
#include <sys/shm.h>
#include <sys/types.h>
#include <unistd.h>

#define SHM_SIZE 1024  // Size of shared memory segment

int main() {
    int shmid;
    key_t key = 1234;  // Shared memory key
    char *shm;

    // Access the shared memory segment
    shmid = shmget(key, SHM_SIZE, 0666);
    if (shmid < 0) {
        perror("shmget");
        exit(1);
    }

    // Attach to the shared memory segment
    shm = shmat(shmid, NULL, 0);
    if (shm == (char *)(-1)) {
        perror("shmat");
        exit(1);
    }

    printf("Shared memory id is %d\n", shmid);
    printf("Memory Attached at %p\n", shm);

    // Read from shared memory
    while (1) {
        if (*shm != '\0') {  // If there is something written
            printf("You Wrote: %s\n", shm);
            *shm = '\0';  // Clear the memory after reading
        }
        sleep(1);  // Wait before checking again
    }

    // Detach from the shared memory segment
    shmdt(shm);
    return 0;
}
```




## OUTPUT
![image](https://github.com/user-attachments/assets/ae7c5a7b-d55d-4c95-b807-ad726194834e)

$ipcs
![image](https://github.com/user-attachments/assets/5c245800-c910-4a66-94c3-2c920e4eb7ef)


# RESULT:
The program is executed successfully.
