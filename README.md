# Dejafoo

[Dejafoo](dejafoo) is a simple bash script to run a query on multiple versions of CockroachDB on your laptop.
This lets you compare the results.
Typically, this query is from a statement diagnostics bundle, with the goal of comparing the ```EXPLAIN``` plans that
are generated for this query with different versions of CockroachDB.

This uses ```cockroach start-single-node``` in insecure mode.

## Instructions

Detailed  instructions in the script comments, so they are not repeated here.
But they include downloading the various versions of CockroachDB you care about,
specifying various file locations, etc.

## Operation

The script takes no command-line arguments.  All parameters are specified in the script itself.

For each version of CockroachDB that you specify, this script will:

- Start CockroachDB from scratch
- Create a database
- Create a table schema
- Load table statistics
- Run the SQL query
- Stop CockroachDB and clean up 

Each CockroachDB version creates a new CockroachDB data directory from scratch.
This script keeps these data directories for later debugging if desired.

## Sample Output

```
$ ./dejafoo


======================================================================================
CockroachDB version: cockroach-v21.1.12.darwin-10.9-amd64
======================================================================================

*
* WARNING: ALL SECURITY CONTROLS HAVE BEEN DISABLED!
* 
* This mode is intended for non-production testing only.
* 
* In this mode:
* - Your cluster is open to any client that can access localhost.
* - Intruders with access to your machine or network can observe client-server traffic.
* - Intruders can log in without password and read or write any data in the cluster.
* - Intruders can consume all your server's resources and cause unavailability.
*
*
* INFO: To start a secure server without mandating TLS for clients,
* consider --accept-sql-without-tls instead. For other options, see:
* 
* - https://go.crdb.dev/issue-v/53404/v21.1
* - https://www.cockroachlabs.com/docs/v21.1/secure-a-cluster.html
*
                                       crdb_version
------------------------------------------------------------------------------------------
  CockroachDB CCL v21.1.12 (x86_64-apple-darwin19, built 2021/11/15 16:04:24, go1.15.14)
(1 row)

Time: 47ms

dejafoo: Creating the database orderreceived
CREATE DATABASE

Time: 129ms

dejafoo: Turning off automatic table statistics collection
SET CLUSTER SETTING

Time: 114ms

dejafoo: Creating the schema
CREATE TABLE

Time: 113ms

dejafoo: Loading the table statistics
ALTER TABLE

Time: 197ms

dejafoo: Running the query - iteration 1
                                               info
--------------------------------------------------------------------------------------------------
  distribution: local
  vectorized: true

  • delete
  │ from: order_update
  │ auto commit
  │
  └── • limit
      │ estimated row count: 1,000
      │ count: 1000
      │
      └── • filter
          │ estimated row count: 5,293,657
          │ filter: (create_ts < '2021-09-21 00:00:00') AND (status = 'PROCESSED')
          │
          └── • scan
                estimated row count: 10,017,881 (100% of the table; stats collected 43 days ago)
                table: order_update@primary
                spans: FULL SCAN
(19 rows)

Time: 108ms

dejafoo: Shutting down CockroachDB
dejafoo: Killed PID 46288, now sleeping 5 seconds...
initiating graceful shutdown of server
server drained and shutdown completed
dejafoo: Moving CockroachDB data directory to [...]/cockroach-data_RENAMED_cockroach-v21.1.12.darwin-10.9-amd64.ldepKNXV


======================================================================================
CockroachDB version: cockroach-v21.2.2.darwin-10.9-amd64
======================================================================================

*
* WARNING: ALL SECURITY CONTROLS HAVE BEEN DISABLED!
* 
* This mode is intended for non-production testing only.
* 
* In this mode:
* - Your cluster is open to any client that can access localhost.
* - Intruders with access to your machine or network can observe client-server traffic.
* - Intruders can log in without password and read or write any data in the cluster.
* - Intruders can consume all your server's resources and cause unavailability.
*
*
* INFO: To start a secure server without mandating TLS for clients,
* consider --accept-sql-without-tls instead. For other options, see:
* 
* - https://go.crdb.dev/issue-v/53404/v21.2
* - https://www.cockroachlabs.com/docs/v21.2/secure-a-cluster.html
*
                                      crdb_version
----------------------------------------------------------------------------------------
  CockroachDB CCL v21.2.2 (x86_64-apple-darwin19, built 2021/12/01 14:38:36, go1.16.6)
(1 row)


Time: 47ms

dejafoo: Creating the database orderreceived
CREATE DATABASE


Time: 115ms

dejafoo: Turning off automatic table statistics collection
SET CLUSTER SETTING


Time: 116ms

dejafoo: Creating the schema
CREATE TABLE


Time: 136ms

dejafoo: Loading the table statistics
ALTER TABLE


Time: 231ms

dejafoo: Running the query - iteration 1
                                               info
--------------------------------------------------------------------------------------------------
  distribution: local
  vectorized: true

  • delete
  │ from: order_update
  │ auto commit
  │
  └── • limit
      │ estimated row count: 1,000
      │ count: 1000
      │
      └── • filter
          │ estimated row count: 5,293,657
          │ filter: (create_ts < '2021-09-21 00:00:00') AND (status = 'PROCESSED')
          │
          └── • scan
                estimated row count: 10,017,881 (100% of the table; stats collected 43 days ago)
                table: order_update@primary
                spans: FULL SCAN
(19 rows)


Time: 85ms

dejafoo: Shutting down CockroachDB
dejafoo: Killed PID 46423, now sleeping 5 seconds...
initiating graceful shutdown of server
server drained and shutdown completed
dejafoo: Moving CockroachDB data directory to [...]/cockroach-data_RENAMED_cockroach-v21.2.2.darwin-10.9-amd64.zY02YIfG
```
