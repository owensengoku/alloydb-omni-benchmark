# alloydb-omni-benchmark

## Setup

### Compute Engine
- Ref
  - https://services.google.com/fh/files/misc/alloydb_omni_olap_benchmarking_guide.pdf

### AlloyDB omni
```
 docker run --name my-omni \
-e POSTGRES_PASSWORD=alloydb-pwd \
-p 5432:5432 \
-v /var/lib/postgresql/data:/var/lib/postgresql/data \
-v /var/lib/postgresql/data2:/var/lib/postgresql/data2 \
-v "$PWD/my-postgresql.conf":/etc/postgresql/postgresql.conf \
-v "$HOME/mykey.json":/etc/postgresql/private-key.json \
-v /dev/shm:/dev/shm \
--log-driver=journald \
--ulimit=nice=-20:-20 \
--ulimit=memlock=-1:-1 \
-d google/alloydbomni
```

## TPC-H Benchmark

### Setup data
- Ref
  - https://services.google.com/fh/files/misc/alloydb_omni_olap_benchmarking_guide.pdf

### Arguments
```
cat << EOF > setup.env
# Private IP of the AlloyDB primary instance
export PGHOST=111.222.333.444
# Postgres default port address. You do not need to change it unless you use non-default port address.
export PGPORT=5432 # default port to connect with postgres
# TPC-H Scale Factor (determines the size of the database that we want to build).
# The scale factors 10, 30, and 100 represent data sets of approximate sizes 20GB, 60GB and 200GB, respectively
export TPCH_SCALE=30
EOF
```
### Validate data size
```
\l+ tpch
```


## Columnar Engine 

### Setup
- Ref
  - https://cloud.google.com/alloydb/omni/current/docs/columnar-engine/configure 
```
ALTER SYSTEM SET google_columnar_engine.enabled = 'on'
ALTER SYSTEM SET google_columnar_engine.memory_size_in_mb=30720;
ALTER SYSTEM SET google_columnar_engine.enable_vectorized_join = 'on';
# 設定完成後記得重新啟動
```

### Setup Tables would be load in Columnar Engine
```
ALTER SYSTEM SET google_columnar_engine.relations='tpch.public.customer,tpch.public.lineitem,tpch.public.nation,tpch.public.orders,tpch.public.part,tpch.public.partsupp,tpch.public.region,tpch.public.supplier';
```

### Checking Columnar Engine Progress
```
select relation_name, block_count_in_cc, total_block_count, block_count_in_cc=total_block_count from g_columnar_relations order by 1;
```

### Manually trigger refresh Columnar Engine
```
SELECT google_columnar_engine_refresh(relation =>'tpch.public.customer');
SELECT google_columnar_engine_refresh(relation =>'tpch.public.lineitem');
SELECT google_columnar_engine_refresh(relation =>'tpch.public.nation');
SELECT google_columnar_engine_refresh(relation =>'tpch.public.orders');
SELECT google_columnar_engine_refresh(relation =>'tpch.public.part');
SELECT google_columnar_engine_refresh(relation =>'tpch.public.partsupp');
SELECT google_columnar_engine_refresh(relation =>'tpch.public.region');
SELECT google_columnar_engine_refresh(relation =>'tpch.public.supplier');
```

### Show Columnar Engine Settings
```
SHOW google_columnar_engine.enabled;
SHOW google_columnar_engine.memory_size_in_mb;
SHOW google_columnar_engine.enable_auto_columnarization;
```




