# Mat

`Mat` 是 Opencv 的基本数据结构，本文对 `Mat` 类的使用方法进行记录。
## 创建和元素访问

```cpp
#include <iostream>
#include <opencv2/core/mat.hpp>
using namespace std;
using namespace cv;

int main(int argc, char **argv){

    /** 创建Mat对象 **/
    // 1. 使用构造函数，或者是 create 函数
    Mat A(7, 7, CV_32FC2, Scalar(1,3));     // complex matrix, filled with 1+3j
    A.create(10, 10, CV_8UC1);              // 当创建的形状与类型与原来的不同时才会重新重新分配
    // 创建一个多维的 Mat 对象 （略）
    // 使用赋值构造函数，或者赋值运算符号（浅拷贝，只拷贝头引用，时间复杂度为O(1)）；使用clone函数得到深拷贝
    Mat B(A);       // 拷贝构造函数，浅拷贝
    B = A;          // 浅拷贝
    B = A.clone();  // 深拷贝
    A.copyTo(B);
    // 2. 从另外一个对象的部分构造，时间复杂度为 O(1)
    A.row(3) = A.row(4) * 3;
    Mat C(Size(640, 480), CV_8UC3);
    Mat D(C, Rect(10,10, 100, 100));    // 选取ROI，同样是浅拷贝
    D = Scalar(0,255,0);                // 使用 (0,255,0) 填充图像
    Mat E = A(Range(1,3), Range(2,4));  // 选取 1，2行，2，3列
    // 使用 ones(), zeros(), eye()
    A += Mat::eye(A.rows,A.cols,CV_8UC1);
    // 使用逗号分隔的初始值
    Mat F = ( Mat_<double>(3,3) << 1,2,3,4,5,6,7,8,9 );

    // 3. 为用户数据创建Mat头
    double m[3][3] = {
        {1, 0, 0}, {0, 2, 0}, {0, 0, 3}
        };

    Mat M = Mat(3, 3, CV_64F, m).inv();
    for(int i = 0; i < M.rows; i++){
        const double *Mi = M.ptr<double>(i);
        for(int j = 0; j < M.cols; j++){
            cout << Mi[j] << "\t";
        }
        cout << endl;
    }
    cout << endl;
    for(int i = 0; i < 3; i++){
        const double *mi = m[i];
        for(int j = 0; j < 3; j++){
            cout << mi[j] << "\t";
        }
        cout << endl;
    }

    /** 访问Mat对象元素 **/
    A.at<uchar>(3,4) += 10;
    // 最高效遍历 Mat 对象的方式
    int sum = 0;
    for(int i = 0; i < A.rows; ++i){
        const uchar *Ai = A.ptr<uchar>(i);  // 行指针
        for(int j = 0; i < A.cols; ++j)
            sum += Ai[j];
    }
    // 如果Mat存储是连续的，可以将其视为一行进行遍历
    sum = 0;
    int rows = A.rows, cols = A.cols;
    if(A.isContinuous()){
        cols *= rows;
        rows = 1;
    }
    for(int i = 0; i < rows; ++i){
        const uchar* Ai = A.ptr<uchar>(i);
        for(int j = 0; j < cols; ++j){
            sum += Ai[j];
        }
    }
    // 使用迭代器
    sum = 0;
    for(auto it = A.begin<uchar>(), itend = A.end<uchar>(); it != itend; ++it){
        sum += *it;
    }
    cout << sum << endl;

    for(MatConstIterator_<uchar> it = A.begin<uchar>(), it_end = A.end<uchar>(); it != it_end; ++it){
        sum += *it;
    }
    cout << sum << endl;

    return 0;
}
```

## 常用成员函数
1. 构造函数
    ```cpp
    Mat(int rows, int cols, int type, Scalar &s) // 初始化指定Mat的大小和要填充的数值
    Mat(Size size, int type, Scalar &s)  
    Mat(const Mat &m)                       // 拷贝构造函数
    Mat(const Mat &m, const Range &rowRange, const Range &colRange=Range::all())    // 由另外一个矩阵的部分构造
    Mat(const Mat &m, const Rect &roi)
    ```

2. 其他
    ```cpp
    at              // 访问元素
    clone           // 深拷贝
    copyTo          // 带mask拷贝
    col             // 列矩阵头
    colRange        // 返回列跨度的矩阵头
    convertTo       // 这个函数除了能够做类型转换，还能对矩阵逐元素做线性变换
    create          // 分配空间
    cross           // 叉积
    diag            // 得到对角元素
    dot             // 点集
    empty           // 判断矩阵元素是否为空
    forEach         // 逐元素操作
    inv             // 矩阵求逆
    isConfinuous    // 判断矩阵是否连续存储
    mul             // 两个矩阵逐元素乘法或者除法
    ptr             // 指针
    push_back       // 在矩阵底部增加元素
    reserve         // 保留空间
    reshape         // 改变矩阵形状
    resize          // 改变矩阵大小
    row             // 行矩阵头
    rowRange        // 返回行跨度的矩阵头
    setTo           // 矩阵元素值设置
    t               // 矩阵转置
    total           // 返回矩阵元素数目
    type            // 返回矩阵元素的类型
    ```

