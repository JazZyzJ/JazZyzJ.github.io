

# Molecular Design

小分子药物设计，这里关注小分子药物的性质：

## Drug-likeness Properties

### Molecular Weight

分子量，简写为MolWt。指构成一个分子的所有原子的原子量总和，单位是道尔顿（Dalton, Da），在药物化学中，分子大小是影响药物扩散和转运能力的关键因素。

- **吸收**： 较小的分子（通常 < 500 Da）更容易通过被动扩散穿过细胞膜（如肠壁细胞），这是口服药物吸收的第一步。
- **分布**： 过大的分子可能难以从血液循环进入组织，或穿过血脑屏障等生理屏障。
- **结合**：分子需要有合适的大小才能与靶点蛋白的口袋结合。

!!! note
    Lipinski规则建议分子量应小于500 Da，但现在的许多药物分子量都大于500 Da（蛋白激酶抑制剂），这被称为Beyond Rule of Five。

### cLogP

是计算出的油水分配系数，是化合物在正辛醇（n-octanol，模拟生物膜的脂质环境）和水（模拟液体环境）中达到分配平衡时，化合物在正辛醇和水中的浓度比值的对数。用于衡量化合物的亲脂性和亲水性。


!!! note

    Lipinski规则建议logP应小于等于5

    理想药物的logP值在1-3之间，当logP值大于5时，药物可能有着差的溶解度和潜在毒性，logP小于0则意味着跨膜运输很困难。



以上方法属于描述符，可以用：

!!! code 
    ```python
    from rdkit import Chem
    from rdkit.Chem import Descriptors

    mol = Chem.MolFromSmiles('C1CCCCC1')

    print(Descriptors.ExactMolWt(mol))
    print(Descriptors.cLogP(mol))
    ```

来计算。

在上文中提到的几个描述符被集成到了一个规则中，即Lipinski五规则，用于快速判断一个化合物是非具有良好的口服特性，被称为五规则是因为其阈值都是5或者5的倍数。

### HBD & HBA

氢键供体（Hydrogen Bond Donor, HBD）和氢键受体（Hydrogen Bond Acceptor, HBA）与分子跨膜运输有关。

- **供体**是指指那些与电负性原子（通常是N或O）相连的氢原子，例如-OH, -NH2, -NH等中的H。过多的氢键供体会增加分子与水分子的相互作用，使其难以脱去水合壳并穿过脂质膜。
- **受体**是指那些具有孤电子对的电负性原子（通常是O或N），例如羰基中的O，氨基中的N等。过多的氢键受体也会导致分子极性过强，不利于跨膜吸收。


这两个描述符的计算方法是：

!!! code 
    ```python
    from rdkit import Chem
    from rdkit.Chem import Lipinski

    mol = Chem.MolFromSmiles('C1CCCCC1')

    print(Lipinski.NumHDonors(mol))
    print(Lipinski.NumHAcceptors(mol))

    ```

**如果一个药物不具有良好的口服性质，则通常会违反Lipinski五规则中的至少两个。**

---



Linpinski是一个二元判断，而Quantitative Estimate of Drug-likeness（QED）提供了一个连续分数，范围是0-1，0表示完全不符合药物性质，1表示完全符合药物性质。

QED考虑了八个描述符，除了Lipinski中的四个，还有：



### PSA（TPSA）

（拓扑）极性表面积 （Topological） Polar Surface Area，简写为（T）PSA。分子中所有极性原子（主要是氧和氮）的表面积总和。用于预测药物的转运性质，特别是口服吸收和血脑屏障通透性。

- **吸收**：TPSA与分子的氢键成键能力高度相关。一个分子要穿过细胞膜，必须先脱去其周围的水分子“外壳”，这个过程需要能量。TPSA越高，分子与水的相互作用越强，脱去水壳就越困难，因此肠道吸收率就越低。通常认为口服需要TPSA小于140Å²
- **血脑屏障（BBB）通透性**：大脑有非常严密的保护机制，即血脑屏障，它会阻止极性分子进入。因此，作用于中枢神经系统的药物通常需要较低的TPSA。通常认为TPSA小于90Å²的分子更容易通过血脑屏障，更严格的需要60-70Å²。

以及**结构**描述符：

### 可旋转键数量

可旋转键通常被定义为任何连接两个非氢原子的非环单键。此外，还有一些例外，例如酰胺的C-N键由于共振效应具有部分双键性质，通常不被视为可旋转键。

- **灵活性**：可旋转键数量越多，分子越灵活，柔性越强，通过旋转键的旋转变换三维结构进而更容易实现进入靶点时的最佳匹配，增强亲和力
- **熵代价**：柔性越高，系统混乱度越高，对结合能不利

QED的计算中，要求可旋转键数量小于等于10，同时偏爱处于中间值的分子。

### 芳香环数量

指分子中共轭的、平面的、满足休克尔规则（Hückel's rule, 4n+2个π电子）的环状体系的数量。常见的例子包括苯环、吡啶环、咪唑环等。


!!! note

    芳香环的意义是：

    1. **结构骨架与刚性**：芳香环是药物分子中非常常见的结构单元。它们是刚性的平面结构，可以作为稳固的“脚手架”，将不同的官能团以确定的空间取向连接起来，这对于精确地与靶点相互作用至关重要。
    2. **π-π相互作用**: 芳香环富含π电子，能够与蛋白质靶点中的其他芳香环（如苯丙氨酸、酪氨酸、色氨酸的侧链）形成π-π堆积（π-π stacking）这种重要的非共价相互作用，从而增强结合亲和力。

但是过多的芳香环会带来：

1. **代谢不稳定问题**：过多的芳香环（尤其是未经修饰的苯环）可能会带来问题。它们容易被体内的细胞色素P450（CYP450）酶家族氧化，这可能导致代谢不稳定（药物被过快清除）或形成有毒的活性代谢物。这个问题被称为“代谢不稳定性”。
2. **溶解度问题**：大量的芳香环通常会导致分子亲脂性增加（logP升高）和水溶性下降。

通常，1-3个芳香环是比较理想的，没有芳香环是可行的，当出现4个或更多芳香环时，被称为芳香环堆积，有代谢和溶解度问题。

### 警示结构基团数量

警示结构基团（也称结构警报、毒性基团或不期望的官能团）是指一些已知的、与毒性、化学不稳定性、差的代谢性质或在药物筛选中引起假阳性相关的化学亚结构。

常见的有：

- 硝基（-NO2）：可在体内被还原为具有毒性的羟胺和亚硝基衍生物。
- 活泼醛基（-CHO）：容易与蛋白质的氨基fanying

他们可以通过：

!!! code 
    ```python
    from rdkit import Chem
    from rdkit.Chem import Lipinski

    mol = Chem.MolFromSmiles('C1CCCCC1')

    rotatable_bonds = Lipinski.NumRotatableBonds(mol)
    aromatic_rings = Lipinski.NumAromaticRings(mol)

    # ---  警示结构基团数量 (Structural Alerts) ---
        # QED uses a specific list of alerts. A common way to check for general
        # undesirable groups is using RDKit's FilterCatalog. We'll use the "BRENK" set,
        # which is widely used to identify problematic fragments.
        params = FilterCatalogParams()
        params.AddCatalog(FilterCatalogParams.FilterCatalogs.BRENK)
        catalog = FilterCatalog(params)

        alerts = []
        # Find all matches
        entries = catalog.GetMatches(mol)
        for entry in entries:
            alerts.append(entry.GetDescription())
        
        # The number of alerts is the count of unique descriptions.
        # QED's internal alert count might differ slightly, but this is the standard approach.
        num_alerts = len(alerts)
    ```

### 杂原子比例

在化合物中非C、H原子的比例，通常会鼓励一定量的杂原子来丰富理化性质。

!!! code 
    ```python
    num_atoms = mol.GetNumAtoms()
        if num_atoms == 0:
            heteroatom_fraction = 0
        else:
            num_heteroatoms = 0
            for atom in mol.GetAtoms():
                # 杂原子指非碳、非氢的原子
                if atom.GetAtomicNum() not in [6, 1]: # Atomic number 6 is Carbon, 1 is Hydrogen
                    num_heteroatoms += 1
            heteroatom_fraction = num_heteroatoms / num_atoms
    ```

### Csp3

碳原子sp3杂化比例，可以改善分子的平面性，通常建议Csp3大于0.25

!!! code
    ```python
    num_heavy_atoms = mol.GetNumHeavyAtoms() # 获取重原子（非氢原子）数量
    if num_heavy_atoms == 0:
        csp3_fraction = 0
    else:
        # RDKit has a dedicated function for this
        num_sp3_carbons = len([atom for atom in mol.GetAtoms() if atom.GetHybridization() == Chem.HybridizationType.SP3 and atom.GetAtomicNum() == 6])
        num_carbons = len([atom for atom in mol.GetAtoms() if atom.GetAtomicNum() == 6])
        if num_carbons == 0:
            csp3_fraction = 0
        else:
            csp3_fraction = num_sp3_carbons / num_carbons
    
    # RDKit also provides a direct descriptor for this, which calculates C(sp3)/C(all heavy atoms)
    # Let's stick to the C(sp3)/C(total carbons) definition as it is more common.
    # The descriptor is rdkit.Chem.Descriptors.FractionCSP3(mol)
    ```


### 螺原子数量

一个螺原子是一个同时属于且仅属于两个环的原子。这个原子是两个环的唯一公共连接点，就像两个环被“拧”在了一起。

引入了三维性，但同时合成会变得更加困难。

!!! code
    ```python
    # 一个原子是螺原子，如果它是且仅是两个环的公共成员
    # RDKit's Chem.GetSymmSSSR finds the smallest set of smallest rings
    spiro_atoms = 0
    # We need to get ring information
    ri = mol.GetRingInfo()
    for atom_idx in range(num_heavy_atoms):
        # AtomMemberships gives how many rings an atom is part of.
        if ri.NumAtomRings(atom_idx) == 2:
            spiro_atoms += 1
            
    # A more robust RDKit way using properties
    spiro_atom_count_prop = len([i for i, _ in Chem.GetSymmSSSR(mol) if len(i) > 2 and i[2] == i[0]]) # This is a bit complex
    # The atom membership method is more straightforward and generally correct for this purpose.
    ```

## Synthesis Availability

可合成性就是连接计算设计 (in silico design) 和化学现实 (chemical reality) 的桥梁。它评估的是一个给定的分子结构在现实世界中被制造出来的难易程度。一个好的生成模型，必须扮演一个既懂AI又懂化学的“专家”，它生成的分子不仅要“好用”，更要“能造”。

通常我们评估一个分子的合成难度时，考虑以下几个方面：

- 结构复杂度
    - 立体中心(Stereocenters): 分子中存在的手性中心（即有特定三维构象的原子）越多，且越难控制，合成难度就越大。
    - 复杂的环状结构(Complex Ring Systems): 像螺环、桥环或含有多个杂原子的奇特环系，通常没有现成的合成方法，需要专门设计路线。
    - 官能团的兼容性 (Functional Group Compatibility): 在一个多步合成中，需要保证某一步的反应条件不会破坏分子上已经存在的其他有用官能团。


- 合成路线的成熟度
- 起始原料的可获得性


### SA Score

SA Score是基于两个方向的启发性方法，并不是直接规划路线，而是凭感觉给出分数，这个感觉来自：

1. 基于片段的

    算法将分子拆解成一系列小的化学片段。然后在一个巨大的化学数据库（如包含了数千万已知化合物的PubChem数据库）中查询这些片段的出现频率。

    出现频率越高，说明这个片段越常见，越容易合成，分数对应是负的（对合成难度贡献为负数）。

2. 基于复杂度的

    对出现的“麻烦”进行惩罚，例如刚才提到的那些复杂结构


### 基于逆合成分析

规划分子的合成路径

    

