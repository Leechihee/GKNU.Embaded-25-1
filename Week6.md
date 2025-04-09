# mkdir.c / 디렉토리 생성 함수
```c
#include <sys/stat.h>
#include <stdlib.h>
#include <stdio.h>

int main()
{
	if(mkdir("han",0755)==-1)
	{
		perror("han");
		exit(1);
	}
	return 0;
}
```
# rmdir.c / 디렉토리 삭제 함수
```c
#include <unistd.h>
#include <stdlib.h>
#include <stdio.h>

int main()
{
	if(rmdir("han")==-1)
	{
		perror("han");
		exit(1);
	}
	return 0;
}
```
# cwd.c / 디렉토리 경로 확인 함수
```c
#define _GNU_SOURCE
#include <unistd.h>
#include <stdlib.h>
#include <stdio.h>

int main()
{
	char* cwd;

	cwd = get_current_dir_name();
	printf("cwd = %s\n",cwd);
	free(cwd);
	return 0;
}
```
# rename.c / 파일&디렉토리 이름 변경
```c
#include <sys/stat.h>
#include <stdlib.h>
#include <stdio.h>

int main()
{
	if(rename("han","kor")==-1)
	{
		perror("rename");
		exit(1);
	}
	return 0;
}
```
# chdir.c / 디렉토리 이동
```c
#include <unistd.h>
#include <stdlib.h>
#include <stdio.h>

int main()
{
	char* cwd;
	cwd = getcwd(NULL,BUFSIZ);
	printf("1.Current Directory : %s\n",cwd);

	chdir("bit");
	cwd = getcwd(NULL,BUFSIZ);
	printf("2.Current Directory : %s\n",cwd);
	free(cwd);
}
```
# mkdir.c를 이용하여 디렉토리 100개 만들기
```c
#include <sys/stat.h>
#include <stdlib.h>
#include <stdio.h>
#include <string.h>

int main()
{
	char num[100];
	for(int i = 0;i<100;i++)
	{
		char Filename[200] = "han";
		sprintf(num,"%d",i);
		strcat(Filename,num);
		if(mkdir(Filename,0755)==-1)
		{
			perror("han");
			exit(1);
		}
	}
}
```
