# `UDP`发送端

```c
#include <stdio.h>
#include <stdlib.h>
#include <sys/socket.h>
#include <sys/types.h>
#include <arpa/inet.h>
#include <string.h>
#include <unistd.h>

int main()
{
    //1.套接字获得通信文件描述符
    int sockfd = 0;
    if((sockfd = socket(AF_INET,SOCK_DGRAM,0)) == -1){
        perror("socket err");
        exit(1);
    }
    char buf[1024] = "";
    struct sockaddr_in client_addr;

    client_addr.sin_family = AF_INET;
    client_addr.sin_addr.s_addr = inet_addr("192.168.217.1");
    client_addr.sin_port = htons(8080);

    while (1) {
        fgets(buf,sizeof(buf),stdin);
        sendto(sockfd,buf,sizeof (buf),0,(struct sockaddr*)&client_addr,sizeof (client_addr));
    }
    close(sockfd);
    return 0;
}
```

# `UDP`接收端

```c++
#include <iostream>
#include <sys/socket.h>
#include <arpa/inet.h>
#include <sys/types.h>
#include <cstring>
#include <unistd.h>
using namespace std;

int main()
{
    int sockfd;
    if((sockfd = socket(AF_INET,SOCK_DGRAM,0))== -1){
        perror("socket err");
        exit(1);
    }

    struct sockaddr_in serv_addr;
    serv_addr.sin_family = AF_INET;
    serv_addr.sin_addr.s_addr = inet_addr("192.168.217.129");
    serv_addr.sin_port = htons(8888);
    int ret = bind(sockfd,(struct sockaddr*)&serv_addr,sizeof(serv_addr));
    if(ret == -1){
        perror("bind err");
        exit(1);
    }
    char buf[1024] = "";
    struct sockaddr_in clie_addr;
    socklen_t clie_len = sizeof(clie_addr);
    bzero(&clie_addr,clie_len);
    while (1) {
        if((recvfrom(sockfd,buf,sizeof(buf),0,(struct sockaddr*)&clie_addr,&clie_len)) == -1){
            perror("recvfrom err");
            exit(1);
        }
        cout << "server ip:" << inet_ntoa(clie_addr.sin_addr) <<
                "\tserver port:" << ntohs(clie_addr.sin_port) << endl;
        cout <<"发送的内容为："<< buf << endl;
        memset(buf,0,sizeof(buf));
    }
    close(sockfd);
    return 0;
}
```

