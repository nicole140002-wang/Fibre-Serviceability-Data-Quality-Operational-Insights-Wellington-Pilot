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

# 2. 数据检查
内容包括以下:
Geometry类型	Polygon还是MultiPolygon —— 看起来Fibre Coverages块更大，是multipolygon；SFA Parcels是每一个小块，是polygon类型
CRS	是否都是EPSG:2193 —— 都是 EPSG:2193 - NZGD2000 / New Zealand Transverse Mercator 2000
Feature数量	了解数据规模 —— 光纤覆盖layer，数据量小，2008个，SFA块非常多1,683,966个面
Provider字段	能否按运营商筛选 —— 光纤覆盖layer只有两个字段，fid和urban； SFA 字段有很多字段。
地块唯一编号	后面汇总和Join使用 —— 有
地区字段	能否筛选Wellington ——SFA有
日期字段	理解数据时间 ——SFA有
空值情况	初步数据质量检查 —— fibre图层，没有空值，有0；SFA 目前看起来有两个字段有，其他还不确定，因为数据量大，需要python查看。
