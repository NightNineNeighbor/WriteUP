```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/socket.h>
#include <arpa/inet.h>

int main(int argc, char* argv[], char* envp[]){
	printf("Welcome to pwnable.kr\n");
	printf("Let's see if you know how to give input to program\n");
	printf("Just give me correct inputs then you will get the flag :)\n");

	// argv
	if(argc != 100) return 0;
	if(strcmp(argv['A'],"\x00")) return 0;
	if(strcmp(argv['B'],"\x20\x0a\x0d")) return 0;
	printf("Stage 1 clear!\n");

	// stdio
	char buf[4];
	read(0, buf, 4);
	if(memcmp(buf, "\x00\x0a\x00\xff", 4)) return 0;
	read(2, buf, 4);
        if(memcmp(buf, "\x00\x0a\x02\xff", 4)) return 0;
	printf("Stage 2 clear!\n");

	// env
	if(strcmp("\xca\xfe\xba\xbe", getenv("\xde\xad\xbe\xef"))) return 0;
	printf("Stage 3 clear!\n");

	// file
	FILE* fp = fopen("\x0a", "r");
	if(!fp) return 0;
	if( fread(buf, 4, 1, fp)!=1 ) return 0;
	if( memcmp(buf, "\x00\x00\x00\x00", 4) ) return 0;
	fclose(fp);
	printf("Stage 4 clear!\n");

	// network
	int sd, cd;
	struct sockaddr_in saddr, caddr;
	sd = socket(AF_INET, SOCK_STREAM, 0);
	if(sd == -1){
		printf("socket error, tell admin\n");
		return 0;
	}
	saddr.sin_family = AF_INET;
	saddr.sin_addr.s_addr = INADDR_ANY;
	saddr.sin_port = htons( atoi(argv['C']) );
	if(bind(sd, (struct sockaddr*)&saddr, sizeof(saddr)) < 0){
		printf("bind error, use another port\n");
    		return 1;
	}
	listen(sd, 1);
	int c = sizeof(struct sockaddr_in);
	cd = accept(sd, (struct sockaddr *)&caddr, (socklen_t*)&c);
	if(cd < 0){
		printf("accept error, tell admin\n");
		return 0;
	}
	if( recv(cd, buf, 4, 0) != 4 ) return 0;
	if(memcmp(buf, "\xde\xad\xbe\xef", 4)) return 0;
	printf("Stage 5 clear!\n");

	// here's your flag
	system("/bin/cat flag");
	return 0;
}
```

```
from subprocess import *
import os, socket

payload = []
payload.append('./input')
for i in range(ord('A')-1):
    payload.append(str(i))
payload.append('')          # input \x00
payload.append('\x20\x0a\x0d')
payload.append('8888')          # port number for stage5
for i in range(100-len(payload)):
    payload.append('dummy')
# for stage1

stdin_r, stdin_w = os.pipe()
stdout_r, stdout_w = os.pipe()
stderr_r, stderr_w = os.pipe()
os.write(stdin_w, "\x00\x0a\x00\xff")
os.write(stderr_w, "\x00\x0a\x02\xff")
# for stage2

os.environ["\xde\xad\xbe\xef"] = "\xca\xfe\xba\xbe"
print os.environ["\xde\xad\xbe\xef"]
# for stage3

f = open("\x0a", "w")
f.write("\x00\x00\x00\x00")
f.close()
# for stage4

process = Popen(payload, stdout=1, stderr=stderr_r, stdin=stdin_r)
os.close(stdin_r)
os.close(stdin_w)
os.close(stderr_r)
os.close(stderr_w)

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.connect(('127.0.0.1', 8888))
s.sendall("\xde\xad\xbe\xef")
s.close()
#for stage5

process.communicate()
```
해답 파이썬 코드

## Stage1
```
// argv
if(argc != 100) return 0;
if(strcmp(argv['A'],"\x00")) return 0;
if(strcmp(argv['B'],"\x20\x0a\x0d")) return 0;
printf("Stage 1 clear!\n");
```
`argv['65']``argv['66']` 에 해당하는 값을 넣으면 된다.

`\x00` 을 입력해야 하므로 약간 다르게 입력해야 한다.

## Stage2
```
// stdio
char buf[4];
read(0, buf, 4);
if(memcmp(buf, "\x00\x0a\x00\xff", 4)) return 0;
read(2, buf, 4);
			if(memcmp(buf, "\x00\x0a\x02\xff", 4)) return 0;
printf("Stage 2 clear!\n");
```
`read(0, buf, 4);` `read(2, buf, 4);` 즉 표준 입력과 표준 출력에서 해당하는 값을 받아야 한다.

## Stage3
```
// env
if(strcmp("\xca\xfe\xba\xbe", getenv("\xde\xad\xbe\xef"))) return 0;
printf("Stage 3 clear!\n");
```
환경 변수 설정

## stage4
```
FILE* fp = fopen("\x0a", "r");
if(!fp) return 0;
if( fread(buf, 4, 1, fp)!=1 ) return 0;
if( memcmp(buf, "\x00\x00\x00\x00", 4) ) return 0;
fclose(fp);
```
해당하는 파일에 해당하는 값을 넣어야 한다.

## Stage5
```
if( recv(cd, buf, 4, 0) != 4 ) return 0;
if(memcmp(buf, "\xde\xad\xbe\xef", 4)) return 0;
```
4byte의 해당 문자를 보내면 된다.





## 파이썬 OS 모듈

`op.pipe()`
파이프를 생성한다. 함수를 실행하면 읽기, 쓰기 전용 파이프의 파일 디스크립터가 반환된다
```
>>>pipe()
(3,4)
```

`os.write(fd,str)`
파일 기술자 fd에 문자열을 작성하는데 사용한다. 실제 문자열의 길이가 반환된다.

`os.close(fd)`
해당 파일 디스크립터를 닫는다.

`os.environ`
환경변수를 나타내는 딕셔너리

`os.popen(command[,mode[,bufsize]])`

`communicate()`
자식 프로세스의 출력을 읽어오고, 자식 프로세스가 종료할 때까지 대기한다.
