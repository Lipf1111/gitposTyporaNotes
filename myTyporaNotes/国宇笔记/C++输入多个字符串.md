```c++
#include <stdio.h>
#include <stdlib.h>
#include"string.h"
void sort(char *p[],int n){
    char *temp;
    int min;
    for(int i=0;i<n-1;i++){
        min=i;
        for(int j=i+1;j<=n-1;j++){
            if(strcmp(p[min],p[j])>0)
                min=j;
        }
        temp=p[min];
        p[min]=p[i];
        p[i]=temp;
    }
}
int main(){
    int n;
    printf("Give n:");
    scanf("%d",&n);
    char **p;
    p=(char **)malloc(sizeof(char *)*n);
    char str[10];
    for(int i=0;i<=n-1;i++){
        scanf("%s",str);
        p[i]=(char *)malloc(sizeof(char)*(strlen(str)));
        strcpy(p[i],str);
    }
    sort(p,n);
    for(int i=0;i<=n-1;i++){
        printf("%s\n",p[i]);
    }
    return 0;
}
```

**scanf**

主要有三种情况，每说一种情况，我给你举个例子吧，帮助你理解

- 遇到空格，或"回车"，或"跳格"，例子：scantf（"%d%d",&a,&b）如果这样输入：12空格23当对a输入时，录入了12，然后遇到空格，满足条件，停止对a录入，所以a的值为12
- 按指定宽度结束，例如"%3d"，只取前3列
- 遇到非法输入时。例如：scantf("%c%f",&b,&c) 若这样输入：a 123o.26 本例子中，b得到a,对c输入本该是1230.26，但是把0错打成了o(字母 欧)，遇到o属于非法输入，结束对c的录入，所以c得值为123