# Adaptive Leaf Synthesis Algorithm (ALSA)
The Adaptive Leaf Synthesis Algorithm (ALSA) is a parameterized leaf synthesis tool for three-dimensional tree reconstruction. It is designed to generate complete foliage-added individual tree models from trunk–branch OBJ models. The algorithm can be used for three-dimensional forest scene reconstruction, visual representation, and three-dimensional radiative transfer simulation. It takes a reconstructed trunk–branch OBJ mesh model as input and generates parameterized leaf structures for simulation and visualization through branch-structure-constrained extraction of candidate leaf attachment points, user-defined leaf-shape parameters, density regulation, and randomized spatial placement strategies.


## Algorithm Overview
The basic workflow of ALSA includes:
Reading the trunk–branch OBJ model;
Parsing mesh vertices and faces;
Identifying candidate leaf attachment positions from fine-branch regions;
Reducing local leaf over-aggregation through a minimum-spacing constraint;
Generating parameterized leaf primitives;
Placing leaves in three-dimensional space based on positional perturbation, azimuth, and inclination controls;
Exporting the complete foliage-added individual tree OBJ model;
Saving woody structures and leaf structures as separate OBJ groups.

<p align="center">
  <<img src="6.png"> width="850">
</p>

<p align="center">
  <b>Figure 1.</b> Overall workflow of the Adaptive Leaf Synthesis Algorithm (ALSA).
</p>


## 主要功能

* 可从树干—枝条 OBJ 网格模型生成完整带叶三维单木模型；
* 支持 矩形叶片卡片和 椭圆形三角扇叶片图元；
* 可用于针叶树和阔叶树的叶片结构模拟；
* 基于 KD-tree 局部邻域分析识别细枝候选挂叶区域；
* 可通过局部厚度阈值、最小挂叶点间距和每个挂叶点的叶片数量调节叶片密度；
* 支持批量处理和单木处理；
* 输出 OBJ 文件中包含独立的 `stem_branch` 和 `leaves` 组；
* 自动统计生成的叶片数量和总叶面积；
* 支持 Z-up 方向统一和树木基部高程归零。


## 方法原理

ALSA 采用一种枝条结构约束的参数化叶片合成策略。算法输入为包含网格顶点和面片信息的树干—枝条 OBJ 模型。在相关研究中，树干—枝条模型由 AdQSM 算法生成，但 ALSA 本身并不局限于 AdQSM 生成的模型 （经测试，该算法同样适用于 Yang et al.（AI&VIS 团队）开发的 SmartQSM 算法所重建的枝干模型。）。只要输入 OBJ 文件能够合理表达树干和枝条结构，均可作为 ALSA 的输入。
ALSA 的输出为添加叶片后的完整单木 OBJ 模型。输出模型包含两个明确划分的组成部分：
* `stem_branch`：表示树干和枝条等木质结构；
* `leaves`：表示合成叶片结构。
这种组分分离方式便于在 LESS、DART、Blender 或其他三维建模与辐射传输软件中，分别为木质结构和叶片结构赋予不同的材质或光学属性。


## 叶片图元类型

ALSA 当前支持两类基本叶片图元。
 1. 矩形叶片图元
矩形模式将每片叶片表示为一个平面四边形叶片卡片，由 4 个顶点和 2 个三角面组成。该模式几何复杂度低，适合快速构建针状、条带状或狭长形叶片。
典型适用对象包括：
* 云杉针叶；
* 针叶树叶片结构；
* 条带状叶片；
* 大规模轻量化三维森林场景构建。
2. 椭圆形叶片图元
椭圆模式将每片叶片表示为三角扇网格。算法首先定义叶片中心点，然后沿椭圆轮廓生成多个边界顶点，最后将这些边界顶点与中心点连接形成三角面片。
典型适用对象包括：
* 杨树叶片等；
* 阔叶树叶片；
* 椭圆形或近圆形叶片；
* 豆科植物叶片；
* 边缘较平滑的宽叶形态。

<p align="center">
  <img src="docs/fig_leaf_primitives.png" width="750">
</p>

<p align="center">
  <b>图 2.</b> ALSA 中使用的参数化叶片图元。
</p>

---

## 示例结果

通过调整叶形参数和空间布局参数，ALSA 可以生成不同树种或不同叶片形态特征的三维树木模型。

<p align="center">
  <img src="docs/fig_species_examples.png" width="850">
</p>

<p align="center">
  <b>图 3.</b> 不同叶形参数和空间布局参数下的树木重建效果。
</p>

ALSA 生成的单木模型可进一步用于构建样地尺度或林分尺度的三维森林场景。

<p align="center">
  <img src="docs/fig_reconstruction_results.png" width="850">
</p>

<p align="center">
  <b>图 4.</b> 阔叶树和针叶树三维重建结果示例。
</p>

---



## 仓库结构

推荐的仓库结构如下：

```text
adaptive-leaf-synthesis-algorithm/
│
├── run_alsa.py              # 用户运行入口，用于设置参数并调用 ALSA 主程序
├── config.py                # 默认参数配置文件
├── pipeline.py              # 主处理流程，包括批量处理、单木处理和单 OBJ 完整处理
├── mesh_io.py               # OBJ 读取与 stem_branch / leaves 分组输出
├── leaf_model.py            # 叶片图元生成、候选挂叶点提取和叶面积统计
├── transforms.py            # Z-up 转换、落地和坐标变换
├── requirements.txt         # Python 依赖库
├── README.md                # 项目说明文档
│
├── examples/
│   ├── input/
│   │   └── sample_trunk_branch.obj
│   └── output/
│       └── sample_foliage_added.obj
│
└── docs/
    ├── fig_workflow.png
    ├── fig_leaf_primitives.png
    ├── fig_species_examples.png
    └── fig_reconstruction_results.png
```



---

## 安装依赖

ALSA 基于 Python 开发，核心依赖如下：

```bash
pip install numpy scipy
```

推荐的 `requirements.txt` 内容为：

```text
numpy
scipy
```

推荐 Python 版本：

```text
Python >= 3.10
```





---

## 使用方法

### 1. 设置运行参数

打开 `run_alsa.py`，修改输入输出路径和叶片合成参数。

批量处理模式：
```python
cfg.PROCESS_MODE = "BATCH"
cfg.INPUT_ROOT = r"path\to\input_obj_folder"
cfg.OUTPUT_ROOT = r"path\to\output_folder"
cfg.OBJ_GLOB = "*.obj"
cfg.RECURSIVE = True
```

单木处理模式：
```python
cfg.PROCESS_MODE = "SINGLE"
cfg.SINGLE_INPUT_OBJ = r"path\to\single_tree.obj"
cfg.SINGLE_OUTPUT_OBJ = r"path\to\output_tree.obj"
```

### 2. 选择叶片图元类型

针叶树或窄条状叶片建议使用矩形叶片图元：

```python
cfg.LEAF_SHAPE = "RECTANGLE"

cfg.RECT_LEAF_LENGTH = 0.025
cfg.RECT_LEAF_WIDTH = 0.002
cfg.RECT_LEAF_SIZE_RANDOMNESS = 0.15
```

阔叶树建议使用椭圆形叶片图元：

```python
cfg.LEAF_SHAPE = "ELLIPSE"

cfg.ELLIPSE_LEAF_LENGTH = 0.08
cfg.ELLIPSE_LEAF_WIDTH = 0.06
cfg.ELLIPSE_LEAF_SIZE_RANDOMNESS = 0.15
cfg.ELLIPSE_LEAF_SEGMENTS = 12
```

修改叶形相关参数后，需要更新当前实际使用的叶片参数：

```python
cfg.update_active_leaf_params()
```

### 3. 调节叶片密度

主要叶片密度控制参数如下：

```python
cfg.NUM_LEAVES_PER_BRANCH = 1
cfg.RADIUS_THRESHOLD = 0.01
cfg.MIN_CLUSTER_SPACING = 0.02
cfg.ATTACH_JITTER = 0.002
```

参数说明：

| 参数                        | 含义                  |
| ------------------------- | ------------------- |
| `NUM_LEAVES_PER_BRANCH`   | 每个最终保留挂叶点生成的叶片数量    |
| `RADIUS_THRESHOLD`        | 用于识别候选细枝顶点的局部网格间距阈值 |
| `MIN_CLUSTER_SPACING`     | 最终挂叶点之间的最小距离        |
| `ATTACH_JITTER`           | 叶片附着位置的随机扰动幅度       |
| `Z_ANGLE_RANGE`           | 方位角旋转范围             |
| `INCLINATION_ANGLE_RANGE` | 倾角范围                |
| `RANDOM_SEED`             | 随机种子，用于保证结果可重复      |

### 4. 运行 ALSA

```bash
python run_alsa.py
```

---

## 输入数据

输入文件应为树干—枝条 OBJ 模型。

OBJ 文件应包含：

* 网格顶点；
* 网格面片；
* 由树干和枝条组成的木质结构。

示例输入：

```text
sample_trunk_branch.obj
```

尽管相关研究中使用的是 AdQSM 生成的树干—枝条 OBJ 模型，但 ALSA 也可以应用于其他来源的树干—枝条 OBJ 模型，前提是其网格结构适合进行叶片附着点识别。

---

## 输出结果

ALSA 输出添加叶片后的完整单木 OBJ 模型。

输出 OBJ 文件包含两个独立组：

```text
g stem_branch
g leaves
```

示例输出：

```text
sample_foliage_added.obj
```

程序运行过程中会打印：

```text
生成叶片数量: xxxx 片
总叶面积: xx.xxxxxx m²
```

在批量处理模式下，程序还会打印所有处理树木的总叶片数量和总叶面积。

---

## 主要参数说明

| 参数                        |     单位 | 作用                |
| ------------------------- | -----: | ----------------- |
| `LEAF_SHAPE`              |      - | 选择矩形或椭圆形叶片图元      |
| `RECT_LEAF_LENGTH`        |      m | 矩形叶片卡片长度          |
| `RECT_LEAF_WIDTH`         |      m | 矩形叶片卡片宽度          |
| `ELLIPSE_LEAF_LENGTH`     |      m | 椭圆叶片长轴长度          |
| `ELLIPSE_LEAF_WIDTH`      |      m | 椭圆叶片短轴宽度          |
| `ELLIPSE_LEAF_SEGMENTS`   |      - | 椭圆叶片边界离散段数        |
| `LEAF_SIZE_RANDOMNESS`    |      - | 叶片尺寸随机变化幅度        |
| `NUM_LEAVES_PER_BRANCH`   |      - | 每个挂叶点生成的叶片数量      |
| `RADIUS_THRESHOLD`        |      m | 候选细枝顶点筛选阈值        |
| `MIN_CLUSTER_SPACING`     |      m | 保留挂叶点之间的最小间距      |
| `ATTACH_JITTER`           |      m | 叶片附着位置随机扰动幅度      |
| `Z_ANGLE_RANGE`           | degree | 方位角采样范围           |
| `INCLINATION_ANGLE_RANGE` | degree | 倾角采样范围            |
| `DO_ZUP`                  |   bool | 是否将模型转换为 Z-up 方向  |
| `UP_MODE`                 |      - | 输入模型原始向上轴方向       |
| `GROUND_TO_Z0`            |   bool | 是否将模型最低点移动到 Z = 0 |

---

## 推荐参数设置

### 青海云杉 / 针叶树叶片

```python
cfg.LEAF_SHAPE = "RECTANGLE"

cfg.RECT_LEAF_LENGTH = 0.025
cfg.RECT_LEAF_WIDTH = 0.002
cfg.RECT_LEAF_SIZE_RANDOMNESS = 0.20

cfg.NUM_LEAVES_PER_BRANCH = 1
cfg.RADIUS_THRESHOLD = 0.01
cfg.MIN_CLUSTER_SPACING = 0.015
cfg.ATTACH_JITTER = 0.0005
```

### 杨树 / 阔叶树叶片

```python
cfg.LEAF_SHAPE = "ELLIPSE"

cfg.ELLIPSE_LEAF_LENGTH = 0.08
cfg.ELLIPSE_LEAF_WIDTH = 0.06
cfg.ELLIPSE_LEAF_SIZE_RANDOMNESS = 0.15
cfg.ELLIPSE_LEAF_SEGMENTS = 12

cfg.NUM_LEAVES_PER_BRANCH = 1
cfg.RADIUS_THRESHOLD = 0.01
cfg.MIN_CLUSTER_SPACING = 0.02
cfg.ATTACH_JITTER = 0.002
```

这些参数可根据树种形态、输入模型分辨率、冠层密度、目标叶面积或目标 LAI 进一步调整。

---

## 关于叶面积和 LAI

ALSA 生成的总叶面积主要取决于：

```text
最终保留挂叶点数量
× 每个挂叶点生成的叶片数量
× 单片叶片图元面积
```

对于矩形叶片卡片，单片叶面积近似为：

```text
leaf length × leaf width
```

对于椭圆形叶片图元，叶面积由三角扇几何近似计算，并接近于：

```text
π × leaf length × leaf width / 4
```

生成的总叶面积可用于调节叶片密度参数，使重建模型与样地尺度冠层条件或目标 LAI 大体一致。

---

## 注意事项

* ALSA 是一种参数化叶片合成算法，不是直接恢复真实单片叶片的重建方法；
* 输出质量取决于输入树干—枝条 OBJ 模型的质量；
* `RADIUS_THRESHOLD` 是基于网格顶点间距的代理指标，不应理解为真实枝条半径；
* `MIN_CLUSTER_SPACING` 控制的是挂叶点之间的距离，而不是叶尖之间的视觉距离；
* 大规模森林场景构建中，矩形叶片卡片比高分段椭圆叶片更加节省内存和存储空间；
* 如果 OBJ 文件很大，不建议设置过高的 `ELLIPSE_LEAF_SEGMENTS`；
* 如果需要严格控制总叶面积，建议减小 `LEAF_SIZE_RANDOMNESS` 或将其设置为 0。

---

## 引用方式

如果在研究中使用 ALSA，请引用相关论文或软件发布版本。

```bibtex
@article{ALSA2026,
  title = {Tree-neighborhood scale structure–radiation coupling analysis and modeling using multi-source remote sensing and ensemble learning},
  author = {Guanjun Lian, Huaiqing Zhang, Hua Liua, Tingdong Yang, Hanqing Qiu, Yang Liu et al},
  journal = {To be updated},
  year = {2026}
}
```

正式引用信息将在论文发表后更新。

---

## 许可证

公开发布前请根据需要指定许可证。

推荐选项：

* MIT License：适合开放、可复用的科研代码；
* BSD 3-Clause License：适合宽松的学术软件发布；
* No license：适合暂时私有或限制性共享。



---

## 联系方式

如有问题或建议，请联系：

```text
Name: Guanjun Lian
Email: 1669835441@qq.com
Institution: Institute of Forest Resource Information Techniques, Chinese Academy of Forestry, Beijing 100091, China
```


---

## 致谢

本项目面向基于树干—枝条 OBJ 模型的三维树木重建与辐射传输模拟。相关研究中使用的树干—枝条模型由 AdQSM 生成，ALSA 生成的带叶三维树木模型可用于后续可视化表达和三维辐射传输模拟。
