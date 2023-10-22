```
#include <iostream>

using namespace std;

#include <ctime>
// Eigen 核心部分
#include <Eigen/Core>
// 稠密矩阵的代数运算（逆，特征值等）
#include <Eigen/Dense>

using namespace Eigen;

#define MATRIX_SIZE 5

int main(int argc, char **argv){

  Matrix<double, MATRIX_SIZE, MATRIX_SIZE> matrix_NN = MatrixXd::Random(MATRIX_SIZE, MATRIX_SIZE); //随机一个大号矩阵出来当题目
  matrix_NN = matrix_NN * matrix_NN.transpose();  // 保证半正定
  Matrix<double, MATRIX_SIZE, 1> b_Nd = MatrixXd::Random(MATRIX_SIZE, 1);

  cout<<"求解：Ax = b"<<endl;
  cout<<"其中 A ="<<matrix_NN<<", b = "<<b_Nd.transpose()<<endl;

   //直接求逆
 Matrix<double, MATRIX_SIZE, 1> x = matrix_NN.inverse() * b_Nd;
 cout<<"方法一：直接求逆 : x = "<< x.transpose()<<endl;

//方法二：QR分解
 x = matrix_NN.colPivHouseholderQr().solve(b_Nd);
 
cout<<"方法二：矩阵分解 : x = "<< x.transpose()<<endl;

return 0;

}
```