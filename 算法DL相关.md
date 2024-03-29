# 算法面经

## Deep learning

- #### 一个卷积网络，输入是512 * 512 * 3，输出是256 * 256 * 12，卷积核应该怎么设计

$$
\normalsize
\text{Convolutional Layer:}
\quad
\text{输出尺寸} = \left\lfloor \frac{\text{输入尺寸} - \text{卷积核尺寸} + 2 \times \text{填充}}{\text{步长}} \right\rfloor + 1 
$$

$$
\normalsize
\text{Pooling Layer:} \quad \text{Output Size} = \left\lfloor \frac{\text{Input Size} - \text{Pooling Kernel Size}}{\text{Stride}} \right\rfloor + 1
$$



- #### 如果模型不收敛如何解决:
   - **调整学习率**：过高可能会导致模型在最优解附近震荡，过低会导致收敛速度慢
   - **优化器选择**：尝试不同的优化器，例如Adam可能比SGD表现更好
   - **数据预处理**：标准化或归一化输入数据
   - **增加更多数据**：使用数据增强或获取更多训练数据
   - **模型复杂度**：如果模型太复杂，可能会导致过拟合，从而在验证集或测试集上表现不佳。考虑简化模型结构，或者增加正则化项
- #### 如何判断梯度消失或梯度爆炸：
   - **梯度消失**：表现为网络深层的**梯度接近于零**，这会导致**权重更新非常缓慢或停滞不前**。可以通过检查各层梯度的大小来判断是否发生梯度消失。
   - **梯度爆炸**：表现为非常大的梯度值，可能导致模型权重的急剧变化，使模型变得不稳定。可以通过观察训练过程中是否**出现数值溢出或者非常大的权重更新**来判断
- #### 如何解决梯度消失和梯度爆炸：
   - **梯度消失：**
      - 使用**ReLU与其变体**作为激活函数----Leaky ReLU（`f(x) = x if x > 0 else alpha * x`)、Parametric ReLU (PReLU,alpha可学习)，ReLU函数因其在正区间内具有恒定梯度（1）而被广泛用于缓解梯度消失问题。其变体如Leaky ReLU和Parametric ReLU通过为负输入提供小的非零梯度，进一步减轻了梯度消失的问题
      - 可以使用**ResNet**----引入一种“捷径”或“直接路径”，使得部分梯度可以绕过一些层直接传播、为梯度提供了一条避开潜在梯度消失问题的路径
        
   - **梯度爆炸：**
      - 进行**梯度裁剪**(Gradient Clipping)：在反向传播时，通过将梯度限制在预定的阈值内，防止过大的梯度更新，从而避免数值不稳定
      - **调整学习率**：使用较小的学习率或自适应学习率算法
      - **网络架构调整**：对于非常深的网络，可以考虑引入残差连接或使用较浅的网络架构，以减少梯度在传播过程中的累积效应
     
        
   - **梯度消失与爆炸的通用措施：**
      - 使用**批量归一化(BatchNorm)**----减少训练过程中各层输入分布的改变, 维持每层输出的分布更加稳定，从而减少梯度消失与爆炸的风险.
      - 选择合适的**权重初始化**----He(ReLU)或者Xavier(Sigmoid,Tanh)
      -  **权重正则化**：L1正则化倾向于产生稀疏的权重矩阵，而L2正则化则倾向于平滑的权重，L1和L2正则化有助于控制权重的规模，**间接**减轻梯度爆炸问题与梯度消失问题

- #### 为什么会出现梯度消失或者爆炸？

<p style="margin-left: 200px;">
    &nbsp; &nbsp; &nbsp; &nbsp;主要是深度学习网络结构导致，由于<strong>反向传播中具有链式法则</strong>，梯度是每层梯度的乘积，随着网络加深，梯度会进行积累，因此会导致<strong>（梯度越来越小）梯度消失｜（梯度变得异常大）梯度爆炸</strong><br>
    &nbsp; &nbsp; &nbsp; &nbsp;使用像Sigmoid或Tanh这样的饱和激活函数时，当输入值很大或很小，它们的<strong>导数（梯度）接近于零，导致梯度消失</strong><br>
    &nbsp; &nbsp; &nbsp; &nbsp;如果<strong>权重初始化过大</strong>，则初始的梯度可能会非常大，随着反向传播过程中的连乘效应，这些梯度可能会进一步增长，导致<strong>梯度爆炸</strong>
</p>

- #### 梯度剪裁的过程怎么实现
   1. 按值剪裁：梯度的每个元素都被剪裁到一个指定的最小值和最大值之间
   2. 按范数剪裁：根据梯度的整体范数来进行剪裁，如果梯度的范数超过了一个指定的阈值，则梯度会被缩放以保持其范数不超过这个阈值

- #### L1、L2
  -  **L1正则化**：
     ***L1正则化通过向损失函数添加模型权重的绝对值之和来工作。***  
     L1正则化倾向于产生稀疏（“稀疏”是指大部分元素为零的矩阵）的权重矩阵  
     这种特性使得L1正则化可以用作特征选择的一种手段，因为它倾向于保留少量重要的特征权重，而将其他不重要的特征权重降为零
  -  **L2正则化**:
     ***L2正则化通过向损失函数添加模型权重的平方和来工作。***  
     L2正则化倾向于均匀地减小权重的值，而不是使它们变为零。  
     这种特性对于防止模型过拟合非常有效，因为它会惩罚权重参数的极端值，从而使权重分布更加平滑


$$
\normalsize
\text{L1 Norm:} \quad ||\mathbf{w}||_1 = \sum _{i} |w _ i|
$$

$$
\normalsize
\text{L2 Norm:} \quad ||\mathbf{w}||_2 = \sqrt{\sum _{i}w _i^2}
$$

- #### 为什么L1能产生稀疏,  为什么L2平滑数据?

   - **L1**：通过在每个权重处形成角点*(在这一点处导数发生突变，或者在这一点处函数不可微，绝对值函数0点处*)并促使权重达到零，导致模型稀疏，适用于特征选择。
   - **L2**：通过对所有权重施加均匀的小惩罚，使权重值较小但非零，从而平滑数据，有助于防止过拟合
 

- #### 常用激活函数：
   - **ReLU：**`f(x) = max(0, x)`，减少了梯度消失问题，并且计算上比较高效
   - **Sigmoid**：经典的激活函数，输出范围在0到1之间，通常用于二分类问题的输出层。缺点是它容易引起梯度消失问题，且输出不是以0为中心的
   - **Tanh (Hyperbolic Tangent)**：形式是 `tanh(x)`，输出范围在-1到1之间，比Sigmoid函数输出更加以0为中心，但同样可能导致梯度消失
   - **Leaky ReLU**：ReLU的变种，尝试解决ReLU的“死神经元”问题。对于负输入值，它会给出一个非零斜率，例如 `f(x) = x if x > 0 else alpha * x`，其中`alpha`是一个小的常数
   - **SeLU：**`f(x) = lambda * (max(0, x) + min(0, alpha * (exp(x) - 1)))`，`lambda`和`alpha`为预定义的常数，能**自动归一化输出**，帮助缓解梯度消失问题，适合有大量层、特别是使用批量归一化的情况
   - **Swish：**`f(x) = x * sigmoid(beta * x)`，其中`beta`是一个可学习的参数或固定值，Swish在**负输入**值区域提供平滑的非零梯度，有助于改善深层网络的训练
     
- #### 常用训练trick：
   - 以上防止梯度爆炸消失的方法可以算作trick
   - 数据增强：对图像进行旋转、翻转、缩放等变换，或对文本数据进行重排列或同义词替换
   - 早停：如果在验证集的性能不再提升可以停止训练，避免过拟合
   - 迁移学习：预训练模型为起点，可以相对减少数据集不足的影响
   - 特征处理：针对训练目标对输入的数据进行合适的预处理



- #### 手写NMS代码

```python
def NMS(boxes, scores, iou_threshold):
  areas = [(box[2] - box[0]) * (box[3] - box[0]) for box in boxes]
  indices = sorted(range(len(boxes)), key=lambda i: scores[i], reverse=True)
  select_indices = []
  while indices:
      current = indices.pop(0) # score最高box的indice
      select_indices.append(current)
      ious = []
      for idx in indices:
          inter_x1 = max(boxes[current][0], boxes[idx][0])
          inter_y1 = min(boxes[current][1], boxes[idx][1])
          inter_x2 = min(boxes[current][2], boxes[idx][2])
          inter_y2 = max(boxes[current][3], boxes[idx][3])
          inter_area = max(0,(inter_x2 - inter_x1)) * max(0, (inter_y2 - inter_y2))
          out_area = areas[current] + areas[idx] - inter_area
          iou = inter_area / out_area
          ious.append(iou)
      indices = [idx for idx, iou in zip(indices, ious) if iou <= iou_threshold] # 移除iou大于threshold的index
  return select_indices
```

- #### 手写conv代码

```python
import numpy as np
def conv2d(input_feature_map, kernel, stride, padding):
    input_padded_map = np.pad(input_feature_map, padding, mode='constant', constant_values=0)
    input_height, input_width = input_padded_map.shape
    kernel_height, kernel_width = kernel.shape
    out_height = （(input_height - kernel_height) // stride） + 1
    out_width = (input_width - kernel_width) // stride + 1
    out_feature_map = np.zero((out_height, out_width))
    for i in range(0, out_height):
        for j in range(0, out_width):
            current_window = input_padded_map[i*stride:i*stride+kernel_width, j*stride:j*stride+kernel_height]
            out_feature_map[i,j] = np.sum(current_window * kernel)# @矩阵相乘 *元素相乘
    return out_feature_map
```

<br>
<br>

## CV

- #### cv::Mat中数据内存的排列顺序是HWC（numpy.array也是HWC）

<p style="margin-left: 40px;">
&nbsp; &nbsp; &nbsp; &nbsp;假设图像是RGB存储(cv::mat是bgr)，对于图像中的第一个像素，先存储R通道的值，紧接着是这个像素的G通道值，然后是B通道值。接着是图像中第二个像素的RGB值，依此类推(torch需要的是chw因此需要transpose)
</p>

- #### C++中opencv的mat是怎么深拷贝、浅拷贝的？（调用什么函数）
   - 浅拷贝：浅拷贝可以通过简单地使用赋值操作符来完成-复制头信息和增加引用计数

     `cv::Mat ori; cv::Mat shallowCopy = original;`

   - 深拷贝：深拷贝会复制 Mat对象的数据和头信息，生成一个完全独立的副本
      1. 使用 ’**clone()‘** ：`cv::Mat deepCopy = original.clone();`

      2. 使用 ‘**copyTo() ’** ：`cv::Mat deepCopy; original.copyTo(deepCopy);`

   - ’**clone()‘** 和 ’**copyTo()‘** 的区别：
      1. `clone`得到mat对象将拥有与原始对象完全相同的数据，但是在内存中的位置是完全独立的(相当于直接返回新的对象)。 `copyTo`则需要一个已存在的 Mat对象作为目标。
      2. `copyTo()` 提供了通过掩码进行条件复制的能力，而 `clone()` 总是复制整个矩阵(复杂程度copyto > clone)

- #### 图像编码
   - 常用JPEG、PNG、GIF
      - **JPEG**：适用于彩色和灰度图像的有损压缩。它利用人眼对高频细节较不敏感的特性，通过离散余弦变换（DCT）和量化步骤来减小文件大小，广泛应用于日常摄影和网络图像
      - **PNG**: 提供无损压缩，支持透明度（Alpha通道），适合需要高质量图像或保留图像所有细节的场合，如网页设计和艺术作品。
      - **GIF**: 支持简单的动画和透明背景，但仅限于256色，适用于简单图形和网页动画
        
<br>
<br>

## 轻量化部署

- #### 量化之后出现了精度损失应该怎么办？

   *Before Quantization*

   - **修改量化策略**：例如从8位量化改为16位量化，或**非均匀量化，**在数据的关键部分使用更高的精度，而在不那么重要的部分使用较低的精度
   - **量化感知训练（Quantization Aware Training, QAT）**：训练过程中进行，模拟，可以对量化误差进行补偿
   - **混合精度量化** ：模型中不同的部分用不同的精度进行量化，一般计算密集型操作（如矩阵乘法）中使用低精度，而在需要高数值稳定性的操作（如损失函数计算）中使用全精度（浮点数32）
   - **校准 (Calibration)**：确定量化过程中使用的比例因子或参数的过程，比如使用Rknn量化时填入的mean和std。  量化时需要确定如何**将浮点数值映射到较低精度的数值**。校准过程涉及**使用代表性数据集来分析激活的分布**，从而选择合适的缩放因子或阈值。

   *After Quantization*

   - **微调（Fine-tuning）**：指在量化模型之后，对量化的模型进行附加的训练，这个过程中模型可以调整它的权重来弥补量化误差
   - **知识蒸馏 (Knowledge Distillation)**：使用一个未量化的高精度模型（教师模型）来指导量化模型（学生模型），使量化模型学习复现高精度模型的行为

- #### 量化是怎么是实现的？

   **量化**是指将模型中的高精度数据（如32位浮点数）转换为低精度（如8位整数）的过程

   - 量化可以分为均匀量化（浮点数被均匀地映射到整数范围内）、非均匀量化（根据数据的分布进行量化）
   - 量化核心参数是缩放因子与零点
      - 缩放因子Scale Factor用于确定浮点数值和量化整数值之间的映射关系；在量化过程中，每个浮点数都被除以缩放因子，然后四舍五入到最接近的整数
      - 零点Zero Point代表实际的零值（0.0）的整数，确定了量化数值范围的中心点
   - 量化过程中需要将浮点数值（如权重、激活）转换为量化整数，后处理时则需要将整数反量化为浮点数，公式如下：

$$
\text{Quantized Value} = \text{round}\left(\frac{\text{Real Value}}{\text{Scale Factor}}\right) + \text{Zero Point}
$$

$$
\text{Real Value} = (\text{Quantized Value} - \text{Zero Point}) \times \text{Scale Factor}
$$

- #### 什么是8bit量化？
   8bit也就是INT8量化----将数据(如神经网络中的权重和激活)被量化为8位整数(用0到255来表示),可以将float32模型大小减少约4倍

- #### bfloat16和fp16(half float point)同样内存大小，那他们节约的内存大小应该是一样的吗，各自的优缺点又有哪些？
   - 二者都是用16位(2字节)来存储每个浮点数，相比于fp32都是节约了一半的内存
   - bfloat16：
      - `bfloat16`使用1位符号位、8位指数位和7位尾数位。与`fp16`相比，`bfloat16`的更多指数位使得它可以表示更大范围的数值，这在深度学习中尤其有用，因为它可以更好地处理高动态范围的数据
      - 由于bfloat16和fp32的指数为相同，因此转换更加*简单高效*
      - 由于只有7位尾数，具体数值精度不如fp16
   - fp16：
      - `fp16`使用1位符号位、5位指数位和10位尾数位。较多的尾数位使得`fp16`在表示特定数值时比`bfloat16`具有更高的精度
      - 由于`fp16`的指数位较少，它的数值表示范围不如`bfloat16`宽广，这可能在处理具有高动态范围数据的深度学习应用中成为限制。同理与fp32之间的转换也比bloat16困难

