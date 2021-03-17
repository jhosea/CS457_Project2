# CS 457 Project 1: Metadata Management

This program manages the metadata in a database system with the ability to create and delete databases as well as create, delete, query, and alter a table within a give database.

## Requirements:
* Python 3
* pandas

## How to use and valid commands:

To run this program, you can pass a SQL file as a command line argument like the following:  
```
$ python3 project1.py test.sql
```
If no file is specified, it will use the standard input.

### Using commands:
_Note:_ Commands must end with a semicolon.  
To exit the program simply enter `exit`
* Create database:
  * To create a database the command must be formatted as `CREATE DATABASE database_name;`
* Drop database:
  * To drop a database the command must be formatted as `DROP DATABASE database_name;`
* Use database:
  * To use a database the command must be formatted as `USE database_name;`
  * _Note:_ To perform an operation on a table, you use must be using a database.
* Create table:
  * To create a table the command must be formatted as `CREATE TABLE table_name;`
* Drop table:
  * To drop a table the command must be formatted as `DROP TABLE table_name;`
* Query table:
  * To query a table the command must be formatted as `SELECT [* or column_names] FROM table_name;`
  * _Note:_ You are only able to select columns
* Alter table:
  * To add a column to a table the command must be formatted as `ALTER TABLE table_name ADD column_name column_type;`
  * To remove a column to a table the command must be formatted as `ALTER TABLE table_name REMOVE column_name;`

## Structure:
* Code File Structure:
  * `project1.py` is the main script with `sql_commands.py` parsing the commands and executing them.
* Database Structure:
  * When a database is created, it will be created under the directory `databases`. For example, if you run the command `CREATE DATABASE db_1;` a directory called `databases` will be created if it does not exist and then the directory `db_1` will be created with the path `databases/db_1`.
  * When a table is created, it will be created under the database specified in the `USE database;` command. The table itself is stored as a CSV with the schema being stored as a JSON file. If you run `USE db_1;` followed by `CREATE TABLE tbl_1;` it would result in two files being created with the paths: `databases/db_1/tbl_1.csv` and `databases/db_1/tbl_1_schema.json`
* Functional Overview:
  * `project1.py` first detects if a file was passed as a command line argument. If a file is passed, it will open the file and parse the commands using semicolons. It will then pass each command to the `execute_command` function in `sql_commands.py`.
  * `execute_command` then removes the semicolon and parses the command into individual words. It then uses the first word as a key for a dictionary of commands that maps the keywords to a function. It will then run the function passing in the parsed command and the database name (if one has not been specified this is an empty string.)
  * If the first word is `create`, then the `create` function is run which uses the second word of the command to decide whether to call `create_table` or `create_database`. Both functions simply check if the specified database or table exists and creates it if not and also returns the database name if one was previously specified through `USE database;`
  * If the first word is `drop`, then the `drop` function is run which uses the second word of the command to decide whether to call `drop_table` or `drop_database`. Both functions simply check if the specified database or table exists and deletes it if so  and also returns the database name if one was previously specified through `USE database;`
  * If the first word is `use`, then the `use_database` function is run and returns the database name.
  * If the first word is `select`, then the `select_command` function is run. This function first checks if the database exists. Then is uses the select and from keywords to identify the column names and the table name. It then checks that the table exists and then loads the table into a pandas DataFrame and checks if the columns exists if `*` was not used. It then prints the DataFrame.
  * If the first work is `alter`, then the `alter_table` function is run. This function ensures that the command is at least five words long and the second work is `table`, then uses the third word to find and load the table and schema. The fourth word is used to identify whether we want to add or remove a column. The fifth word specifies a column name. If we want to add a column, we check for a sixth word specifying the column type and then calls `add_to_table`. If we want to remove a column, we call `remove_from_table`.
  * `add_to_table` takes the table path, schema path, name of the column to add, and data type of the column to add. It then loads the table and schema and checks if the column exists. If it doesn't then it adds the column to the DataFrame and the schema dictionary. Then the DataFrame overwrites the CSV and the dictionary overwrites the JSON schema file.
  * `remove_to_table` takes the table path, schema path, name of the column to remove. It then loads the table and schema and checks if the column exists. If it does then it removes the column from the DataFrame and the schema dictionary. Then the DataFrame overwrites the CSV and the dictionary overwrites the JSON schema file.
