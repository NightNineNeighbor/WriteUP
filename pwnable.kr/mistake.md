```
#include<stdio.h>
#include<fcntl.h>

#define PW_LEN 10
#define XORKEY 1

void xor(char* s, int len){
	int i;
	for(i=0; i<len; i++){
		s[i] ^= XORKEY;
	}
}

int main(int argc, char* argv[]){

	int fd;
	if(fd=open("/home/mistake/password",O_RDONLY,0400) < 0){
		printf("can't open password %d\n", fd);
		return 0;
	}

	printf("do not bruteforce...\n");
	sleep(time(0)%20);

	char pw_buf[PW_LEN+1];
	int len;
	if(!(len=read(fd,pw_buf,PW_LEN) > 0)){
		printf("read error\n");
		close(fd);
		return 0;		
	}

	char pw_buf2[PW_LEN+1];
	printf("input password : ");
	scanf("%10s", pw_buf2);

	// xor your input
	xor(pw_buf2, 10);

	if(!strncmp(pw_buf, pw_buf2, PW_LEN)){
		printf("Password OK\n");
		system("/bin/cat flag\n");
	}
	else{
		printf("Wrong Password\n");
	}

	close(fd);
	return 0;
}

```

비교연산자와 산술 연산자간의 우선순위를 고려하지 않은 코드이다.

Line 17에서 `fd=open("/home/mistake/password",O_RDONLY,0400) < 0`이 있는데

이것의 정확한 코드는 `(fd=open("/home/mistake/password",O_RDONLY,0400)) < 0`

이여야 한다. 하지만 저 잘못된 코드에 의해 `fd = 0`이 되고,

Line 27 에서 `read(0,pw_buf,PW_LEN)`이므로 password파일이 아닌 표준 입력에서 값을 받게 된다.
