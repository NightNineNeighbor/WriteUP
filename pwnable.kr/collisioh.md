#FD

<pre><code>
#include <stdio.h>
#include <string.h>
unsigned long hashcode = 0x21DD09EC;
unsigned long check_password(const char* p){
	int* ip = (int*)p;
	int i;
	int res=0;
	for(i=0; i<5; i++){
		res += ip[i];
	}
	return res;
}

int main(int argc, char* argv[]){
	if(argc<2){
		printf("usage : %s [passcode]\n", argv[0]);
		return 0;
	}
	if(strlen(argv[1]) != 20){
		printf("passcode length should be 20 bytes\n");
		return 0;
	}

	if(hashcode == check_password( argv[1] )){
		system("/bin/cat flag");
		return 0;
	}
	else
		printf("wrong passcode.\n");
	return 0;
}

</code></pre>

`unsigned long check_password(const char* p)`
focus on that function

`check_password` get char pointer

after that it convert char pointer to int pointer

in this system char pointer is 1byte, int pointer is 4byte

as a result 20byte argument is devided by 5 

so, to pwn this code aaaa + bbbb + cccc + dddd + eeee =  0x21DD09EC

as a calculate pwn code is ./col `python -c 'print "\xC8\xCE\xC5\x06"*4+"\xcc\xce\xc5\x06"'`






