# SQL2CSV

sql2csv is a command line tool to export query results to csv file

You gonna need it only if you are going to export big amounts of data. Otherwise consider use PowerShell.

This tool is used in conjunction with `bq load` to import data into BigQuery, e.g.:

```
sql2csv.exe --query="SELECT NotebookID, Name, Surname, convert(varchar, AddDate, 120), HeadquarterCityID AS CityID FROM NotebookEmployee with (nolock)" --output=notebookemployee.csv
bq load --replace=true db.notebookemployee notebookemployee.csv "NotebookID:INTEGER,Name:STRING,Surname:STRING,AddDate:DATETIME,CityID:INTEGER"
```

## CSV

- All fields are surrounded with double quotes
- Quotes inside fields are escaped following to RFC with two doublequotes
- Output file is UTF-8 without BOM
- Newlines are `\n`

## Usage example:

```
sql2csv.exe --query="SELECT * FROM NotebookEmployee with (nolock)" --output=notebookemployee.csv --password=secret123
```

## Required options

`--query="select 1"` or `--input=query.sql` - provide query you wish to export

`--output=data.csv` - file to save results to

## Optional

`--server=localhost` - servername to connect to
`--database=RabotaUA2` - database to run query against
`--username=sa` - username
`--password=secret123` - password

# Build

Just clone repository, open solution in visual studio and build it

# Performance

**bcp**

```
bcp "SELECT ID, VacancyApplyID, QUOTENAME(convert(varchar, AddDate, 120), '\"'), QUOTENAME(FileName, '\"'), CheckSum, FileSize FROM VacancyApplyCVs with (nolock)" queryout "vacancyapplycvs.csv" -c -C65001 -t"," -r"\n"
2382128 rows copied.
Network packet size (bytes): 4096
Clock Time (ms.) Total     : 5600   Average : (425380.00 rows per sec.)
```

**sql2csv**

```
sql2csv.exe --query="SELECT ID, VacancyApplyID, convert(varchar, AddDate, 120), FileName, CheckSum, FileSize FROM VacancyApplyCVs with (nolock)" --output=vacancyapplycvs.csv
2 382 135 read in 00:00:06.9164592
2 382 135 process in 00:00:11.5524886
2 382 135 write in 00:00:11.5530416
Done in 00:00:11.5554066
```

Unfortunately we do not have managements object installed on server so can not compare with PowerShells Invoke-SqlCmd but bet it will be way to slower