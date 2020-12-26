###  char const \*,const char\*与char\* const的区别

看\*和const谁离右边的定义指针名最近

- *离得近的话表示该指针指向一个常量字符串，不能通过该指针改变字符串的内容
- const离得近的话表示这是一个常量指针，指针指向的位置一开始就确定，不能改变。 

char const \*,const char\*效果一样。

```c++
#include <stdio.h>
int main(){
	char a[6] = "abcd", b[10] = "1234";
    
	const char* ptr1;
	ptr1 = a;
	printf("11: %s\n", ptr1);
	ptr1 = b;
	printf("12: %s\n\n", ptr1);
	//ptr1[1]='O';
	//printf("13: %s\n",ptr1);
	//C3892	“ptr1”:不能给常量赋值

	char* const ptr2 = a; 
	printf("21: %s\n", ptr2);
	//ptr2 =b;
	//printf("22: %s\n", ptr2); 
	//C3892	“ptr2” : 不能给常量赋值
	ptr2[1] = 'O'; 
	printf("23: %s\n\n", ptr2);
	
	char const* ptr3; //same as case1
	ptr3 = a;
	printf("31: %s\n", ptr3);
	ptr3 = b; 
	printf("32: %s\n\n", ptr3);
	//ptr3[1]='O'; 
	//printf("33: %s\n", ptr3);
	//C3892	“ptr3” : 不能给常量赋值
	
	return 0;
}
```

```c++
11: abcd
12: 1234
    
21: abcd
23: aOcd
    
31: aOcd
32: 1234
```

