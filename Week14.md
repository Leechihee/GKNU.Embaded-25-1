# myfifo.c
```c
#include <sys/types.h>
#include <sys/stat.h>
#include <stdlib.h>
#include <stdio.h>

int main()
{
	if(mkfifo("BIT-FIFO",0644) == -1)
	{
		perror("mkfifo");
		exit(1);
	}
	return 0;
}
```
# Server.c
```c
#include <sys/types.h>
#include <fcntl.h>
#include <unistd.h>
#include <sys/stat.h>
#include <stdlib.h>
#include <stdio.h>
#include <string.h>

int main()
{
	int pd, n;
	char msg[] = "Hello, FIFO";

	printf("Server =====\n");

	if(mkfifo("./HAN-FIFO",0666) == -1)
	{
		perror("mkfifo");
		exit(1);
	}
	if((pd = open("./HAN-FIFO",O_WRONLY)) == -1)
	{
		perror("open");
		exit(1);
	}

	printf("To Client : %s\n", msg);

	n = write(pd,msg,strlen(msg)+1);
	if(n == -1)
	{
		perror("write");
		exit(1);
	}
	close(pd);
	return 0;
}
```
# Cilent.c
```c
#include <fcntl.h>
#include <unistd.h>
#include <stdlib.h>
#include <stdio.h>

int main()
{
	int pd, n;
	char inmsg[80];

	if(pd = open("./HAN-FIFO",O_RDONLY) == -1)
	{
		perror("open");
		exit(1);
	}

	printf("Client =====\n");
	write(1,"From Server :", 13);
	while((n == read(pd,inmsg,80)) > 0)
		write(1,inmsg,n);

	if(n == -1)
	{
		perror("read");
		exit(1);
	}

	write(1,"\n",1);
	close(pd);
	return 0;
}
```
# fifo_server.c
```c
#include <sys/stat.h>
#include <fcntl.h>
#include <unistd.h>
#include <stdlib.h>
#include <stdio.h>
#include <string.h>

int main() {
    int to_client_fd, to_server_fd;
    char send_buf[100], recv_buf[100];
    int n;
    
    printf("Server =====\n");
    // FIFO 파일 생성
    mkfifo("to_client_fifo", 0666);
    mkfifo("to_server_fifo", 0666);
    
    // FIFO 열기
    to_client_fd = open("to_client_fifo", O_WRONLY);
    to_server_fd = open("to_server_fifo", O_RDONLY);
    
    if (to_client_fd == -1 || to_server_fd == -1) {
        perror("open");
        exit(1);
    }
    
   while (1) {
        // 메시지 보내기
        printf(" (Server): ");
        fflush(stdout); //표준 출력 버퍼를 비움
        if (fgets(send_buf, sizeof(send_buf), stdin) == NULL)
            break;
        write(to_client_fd, send_buf, strlen(send_buf));
        
        // 메시지 받기
        n = read(to_server_fd, recv_buf, sizeof(recv_buf));
        if (n > 0) {
            recv_buf[n] = '\0';
            printf("Client: %s\n", recv_buf);
        }
    }

    close(to_client_fd);
    close(to_server_fd);
    return 0;
}
```
# fifo_client.c
```c
#include <fcntl.h>
#include <unistd.h>
#include <stdlib.h>
#include <stdio.h>
#include <string.h>

int main() {
    int to_client_fd, to_server_fd;
    char send_buf[100], recv_buf[100];
    int n;

    printf("Client =====\n");

    // FIFO 열기
    to_client_fd = open("to_client_fifo", O_RDONLY);
    to_server_fd = open("to_server_fifo", O_WRONLY);
 
    if (to_client_fd == -1 || to_server_fd == -1) {
        perror("open");
        exit(1);
    }
 
 while (1) {
        // 메시지 보내기
        printf(" (Client): ");
        fflush(stdout);
        if (fgets(send_buf, sizeof(send_buf), stdin) == NULL)
            break;
        write(to_server_fd, send_buf, strlen(send_buf));
        
        // 메시지 받기
        n = read(to_client_fd, recv_buf, sizeof(recv_buf));
        if (n > 0) {
            recv_buf[n] = '\0';
            printf("Server: %s\n", recv_buf);
        }
    }

    close(to_client_fd);
    close(to_server_fd);
    return 0;
}
```
