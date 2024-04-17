# Linux-IPC-Semaphores
Ex05-Linux IPC-Semaphores

# AIM:
To Write a C program that implements a producer-consumer system with two processes using Semaphores.

# DESIGN STEPS:

### Step 1:

Navigate to any Linux environment installed on the system or installed inside a virtual environment like virtual box/vmware or online linux JSLinux (https://bellard.org/jslinux/vm.html?url=alpine-x86.cfg&mem=192) or docker.

### Step 2:

Write the C Program using Linux Process API - Sempahores

### Step 3:

Execute the C Program for the desired output. 

# PROGRAM:

## Write a C program that implements a producer-consumer system with two processes using Semaphores.

```
#include <stdio.h>	
#include <stdlib.h>     
#include <unistd.h>	 
#include <time.h>	 
#include <sys/types.h>   
#include <sys/ipc.h>     
#include <sys/sem.h>	
#define NUM_LOOPS	20	
#if defined(__GNU_LIBRARY__) && !defined(_SEM_SEMUN_UNDEFINED)
#else
union semun
{
	int val;                   
        struct semid_ds *buf;       
        unsigned short int *array;  
        struct seminfo *__buf;      
};
#endif
int main(int argc, char* argv[])
{
	int sem_set_id;	      
	union semun sem_val;    
    	int child_pid;	    
	int i;		     
    	struct sembuf sem_op;    
    	int rc;		     
    	struct timespec delay;  
    	sem_set_id = semget(IPC_PRIVATE, 1, 0600);
    	if (sem_set_id == -1)
    	{
        	perror("main: semget");
		exit(1);
    	}
    	printf("semaphore set created, semaphore set id '%d'.\n", sem_set_id);
    	sem_val.val = 0;
    	rc = semctl(sem_set_id, 0, SETVAL, sem_val);
    	child_pid = fork();
    	switch (child_pid)
    	{
		case -1:	
        	perror("fork");
		exit(1);
        	case 0:		
		for (i=0; i<NUM_LOOPS; i++)
        	{
            		sem_op.sem_num = 0;
	    		sem_op.sem_op = -1;
	    		sem_op.sem_flg = 0;
            		semop(sem_set_id, &sem_op, 1);
	    		printf("consumer: '%d'\n", i);
	    		fflush(stdout);
		}
		break;
		default:	
		for (i=0; i<NUM_LOOPS; i++)
        	{
			printf("producer: '%d'\n", i);
	    		fflush(stdout);
	    		sem_op.sem_num = 0;
            		sem_op.sem_op = 1;
			sem_op.sem_flg = 0;
	    		semop(sem_set_id, &sem_op, 1);
			if (rand() > 3*(RAND_MAX/4))
            		{
	    	        	delay.tv_sec = 0;
	    	        	delay.tv_nsec = 10;
                		sleep(10);
            		}
		if(NUM_LOOPS>=10)
            	{
			semctl(sem_set_id, 0, IPC_RMID, sem_val) ;} // Remove the sem_set_id
		}
		break;
        }
	return 0;
}
    
```

## OUTPUT
$ ./sem.o 

```
semaphore set created, semaphore set id '0'.
producer: '0'
producer: '1'
consumer: '0'
consumer: '1'
consumer: '2'
consumer: '3'
consumer: '4'
consumer: '5'
consumer: '6'
consumer: '7'
consumer: '8'
consumer: '9'
consumer: '10'
consumer: '11'
consumer: '12'
consumer: '13'
consumer: '14'
consumer: '15'
consumer: '16'
consumer: '17'
consumer: '18'
consumer: '19'
producer: '2'
producer: '3'
producer: '4'
producer: '5'
producer: '6'
producer: '7'
producer: '8'
producer: '9'
producer: '10'
producer: '11'
producer: '12'
producer: '13'
producer: '14'
producer: '15'
producer: '16'
producer: '17'
producer: '18'
producer: '19'
```

$ ipcs

```
------ Message Queues --------
key        msqid      owner      perms      used-bytes   messages
 
------ Shared Memory Segments --------
key        shmid      owner      perms      bytes      nattch     status
 
------ Semaphore Arrays --------
key        semid      owner      perms      nsems
```

# RESULT:
The program is executed successfully.
