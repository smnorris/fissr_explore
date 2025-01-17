.PHONY: all clean

DATABASE_URL=postgresql://postgres:postgres@localhost:8003/bcfishpass

INPUT_FILE=../data/fiss_density.csv
GENERATED_FILES = .fiss_density ../data/fiss_density_pts.geojson ../data/fiss_density_watersheds.geojson
PSQL_CMD=psql $(DATABASE_URL) -v ON_ERROR_STOP=1

# Make all targets
all: $(GENERATED_FILES)

.fiss_density: $(INPUT_FILE)
	$(PSQL_CMD) -c "CREATE SCHEMA IF NOT EXISTS temp"
	$(PSQL_CMD) -c "DROP TABLE IF EXISTS temp.fiss_density"
	csvsql --db $(DATABASE_URL) --table fiss_density --insert $< --db-schema temp
	touch $@

../data/fiss_density_pts.geojson:
	$(PSQL_CMD) -f sql/01_fiss_density_distinct.sql # lots of dups in source, find distinct pts
	$(PSQL_CMD) -f sql/02_fiss_density_events.sql   # join distinct source locations to all streams within 200m
	$(PSQL_CMD) -f sql/03_fiss_density_pts.sql      # join distinct source locations to best guess matching stream, add additional info
	$(PSQL_CMD) -f sql/04_fiss_density_barriers.sql # find all barriers downstream of points
	ogr2ogr -f GeoJSON $@ -a_srs EPSG:3005 \
		"PG:$(DATABASE_URL)" \
		-sql "select \
				a.fiss_density_ids, \
				a.distance_to_stream, \
				a.stream_order, \
				a.stream_order_parent, \
				a.stream_magnitude, \
				a.gradient, \
				a.map, \
				a.map_upstream, \
				a.upstream_area_ha, \
				a.channel_width, \
				a.channel_width_source, \
				a.mad_m3s, \
				b.barriers_majordams_dnstr, \
				b.barriers_falls_dnstr, \
				b.barriers_subsurfaceflow_dnstr, \
				b.barriers_gradient_dnstr, \
				b.barriers_anthropogenic_dnstr, \
				b.barriers_pscis_dnstr, \
				b.all_pscis_dnstr, \
				b.all_pscis_scores_dnstr, \
				a.geom \
			from temp.fiss_density_pts a \
			left outer join temp.fiss_density_barriers b \
			on a.fiss_density_distinct_id = b.fiss_density_distinct_id \
			order by fiss_density_ids"

../data/fiss_density_watersheds.geojson: ../data/fiss_density_pts.geojson
	$(PSQL_CMD) -f sql/05_fiss_density_watersheds.sql  # create watersheds
	# dump to temp geojson file
	$(PSQL_CMD) -Xt <<< "select \
		st_asgeojson(t.*) \
		from \
		( select \
			fiss_density_watersheds_id, \
			fiss_density_ids, \
			st_multi(st_simplifypreservetopology(geom, 20))::geometry(multipolygon, 3005) as geom \
		from temp.fiss_density_watersheds \
		) as t" | rio -q zonalstats \
			-r ~/data/bc/raster/dem/bc_dem.tif \
			--prefix 'elev_' \
			--stats 'min max mean percentile_60' > $@

# precipitation
# map.csv: fiss_density_watersheds.geojson
# 	wget --trust-server-names -qNP data http://raster.climatebc.ca/download/Normal_1981_2010MSY/Normal_1981_2010_annual.zip
# 	unzip -qun data/Normal_1981_2010_annual.zip -d data/climatebc
# 	# resample/warp the precip data to match DEM raster resolution (don't bother aligning for now)
# 	gdalwarp data/climatebc/map data/mean_annual_precip.tif -overwrite -t_srs EPSG:3005 -of COG -co COMPRESS=DEFLATE -tr 25 25
# 	fio cat $< | parallel \
#       --pipe \
#       "rio -q zonalstats \
#         -r mean_annual_precip.tif \
#         --prefix 'map_' \
#         2>/dev/null" | \
#     jq '.features[].properties | [.fiss_density_watersheds_id, .map_min, .map_max, .map_mean]' | \
#     jq -r --slurp '.[] | @csv' > $@

# bec zones
#../data/bec.csv: fiss_density_watersheds.geojson
#	$(PSQL_CMD) -f sql/06_fiss_density_watersheds_bec.sql
#	psql2csv "SELECT * FROM temp.fiss_density_watersheds_bec" > $@

clean:
	rm -Rf $(GENERATED_FILES)