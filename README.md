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
