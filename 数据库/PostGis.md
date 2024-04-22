## 三种坐标格式

### WKT

### WKB

### GeoJson

## 合并坐标

### MutiPolygon

```sql
SELECT ST_Union(geom) AS merged_geom
FROM your_table;
```

效果如下：

![image-20240115152043182](./PostGis.assets/image-20240115152043182.png)