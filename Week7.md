# 파일 만들기
```c
#include <sys/types.h>
#include <sys/stat.h>
#include <unistd.h>
#include <stdlib.h>
#include <stdio.h>
#include <fcntl.h>

int main()
{
	int fd; // 파일 기술자를 담기위한 정수형 변수 선언
	mode_t mode;

	mode = S_IRUSR | S_IWUSR | S_IRGRP | S_IROTH;

	fd = open("test.txt",O_CREAT, mode);
  // open()함수는 파일 기술자를 정수형으로 리턴하는 함수이며 매개변수로 읽을 파일 이름, flag, mode를 받는다.
	if(fd == -1) // open 함수가 파일을 열 수 없다면 -1을 리턴한다.
	{
		perror("Creat");
		exit(1);
	}
	printf("test.txt : %d\n",fd); // 파일 기술자를 확인하기위해 출력
	close(fd); // 파일을 닫기 위한 함수.
	return 0;
}
```
# 읽기 함수
```c
#include <fcntl.h>
#include <unistd.h>
#include <stdlib.h>
#include <stdio.h>

int main()
{
	int fd, n;
	char buf[10];
	fd = open("test.txt",O_RDONLY); // flag인 O_RDONLY는 읽기모드로 파일을 연다는 변수이다.
	if(fd == -1)
	{
		perror("Open");
		exit(1);
	}
	n = read(fd,buf,5); // 5바이트 만큼 읽고 buf에 넣는다.
	if(n==-1)
	{
		perror("Read");
		exit(1);
	}
	buf[n] = '\0'; // 문자열 취급을 하기 위해 끝자리에 널 문자 삽입
	printf("n=%d Buf : %s\n",n,buf);
	close(fd);
	return 0;
}
```
**파일 내용을 끝까지 읽고 싶으면 반복문을 사용하여 끝까지 읽게하면 된다**
# 파일 읽고 출력하기
```c
#define _GNU_SOURCE
#include <stdio.h>
#include <unistd.h>
#include <sys/stat.h>
#include <sys/types.h>
#include <fcntl.h>
#include <stdlib.h>

int main()
{
	int fd = open("mint.txt",O_CREAT | O_WRONLY,0755);
	// open(int fd, flag, mode);
	if(fd == -1)
	{
		perror("num");
		exit(1); // stdlib
	}
	char buf[11] = "123456789";
	write(fd,buf,10); // write(fd, buf, size);
	close(fd);

	fd = open("mint.txt",O_RDONLY);
	char rbuf[10];
	while(read(fd,rbuf,3)>0)
		printf("%s ", rbuf);
	printf("\n");
	close(fd);
	return 0;
}
```
