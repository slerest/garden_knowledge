# csv

to **import** csv data into table:

```
\copy schema_name.table_name from '/hostdata/filename.csv' delimiter ';' CSV HEADER;
```

to **import** csv data into table with specific column:
```
\copy schema_name.table_name(column1, column2) from '/hostdata/filename.csv' delimiter ';' CSV HEADER;
```

to **export** data into csv file:

```
\copy (SELECT * FROM schema_name.table_name) To '/hostdata/filenmae.csv' WITH CSV DELIMITER ';' HEADER
```
