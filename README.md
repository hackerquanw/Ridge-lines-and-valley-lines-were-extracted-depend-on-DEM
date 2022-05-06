# Ridge-lines-and-valley-lines-were-extracted-depend-on-DEM
# 山谷线和山脊线提取
# Running environment: Arcmap
# 编写背景
山脊线和山谷线的提取在很多行业，例如环境保护、水文水利、农业等行业应用很广泛。目前主要依赖ArcGIS中的空间分析工具来完成，需要使用13个空间分析工具来完成，费时费力，同时会产生许多中间过程文件。本插件使用Python语言，基于ArcGIS10进行二次开发，基于DEM数字高程模型，使用1个脚本来实现山脊线和山谷线的提取。技术问题可以在QQ群讨论：960933115
# 运行原理
本插件运行原理如下图所示：

![image](https://user-images.githubusercontent.com/44941550/167169080-4bdd9b6c-cc24-40ac-a3bd-9b84dadcbf0e.png)

图1：山谷线和山脊线提取脚本运行过程

（1）坡向分析

对输入的地形图进行坡向分析（Aspect），结果记为A。

結果示例如下：

![image](https://user-images.githubusercontent.com/44941550/167169165-0663364d-7a1b-4187-94ed-e328eb11cd0c.png)

（2）坡度分析Slope

对第一步的结果进行坡度分析，结果记为SOA1。

（3）反地形DEM

求取原始DEM数据层的最大高程值，记为H；通过栅格计算器计算，公式为H-DEM，得到与原来地形相反的DEM数据层，既反地形DEM。结果记为rastercalc。

![image](https://user-images.githubusercontent.com/44941550/167169217-759b650e-326a-4766-b18a-cedf48c68976.png)

（4）反地形的坡向分析

求反地形DEM的坡向。结果记为Aspect_raste1.

（5）反地形的坡度分析

求反地形的坡向变率，记为SOA2

（6）然后利用soa1和soa2求得没有误差的DEM的坡向变率（注意大小写一致）

利用栅格计算器，公式：(([SOA1]+[SOA2])-Abs([SOA1]-[SOA2]))/2 ，结果记为SOA。

![image](https://user-images.githubusercontent.com/44941550/167169264-b7c049fc-119f-4a63-866e-3f90f6fafc5c.png)

（7）邻域计算Block Statistics

设置Statistic type（默认为MEAN平均值），领域的类型默认为矩形（也可以设置为其他类型），领域大小自行设置（可以是3×3，下图设置为275×275）。结果记为B。

![image](https://user-images.githubusercontent.com/44941550/167169298-a06a38b9-b51c-4230-98d0-d8c1621eb711.png)


（8）求出正负地形分布区域

使用栅格计算器，公式：C=[DEM]-[B]，结果记为c1。

![image](https://user-images.githubusercontent.com/44941550/167169343-12bcf933-5688-408d-9daf-59b2209b3412.png)


（9）求山脊线

使用栅格计算器，公式："C">0 & "soa">70

![image](https://user-images.githubusercontent.com/44941550/167169384-d0e312bd-83fe-4c0f-86db-3b89ec710794.png)

（10）求山谷线

这一步要多次使用栅格计算工具

"soa" > 70 得D

"c" > 0 得E1

"c" < 0 得E2

"D" & "E1" 得DE1 得到山脊线

"D" & "E2" 得DE2得到山谷线

# 操作步骤

![image](https://user-images.githubusercontent.com/44941550/167169446-315b2655-0027-453d-ac1c-041e6324669f.png)

input raster 

输入数据集（栅格地形图）

output ridgelines 

输出山脊线的名称和路径（栅格）

output valley line 

输出山谷线名称和路径（栅格）

max elevation 

输入数据集（地形图）的最高海拔高度

Neighborhood 

邻域设置，Unit单位为Cell的时候，Height和Width里面的数值单位是像元数，Unit单位为Map的时候，单位是米。如果需要得到更加粗糙的结果，可以增加Height和Width里面的数值。如上图中的设置为默认设置，领域类型为矩形Rectangle，领域大小为3×3，统计方法为取平均值MEAN。
