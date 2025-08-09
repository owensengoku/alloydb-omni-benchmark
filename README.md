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
