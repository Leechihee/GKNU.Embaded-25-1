# environ // 환셩 변수 검색하기
```c
#include <unistd.h>
#include <stdio.h>

extern char **environ;

int main()
{
	char **env;

	env = environ;
	while(*env)
	{
		printf("%s\n",*env);
		env++;
	}
	return 0;
}
```
# arg_std // 메인함수 매개변수 활용
```c
#include <stdio.h>

int main(int argc, char **argv, char **envp)
{
	printf(" 값 목록:\n");
	for(int i = 0;i<argc;i++)
	{  
		printf("argv[%d]: %s\n", i ,argv[i]);
	}
	return 0;
}
```
# evn_std // 메인함수 환경변수 활용
```c
#include <stdio.h>

int main(int argc, char **argv, char **envp)
{
	char **env;

	env = envp;
	while(*env)
	{
		printf("%s\n",*env);
		env++;
	}
	return 0;
}
```
# std_sys // 시스템 함수 사용
```c
#include <stdlib.h>

int main()
{
	system("ls -l");
	return 0;
}
```
