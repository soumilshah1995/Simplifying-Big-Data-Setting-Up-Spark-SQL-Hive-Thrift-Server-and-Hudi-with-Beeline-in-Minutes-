# Spark SQL and Hudi with Beeline Setup

This guide outlines the steps to set up a Spark SQL Hive Thrift Server with Hudi integration and connect to it using Beeline. This setup allows you to create and interact with Hudi tables using Spark SQL and Hive Thrift Server.

## Step 1: Launch Spark SQL Hive Thrift Server with Hudi Packages

Use the following `spark-submit` command to launch the Thrift server with Spark SQL and Hudi packages:

```bash
# Launch the Spark SQL Hive Thrift Server with necessary configurations
spark-submit \
  --master 'local[*]' \
  --conf spark.executor.extraJavaOptions=-Duser.timezone=Etc/UTC \
  --conf spark.eventLog.enabled=false \
  --conf spark.sql.warehouse.dir=file:///Users/soumilshah/Desktop/soumil/sparkwarehouse \
  --conf spark.hadoop.hive.metastore.warehouse.dir=file:///Users/soumilshah/Desktop/soumil/sparkwarehouse \
  --conf 'spark.serializer=org.apache.spark.serializer.KryoSerializer'   \
  --conf 'spark.sql.extensions=org.apache.spark.sql.hudi.HoodieSparkSessionExtension'  \
  --conf 'spark.kryo.registrator=org.apache.spark.HoodieSparkKryoRegistrar' \
  --class org.apache.spark.sql.hive.thriftserver.HiveThriftServer2 \
  --name "Thrift JDBC/ODBC Server" \
  --executor-memory 512m \
  --packages 'org.apache.spark:spark-hive_2.12:3.4.0,org.apache.hudi:hudi-spark3.4-bundle_2.12:0.14.0'
```
##  step 2: Connect using Beeline

Once the Thrift server is running, use the following command to connect to it using Beeline:

```
# Connect Beeline to the Hive Thrift Server
beeline -u jdbc:hive2://0.0.0.0:10000/default

```


## Step 3: Create and Interact with Hudi Tables

Now that you are connected, you can create and interact with Hudi tables using Spark SQL queries. Here is an example of creating a partitioned Hudi table and inserting data:
```bash

-- create a Hudi table that is partitioned.
CREATE TABLE hudi_table (
    ts BIGINT,
    uuid STRING,
    rider STRING,
    driver STRING,
    fare DOUBLE,
    city STRING
) USING HUDI
PARTITIONED BY (city);

-- Insert data into the Hudi table
INSERT INTO hudi_table
VALUES
(1695159649087,'334e26e9-8355-45cc-97c6-c31daf0df330','rider-A','driver-K',19.10,'san_francisco'),
(1695091554788,'e96c4396-3fad-413a-a942-4cb36106d721','rider-C','driver-M',27.70 ,'san_francisco'),
(1695046462179,'9909a8b1-2d15-4d3d-8ec9-efc48c536a00','rider-D','driver-L',33.90 ,'san_francisco'),
(1695332066204,'1dced545-862b-4ceb-8b43-d2a568f6616b','rider-E','driver-O',93.50,'san_francisco'),
(1695516137016,'e3cf430c-889d-4015-bc98-59bdce1e530c','rider-F','driver-P',34.15,'sao_paulo'    ),
(1695376420876,'7a84095f-737f-40bc-b62f-6b69664712d2','rider-G','driver-Q',43.40 ,'sao_paulo'    ),
(1695173887231,'3eeb61f7-c2b0-4636-99bd-5d7a5a1d2c04','rider-I','driver-S',41.06 ,'chennai'      ),
(1695115999911,'c8abbe79-8d89-47ea-b4ce-4d224bae5bfa','rider-J','driver-T',17.85,'chennai');

```
