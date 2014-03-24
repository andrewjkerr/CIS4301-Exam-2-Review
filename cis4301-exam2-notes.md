# CIS4301 Exam 2 Notes
__Compiled by: Andrew Kerr | www.andrewjkerr.com__

These notes are compiled from various places such as the course textbook, some Lynda.com tutorials (Foundations of Programming: Databases with Simon Allardice, SQL Essential Training with Bill Weinman, and PostgreSQL 9 with PHP Essential Training with Bill Weinman), [Ryan Roden-Corrent's really fantastic class notes](https://github.com/murphyslaw480/cis4301-notes) and the Internet.

_Disclaimer: I am not responsible for any misinformation. If you use my notes and get a problem wrong because of it, it's not my fault. Seriously._

## Table of Contents

* [Informal Design Guidelines](#informal-design-guidelines)
* [Functional Dependencies](#functional-dependencies)
* [Normalization](#understanding-normalization)
	* [First Normal Form](#first-normal-form)
	* [Second Normal Form](#second-normal-form)
	* [Third Normal Form](#third-normal-form)
* [Indexes](#indexes)
* [Views](#views)

## Informal Design Guidelines

* Slides from class: https://www.cise.ufl.edu/class/cis4301sp14/slides/fd.pdf
	* Do not recommend - very unclear and confusing.
* We can discuss database quality!
	* Levels of quality discussion
		* Logical (conceptual) level
		* Implementation (physical storage) level
	* Measures of quality
		* Clear attribute semantics
		* Reducing redundant information
		* Reducing NULLs
		* Disallowing possibility of generating spurious tuples
			* "Spurious tuples are created when two tables are joined on attributes that are neither primary keys nor foreign keys." (From http://www.spurioustuples.net/?page_id=16)

### Guideline 1

* Design the relation schema so it's easy to explain its meaning
* Do not combine attributes from multiple entity types and relationship types into a single relation
	* Example: Do not combine employee and department information into the same table

### Guideline 2

* Design the relation schema so that no update anomalies are present in the relations
	* Update anomalies happen when you need to update data in more than one spot
	* Sometimes unavoidable so make sure to mark them well
		* Note clearly and make sure your application updates the database correctly

### Guideline 3

* Avoid placing attributes in a relation whose values may frequently be NULL
* If NULLs are unavoidable, make sure they apply to a minority of tuples
* Why are NULLs bad?
	* Wasted storage space - even though it's NULL, the space is still reserved.

### Guideline 4

* Design relation schemas to be joined with equality conditions on attributes that are approrpriately related
* Avoid relations that contain matching attributes that are not (foreign key, primary key) combinations

## Functional Dependencies

* Slides from class: https://www.cise.ufl.edu/class/cis4301sp14/slides/fd.pdf
* Inference rules for functional dependencies (aka Armstrong's Axioms):
	* Reflexive Rule: X -> X
	* Augmentation Rule: {X -> Y} |= XZ -> YZ
	* Transitive Rule: {X -> Y, Y -> Z } |= X -> Z
	* Decomposition Rule: {X -> YZ} |= X -> Y
	* Union Rule: {X -> Y, X -> Z} |= X -> YZ
	* Psuedotransitive Rule: {X -> Y, WY -> Z} |= WX -> Z
* Definition: Constraint between two sets of attributes from the database - denoted by X -> Y
* Example: in a table containing SSNs and names, it can be said that SSN -> name (name is functionally dependent upon SSN) since a name can be uniquely determined from a SSN. However, name -> SSN is untrue because a person can have the same name, but different SSNs.
* Different types of keys relating to FDs:
	* Superkey: a set of attributes in which all attributes of the schema are functionally dependent.
	* Candidate Key: "A Candidate Key can be any column or a combination of columns that can qualify as unique key in database. There can be multiple Candidate Keys in one table. Each Candidate Key can qualify as Primary Key." (from https://stackoverflow.com/questions/12813363/what-is-the-difference-between-a-candidate-key-and-a-primary-key)

##  Normalization

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
* Types of Views
	* Virtual: Just the query is stored, not the results
	* Materialized: The query is actually constructed and stored
		* In PostgreSQL, materialized views can be refreshed using ```REFRESH MATERIALIZED VIEW```
	* More in-depth analysis/information on StackOverflow: https://stackoverflow.com/questions/93539/what-is-the-difference-between-views-and-materialized-views-in-oracle
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
	* Materialzied view (as discussed above):
	
		```sql
		CREATE MATERIALIZED VIEW viewName AS
			SELECT columns FROM table WHERE some > operators;
		```
* Using views in PostgreSQL
	* Quite easy. Just use it like you would a table:
		
		```sql
		SELECT * FROM viewName;
		```
