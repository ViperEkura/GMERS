# GMRES 线性方程组求解器

## 项目概述
本项目实现了一个基于GMRES算法的稀疏线性方程组求解器，支持CSR格式稀疏矩阵输入。程序包含完整的预处理、迭代求解和结果输出流程。

## 项目结构
```bash
GMERS/
├── bin/                # 可执行文件输出目录
├── include/            # 头文件
│   ├── gmres.hpp       # 核心算法声明
│   └── sparseMatrix.hpp # 稀疏矩阵类定义
├── src/                # 源码文件
│   ├── gmres.cpp       # GMRES算法实现
│   └── main.cpp        # 主程序入口
├── CMakeLists.txt      # 构建配置
└── README.md           # 项目说明
```

## 依赖要求
- CMake 3.10+
- C++11兼容编译器
- 标准库支持（无第三方依赖）

## 构建流程
```bash
# 1. 创建构建目录
mkdir build && cd build

# 2. 生成构建配置
cmake ..

# 3. 编译项目
make
# 构建完成后可执行文件位于 bin/gmres
```


## 使用方法

输入文件格式
需要CSR格式二进制矩阵文件，包含以下内容：

矩阵元数据（nrows, ncols, nnz）
行指针数组（nrows+1个元素）
列索引数组（nnz个元素）
数值数组（nnz个元素）
示例矩阵生成
```cpp
// matrix_gen.cpp
#include "sparseMatrix.hpp"

int main() {
    SparseMatrix<double> A(3,3,6);
    // 初始化CSR数据（示例矩阵：
    // [1 2 0]
    // [0 3 4]
    // [5 0 6]
    uint row_ptr[] = {0,2,4,6};
    uint col_ind[] = {0,1,1,2,0,2};
    double values[] = {1,2,3,4,5,6};
    
    memcpy(A.rows, row_ptr, 4*sizeof(uint));
    memcpy(A.cols, col_ind, 6*sizeof(uint));
    memcpy(A.vals, values, 6*sizeof(double));

    // 保存为二进制文件
    std::ofstream out("matrix.bin", std::ios::binary);
    out.write((char*)&A.nrows, sizeof(A.nrows));
    out.write((char*)&A.ncols, sizeof(A.ncols));
    out.write((char*)&A.nnz, sizeof(A.nnz));
    out.write((char*)A.rows, (A.nrows+1)*sizeof(uint));
    out.write((char*)A.cols, A.nnz*sizeof(uint));
    out.write((char*)A.vals, A.nnz*sizeof(double));
    return 0;
}
```
运行求解器

```bash
# 假设已生成 matrix.bin
./bin/gmres ../matrix.bin
```
## 输出说明

```
控制台输出：
matrix: M = 3, N = 3
start running gmres
iters = 12, time = 0.123ms, resid = 9.876e-7
结果文件 gmres_time.txt 格式：
matrix_name iterations time(ms) residual
算法参数（不可修改项）
参数名	值	说明
RESTART_TIMES	20	重启次数
REL_RESID_LIMIT	1e-6	相对残差终止阈值
ITERATION_LIMIT	10000	最大迭代次数
```
