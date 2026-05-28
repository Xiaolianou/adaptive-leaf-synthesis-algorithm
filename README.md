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


## Main Features

* Generates complete foliage-added 3D individual-tree models from trunk–branch OBJ mesh models;
* Supports rectangular leaf cards and elliptical triangular-fan leaf primitives;
* Can be used for foliage-structure simulation of both coniferous and broadleaf trees;
* Identifies candidate leaf attachment regions on fine branches using KD-tree-based local neighborhood analysis;
* Allows foliage density regulation through local thickness thresholds, minimum spacing between attachment points, and the number of leaves generated at each attachment point;
* Supports both batch processing and single-tree processing;
* Outputs OBJ files containing independent `stem_branch` and `leaves` groups;
* Automatically reports the generated leaf number and total leaf area;
* Supports unified Z-up orientation and tree-base elevation normalization.

## Methodological Principle

ALSA adopts a branch-structure-constrained parametric foliage synthesis strategy. The algorithm takes a trunk–branch OBJ model containing mesh vertices and faces as input. In the associated study, the trunk–branch models were generated using the AdQSM algorithm, but ALSA itself is not limited to AdQSM-derived models. The algorithm has also been tested on trunk–branch models reconstructed using SmartQSM, developed by Yang et al. from the AI&VIS team. As long as the input OBJ file can reasonably represent trunk and branch structures, it can be used as input for ALSA.

The output of ALSA is a complete foliage-added individual-tree OBJ model. The output model contains two clearly separated components:

* `stem_branch`: woody structures, including trunks and branches;
* `leaves`: synthesized foliage structures.

This component-separated design facilitates the assignment of different material or optical properties to woody structures and foliage structures in LESS, DART, Blender, or other 3D modeling and radiative transfer software.



## Leaf Primitive Types

ALSA currently supports two basic types of leaf primitives.

### 1. Rectangular Leaf Primitive

In rectangular mode, each leaf is represented as a planar quadrilateral leaf card consisting of 4 vertices and 2 triangular faces. This mode has low geometric complexity and is suitable for rapidly constructing needle-like, strip-like, or elongated leaves.

Typical applicable cases include:

* spruce needles;
* coniferous foliage structures;
* strip-like leaves;
* large-scale lightweight 3D forest scene construction.

### 2. Elliptical Leaf Primitive

In elliptical mode, each leaf is represented as a triangular-fan mesh. The algorithm first defines the leaf center point, then generates multiple boundary vertices along the elliptical outline, and finally connects these boundary vertices to the center point to form triangular faces.

Typical applicable cases include:

* poplar leaves;
* broadleaf tree leaves;
* elliptical or nearly circular leaves;
* leguminous plant leaves;
* broadleaf forms with relatively smooth margins.

<p align="center">
<img src="1.png" width="750">
</p>

<p align="center">
  <b>Figure 2.</b> Parameterized leaf primitives used in ALSA.
</p>

---

## Example Results

By adjusting leaf-shape parameters and spatial layout parameters, ALSA can generate 3D tree models with different species-specific characteristics or leaf morphological features.

<p align="center">
 <img src="2.png" width="850">
</p>

<p align="center">
  <b>Figure 3.</b> Tree reconstruction results under different leaf-shape parameters and spatial layout parameters.
</p>


The individual-tree models generated by ALSA can be further used to construct plot-scale or stand-scale 3D forest scenes.


<p align="center">
<img src="3.png" width="850">
</p>

<p align="center">
  <b>Figure 4.</b> Three-dimensional reconstruction results of Chinese white poplar and Qinghai spruce after material assignment.
</p>

<p align="center"> 
<img src="4.png" width="850">
</p>

<p align="center">
<b>Figure 5.</b> Comparison between the reconstructed complete Qinghai spruce model and the original point cloud.

</p> <p align="center"> <img src="5.png" width="850"> 
</p> <p align="center">
<b>Figure 6.</b> Complete Qinghai spruce plot constructed from the reconstructed models and the corresponding 3D radiative transfer simulation results. 
</p>
---

---

## Usage

### 1. Configure Runtime Parameters

Open `run_alsa.py` and modify the input/output paths and foliage synthesis parameters.

Batch-processing mode:

```python
cfg.PROCESS_MODE = "BATCH"
cfg.INPUT_ROOT = r"path\to\input_obj_folder"
cfg.OUTPUT_ROOT = r"path\to\output_folder"
cfg.OBJ_GLOB = "*.obj"
cfg.RECURSIVE = True
```

Single-tree processing mode:

```python
cfg.PROCESS_MODE = "SINGLE"
cfg.SINGLE_INPUT_OBJ = r"path\to\single_tree.obj"
cfg.SINGLE_OUTPUT_OBJ = r"path\to\output_tree.obj"
```

### 2. Select the Leaf Primitive Type

For coniferous trees or narrow strip-like leaves, rectangular leaf primitives are recommended:

```python
cfg.LEAF_SHAPE = "RECTANGLE"

cfg.RECT_LEAF_LENGTH = 0.025
cfg.RECT_LEAF_WIDTH = 0.002
cfg.RECT_LEAF_SIZE_RANDOMNESS = 0.15
```

For broadleaf trees, elliptical leaf primitives are recommended:

```python
cfg.LEAF_SHAPE = "ELLIPSE"

cfg.ELLIPSE_LEAF_LENGTH = 0.08
cfg.ELLIPSE_LEAF_WIDTH = 0.06
cfg.ELLIPSE_LEAF_SIZE_RANDOMNESS = 0.15
cfg.ELLIPSE_LEAF_SEGMENTS = 12
```

After modifying leaf-shape-related parameters, update the active leaf parameters:

```python
cfg.update_active_leaf_params()
```

### 3. Adjust Foliage Density

The main parameters for controlling foliage density are:

```python
cfg.NUM_LEAVES_PER_BRANCH = 1
cfg.RADIUS_THRESHOLD = 0.01
cfg.MIN_CLUSTER_SPACING = 0.02
cfg.ATTACH_JITTER = 0.002
```

Parameter description:

| Parameter                 | Description                                                                  |
| ------------------------- | ---------------------------------------------------------------------------- |
| `NUM_LEAVES_PER_BRANCH`   | Number of leaf primitives generated at each retained attachment point        |
| `RADIUS_THRESHOLD`        | Local mesh-spacing threshold used to identify candidate fine-branch vertices |
| `MIN_CLUSTER_SPACING`     | Minimum distance between retained leaf attachment points                     |
| `ATTACH_JITTER`           | Random perturbation amplitude of the leaf attachment position                |
| `Z_ANGLE_RANGE`           | Azimuth rotation range                                                       |
| `INCLINATION_ANGLE_RANGE` | Inclination angle range                                                      |
| `RANDOM_SEED`             | Random seed used to ensure reproducible random leaf generation               |

### 4. Run ALSA

```bash
python run_alsa.py
```

---

## Input Data

The input file should be a trunk–branch OBJ model.

The OBJ file should contain:

* mesh vertices;
* mesh faces;
* woody structures consisting of trunks and branches.

Although the trunk–branch OBJ models used in the associated study were generated using AdQSM, ALSA can also be applied to trunk–branch OBJ models from other sources, provided that their mesh structures are suitable for leaf attachment-point identification.

---

## Output

ALSA outputs a complete foliage-added individual-tree OBJ model.

The output OBJ file contains two independent groups:

```text
g stem_branch
g leaves
```

During execution, the program reports:

```text
Generated leaf number: xxxx leaves
Total leaf area: xx.xxxxxx m²
```

In batch-processing mode, the program also reports the total number of generated leaves and the total leaf area for all processed trees.


---

## Main Parameter Description

| Parameter                 |   Unit | Description                                                                  |
| ------------------------- | -----: | ---------------------------------------------------------------------------- |
| `LEAF_SHAPE`              |      - | Selects the leaf primitive type, either rectangular or elliptical            |
| `RECT_LEAF_LENGTH`        |      m | Length of the rectangular leaf card                                          |
| `RECT_LEAF_WIDTH`         |      m | Width of the rectangular leaf card                                           |
| `ELLIPSE_LEAF_LENGTH`     |      m | Major-axis length of the elliptical leaf primitive                           |
| `ELLIPSE_LEAF_WIDTH`      |      m | Minor-axis width of the elliptical leaf primitive                            |
| `ELLIPSE_LEAF_SEGMENTS`   |      - | Number of boundary segments used to discretize the elliptical leaf primitive |
| `LEAF_SIZE_RANDOMNESS`    |      - | Random variation amplitude applied to leaf size                              |
| `NUM_LEAVES_PER_BRANCH`   |      - | Number of leaf primitives generated at each attachment point                 |
| `RADIUS_THRESHOLD`        |      m | Threshold used to identify candidate fine-branch vertices                    |
| `MIN_CLUSTER_SPACING`     |      m | Minimum spacing between retained leaf attachment points                      |
| `ATTACH_JITTER`           |      m | Random perturbation amplitude of the leaf attachment position                |
| `Z_ANGLE_RANGE`           | degree | Sampling range of the azimuth angle                                          |
| `INCLINATION_ANGLE_RANGE` | degree | Sampling range of the inclination angle                                      |
| `DO_ZUP`                  |   bool | Whether to convert the model to a Z-up orientation                           |
| `UP_MODE`                 |      - | Original upward-axis direction of the input model                            |
| `GROUND_TO_Z0`            |   bool | Whether to translate the lowest point of the model to Z = 0                  |
| `RANDOM_SEED`             |      - | Ensures reproducible random leaf generation                                  |

---


## Recommended Parameter Settings

### Coniferous Leaves

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

### Broadleaf Leaves

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

These parameters can be further adjusted according to species-specific leaf morphology, input model resolution, canopy density, target leaf area, or target LAI.

---

## Leaf Area

The total leaf area generated by ALSA mainly depends on:

```text
number of retained leaf attachment points
× number of leaves generated at each attachment point
× area of a single leaf primitive
```

For rectangular leaf cards, the area of a single leaf is approximately:

```text
leaf length × leaf width
```

For elliptical leaf primitives, the leaf area is estimated from the triangular-fan geometry and is close to:

```text
π × leaf length × leaf width / 4
```

The generated total leaf area can be used to adjust foliage density parameters so that the reconstructed model remains broadly consistent with plot-scale canopy conditions or the target LAI.




## Notes

* ALSA is a parametric foliage synthesis algorithm, not a method for directly reconstructing real individual leaves.
* The quality of the output model depends strongly on the quality of the input trunk–branch OBJ model.
* `RADIUS_THRESHOLD` is a mesh-spacing-based proxy used to identify geometrically fine branch regions. It should not be interpreted as the real branch radius.
* `MIN_CLUSTER_SPACING` controls the minimum distance between retained leaf attachment points, rather than the visual distance between leaf tips.
* For large-scale forest scene construction, rectangular leaf cards are more memory- and storage-efficient than high-segment elliptical leaf primitives.
* If the input OBJ file is large, an excessively high value of `ELLIPSE_LEAF_SEGMENTS` is not recommended.
* If strict control of the total leaf area is required, `LEAF_SIZE_RANDOMNESS` should be reduced or set to `0`.
* If the trunk–branch model is sparse, the leaf size can be moderately increased to improve visual continuity, especially for coniferous trees.
* The current version of ALSA provides basic control of leaf placement and orientation. More advanced strategies, including leaf inclination angle regulation and layer-specific LAI-based foliage density control, are still being optimized.

---


## Citation

If you use ALSA in your research, please cite the associated paper or the corresponding software release.

```bibtex
@article{Lian2026ALSA,
  title   = {Tree-neighborhood scale structure--radiation coupling analysis and modeling using multi-source remote sensing and ensemble learning},
  author  = {Lian, Guanjun and Zhang, Huaiqing and Liu, Hua and Yang, Tingdong and Qiu, Hanqing and Liu, Yang and others},
  journal = {To be updated},
  year    = {2026}
}
```

The formal citation information will be updated once the associated paper has been published.


---



## License

This project is released under the MIT License. This license permits reuse, modification, and distribution of the code, provided that the original copyright notice and license terms are retained.


---

## Contact

If you have any questions or suggestions, please feel free to contact the project maintainer at:
Contact

If you have any questions or suggestions, please feel free to contact the project maintainer at:
```text
Name: Guanjun Lian
Email: 1669835441@qq.com
Institution: Institute of Forest Resource Information Techniques, Chinese Academy of Forestry, Beijing 100091, China
```


---

## Acknowledgements

This project is designed for 3D tree reconstruction and radiative transfer simulation based on trunk–branch OBJ models. In the associated study, the trunk–branch models were generated using AdQSM. The foliage-added 3D tree models generated by ALSA can be further used for visual representation and 3D radiative transfer simulation.

