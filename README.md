# Fibre-Serviceability-Data-Quality-Operational-Insights-Wellington-Pilot
整合光纤覆盖、地块、地址和服务订单数据，检查数据质量，发现覆盖与业务记录之间的不一致，并制作面向运营团队和管理层的空间报告。

# 1. 数据下载：
从这个网站"Commerce Commission"下载全国光纤覆盖 GeoPackage，数据截至 2025年6月30日，坐标系为 EPSG:2193。
网站地址是：https://www.comcom.govt.nz/regulated-industries/telecommunications/monitoring-the-telecommunications-market/telecommunications-connectivity-map/
和https://www.comcom.govt.nz/regulated-industries/telecommunications/regulated-services/consumer-protections-for-copper-withdrawal/map-of-specified-fibre-areas/
预计可以用的数据包括：
（1）Fibre Coverage：覆盖范围数据； 数据名：Coverage public 2025 – Fibre – 30 June 2025（49 MB, GZ），共计 2008 条数据记录。
<img width="370" height="233" alt="image" src="https://github.com/user-attachments/assets/5f61f25d-0ac0-41ff-8955-0f8b98f0d2e5" />

（2）SFA Parcels：被认定可获得光纤服务的地块。 数据名： 2025-SFA-map-GPKG, 共计：1683966 条数据记录。
<img width="2338" height="388" alt="image" src="https://github.com/user-attachments/assets/50338342-806b-48ec-837e-3d884e0125e5" />

