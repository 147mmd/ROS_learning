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


int main(int argc,char **argv){
    
 Matrix<double , 3 , 3> matrix_33;
 Matrix<double, MATRIX_SIZE, MATRIX_SIZE> matrix_NN = MatrixXd::Random(MATRIX_SIZE, MATRIX_SIZE); //随机一个大号矩阵出来
int i=0,j=0;
 for (i=0 ; i<3 ;i++){
    for(j=0 ; j<3;j++ ) {
        matrix_33(i,j)=matrix_NN(i,j);
    }

 }
cout<<"bigger matrix : "<< matrix_NN<<endl;
cout <<"matrix 3x3 form bigger matrix :"<< matrix_33<<endl;
return 0;
    

}
```