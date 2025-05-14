# fork_execlp.c
```c
#include <sys/types.h>
#include <unistd.h>
#include <stdlib.h>
#include <stdio.h>

int main()
{
	pid_t pid;

	switch(pid = fork())
	{
		case -1:
			perror("fork");
			exit(1);
			break;
		case 0:
			printf("--> Child process\n");
			if(execlp("ls","ls","-a",(char*)NULL) == -1)
			{
				perror("execlp");
				exit(1);
			}
			exit(0);
			break;
		default:
			printf("--> Pranet Process - My Pid:%d\n",(int)getpid());
			break;
	}
	return 0;
}
```
# wait.c
```c
#include <sys/types.h>
#include <unistd.h>
#include <stdlib.h>
#include <stdio.h>

int main()
{
	int status;
	pid_t pid;

	switch(pid = fork())
	{
		case -1:
			perror("fork");
			exit(1);
			break;
		case 0:
			printf("--> Child process\n");
			exit(2);
			break;
		default:
			while(wait(&status) != pid)
				continue;
			printf("--> Parent Process");
			printf("Status: %d, %x\n", status, status);
			printf("Child process Exit Status:%d\n", status>>8);
			break;
	}
	return 0;
}
```
# kill.c
```c
#include <sys/types.h>
#include <unistd.h>
#include <signal.h>
#include <stdio.h>

int main()
{
	printf("Before SIGCONT Signal to parent.\n");
	kill(getppid(), SIGCONT); // 일시 중지 후 다시 실행

	printf("Before SIGQUIT Signal to me.\n");
	kill(getpid(), SIGQUIT); // 프로세스 종료

	printf("After SIGQUIT Signal.\n"); // 종료시켰기 때문에 실행X
	return 0;
}
```
