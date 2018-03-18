# CLICKPANDAS

Simple class to retrieve data from Clickhouse database to pandas dataframe.
Written as wrapper around infi.clickhouse_orm

MAIN FEATURES:

- very simple interface for execute queries
- supporting parallel execution of simple queries
- supporting out-of-memory dataframes using dask
- supporting inserts to clickhouse table

HOW TO USE:

1) Create json in /home/config_path with the following fields:
db_name (database name), db_url (clickhouse url), username, password

2) Write simple wrapper around query:

       def get_count_rows(start, end):
           sql = "select count(*) from some_table where reg_time > '{0}'' and reg_time < '{1}'"
           return sql.format(start, end)

3) Write the following:
  
      connection = clickpandas("/directory_with_config")
      res = connection.execute_query(get_count_rows, start='2018-01-01 00:00:01', end='2018-02-01 00:00:01')
  
      res will be pd.DataFrame
  
4) To execute this query in parallel write the following*:

      res = connection.parallel_execute(get_count_rows, n_splits=4, start='-//-', end='-//-')
  
      Where n_splits is number of proccesses (default value 2)
  
      res will be pd.DataFrame
  
5) To retrive large amount of data (which is obviously bigger than RAM) write the following**:
  
      res = connection.out_of_core_execute('reg_time', get_count_rows, start='-//-', end='-//-')
  
      res will be dd.DataFrame***
  
 
-------------------------------------------------------------------------------------

NOTES:

* parallel_execute() method splits query into n_splits queries by datetime field (in our example - reg_time) 
  and executes them simultaneously. In future we will support other splitting fields.

** out_of_core_execute() method can handle queries from single table and not supports joins

*** dask dataframe will be replaced in future by our out-of-RAM dataframe implementation due to limitations
    of dask read_sql_table method.
