### C++面试常见算法题

#### 1.实现strcpy.

```c++
char* MyStrCpy( char *pDest, const char *pSrc ){
    if( nullptr == pDest || nullptr == pSrc ){
        return nullptr;
    }
    if( pDest == pSrc ){
        return pDest;
    }
    char *pIter = pDest;//用pTter是因为循环后指针在最后，pDest是为了做记录
    while( ( *pIter++=*pSrc++ ) !='\0' );//pSrc把最后的\0也赋值给了pIter
    return pDest;
}
```

#### 2.实现strcat(str,ptr)是将字符串ptr内容连接到字符串str后，然后得到一个组合后的字符串str

```c++
char* MyStrCat( char *pDest, const char *pSrc ){
    if(pDest==nullptr) return pSrc;//?
    if(pSrc==nullptr) return pDest;
    char *pIter = pDest + strlen( pDest );
    while( ( *pIter++=*pSrc++ ) != '\0' );
    return pDest;
}
```

#### 3.实现CString字符串类缺省四个方法

```c++
class MyCString{
public:
    MyCString( char *pData = nullptr ){//初始化
        if( nullptr == pData ){
            mpData = new char[ 1 ];
            assert( nullptr != mpData );
            *mpData = '\0';
        }else{
            mpData = new char[ strlen( pData ) + 1 ];
            assert( nullptr != mpData );
            strcpy( mpData, pData );
        }
    }
    MyCString( const MyCString &Other ){//初始化
        mpData = new char[ strlen( Other.mpData ) + 1 ];
        assert( nullptr != mpData );
        strcpy( mpData, Other.mpData );
    }
    ~MyCString(){
        if( nullptr != mpData ){
            delete [] mpData;
            mpData = nullptr;
        }
    }
    const MyCString& operator =( const MyCString &Other ){//赋值操作
        if( this == &Other ){
            return *this;
        }
        delete [] mpData;
        mpData = new char[ strlen( Other.mpData ) + 1 ];
        assert( nullptr != mpData );
        strcpy( mpData, Other.mpData );
        return *this;
    }
private:
    char *mpData;
};
```

#### 4.不使用第三个变量交换两个数的值

```c++
void SwapA( int &A, int &B ){
    if( A == B ){
        return;
    }
    A = A + B;
    B = A - B;
    A = A - B;
}
void SwapB( unsigned int &A, unsigned int &B ){
    if( A == B ){
        return;
    }
    //A^=B^=A^=B;
    A = A ^ B;
    B = A ^ B;
    A = A ^ B;
}
```

#### 5.C语言中字符串转数字的方法是什么( atoi )，请实现它

```c++
int Myatoi( const char *pStr ){
    assert( nullptr != pStr);
    const int Len = strlen( pStr);
    int Value = 0;
    int Times = 1;
    for( int i = Len -1; i >= 0; --i, Times *= 10 ){
        Value += ( pStr[ i ] - '0' ) * Times;
    }
    return Value;
}
```

#### 6.实现一个将字符串逆序的方法

```c++
char* MyInverted( char *pDest ){
    assert( nullptr != pDest );
    const int Len = strlen( pDest );
    char T = 0;
    for( int i = 0; i < Len / 2; ++i ){
        T = pDest[ i ];
        pDest[ i ] = pDest[ Len - i - 1 ];
        pDest[ Len - i -1 ] = T;
    }
    return pDest;
}
```

#### 7.实现一个将字符串中所有字母转换为大写的方法

```c++
char* MyUpper( char *pDest ){
    assert( nullptr != pDest );
    for( char *i = pDest; *i != '\0'; ++i ){
        if( *i < 'a' || *i > 'z' ){
            continue;
        }
        *i -= 'a' - 'A';
    }
    return pDest;
}
```

#### 8.已知一个数组已经降序排序请用二分查字法找到其中的某个元素找到返回索引否则返回-1

```c++
int BinarySearch( int *pArray, int Count, int Value ){
    assert( nullptr != pArray );
    int Left = 0;
    int Right = Count -1;
    int Mid = 0;
    while( Left <= Right ){
        Mid = ( Left + Right ) / 2;
        if( Value < pArray[ Mid ] ){
            Right = Mid - 1;
        } else if( Value > pArray[ Mid ] ) {
            Left = Mid + 1;
        }else{
            return Mid;
        }
    }
    return -1;
}
```

#### 9.删除链表中值为Value的所有元素( [Head]->[node1]->[node2]->...[noden] )

```c++
struct Node{
    Node *mpNext;
    int mData;
};
void DeleteFromList( Node *pHead, int Value ){
    Node *pPrev = pHead;
    Node *pNext = pHead->mpNext;
    while( nullptr != pNext ){
        if( pNext->mData != Value ){
            pPrev = pNext;
            pNext = pNext->mpNext;
        }else{
            pPrev->mpNext = pNext->mpNext;
            delete pNext;
            pNext = pPrev->mpNext;
        }
    }
}
```

```c++
struct Node{
    Node *mpNext;
    int mData;
    Node(int a):mpNext(nullptr),mData(a){}
};
//链表从开始就是节点元素
void DeleteFromList( Node *pHead, int Value ){
    Node *newNode=new Node(-1);
    newNode->mpNext=pHead;
    Node *pPrev = newNode;
    Node *pNext = newNode->mpNext;
    while( nullptr != pNext ){
        if( pNext->mData != Value ){
            pPrev = pNext;
            pNext = pNext->mpNext;
        }else{
            pPrev->mpNext = pNext->mpNext;
            delete pNext;
            pNext = pPrev->mpNext;
        }
    }
}
```

#### 10.在链表Index位置插入新的值为Value的元素

```c++
struct Node{
    Node *mpNext;
    int mData;
};
void InsertFromList( Node *pHead, int Index, int Value ){
    Node *pIter = pHead;
    for( int i = 0; i < Index && nullptr != pIter; ++i, pIter = pIter->mpNext );
    assert( nullptr != pIter );
    Node *pNew = new Node;
    pNew->mData = Value;
    pNew->mpNext = pIter->mpNext;
    pIter->mpNext = pNew;
}
```

#### 11.将链表逆序

```c++
Node* InvertedFromList( Node *pHead ){
    //A->B->C
    Node *pPrev = pHead;                //A
    Node *pNext = pHead->mpNext;        //B
    Node *pNextNext = nullptr;          //C
    while( nullptr != pNext ){
        pNextNext = pNext->mpNext;      //C = B->C
        pNext->mpNext = pPrev;          //B->A
        pPrev = pNext;                  //A = B
        pNext = pNextNext;              //B = C
    }
    pHead->mpNext = nullptr;            //C->B->A->null
    return pPrev;                       //return C( new head )
}
```

#### 12.判断X年X月X日是这年的第几天

```c++
int GetDay( int Year, int Month, int Day ){
    int MonthDays[ 13 ] = { 0, 31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31 };
    if( ( Year % 4 == 0 && Year % 100 != 0 ) || ( Year % 400 == 0 ) ){
        ++MonthDays[ 2 ];
    }
    int Days = 0;
    for( int i = 1; i < Month; ++i ){
        Days += MonthDays[ i ];
    }
    Days += Day;
    return Days;
}
```

#### 13.求斐波拉契数列第N项

```c++
int GetFibonacci1( int N ){
    if( 1 == N || 2 == N ){
        return 1;
    }
    if( 3 == N ){
        return 2;
    }
    int A = 2;
    int B = 3;
    int C = 5;
    for( int i = 0; i < N - 4; ++i ){
        C = A + B;
        A = B;
        B = C;
    }
    return C;
}
```

#### 14.递归求斐波拉契数列数列第N项

```c++
int GetFibonacci2( int N ){
    if( 1 == N || 2 == N ){
        return 1;
    }
    return GetFibonacci2( N - 1 ) + GetFibonacci2( N - 2 );
}
```

#### 15.实现一个产生[N-M]区间数字的随机方法

```c++
int GetRandomRange( int N, int M ){
    if( N == M ){
        return N;
    }
    if( N > M ){
        N = N + M;
        M = N - M;
        N = N - M;
    }
    return N + ( rand() % ( M - N + 1 ) );
}
```

#### 16.实现一个产生[0~1]之间的随机浮点数  

```c++
double GetRandomRange()  {  
	return rand() / static_cast< double >( RAND_MAX );  
}  
```

#### 17.实现一个打印出1-1000之间的所有素数的方法

```c++
void PrintfPrime(){
    //1不是素数
    //2是最小非奇数素数
    //直接从3开始
    printf( "2\n" );
    bool b = false;
    for( int i = 3; i <= 1000; ++i ){
        b = true;
        for( int j = 2; j <= i / 2; ++j ){
            if( i % j == 0 ){
                b = false;
                break;
            }
        }
        if( b ){
            printf( "%d\n", i );
        }
    }
}
```

#### 18.已知Z = X + Y 其中 Z, X, Y 均为无符号int型 定义一个宏判断Z是否已经越界

```c++
#define IS_OVER_FLOW( Z, X, Y ) ( Z < ( ( X ) < ( Y ) ? ( Y ) : ( X ) ) )  
```

#### 19.请用栈实现队列

```c++
int QueuePop( std::stack< int > &StackA ){
    std::stack< int > StackB;
    while( false == StackA.empty() ){
        StackB.push( StackA.top() );
        StackA.pop();
    }
    const int top = StackB.top();
    StackB.pop();
    while( false == StackB.empty() ){
        StackA.push( StackB.top() );
        StackB.pop();
    }
    return top;
}
```

#### 20.已知X班X成绩0-100分编写一个方法实现0-59打印不合格,60-69打印合格,70-79打印良好,80-100打印优秀

```c++
//不能使用if,:?,switch
void PrintScore( int Score ){
    assert( Score >= 0 && Score <= 100 );
    const char *pString[] ={
        "不合格",
        "不合格",
        "不合格",
        "不合格",
        "不合格",
        "不合格",
        "合格",
        "良好",
        "优秀",
        "优秀",
        "优秀",
    };
    printf( "%s\n", pString[ Score / 10 ] );
}
```

#### 21.实现strncpy

```c++
char *Mystrncpy( char *pDest, const char *pSrc, int Count ){
    assert( NULL != pDest && NULL != pSrc );
    if( pDest == pSrc ){
        return pDest;
    }
    if( Count <= 0 ){
        return pDest;
    }
    char *pStart = pDest;
    while( ( Count-- ) > 0 && ( *pStart++=*pSrc++ ) );
    *pStart = '\0';
    return pDest;
}
```

#### 22.C语言中数字转字符串的方法是什么？(itoa)请实现他

```c++
char* Myitoa( char *pDest, int val, int radix ){
    assert( NULL != pDest );
    assert( radix > 1 );
    const bool IsMinu = val < 0;
    char buffer[ 16 ] = {};
    int count = 0;
    do{
        buffer[ count++ ] = abs(val) % radix;
        val /= radix;
    }while( val );//结果为倒序
	if( IsMinu ){
        pDest[ 0 ] = '-';
        for( int i = 0; i < count; ++i ){
            pDest[ i + 1 ] = '0' + buffer[ count - i - 1 ];
        }
        pDest[ count + 1 ] = '\0';
    }else{
        for( int i = 0; i < count; ++i ){
            pDest[ i ] = '0' + buffer[ count - i - 1 ];
        }
        pDest[ count ] = '\0';
    }
    return pDest;
}
```

#### 23.如何判断链表是否有环

```c++
bool IsLoop( Node *pHead ){
    //[H->A->B->C->A]
    assert( NULL != pHead );
    Node *pNext = pHead->mpNext;
    Node *pNextNext = pHead->mpNext;
    while( NULL != pNextNext && NULL != pNextNext->mpNext ){
        pNext = pNext->mpNext;//[ B、C、A ]
        pNextNext = pNextNext->mpNext->mpNext;//[C、B、A]
        if( pNext == pNextNext ){
            return true;
        }
    }
    return false;
}
```

#### 24.统计出一个字符串每种字母出现的次数要求时间复杂度为O(n)

```c++
void CountLetter( const char *pSrc ){
    int count[ 256 ] = {};
    for( ; *pSrc !='\0'; ++pSrc ){
        const char &c = *pSrc;
        if( ( c < 'A' || c > 'z') && ( c < 'a' || c > 'z' ) ){
            continue;
        }
        ++count[ c ];
    }
}
```

#### 25.选择排序的思想是什么？( 每次找到最大或最小的值放在数组的低位上 )请实现它

```c++
void SelectSort( int *pArray, int count ){
    for( int i = 0; i < count; ++i ){ //默认低位元素最小
        int MinValue = pArray[ i ];//默认保存低位元素的索引
        int MinIndex = i;//除开第一个元素找是否还有比它还小的元素( 升序 )
        for( int j = i + 1; j < count; ++j ){//发现找到比它还小的元素重新赋值和保存索引
            if( pArray[ j ] < MinValue ){
                MinValue = pArray[ j ];
                MinIndex = j;
            }
        }//将找到最小元素放在数组低位上面
        const int Temp = pArray[ i ];
        pArray[ i ] = MinValue;
        pArray[ MinIndex ] = Temp;
    }
}
```

#### 26.冒泡排序的思想是什么?(升序排序中越小的数往低位走，越大的数往高位走,每次与相邻元素比较导致的特点)请实现它

```c++
void BubbleSort( int *pArray, int count ){
    //eg.[6][8][8][0][9][1]
    //i = 0,j < 5    [6][8][0][8][1][9]
    //i = 1,j < 4    [6][0][8][1][8][9]
    //i = 2,j < 3    [0][6][1][8][8][9]
    //i = 3,j < 2    [0][1][6][8][8][9]
    //到此为止已经排序OK了
    //i = 4,j < 1    [0][1][6][8][8][9]
    //i = 5,j < 0    [0][1][6][8][8][9]
    for( int i = 0; i < count; ++i ){
        for( int j = 0; j < count - i - 1; ++j ){
            if( pArray[ j ] > pArray[ j + 1 ] ){
                const int Temp = pArray[ j ];
                pArray[ j ] = pArray[ j + 1 ];
                pArray[ j + 1 ] = Temp;
            }
        }
    }
}
```

#### 27.已知两个数组有序,实现一个方法将他们合并后任然有序

```c++
void MergeSort( int *pMerge, int *p1, int p1len, int *p2, int p2len ){
    assert( nullptr != pMerge && nullptr != p1 && nullptr != p2 );
    int i = 0;
    int j = 0;
    int k = 0;
    while( i < p1len && j < p2len ){
        if( p1[ i ] < p2[ j ] ){
            pMerge[ k ] = p1[ i ];
            ++k;
            ++i;
        }else {
            pMerge[ k ] = p2[ j ];
            ++k;
            ++j;
        }
    }
    while( i < p1len ) {
        pMerge[ k ] = p1[ i ];
        ++k;
        ++i;
    }
    while( j < p2len ){
        pMerge[ k ] = p2[ j ];
        ++k;
        ++j;
    }
}
```

#### 28.实现一个算法找到数组中第二大的数

```c++
int FindSec( int *p, int len ){
    assert( nullptr != p );
    int maxv = p[ 0 ];
    int secv = p[ 0 ];
    for( int i = 1; i < len; ++i ){
        if( maxv < p[ i ] ){
            secv = maxv;
            maxv = p[ i ];
        }
    }
    return secv;
}
```

#### 29.排序对比

```c++
#include <iostream>
using namespace std;
void quickSort(int s[], int left, int right) {//快速排序
    if (left < right) {
        int i = left, j = right, x = s[left];
        while (i < j) {
            while (i < j && s[j] >= x) {
                j--;
            }
            if (i < j) {
                s[i++] = s[j];
            }
            while (i < j && s[i] < x) {
                i++;
            }
            if (i < j) {
                s[j--] = s[i];
            }
        }
        s[i] = x;
        quickSort(s, left, i -1);
        quickSort(s, i + 1, right);
    }
}
void bubbleSort1(int arr[], int length){ //冒泡排序,往后冒泡
    int i, j;
    for (i = 0; i < length - 1; i++) {
        for (j = 0; j < length - i - 1; j++) {
            if (arr[j] < arr[j + 1]) {
                swap(arr[j], arr[j + 1]);
            }
        }
    }
}
void bubbleSort2(int arr[], int length){ //冒泡排序，往前冒泡
    int i, j;
    for (i = 0; i < length - 1; i++) {
        for (j = length - 1; j >=i ; j--) {
            if (arr[j] < arr[j-1]) {
                swap(arr[j], arr[j-1]);
            }
        }
    }
}
void insertSort(int a[],int len){  //插入排序
    for(int i=1;i<len;i++){
        int tmp=a[i];
        int j=i-1;
        while(j>=0 && tmp<a[j]){//将a[i]放进i之前的已排好序的数列中
            a[j+1]=a[j];//将i前的数据中大于a[i]的数后移，腾出放置a[i]的位置
            j--;
        }
        a[j+1]=tmp;
    }
}
void selectSort(int a[],int len){  //选择排序
    for(int i=0;i<len-1;i++){
        int minIdx=i;
        for(int j=i+1;j<len;j++){
            if(a[j]<a[minIdx]){
                minIdx=j;
            }
        }
        if(a[i]>a[minIdx]){
            int tmp=a[i];
            a[i]=a[minIdx];
            a[minIdx]=tmp;
        }
    }
}
void swapSort(int a[],int len){  //交换排序
	//int length = sizeof(a)/sizeof(a[0]);
    for(int i=0;i<len-1;i++) {
        for(int j=i+1;j<len;j++){      
            if(a[i]<a[j]){//找到更大的元素交换      
                int temp = a[i];  
                a[i] = a[j];         
                a[j] = temp;
	 		}
     	}
 	}
}
void shellsort(int a[],int len){  //希尔排序
    //划分成gap个组
    for(int gap=len/2;gap>0;gap/=2){
        //从第gap个元素，逐个对其所在组进行插入排序操作而不需要for每个组进行操作
        for(int i=gap;i<len;i++){
            int j=i-gap;
            //插入排序采用交换法即可
            while(j>=0 && a[j]>a[j+gap]){
                swap(a[j],a[j+gap]);
                j-=gap;
            }
        }
    }
}
public class ShellSort {//java希尔排序
    //核心代码---开始
    public static void sort(Comparable[] arr) {
        int j;
        for (int gap = arr.length / 2; gap >  0; gap /= 2) {
            for (int i = gap; i < arr.length; i++) {
                Comparable tmp = arr[i];
                for (j = i; j >= gap && tmp.compareTo(arr[j - gap]) < 0; j -= gap) {
                    arr[j] = arr[j - gap];
                }
                arr[j] = tmp;
            }
        }
    }
    //核心代码---结束
    public static void main(String[] args) {

        int N = 2000;
        Integer[] arr = SortTestHelper.generateRandomArray(N, 0, 10);
        ShellSort.sort(arr);
        for( int i = 0 ; i < arr.length ; i ++ ){
            System.out.print(arr[i]);
            System.out.print(' ');
        }
    }
}
int main(){
    int array[] = {21, 3, 43, 53, 323, 32, 321, 9, 0, 2, 3232, 4, 1}, k;
    int len = sizeof(array) / sizeof(int);
    cout<<"The orginal arrayare is:"<<endl;
    for(k=0;k<len;k++) {
        cout<<array[k]<<",";
    }
    cout<<endl;
    bubbleSort(array, len);
    //shellsort(array, len);
    selectSort(array, len);
    //insertSort(array, len);
    //quickSort(array,0,len - 1);
    cout<<"The sorted arrayare is:"<<endl;
    for(k=0;k<len;k++) {
        cout<<array[k]<<",";
    }
    cout<<endl;
    cout<<"===============================Binary Search:"<< endl;
    int arr2[] = {1, 2, 3, 4, 5, 6, 7, 77, 200, 500}, i;
    int length = sizeof(arr2) / sizeof(int);
    int filed = 200;
    int place = binarySearch(arr2, length, filed);
    cout << "arrar is : ";
    for(i = 0; i < length; i++) {
        cout<< arr2[i] <<",";
    }
    cout<<"\n";
    cout << "length is : " << length <<endl;
    cout << "filed is : " << filed <<endl;
    cout << "place is : " << place <<endl;
    system("pause");  //暂停一下   请按任意键继续...........
    return 0;
}
int binarySearch(int arr[], int length, int flied){ //二分查找
    int low = 0, high = length, middle = 0;
    while (low < high) {
        middle = (low + high) / 2;
        if (flied == arr[middle]) {
            return middle;
        } else if(flied < arr[middle]) {
            high = middle;
        } else if (flied > arr[middle]) {
            low = middle + 1;
        }
    }
    return -1;
}
```

#### 30.求众数

```cpp
class Solution {//摩尔投票法
public:
    int majorityElement(vector<int>& nums) {
        int count = 0;
        int value = 0;
        for(auto& x:nums) {
            if(count == 0){
                value = x;
                count = 1;
            } else if(value == x){
                count++;
            } else {
               count--;
            }
        }
        return value;
    }
};
```

#### 31.给你一个 n\*m 的二维数组，每行元素保证递增，每列元素保证递增，试问如何使用优秀的时间复杂度找到某个数字（或者判断不存在）

```c++
class Solution {
public:
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        if (matrix.size() == 0 || matrix[0].size() == 0) return false;
        const int M = matrix.size(), N = matrix[0].size();
        int i = M - 1, j = 0;
        while (i >= 0 && j < N) {
            if (matrix[i][j] == target) {
                return true;
            } else if (matrix[i][j] < target) {
                ++j;
            } else {
                --i;
            }
        }
        return false;
    }
};//时间复杂度O(m+n)
```

