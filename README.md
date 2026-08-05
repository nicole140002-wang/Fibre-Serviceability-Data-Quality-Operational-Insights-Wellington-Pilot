# Fibre-Serviceability-Data-Quality-Operational-Insights-Wellington-Pilot
整合光纤覆盖、地块、地址和服务订单数据，检查数据质量，发现覆盖与业务记录之间的不一致，并制作面向运营团队和管理层的空间报告。

# 1. 数据下载：
从这个网站"Commerce Commission"下载全国光纤覆盖 GeoPackage，数据截至 2025年6月30日，坐标系为 EPSG:2193。
网站地址是：https://www.comcom.govt.nz/regulated-industries/telecommunications/monitoring-the-telecommunications-market/telecommunications-connectivity-map/
和https://www.comcom.govt.nz/regulated-industries/telecommunications/regulated-services/consumer-protections-for-copper-withdrawal/map-of-specified-fibre-areas/
预计可以用的数据包括：
（1）Fibre Coverage：覆盖范围数据； 数据名：Coverage public 2025 – Fibre – 30 June 2025（49 MB, GZ），共计 2008 条数据记录。  
<img width="370" height="233" alt="image" src="https://github.com/user-attachments/assets/5f61f25d-0ac0-41ff-8955-0f8b98f0d2e5" />
<img width="1773" height="1229" alt="image" src="https://github.com/user-attachments/assets/8cfc96b3-c6db-4ee1-9a66-f26eded9ecec" />
<img width="1076" height="1299" alt="image" src="https://github.com/user-attachments/assets/2d08aa47-e0cc-4e4a-a7cf-f643a07696e6" />

（2）SFA Parcels：被认定可获得光纤服务的地块。 数据名： 2025-SFA-map-GPKG, 共计：1683966 条数据记录。
<img width="2338" height="388" alt="image" src="https://github.com/user-attachments/assets/50338342-806b-48ec-837e-3d884e0125e5" />
<img width="1723" height="1259" alt="image" src="https://github.com/user-attachments/assets/917a75f2-6666-446d-8b0c-4b8c4b6d1695" />
<img width="1075" height="1282" alt="image" src="https://github.com/user-attachments/assets/822376ab-b146-486e-bda2-a850f4b24f02" />

# 2. Chorus SFA Parcel and Published Fibre Coverage Consistency Audit Chorus指定光纤地块与公开光纤覆盖数据一致性审查
#### 核心问题是：被SFA数据认定为Chorus可提供指定光纤服务的地块，是否与2025年6月30日的公开Fibre Coverage空间范围一致？
#### 因为SFA当前版本使用了截至2025年11月7日的供应商信息，而Fibre Coverage时间是2025年6月30日，两套数据之间存在时间差，所以出现不一致并不一定是错误。
内容包括以下:
Geometry类型	Polygon还是MultiPolygon —— 看起来Fibre Coverages块更大，是multipolygon；SFA Parcels是每一个小块，是polygon类型
CRS	是否都是EPSG:2193 —— 都是 EPSG:2193 - NZGD2000 / New Zealand Transverse Mercator 2000
Feature数量	了解数据规模 —— 光纤覆盖layer，数据量小，2008个，SFA块非常多1,683,966个面
Provider字段	能否按运营商筛选 —— 光纤覆盖layer只有两个字段，fid和urban； SFA 字段有很多字段。
地块唯一编号	后面汇总和Join使用 —— 有
地区字段	能否筛选Wellington ——SFA有
日期字段	理解数据时间 ——SFA有
空值情况	初步数据质量检查 —— fibre图层，没有空值，有0；SFA 目前看起来有两个字段有，其他还不确定，因为数据量大，需要python查看。

**结论：** 
-> SFA图层是详细的地块级业务数据，有地块标识、供应商、评估日期和生效日期；
-> Fibre Coverage图层只是技术覆盖范围，所以只保留内部编号和城乡分类，没有供应商字段。
-> 因此，不能通过属性字段把两个图层直接连接，必须使用**空间叠加分析**。
-> Fibre coverage 数据中的 fid	GeoPackage内部要素编号，通常没有业务分析价值；urban	城乡类别标志，通常1代表urban、0代表rural；它不是“是否有光纤”的字段
-> SFA不是普通的“网络覆盖面”，而是监管意义上被认定已有指定光纤服务的地块。该认定与Chorus未来可以停止铜线服务有关，而且SFA数据明确包含地块、SFA名称和光纤服务提供商。
-> 关于 SFA 属性字段的解读
| 字段                 | 项目中的作用                   |
| ------------------ | ------------------------ |
| `id`               | SFA记录或地块的唯一编号            |
| `appellation`      | 法定地块描述，例如Lot 3 DP 611167 |
| `affected_surveys` | 相关测量图编号                  |
| `parcel_intent`    | 地块权属或用途类型                |
| `topology_type`    | 地块拓扑类型                   |
| `land_district`    | 土地登记区                    |
| `titles`           | 地契编号                     |
| `survey_area`      | 测量面积                     |
| `calc_area`        | GIS计算面积                  |
| `provider`         | 光纤基础设施提供商                |
| `date_assessed`    | 被评估为SFA的日期               |
| `date_effective`   | SFA正式生效日期                |
| `partial`          | 是否只有部分地块被认定为SFA          |
| `sfa_name`         | SFA区域名称                  |

# 3. 检查数据
查看2个layer属性：
（SFA）
Geometry:Polygon (MultiPolygon) ;GEOMETRY COLUMN: geom
CRS: EPSG:2193 - NZGD2000 / New Zealand Transverse Mercator 2000
Feature count: 1,683,966
Extent: 1175316.2560095430817455,4794485.4075714200735092 : 2081196.5831891379784793,6180548.9230163292959332

确认至少存在：

id √
provider √
partial √
date_assessed √
date_effective √
sfa_name √
calc_area √
------------------------------------------------------------------------------------------------------
（FIBRE COVERAGE）
Geometry:Polygon (MultiPolygon) ;GEOMETRY COLUMN: geom
CRS: EPSG:2193 - NZGD2000 / New Zealand Transverse Mercator 2000
Feature count: 2,008
Extent: 1175316.2999996240250766,4794485.4000005386769772 : 2081196.6000069910660386,6180548.8999982196837664
确认字段是：
fid √
urban √
其中urban = 0 的区块是：不代表没有光纤，只代表该光纤覆盖要素属于非城市类别。

# 4. 建立研究区域
建立两个边界：
wellington_city_boundary
wellington_study_area_500m
其中：
wellington_city_boundary用于最终统计和制图；
wellington_study_area_500m用于提取数据，避免城市边界附近的地块或覆盖范围被意外截断。 
数据下载： 从 Stats NZ Geographic Data Service 下载一个 Territorial Authority 边界数据，例如：
Territorial Authority 2025
找到其中值为：
Wellington City
创建500米缓冲区：
<img width="1611" height="1267" alt="image" src="https://github.com/user-attachments/assets/134df2f7-face-486d-bd8e-3b3fd1dcf781" />

# 5. 提取Wellington范围内的SFA地块 一共65,623条记录
3.4 为什么使用“相交”而不是Clip
Extract by location会保留完整的原始地块。如果使用Clip，处于研究区边缘的地块会被500米缓冲区边界切断，从而改变：
地块面积；
几何形状；
后续覆盖比例计算。
因此现在保留完整地块更合适。
<img width="1555" height="1276" alt="image" src="https://github.com/user-attachments/assets/dd11596b-5d8b-49f8-8922-9dfb89616746" />
Wellington study-area SFA parcels: 65,623
Chorus-related SFA parcels: 65,623
# 6. 提取provider 是chorus的地块
打开 Extract by expression
进入：
Processing Toolbox
→ Vector selection
→ Extract by expression
先使用这个简单表达式：
"provider" LIKE '%Chorus%' 该区域的所有地块都是chorus 提供的。
<img width="1560" height="1289" alt="image" src="https://github.com/user-attachments/assets/f69fbdbf-d85e-48ae-9ed9-4af4cd6db8f2" />

# 7. 提取Wellington范围内的Fibre Coverage
## 7.A 修复Fibre Coverage几何
因为在做fibre和边界intersect时候，报错： Feature (816) from “Fibre_Coverage_2025_RAW” has invalid geometry. Please fix the geometry or change the “Invalid features filtering” option for this input or globally in Processing settings.
Execution failed after 0.14 seconds
这个报错说明 Fibre Coverage 中第816号要素的多边形几何无效，所以 Extract by location 被中止了。
## 7.B 重新提取Wellington Coverage
图中亮粉色的部分是fibre跟sfa不同的部分。
<img width="2264" height="1271" alt="image" src="https://github.com/user-attachments/assets/2b14a473-ed13-4edf-bca9-07d8876850f9" />
截图中右侧几个明显远离研究区的粉色多边形，很可能是因为：
Fibre Coverage是 MultiPolygon；
Extract by location只要一个MultiPolygon中的某一部分与研究区相交，就会保留该要素的全部组成部分；
因此，同一个要素中位于研究区外的其他分离多边形也被一起提取了。
所以接下来要先把Coverage真正裁剪到研究范围内。
# 8.裁剪Fibre Coverage到研究区
<img width="1314" height="1275" alt="image" src="https://github.com/user-attachments/assets/dc9da8f3-b950-4e94-af21-f73599dc8153" />
## Q1: 为什么SFA不用Clip，而Coverage需要Clip
SFA是地块数据，我们希望保留完整地块，避免改变地块面积。
Fibre Coverage是连续覆盖范围，裁剪到研究区不会破坏地块含义，反而可以：
删除无关的MultiPolygon组成部分；
缩小处理范围；
避免把研究区外的覆盖误认为“不一致”；
让后面的面积计算更准确。

# 9. 检查 Wellington Chorus SFA 的几何有效性
在进行 Intersection 之前，确认：sfa_wellington_chorus 是否包含无效几何。

这样你的项目可以记录：
（1）检查了多少个地块；-修复前 65,623，修复后65,623 
（2）发现多少个无效几何； 0
（3）是否经过修复；No repair
（4）后续分析使用的是经过验证的数据。 
All Chorus-related SFA parcels in the pilot area passed the QGIS geometry validity check.
A GEOS geometry validity assessment was performed on 65,623 Chorus-related SFA parcels. All records passed the validation, with no invalid geometries or geometry errors identified.
# 10. 把 Wellington 的 Fibre Coverage 融合成一个总覆盖面。
现在的 fibre_coverage_wellington_clip 由许多覆盖多边形组成。后面如果直接与65,623个SFA地块做 Intersection，重叠的Coverage要素可能使面积被重复统计。
因此先把所有光纤覆盖多边形融合成一个整体：
fibre_coverage_wellington_dissolved，只有一个面状对象。
<img width="1981" height="1483" alt="image" src="https://github.com/user-attachments/assets/e0643ae4-0c3b-44cf-89b2-468c2b823643" />

# 11. 
