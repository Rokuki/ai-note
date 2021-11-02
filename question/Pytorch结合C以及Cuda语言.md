​	

## torch.util.ffi

TH：cpu下pytorch底层文件

THC: GPU下pytorch底层文件

## TH

my_lib.c

```c
#include <TH/TH.h>

# 定义前向传播
int my_lib_add_forward(THFloatTensor *input1, THFloatTensor *input2,
THFloatTensor *output)
{
    # THFloatTensor_isSameSizeAs判断size是否相等
    if (!THFloatTensor_isSameSizeAs(input1, input2))
        return 0;
    # 根据目标THFloatTensor input1来resize output
    THFloatTensor_resizeAs(output, input1);
    # 加法操作，input1+input2输出到output
    THFloatTensor_cadd(output, input1, 1.0, input2);
    return 1;
}

# 定义反向传播
int my_lib_add_backward(THFloatTensor *grad_output, THFloatTensor *grad_input)
{
    THFloatTensor_resizeAs(grad_input, grad_output);
    THFloatTensor_fill(grad_input, 1);
    return 1;
}
```

my_lib.h

```c
int my_lib_add_forward(THFloatTensor *input1, THFloatTensor *input2, THFloatTensor *output);
int my_lib_add_backward(THFloatTensor *grad_output, THFloatTensor *grad_input);
```

build.py 编译输出.o文件

```python
from torch.utils.ffi import create_extension
ffi = create_extension(
name='_ext.my_lib',        # 输出文件地址及名称
headers='src/my_lib.h',    # 编译.h文件地址及名称
sources=['src/my_lib.c'],  # 编译.c文件地址及名称
with_cuda=False            # 不使用cuda
)
ffi.build()
```

functions/add.py 通过torch.autograd.Function定义function类

```python
import torch
from torch.autograd import Function
from _ext import my_lib

class MyAddFunction(Function):
    def forward(self, input1, input2):
        output = torch.FloatTensor()
        my_lib.my_lib_add_forward(input1, input2, output)
        return output

    def backward(self, grad_output):
        grad_input = torch.FloatTensor()
        my_lib.my_lib_add_backward(grad_output, grad_input)
        return grad_input
```

modules/add.py 将定义好的function封装为pytorch 常用的module类

```python
from torch.nn import Module
from functions.add import MyAddFunction

class MyAddModule(Module):
    def forward(self, input1, input2):
        return MyAddFunction()(input1, input2)
```

main.py调用执行测试

```python
import torch
import torch.nn as nn
from modules.add import MyAddModule

class MyNetwork(nn.Module):
    def __init__(self):
        super(MyNetwork, self).__init__()
        self.add = MyAddModule()

    def forward(self, input1, input2):
        return self.add(input1, input2)

model = MyNetwork()
input1, input2 = torch.randn(5, 5), torch.randn(5, 5)
print(model(input1, input2))
print(input1 + input2)
```

## THC

