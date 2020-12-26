有一个整数数组，请你根据快速排序的思路，找出数组中第K大的数。

给定一个整数数组a,同时给定它的大小n和要找的K(K在1到n之间)，请返回第K大的数，保证答案存在。

测试样例：

```
[1,3,5,2,2],5,3
返回：2
```

```c++
class Finder {
public:
    int findKth(vector<int> a, int n, int K) {
        // write code here
        return quick_sort(a, 0, n - 1, n, K);
    }
    //从小到大排列，基准数右边应有k-1个数
    int quick_sort(vector<int>& a, int start, int end, int n, int k) {
        int base = a[start];    //选定一个基准数
        int i = start;
        int j = end;
        int res = 0;
        while (i < j) {
            //先从右边界指向的值向左进行遍历，直到找到第一个数小于基准数
            while (i < j && a[j] >= base) --j; 
            //再从左边界指向的值向右进行遍历，直到找到第一个数大于基准数
            while (i < j && a[i] <= base) ++i;
            //交换，因为前面的循环因为两个特例跳出了。
            //这样使得在i的左边再次都是小于基准的数，同理j的右边也是
            if (i < j) swap(a[i], a[j]);
        }
        swap(a[start], a[j]);    //更新base
        if (n - j == k) return a[j];    //基准值所在的位置就是第k大的数
        //在基准值右边的数小于k个，则说明要寻找的第k大元素就在左半边，
        else if (n - j < k) res = quick_sort(a, start, j-1, n, k);
        //要寻找的第k大元素就在右半边，往右半边寻找。
        else if  (n - j > k) res = quick_sort(a, j+1, end, n, k);
        return res;
    }
};
//时间复杂度：O(nlogn),空间复杂度：O(nlogn)
```

####  取无序数组中第三大的数

```c++
int get3thElement(vector<int>& arr) {
    int fst = arr[0];
    int snd = arr[0];
    int trd = arr[0];
    for (int i = 1;i<arr.size();i++) {
        if (arr[i]>fst){
            trd = snd;
            snd = fst;
            fst=arr[i];
        }else {
            if (arr[i]>snd){
                trd = snd;
                snd = arr[i];
            }else {
                if (arr[i]>trd){
                    trd = arr[i];
                }
            }
        }
    }
    return trd;
 }
//in:vector<int> tmp = {2,5,8,3,4,9,0,23,6};//out:8
int get3thElement(vector<int>& arr) {
    int fst = INT_MIN;
    int snd = INT_MIN;
    int trd = INT_MIN;
    for (int i = 0;i<arr.size();i++) {
        if (arr[i]>fst){
            trd = snd;
            snd = fst;
            fst=arr[i];
        }else if (arr[i] == fst){
            continue;
        }else {
            if (arr[i]>snd){
                trd = snd;
                snd = arr[i];
            }else if (arr[i] == snd){
                continue;
            }else {
                if (arr[i]>trd){
                    trd = arr[i];
                }
            }
        }
    }
    return trd;
}//in:vector<int> tmp = {8,8,8,3,4,8,0,23,6};//out:6
//in:vector<int> tmp = {8,8,8,8,8,8,8,23,8};//out:-2147483648   即最小值
//时间复杂度：O(n),空间复杂度：O(1)
```

