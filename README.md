# de_hackathon_2024

For the joint Digital Earth Australia/Antarctica/Pacific ODC hackathon, September 2024.


## Antimeridian examples

### Sentinel-2 GeoMAD

This STAC Item crosses the antimeridian (180/-180 degrees longitude).

* [Link to item](https://deppcpublicstorage.blob.core.windows.net/output/dep_s2_geomad/0-0-4/066/021/2023/dep_s2_geomad_066_021_2023.stac-item.json)
* [Product definition](https://raw.githubusercontent.com/digitalearthpacific/pacific-cube-in-a-box/main/products/dep_s2_geomad.odc-product.yaml)

### Sentinel-2 Scene (from Element-84)

This STAC Item also crosses the antimeridian.

* [Link to item](https://earth-search.aws.element84.com/v1/collections/sentinel-2-c1-l2a/items/S2A_T01KAA_20240825T221936_L2A)
* [Product definition](https://raw.githubusercontent.com/opendatacube/datacube-dataset-config/main/products/s2_l2a.odc-product.yaml)

### Copernicus DEM

Two adjoining STAC Items that cross the antimeridian a bit

* [Item 1](https://earth-search.aws.element84.com/v1/collections/cop-dem-glo-30/items/Copernicus_DSM_COG_10_S18_00_W180_00_DEM)
* [Item 2](https://earth-search.aws.element84.com/v1/collections/cop-dem-glo-30/items/Copernicus_DSM_COG_10_S18_00_E179_00_DEM)
* [Product definition](https://raw.githubusercontent.com/opendatacube/datacube-dataset-config/main/products/dem_cop_30.odc-product.yaml)


## Opinionated dev environment

The `environment` folder has an opinionated dev environment, with a range of antimeridian
dataset indexing examples. All examples can be indexed, but fail when loading.

To get the environment set up, you need to have installed Docker as well as some
Python libraries:

```bash
pip install odc-apps-dc-tools datacube
```

Export some environment variables:

```bash
export ODC_DEFAULT_DB_HOSTNAME=localhost
export ODC_DEFAULT_DB_USERNAME=dearth
export ODC_DEFAULT_DB_PASSWORD=dearthdbpassword
export ODC_DEFAULT_DB_DATABASE=dearth
export ODC_DEFAULT_INDEX_DRIVER=postgis
```


Then use Make to set things up:

* `make up` do this in its own tab, runs PostGIS
* `make init` initialises the database
* `make product` sets up Datacube product definitions
* `make index` indexes examples for three products.
