# Code+README.md 模板

工程目录结构规范（私人使用），目前包含cpp、python两种语言的工程目录结构。

cpp_demo文件夹为通用工程模板

[toc]

## 一 C++工程

一个良好结构的代码，一般会有以下几个部分：

- **构建系统**：如CMakeLists.txt或者makefile脚本
- **自述文件**：里面有说明(README)和协议(License)
- **源代码**：如果这个工程是开源的，那么它会有它的源代码。很多开源工程源代码放在了src文件夹
- **静态链接库**：作者可能已经为你编好了静态库。很多工程静态链接库放在了lib文件夹中。
- **二进制文件**：作者可能为你生成好了二进制文件，比如Windows下它里面包含dll和lib。很多工程二进制文件放在了bin文件夹。
- **头文件**：基本上所有的开源库都会提供头文件。C/C++毕竟不像C#那样方便，你要知道类和函数的声明才可以正确使用它。很多头文件放在了include文件夹

*开源库的作者应该充分考虑到使用者环境的多样性，开发者应该给使用者至少提供动态链接库的版本和静态链接的版本*，静态库和动态库的区别在于，静态库是不需要导出函数的，它直接被编译进生成的二进制文件中，而动态库需要导出函数，以便自己的工程能在dll中找到某函数的偏移地址。从编译选项的角度来说，动态库往往比静态库多一个宏，表明自己是个动态库。

### 1. cpp_demo——通用C++工程模板

#### 1.1 顶层目录结构

```txt
project_name
├── deploy   
├── build
├── doc
├── 3rdparty
├── include
│   └── project_name
├── project_name
├── tools
├── scripts
├── platforms
├── test
├── LICENSE
├── CMakeLists.txt
├── build.sh
├── toolchain.cmake
├── .gitignore
├── readme.md
└── sample
```

- **deploy :** 用于存放部署、交付的文件，其包含子目录bin、lib、include分别存放本项目最总生成的可执行文件、库文件以及对外所提供的头文件。
- **build :** 用于存放build时cmake产生的中间文件，其包含子目录release和debug。
- **doc :** 用于存放项目的相关文档。
- **3rdparty :** 用于存放第三方库，每个第三库以单独目录的形式组织在3rdparty目录下。其中每个第三方目录下又有 `include` 和 `lib` 分别存放第三方库的头文件和库文件。
- **include/project_name :** 用于存放每个模块以及整个工程对外的头文件。具体格式如下文。
- **project_name :** 存放源码文件，以及内部头文件。具体格式如下文。
- **tools :** 包含一些支持项目构建的工具，如编译器等，一般情况下使用软链接。
- **scripts :** 包含一些脚本文件，如使用Jenkins进行自动化构建时所需要的脚本文件，以及一些用于预处理的脚本文件。
- **platforms :** 用于一些交叉编译时所需要的工具链等文件，按照平台进行划分来组织子目录。每个子目录下存放 `toolchain.cmake` 等用于指定平台的文件。
- **test :** 分模块存放测试代码。
- **LICENSE :** 版权信息说明。
- **CMakeLists.txt :** cmake文件。
- **build.sh :** build脚本文件。
- **.gitignore :** 指明git忽略规则。
- **readme.md :** 存放工程说明文件。
- **sample :** 存放示例代码。

#### 1.2 源文件目录结构说明

```txt
# example modules tree
project_name
├── module_1
│   ├── dir_1
│   │   ├── something.cc
│   │   └── something.h
│   ├── dir_2
│   ├── module_1.cc
│   ├── CMakeLists.txt
│   └── README.md
├── module_2
│   ├── dir_1
│   ├── dir_2
│   ├── module_2.cc
│   ├── CMakeLists.txt
│   └── README.md
├── module_3
├── main.cc
└── CMakeLists.txt
```

1. 总源码文件目录以 `project_name` 命名，即与项目同名，存放在项目根目录下。
2. 源码文件分模块进行组织，分别以各个模块进行命名存放在 `project_name` 目录下，如示例中的 `module_1` 、`module_2`。
3. 在每个子模块目录下，只包含源文件以及该模块内部所调用的头文件。
4. 每个子模块的根目录下存放该模块的主要功能逻辑代码，如 `module_1.cc`。另外，可按照功能再划分子目录进行源码组织，但不可以出现模块嵌套的情况。
5. 若要包含内部头文件时，包含路径要从 `project_name` 开始路径要完整，如`#include "project_name/module1/dir_1/somthing.h"`，以防止头文件名称冲突的情况，同时遵循了[Google C++编码规范](https://zh-google-styleguide.readthedocs.io/en/latest/google-cpp-styleguide/contents/)。

#### 1.3 头文件目录结构说明

```txt
# example include tree
include
└── project_name
    ├── module_1
    │   ├── module_1_header_1.h
    │   └── module_1_header_2.h
    ├── module_2
    │   └── module_2.h
    └── project_name.h
```


（公共）头文件目录以 `include/project_name` 命名，即文件目录为两级，存放在项目根目录下。该目录只包含所有对外的头文件。

头文件同样分模块进行组织，分别以各个模块进行命名存放在 `include/project_name` 目录下，如示例中的 `module_1` 、`module_1`。

`include/project_name` 目录下最多只包含一级子目录，即最多按照模块再划分一级，模块内的功能头文件不再以功能进行划分。

若要包含外部头文件时，包含路径同样要从 `project_name` 开始路径要完整，如`#include "project_name/module_2/module_2.h"`。

#### 1.4 其他说明

1. 针对头文件的包含，顶层 `CMakeLists.txt` 只指定 `${CMAKE_SOURCE_DIR}\include` 和 `${CMAKE_SOURCE_DIR}`，以保证所有的包含规则都是从工程根目录开始包含。
2. 添加 `include` 目录使得公共头文件和对内部文件可以分离开，使多个模块之间合作开发时项目内部结构更加清晰。
3. （暂时）在 `3rdparty` 下存放的工程中用到的第三方库和第三方源码。第三方库尽量不要直接把静态连接库直接放到git仓库中，应该另外提供链接以便下载，或者提供文档说明库的名称和版本自行安装下载，或者提供git仓库自行编译。第三方源码一般为开源的，只提供git链接。





## 二 Python工程

#### 1. 深度学习工程目录模板

> **[深度学习工程模板](https://github.com/SpikeKing/DL-Project-Template)**（DL Project Template），简化加载数据、构建网络、训练模型和预测样本的流程。

参考链接： [深度学习工程模板](https://juejin.im/post/6844903592764129288)

```txt
├── bases
│   ├── data_loader_base.py             - 数据加载基类
│   ├── infer_base.py                   - 预测样本（推断）基类
│   ├── model_base.py                   - 网络结构（模型）基类
│   ├── trainer_base.py                 - 训练模型基类
├── configs                             - 配置文件夹
│   └── simple_mnist_config.json
├── data_loaders                        - 数据加载文件夹
│   ├── __init__.py
│   ├── simple_mnist_dl.py
├── experiments                         - 实验数据文件夹
│   └── simple_mnist                    - 实验名称
│       ├── checkpoints                 - 存储的模型和参数
│       │   └── simple_mnist.weights.10-0.24.hdf5
│       ├── images                      - 图片
│       │   └── model.png
│       └── logs                        - 日志，如TensorBoard
│           └── events.out.tfevents.1524034653.wang
├── infers                              - 推断文件夹
│   ├── __init__.py
│   ├── simple_mnist_infer.py
├── main_test.py                        - 预测样本入口
├── main_train.py                       - 训练模型入口
├── models                              - 网络结构文件夹
│   ├── __init__.py
│   ├── simple_mnist_model.py
├── requirements.txt                    - 依赖库
├── trainers                            - 训练模型文件夹
│   ├── __init__.py
│   ├── simple_mnist_trainer.py
└── utils                               - 工具文件夹
    ├── __init__.py
    ├── config_utils.py                 - 配置工具类
    ├── np_utils.py                     - NumPy工具类
    ├── utils.py                        - 其他工具类

```

