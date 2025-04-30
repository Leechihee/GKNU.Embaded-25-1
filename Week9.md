# uname / 시스템 정보 출력하기
```c
#include <sys/utsname.h>
#include <stdio.h>

int main()
{
	struct utsname buf;
	if(uname(&buf)==-1)
	{
		perror("uname");
		return -1;
	}
	printf("OS : %s\n",buf.sysname); // 운영체제 이름
	printf("Host : %s\n",buf.nodename); // 사용자 이름
	printf("Release : %s\n",buf.release); // 릴리즈 정보
	printf("Version : %s\n",buf.version); // 버전 정보
	printf("Machine : %s\n",buf.machine); // 하드웨어 정보
	return 0;
}
```
# Subject / 소프트웨어 다운로드
```c
#include <stdio.h>
#include <sys/utsname.h>
#include <string.h>

int main()
{
	struct utsname buf;
	if(uname(&buf) == -1)
	{
		perror("uname");
		return -1;
	}
	printf("OS : %s\n",buf.sysname);
	printf("Host : %s\n",buf.nodename);
	printf("Release : %s\n",buf.release);
	printf("Version : %s\n",buf.version);
	printf("Machine : %s\n",buf.machine);

	int isL = strcmp("Linux",buf.sysname);
	int isM = strcmp("Darwin", buf.sysname);
	int is86_64 = strcmp("x86_64",buf.machine);
	int isarm64 = strcmp("arm64",buf.machine);
	if(isL == 0)
	{
		if(is86_64==0)
			printf("Linux x86_64 버전 소프트웨어 다운로드를 시작합니다.\n");
		else if(isarm64 == 0)
			printf("Linux ARM64 버전 소프트웨어 다운로드를 시작합니다.\n");
		else
		{
			printf("지원하지 않는 하드웨어입니다.\n");
			return -1;
		}
	}
	else if(isM == 0)
	{
		if(is86_64==0)
			printf("MacOS x86_64 버전 소프트웨어 다운로드를 시작합니다.\n");
		else if(isarm64 == 0)
			printf("MacOS ARM64 버전 소프트웨어 다운로드를 시작합니다.\n");
		else
		{
			printf("지원하지 않는 하드웨어입니다.\n");
			return -1;
		}

	}
	else
	{
		printf("지원하지 않는 OS입니다");
		return -1;
	}
	return 0;
}
```
# Ram Info / 렘 정보 보기
```c
#include <sys/sysinfo.h>
#include <stdio.h>

int main()
{
	struct sysinfo info;
	sysinfo(&info);

	printf("Total Ram: %ld\n", info.totalram);
	printf("Free Ram: %ld\n", info.freeram);
	printf("Num of Processes: %d\n", info.procs);
	return 0;
} 
```
