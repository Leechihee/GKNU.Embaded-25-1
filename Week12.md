# signal.c / SIGINT 캐치(interrupt)
```c
#include <unistd.h>
#include <signal.h>
#include <stdlib.h>
#include <stdio.h>

// 시그널 핸들러 / 매개변수로 받은 시그널비트를 각자 알맞는 처리로 움직이게 하는 함수
void sig_handler(int signo)
{
	printf("\nSignal Handler signum: %d\n", signo);
	psignal(signo, "Received Signal");
}

int main()
{
	void (*hand)(int); // 함수 포인터 선언

  // signal(int signo, *(void)(int)fn) :
  // 매개변수로 어떤 시그널비트를 감지할건지와 해당 시그널비트를 받았을 때 어떤 핸들러 함수를 쓸건지 받는다.

  // SIGINT라는 시그널비트를 감지한다면 해당 시그널 핸들러 함수로 시그널비트를 전달한다.
	hand = signal(SIGINT,sig_handler);
	if(hand == SIG_ERR) // 시그널 비트가 에러라면
	{
		perror("signal"); // 에러 코드를 출력하고
		exit(1); // 프로그렘을 종료한다.
	}

	printf("Wait 1st Ctrl+C... : SIGINT\n");
	pause(); // 입력이 있을 때까지 기다린다.
	printf("After 1st Signal Handler\n");
	printf("Wait 2nd Ctrl + C... : SIGINT\n");
	pause();
	printf("After 2nd Signal Handler\n");
	return 0;
}
```
# alarm.c // SIGALRM 캐치(alarm)
```c
#include <unistd.h>
#include <signal.h>
#include <stdio.h>

// 시그널 핸들러
void sig_handler(int signo)
{
	psignal(signo, "Received Signal");
}

int main()
{
  // SIGALRM인 시그널 비트를 감지하면 시그널 핸들러 함수로 시그널 비트를 넘긴다.
	signal(SIGALRM, sig_handler);

	alarm(2); // 2초 기다린 후에 SIGALRM 비트를 반환한다.
	printf("Wait..\n");
	sleep(3); // 3초 기다린 후 진행
	return 0;
}
```
# mmap.c / 메모리 맵핑
```c
#include <sys/mman.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <unistd.h>
#include <stdlib.h>
#include <stdio.h>

int main(int argc, char *argv[])
{
	int fd;
	caddr_t addr; // char* 과 같은 기능을 하지만 메모리 주소를 다룬다 라는 의도를 명확히 하기위해 사용
	struct stat statbuf;

  // 명령줄 인자 예외처리 : 파일 이름을 입력 안했을 시
	if(argc != 2)
	{
		fprintf(stderr,"Usage : %s filename\n",argv[0]);
		exit(1);
	}

  // 스텟 예외처리 : 사용자가 파일명을 주지 않았을 시
	if(stat(argv[1],&statbuf) == -1)
	{
		perror("stat");
		exit(1);
	}

  // 파일 기술자 예외처리 : 파일을 열 수 없을 때
	if((fd = open(argv[1],O_RDWR)) == -1)
	{
		perror("open");
		exit(1);
	}

  // void *mmap(void* addr, size_t bufSize, int prot, int flag, off_t offset);
  
  // addr은 메모리 지정(NULL 입력시 커널 자동할당), bufSize는 받을 버퍼 사이즈,
  // prot(protected)은 메모리 보호 옵션(읽기,쓰기,실행,접근금지)
  // flag는 매핑 방식(PROT_SAHRED(공유), PROT_PRIVATE(쓰기 시 복사), PROT_ANONYMOUS(파일이 아닌 메모리)
  // fd는 가상메모리로 내용을 옮길 파일 기술자, off_t는 시작 오프셋 지정

  // NULL = 자동할당, statbuf에 담긴 파일 바이트 만큼 버퍼 사이즈 설정, 메모리 보호 옵션을 읽기, 쓰기모드로
  // 메모리 매핑 방식은 공유, fd는 입력 받은 파일기술자, off_t는 시작점 부터
	addr = mmap(NULL, statbuf.st_size, PROT_READ|PROT_WRITE, MAP_SHARED,fd,(off_t)0);
	if(addr == MAP_FAILED) // 매핑에 실패했을 때 예외처리
	{
		perror("addr");
		exit(1);
	}

	close(fd); 파일 기술자에 할당된 파일 닫기

	printf("%s", addr); // 가상 메모리에 담긴 내용을 출력
	return 0;
}
```
# munmap.c / 매핑 해제
```c
#include <sys/mman.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <unistd.h>
#include <stdlib.h>
#include <stdio.h>

int main(int argc, char *argv[])
{
	int fd;
	caddr_t addr;
	struct stat statbuf;

	if(argc != 2)
	{
		fprintf(stderr,"Usage : %s filename\n",argv[0]);
		exit(1);
	}

	if(stat(argv[1],&statbuf) == -1)
	{
		perror("stat");
		exit(1);
	}

	if((fd = open(argv[1],O_RDWR)) == -1)
	{
		perror("open");
		exit(1);
	}
	
	addr = mmap(NULL, statbuf.st_size, PROT_READ|PROT_WRITE, MAP_SHARED,fd,(off_t)0);
	if(addr == MAP_FAILED)
	{
		perror("addr");
		exit(1);
	}

	close(fd);

	printf("%s", addr);
  // 여기까지는 mmap.c 와 동일

  // munmap(void *addr, size_t bufSize);
  // mmap으로 연결한 가상 메모리를 반환하는 함수
  // void *addr은 가상 메모리 해제할 가상 메모리 주소
  // int bufSize는 해제할 가상 메모리 크기

  // 가상 메모리 주소인 addr를, statbuf.st_size만큼 가상 메모리 해제
	if(munmap(addr, statbuf.st_size) == -1) // 만약 메모리 해제가 제대로 실행됐지 않았을 때 예외처리
	{
		perror("munmap");
		exit(1);
	}

	// printf("%s", addr); 가상  메모리를 해제하였기때문에 세그먼트 에러 발생
	return 0;
}
```
