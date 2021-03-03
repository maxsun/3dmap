

# HSV-to-mbtiles preparation process

This is a demo of a mbtiles-based vector map of multibeam sonar data.
`.hsv` files can be converted into Mapbox tiles using the following process:

1. PDAL Pipeline:

```
{
    "pipeline":  [
        {
            "filename": "data/265_122_1331.HSX",
                "type":"readers.mbio",
                "format" : "MBF_HYSWEEP1"
        },
        {
            "type": "writers.gdal",
            "gdaldriver": "GTiff",
            "resolution": 0.000003,
            "filename": "outputfile.tif",
            "nodata": 255
        }
    ]
    }
```

2. (Optional) RGB Encode raster
   
`rio rgbify outputfile.tif rgb_encoded.tif`

3. Transform raster into geojson using polygons:
   
`gdal_polygonize.py outputfile.tif polygonized.json -8`

4. Enforce MULTIPOLYGON format:
   
`ogr2ogr -nlt MULTIPOLYGON polygonized.json out.json`

5. Convert output geojson to mbtiles:
   
`tippecanoe -z16 -o out.mbtiles --drop-densest-as-needed out.json`