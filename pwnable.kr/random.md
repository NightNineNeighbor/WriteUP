```
#include <stdio.h>

int main(){
	unsigned int random;
	random = rand();	// random value!

	unsigned int key=0;
	scanf("%d", &key);

	if( (key ^ random) == 0xdeadbeef ){
		printf("Good!\n");
		system("/bin/cat flag");
		return 0;
	}

	printf("Wrong, maybe you should try 2^32 cases.\n");
	return 0;
}

```

```
ltrace ./random
__libc_start_main(0x4005f4, 1, 0x7ffc1a608528, 0x400670 <unfinished ...>
rand(1, 0x7ffc1a608528, 0x7ffc1a608538, 0)       = 0x6b8b4567
__isoc99_scanf(0x400760, 0x7ffc1a608438, 0x7ffc1a608438, 0x7fb0d0b750a4

```
`rand()`의 특성상 결과값이 항상 같다.

따라사 ltrace를 이용하여 if문을 통과하면 된다.
