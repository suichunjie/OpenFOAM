# OpenFOAM笔记
## 运行过程
1. 转网格 `fluent3DMeshToFoam example.mesh -scale 0.001`  
2. 检查网格 `checkMesh` 进行网格重组提升收敛稳定性和收敛速度 `renumberMesh` 
3. 边界条件修改 */constant/polyMesh/boundary*中壁面为`wall`, 开放空间`patch`, 并删掉`inGroups List<word> 1(wall)`;  
4. 机理转换 `chemkinToFoam /chemkin/Okafor2019.inp /chemkin/Okafor2019-thermo.dat /chemkin/transportProperties /constant/okafor2019 /constant/thermo.okafor`  
5. 包含机理 */constant/chemistryProperties.orig* 添加代码:
`#include "okafor2019"` ；
 /constant/thermophysicalProperties 添加`#include "thermo.okafor"`  在OpenFOAM7里则是在`thermophysicalProperties`添加`foamChemistryFile "$FOAM_CASE/constant/okafor2019";foamChemistryThermoFile "$FOAM_CASE/constant/thermo.okafor";`
6. 边界条件及初始条件设置 */0/U； /0/T.orig; /system/setFieldsDict* 其中orig是为后续初始化提供边界条件设置
7. 初始化 `setFields`  
8. 并行化 修改 */system/decomposeParDict* 运行命令`decomposePar`  
9. 为防止发散可再进行网格重整 `mpirun -np 12 renumberMesh -parallel overwrite`
10.  运行 `mpirun -np 12 reactingFoam -parallel > log &`  运行命令`tail -f log`可实时监测
11. 后处理 `paraFoam -builtin`  

## 小技巧
