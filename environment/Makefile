ANTIMERIDIAN_BOX := 179.99,-17.5,180.0,-17.49

up:
	docker-compose up

init:
	datacube system init

products:
	dc-sync-products products.csv

index: index-dep-s2-geomad index-sentinel-2 index-cop-dem-glo-30

# File: https://deppcpublicstorage.blob.core.windows.net/output/dep_s2_geomad/0-0-4/066/021/2023/dep_s2_geomad_066_021_2023.stac-item.json
index-dep-s2-geomad:
	stac-to-dc \
		--catalog-href='https://stac.staging.digitalearthpacific.org' \
		--bbox='$(ANTIMERIDIAN_BOX)' \
		--datetime='2023' \
		--collections='dep_s2_geomad'

# https://earth-search.aws.element84.com/v1/collections/sentinel-2-c1-l2a/items/S2A_T01KAA_20240825T221936_L2A
index-sentinel-2:
	stac-to-dc \
		--catalog-href='https://earth-search.aws.element84.com/v1/' \
		--bbox='$(ANTIMERIDIAN_BOX)' \
		--datetime="2024-08-25T00:00:00.000Z/2024-08-26T23:59:59.999Z" \
		--collections='sentinel-2-c1-l2a' \
		--rename-product='s2_l2a'

# Two items: https://earth-search.aws.element84.com/v1/search?collections=cop-dem-glo-30&bbox=179.99,-17.5,180.0,-17.49
index-cop-dem-glo-30:
	stac-to-dc \
		--catalog-href='https://earth-search.aws.element84.com/v1/' \
		--bbox='$(ANTIMERIDIAN_BOX)' \
		--collections='cop-dem-glo-30'
