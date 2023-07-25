# pg_later
Execute SQL now and get the results later.

A postgres extension to execute queries asynchronously. 

## Installation

Add pg_later to shared_preload_libraries in postgresql.conf:

```text
shared_preload_libraries = 'pg_later'
```

Clone the repository, build and run the extension with [pgrx](https://github.com/pgcentralfoundation/pgrx):

```bash
cargo pgrx run pg15
```

Initialize the extension's backend:

```sql
CREATE EXTENSION pg_later CASCADE;

SELECT pglater.init();
```

Execute a SQL query now:

```sql
select pglater.exec(
  'select * from pg_available_extensions order by name limit 2'
) as job_id;

 job_id 
--------
     1
(1 row)
```

Come back at some later time, and retrieve the results by providing the job id:

```sql
select pglater.fetch_results(1);

 pg_later_results                                                                                                                                                                                       
--------------------
{
  "query": "select * from pg_available_extensions order by name limit 2",
  "job_id": 1,
  "result": [
    {
      "name": "adminpack",
      "comment": "administrative functions for PostgreSQL",
      "default_version": "2.1",
      "installed_version": null
    },
    {
      "name": "amcheck",
      "comment": "functions for verifying relation integrity",
      "default_version": "1.3",
      "installed_version": null
    }
  ],
  "status": "success"
}
```
