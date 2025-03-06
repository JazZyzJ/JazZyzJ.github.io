## 3.4

今天在尝试复现TamGen模型

- 按照给定脚本安装env，遇到安装很久很慢的问题，应该只能等待（）
  - 发现是libcufft一直无法install，重新运行脚本
- libcufft版本更新导致无法安装原始版本（11.0.2.4），我直接安装了11.2.1.3，不知道会不会有问题


- 构建训练数据，我是直接按照data/README.md的描述安装的
    - 已解决：不需要自己训练，只用跑生成


## SurfGen

- Numpy版本过高，尝试downgrade，解决

- 将pdb文件转换到cif文件：https://mmcif.pdbj.org/converter/index.php?l=en


## DiffSBDD

- pytorch_scattter又爆了

```bash
conda install -c pyg pytorch-scatter
Channels:
 - pyg
 - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
 - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free
 - https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/pytorch
 - defaults
Platform: linux-64
Collecting package metadata (repodata.json): done
Solving environment: failed

LibMambaUnsatisfiableError: Encountered problems while solving:
  - nothing provides python_abi 3.6.* *_cp36m needed by pytorch-scatter-2.0.8-py36_torch_1.8.0_cpu

Could not solve for environment specs
The following packages are incompatible
├─ pin on python 3.13.* =* * is installable and it requires
│  └─ python =3.13 *, which can be installed;
└─ pytorch-scatter =* * is not installable because there are no viable options
   ├─ pytorch-scatter [2.0.8|2.0.9] would require
   │  └─ python_abi =3.6 *_cp36m, which does not exist (perhaps a missing channel);
   ├─ pytorch-scatter [2.0.8|2.0.9] would require
   │  └─ python_abi =3.7 *_cp37m, which does not exist (perhaps a missing channel);
   ├─ pytorch-scatter [2.0.8|2.0.9] would require
   │  └─ cudatoolkit =11.1 *, which does not exist (perhaps a missing channel);
   ├─ pytorch-scatter [2.0.8|2.0.9] would require
   │  └─ python_abi =3.8 *_cp38, which does not exist (perhaps a missing channel);
   ├─ pytorch-scatter [2.0.8|2.0.9] would require
   │  └─ python_abi =3.9 *_cp39, which does not exist (perhaps a missing channel);
   ├─ pytorch-scatter [2.0.9|2.1.0|2.1.1|2.1.2] would require
   │  └─ python >=3.8,<3.9.0a0 *, which conflicts with any installable versions previously reported;
   ├─ pytorch-scatter [2.0.9|2.1.0|2.1.1] would require
   │  └─ python >=3.7,<3.8.0a0 *, which conflicts with any installable versions previously reported;
   ├─ pytorch-scatter [2.0.9|2.1.0|2.1.1|2.1.2] would require
   │  └─ python >=3.9,<3.10.0a0 *, which conflicts with any installable versions previously reported;
   ├─ pytorch-scatter [2.0.9|2.1.0|2.1.1|2.1.2] would require
   │  └─ python >=3.10,<3.11.0a0 *, which conflicts with any installable versions previously reported;
   ├─ pytorch-scatter 2.0.9 would require
   │  └─ cudatoolkit =11.5 *, which does not exist (perhaps a missing channel);
   ├─ pytorch-scatter [2.0.9|2.1.0] would require
   │  └─ cudatoolkit =11.6 *, which does not exist (perhaps a missing channel);
   ├─ pytorch-scatter [2.0.9|2.1.0] would require
   │  └─ pytorch-cuda =11.6 *, which requires
   │     └─ cuda =11.6 *, which does not exist (perhaps a missing channel);
   ├─ pytorch-scatter [2.0.9|2.1.0|2.1.1|2.1.2] would require
   │  └─ pytorch-cuda =11.7 * but there are no viable options
   │     ├─ pytorch-cuda 11.7 would require
   │     │  └─ cuda-cudart >=11.7,<11.8 *, which does not exist (perhaps a missing channel);
   │     └─ pytorch-cuda 11.7 would require
   │        └─ cuda =11.7 *, which does not exist (perhaps a missing channel);
   ├─ pytorch-scatter [2.1.1|2.1.2] would require
   │  └─ pytorch-cuda =11.8 * but there are no viable options
   │     ├─ pytorch-cuda 11.8 would require
   │     │  └─ cuda-cudart >=11.8,<12.0 *, which does not exist (perhaps a missing channel);
   │     └─ pytorch-cuda 11.8 would require
   │        └─ cuda =11.8 *, which does not exist (perhaps a missing channel);
   ├─ pytorch-scatter 2.1.2 would require
   │  └─ python >=3.11,<3.12.0a0 *, which conflicts with any installable versions previously reported;
   ├─ pytorch-scatter 2.1.2 would require
   │  └─ pytorch-cuda =12.1 *, which requires
   │     └─ cuda-cudart >=12.1,<12.2 *, which does not exist (perhaps a missing channel);
   └─ pytorch-scatter 2.1.2 would require
      └─ python >=3.12,<3.13.0a0 *, which conflicts with any installable versions previously reported.

Pins seem to be involved in the conflict. Currently pinned specs:
 - python=3.13

```

原版的python是3.10.4，我安装的版本过高了，是3.13，现在尝试降级，但是发现rdkit版本太高了，也得降级

等等，好像不用。找conda-forge官方文档中的命令好像就可以安装了。


- openbabel爆了

python13版本还是太高了啊

```bash
conda install conda-forge::openbabel
Channels:
 - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
 - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free
 - https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/pytorch
 - defaults
 - conda-forge
Platform: linux-64
Collecting package metadata (repodata.json): done
Solving environment: failed

LibMambaUnsatisfiableError: Encountered problems while solving:
  - package openbabel-2.4.1-py27hc189817_0 requires python >=2.7,<2.8.0a0, but none of the providers can be installed

Could not solve for environment specs
The following packages are incompatible
├─ openbabel =* * is installable with the potential options
│  ├─ openbabel 3.1.1 would require
│  │  └─ python >=3.10,<3.11.0a0 *, which can be installed;
│  ├─ openbabel 3.1.1 would require
│  │  └─ python >=3.11,<3.12.0a0 *, which can be installed;
│  ├─ openbabel 3.1.1 would require
│  │  └─ python [>=3.12,<3.13.0a0 *|>=3.12.0rc3,<3.13.0a0 *], which can be installed;
│  ├─ openbabel [3.0.0|3.1.0|3.1.1] would require
│  │  └─ python >=3.8,<3.9.0a0 *, which can be installed;
│  ├─ openbabel 3.1.1 would require
│  │  └─ python >=3.9,<3.10.0a0 *, which can be installed;
│  ├─ openbabel [2.4.1|3.0.0] would require
│  │  └─ python >=2.7,<2.8.0a0 *, which can be installed;
│  ├─ openbabel [2.4.1|3.0.0|3.1.0|3.1.1] would require
│  │  └─ python >=3.6,<3.7.0a0 *, which can be installed;
│  └─ openbabel [2.4.1|3.0.0|3.1.0|3.1.1] would require
│     └─ python >=3.7,<3.8.0a0 *, which can be installed;
└─ pin on python 3.13.* =* * is not installable because it requires
   └─ python =3.13 *, which conflicts with any installable versions previously reported.

Pins seem to be involved in the conflict. Currently pinned specs:
 - python=3.13
```

发现py12支持度还是可以的，试一下装12




