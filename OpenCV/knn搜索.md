# knn 搜索

`cv::flann::GenericIndex` 是Opencv中用于构建 kd-tree 索引的类，但 OpenCV 文档并没有对该类进行详细的[解释](https://docs.opencv.org/4.5.4/db/d18/classcv_1_1flann_1_1GenericIndex.html)，因此这里对该类的使用方法做一下记录。

```cpp
#include<iostream>
// #include <opencv2/core.hpp>
#include <opencv2/flann.hpp>

using namespace std;

int main(int argc, char **argv){

    // 构建点云
    double features_arr[4][2] = { {0,0}, {10,10}, {0,10}, {10,0} };
    cv::Mat features_mat(4, 2, CV_64FC1, features_arr);

    // 创建kd树，使用L2范数计算距离
    cv::flann::GenericIndex<cvflann::L2<double>> kdtree(features_mat, cvflann::KDTreeIndexParams());

    int k = 2;      // 要搜索得到的点的数量

    // 使用形参为vector的knnSearch函数
    vector<double> query_point = {7.0, 2.0};    // 要查询的点的坐标
    vector<int> indices(k);                     // 搜索得到的最近点在点云中的索引
    vector<double> dists(k);                    // 查询点到搜索得到的最近点的距离
    kdtree.knnSearch(query_point, indices, dists, k, cvflann::SearchParams());

    for(int i = 0; i < k; i++){
        cout << indices[i] << ": " << dists[i] << endl;
    }

    // 使用形参为Mat类型的knnSearch函数
    cv::Mat query_point_mat = (cv::Mat_<double>(1,2) << 7.0, 2.0);

    cv::Mat indices_mat(1, k, CV_32S);      // 注意这里要是 1 x k 大小的 Mat
    cv::Mat dists_mat(1, k, CV_64F);
    
    kdtree.knnSearch(query_point_mat, indices_mat, dists_mat, k, cvflann::SearchParams());

    for(int i = 0; i < k; i++){
        cout << indices_mat.at<int>(0, i) << ": " << dists_mat.at<double>(0, i) << endl;
    }

    return 0;
}
```