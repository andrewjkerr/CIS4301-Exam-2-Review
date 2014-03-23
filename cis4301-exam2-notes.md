# CIS4301 Exam 2 Notes
__Compiled by: Andrew Kerr | www.andrewjkerr.com__

These notes are compiled from various places such as the course textbook, some Lynda.com tutorials (Foundations of Programming: Databases with Simon Allardice, SQL Essential Training with Bill Weinman, and PostgreSQL 9 with PHP Essential Training with Bill Weinman), and the Internet.

_Disclaimer: I am not responsible for any misinformation. If you use my notes and get a problem wrong because of it, it's not my fault. Seriously._

## Table of Contents

* [Understanding Normalization](#understanding-normalization)
	* [First Normal Form](#first-normal-form)
	* [Second Normal Form](#second-normal-form)
	* [Third Normal Form](#third-normal-form)
* [Indexes](#indexes)
* [Views](#views)

## Understanding Normalization

* Normalization is all about the redundancy of information. Below, you'll get an idea of how normalization helps eliminate redundancy and allows for easier updating/insertion/deletion.

### First Normal Form

* Definition: "First normal form says that __each of your columns and each of your tables should contain one value just one value__, and there should be no repeating groups."
* Example of violation:

	Employee Table

	|EmployeeName 	| Department	| ComputerSerial	|
	|---------------|---------------|-------------------|
	|Andrew			| ENG			| 1234ABC, 9475BCA	|
	|John			| HR			| JAKSDFA			|
	
	* Violation: ComputerSerial has multiple values separated by commas
* Example of violation:

	Employee Table
	
	|EmployeeName 	| Department	| ComputerSerial	| ComputerSerial2  |
	|---------------|---------------|-------------------|------------------|
	|Andrew			| ENG			| 1234ABC			| 9475BCA          |
	|John			| HR			| JAKSDFA			|                  |
	
	* Violation: ComputerSerial and ComputerSerial2 is a repeated group
* Indicators:
	* Multiple values will have some sort of separators - commas, dashes, semicolons, etc.
	* "The classic sign of a repeating group column is a column with the same name, and the number tacked onto the end of it just to make it unique, because usually this is a sign of an inflexible design."
* Fix: Create a new table with foreign key:
	
	Employee Table
    
    |EmployeeName 	| Department|
	|---------------|-----------|
    |Andrew			| ENG		|	
    |John			| HR		|	
	
	Computer Table
	
	|EmployeeName	| ComputerSerial |
	|---------------|----------------|
	|Andrew			| 1234ABC        |
	|Andrew			| 9475BCA        |
	|John			| JAKSDFA        |
	
	* _In this case, the foreign key of the Computer Table is the EmployeeName_

### Second Normal Form

* _Note: in order to go to 2NF, you MUST be in 1NF._
* Definition: "Any non-key field should be dependent on the entire primary key."
* Normally a problem with a composite primary key
* Example of violation:

	Classes Table
	
	|Code	| Date		| Title			| Room	| Capacity	| Available|
	|-------|-----------|---------------|-------|-----------|----------|
	|A123	| 1/1		| Learning ABCs	| A101	| 12		| 4        |
	|M345	| 1/1		| Begin DBs		| A102	| 14		| 7        |
	|A123	| 3/1		| Learning ABCs	| B105	| 15		| 5        |
	
	* Violation: The title of the course is not dependent on the entire primary key (in this case, the date)
	* _In this case, the primary key is composed of the Code AND the Date_

* Fix: Create a separate table for Courses:

	Classes Table
	
	|Code	| Date	| Room	| Capacity	| Available |
	|-------|-------|-------|-----------|-----------|
	|A123	| 1/1	| A101	| 12		| 4         |
	|M345	| 1/1	| A102	| 14		| 7         |
	|A123	| 3/1	| B105	| 15		| 5         |
	
	Courses Table
	
	|Code	| Title			 |
	|-------|----------------|
    |A123	| Learning ABCs	 |
    |M345	| Begin DBs      |
	
### Third Normal Form

* _Note: like 2NF, in order to go to 3NF, you MUST be in 2NF and 1NF._
* Definition: "No non-key field is dependent on any other non-key field."
* Example of violation:

	Classes Table

	|Code	| Date	| Room	| Capacity	| Available |
	|-------|-------|-------|-----------|-----------|
	|A123	| 1/1	| A101	| 12		| 4         |
	|M345	| 1/1	| A102	| 14		| 7         |
	|A123	| 3/1	| B105	| 15		| 5         |
	
	* Violation: The capacity of the room is dependent on the room - a non-key field.
	
* Fix: Create a separate table for Rooms:
	Classes Table
    
    |Code	| Date	| Room	| Available |
    |-------|-------|-------|-----------|
    |A123	| 1/1	| A101	| 4         |
    |M345	| 1/1	| A102	| 7         |
    |A123	| 3/1	| B105	| 5         |
	
	Rooms Table
	
	|Room	| Capacity	|
    |-------|-----------|
    |A101	| 12		|
    |A102	| 14		|
    |B105	| 15		|

## Indexes

* Indexes are all about speed of access!
	* Full table scans are inefficient! Indexes make it much faster to locate information so the DBMS does not have to do a full table scan to find your data.
* Clustered Index: The database will order the data in a table on the physical disk itself based on the column defined as the clustered index.
	* A lot of DBMS will automatically make the primary key the clustered index.
* You choose indexes on how often you query using specific attributes.
	* Example: If the primary key is a uid, but you often refer to users by their username, it's helpful to create a secondary index (a non-clustered index) with the username column.
		* Note: Using a non-clustered index is NOT as quick as using a clustered index, but it's still much more efficient than a full table scan.
* Well, why don't we index everything?
	* Every index has a cost
		* "Indexes are a benefit when reading data, but they're a detriment when writing or changing it, because they must be maintained."
		* It's much better to do a few full table scans rather than select a column that you do not use a lot.
* Tips for indexing
	* Selecting secondary indexes are difficult until you find out how your database is being used.
	* Indexing is a trade off - make sure to use it wisely!
	* But indexes can be tweaked without breaking applications.
		* Indexes are just speed-ups - no applications rely on them for anything other than speed.
* How do indexes work?
	* Creates hash table or a b-tree
		* Hash tables are good for quick lookups and point queries
			* Point queries: I want 1 row out of 1 billion rows
			* Ex - "Give me one specific birthday on Facebook"
		* B-trees are good for range queries
			* Ex - "Give me all of the items greater than some value"
* Creating indexes in PostgreSQL
	* Documentation: http://www.postgresql.org/docs/9.3/static/indexes.html
	* Basic (with b-tree):
	
		```sql
		CREATE INDEX name ON table (column);
		```
	* Basic (with a hash table):
	
		```sql
		CREATE INDEX name ON table USING hash (column);
		```
	* Case-insensitive:
	
		```sql
		CREATE INDEX name ON table (lower(column));
		```

## Views

* Views allow you to easily re-use queries.
* PostgreSQL tutorial: http://www.postgresql.org/docs/9.3/static/tutorial-views.html
* Creating views in PostgreSQL
	* Documentation: http://www.postgresql.org/docs/9.3/static/sql-createview.html
	* Basic:
	
		```sql
		CREATE VIEW viewName AS
			SELECT columns FROM table WHERE some > operators;
		```
	* Temporary view (dropped at the end of a session):
	
		```sql
		CREATE TEMPORARY VIEW viewName AS
			SELECT columns FROM table WHERE some > operators;
		```
* Using views in PostgreSQL
	* Quite easy. Just use it like you would a table:
		
		```sql
		SELECT * FROM viewName;
		```
