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

To get the environment set up, you need to have installed Docker. Then follow the instructions below:

## Set up 

### Create folder
`mkdir dehack`

`cd dehack`

### Create environment
`micromamba create --name dehack python=3.11`

`micromamba activate dehack`

### Clone required repositories

#### DE-Hackathon
`git clone https://github.com/opendatacube/de_hackathon_2024.git`

#### Datacube
`git clone https://github.com/opendatacube/datacube-core.git`

#### odc-apps-dc-tools
`git clone https://github.com/opendatacube/odc-tools.git`

#### eo-datasets
`git clone https://github.com/opendatacube/eo-datasets.git`

### Install required repositories
Note that the instructions below require switching to branches that contain datacube code for the 1.9 release, which is not officially out yet.

#### Datacube

```
cd datacube-core
git checkout 1.9.0-rc9
pip install --upgrade -e '.[test]'
./check-code.sh
cd ../
```

#### odc-apps-dc-tools

```
cd odc-tools/apps/dc_tools
git switch develop-1.9
`pip install -e .`
`cd ../../../
```

#### eo-datasets

```
cd eo-datasets
git switch integrate-1.9
pip install -e .
cd ../
```

### Install required packages

#### Required for working with STAC

```
pip install pystac-client
pip install 'odc-stac[botocore]'
```

#### Required for working with iPython notebooks

`pip install ipykernel`

#### Required for plotting

`pip install matplotlib`

## Prepare Datacube
In this step, we must configure the environment so that the datacube package knows which database to connect to (managed by postgres). The precise steps for the database depend on whether you’re using the docker-compose option or the local computer option.
### Prepare the postgres database (docker-compose)

In a new terminal, navigate to the `dehack` folder. Then run:
```
cd de_hackathon_2024/environment
make up
```

Leave this terminal running. The database has the following settings:
| Setting  | Value            |
|----------|------------------|
| Hostname | localhost        |
| Database | dearth           |
| Username | dearth           |
| Password | dearthdbpassword |

These are configured in the `docker-compose.yml` file in the `de_hackathon_2024/environment` folder.

If you want to directly access the database from the terminal, open a new terminal and run

`docker-compose exec postgis psql -U dearth -W dearth`

### Connect the datacube to the database
Return to the original terminal, and ensure the `dehack` micromamba environment is activated. 

#### Set datacube config
For more information related to configuration in datacube 1.9, see the [ODC Docs](https://datacube-core.readthedocs.io/en/develop-1.9/installation/database/configuration.html). This can be done in two ways:

##### Set environment variables
Run the following commands in the terminal

```
export ODC_DEFAULT_DB_HOSTNAME=localhost
export ODC_DEFAULT_DB_DATABASE=dearth
export ODC_DEFAULT_INDEX_DRIVER=postgis
export ODC_DEFAULT_DB_USERNAME=dearth
export ODC_DEFAULT_DB_PASSWORD=dearthdbpassword
```

### Initialise the datacube
In the `de_hackathon_2024/environment` folder, run:

`make init`

If you then run `datacube system check` you should see

```
Version:       1.9.0rc9
Index Driver:  postgis
Database URL:: postgresql://dearth:dearthdbpassword@localhost:5432/dearth

Valid connection:       YES
```

## Add data to datacube

### Create additional spatial indexes

Do this for any coordinate reference systems you might want to query or load data in

`datacube spindex create 3031`

`datacube spindex create 3857`

### Add products

The `Makefile` in the environment folder contains a step for adding product definitions to the datacube. It makes use of the `products.csv` file, which contains the product name followed by a url to the file with the product defintion

Add products by running

`make products`

### Index Data

Index data by running
`make index`

This indexes data from DE Pacific, Element 84’s Earth Search (Sentinel-2 and Copernicus DEM)
### Update additional spatial indexes
This has to be done to get the new data to register in these indexes.

```
datacube spindex update 3031
datacube spindex update 3857
```

## Query and load data
In a Python notebook:

```
from datacube import Datacube

dc = Datacube(raw_config={
   "default": {
      "index_driver": "postgis",
      "db_database": "dearth",
      "db_username": "dearth",
      "db_hostname": "localhost",
      "db_password": "dearthdbpassword"
   }
})

data_3031 = dc.load(
    product="s2_l2a",
    x=(179.95,180),
    y=(-78.0,-77.95),
    dask_chunks={},
    crs="epsg:4326",
    output_crs="epsg:3031",
    resolution=10,
    measurements=["red", "green", "blue"],
)
```