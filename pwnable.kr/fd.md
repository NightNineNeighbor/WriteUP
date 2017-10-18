#FD

<pre><code>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
char buf[32];
int main(int argc, char* argv[], char* envp[]){
	if(argc<2){
		printf("pass argv[1] a number\n");
		return 0;
	}
	int fd = atoi( argv[1] ) - 0x1234;
	int len = 0;
	len = read(fd, buf, 32);
	if(!strcmp("LETMEWIN\n", buf)){
		printf("good job :)\n");
		system("/bin/cat flag");
		exit(0);
	}
	printf("learn about Linux file IO\n");
	return 0;

}

</code></pre>

`int fd = atoi( argv[1] ) - 0x1234;`
focus on this code


> Stdin : fd = 0
> Stdout : fd = 1
> Stderr : fd = 2

i can set fd = 0 by setting `argv[1]` = 0x1234(4660d)

after that i can enter 'LETMEIN' to buf






