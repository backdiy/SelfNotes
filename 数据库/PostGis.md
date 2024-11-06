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

## 函数

### ST_Transform

```sql
raster ST_Transform(raster rast, integer srid, text algorithm=NearestNeighbor, double precision maxerr=0.125, double precision scalex, double precision scaley);

raster ST_Transform(raster rast, integer srid, double precision scalex, double precision scaley, text algorithm=NearestNeighbor, double precision maxerr=0.125);

raster ST_Transform(raster rast, raster alignto, text algorithm=NearestNeighbor, double precision maxerr=0.125);
```

使用指定的像素扭曲算法将已知空间参考系统中的栅格重新投影到另一个已知空间参考系统。如果未指定算法，则使用‘NearestNeighbor’，如果未指定MAXERR，则使用最大错误百分比0.125。

算法选项有：‘NearestNeighbor’、‘Billinine’、‘Cubic’、‘Cubi Spline’和‘Lanczos’。请参阅： [GDAL扭曲重采样方法 ](http://www.gdal.org/gdalwarp.html)了解更多详细信息。

ST_Transform经常与ST_SetSRID()混淆。ST_Transform实际上将栅格的坐标从一个空间参考系更改为另一个空间参考系(并对像素值进行重采样)，而ST_SetSRID()只是更改了栅格的SRID标识符。

与其他变体不同，变体3需要参考栅格作为 `alignto` 。变换后的栅格将被变换到参考栅格的空间参考系(SRID)，并与参考栅格对齐(ST_SameAlign=TRUE)。