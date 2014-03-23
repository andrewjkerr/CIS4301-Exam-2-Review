# CIS4301 Exam 2 Review

_You can find a collection of my notes from this exam here: https://github.com/andrewjkerr/CIS4301-Exam-2-Review/blob/master/cis4301-exam2-notes.md_

## Topics __NOT__ covered (per chapter)
* Chapter 3
	* Closure Functional Dependencies
	* Set cover
	* BCNF, 4NF, 5NF
* Chapter 5
* Chapter 6
	* Sections 6.6.4 - 6.6.6
* Chapter 7
	* Section 7.4
* Chapter 8
	* Section 8.4
* Chapter 9
	* Sections 9.1 - 9.4
	* Stored procedures (should know how to do that, but not on the quiz!)
	* Sections 9.5 - 9.7

## Topics Covered
* _Disclaimer: these were the topics Christan quickly listed in class. The topics not covered (above) are most likely more accurate._
* __Chapters: Chapter 3, Chapters 5-9__
* Normal forms (1NF-3NF)
	* Functional dependencies
		* The 6 rules (Transitive was specifically mentioned!)
	* Transform it to different normal forms/give suggestions
* Queries
	* Will ask about Group Bys and NULLS (aggregates ignore NULLS)
	* Might ask about Rank()
	* Will not ask about arrays
* Indexes
	* When is it a good idea to use an index?
		* Not probability, but give popular queries then ask to create an Index
* Views
	* Virtual Views versus Materialized Views
		* How are they updated?
* Transactions (Chapter 6)
	* Not really specific, but should know about temporary tables and foreign keys
	* Not serializability
* Keys and constraints (Chapter 7)
	* Foreign keys and check constraints
	* Table level and tuple level
* Triggers
	* Triggers are normally before the modification because it allows the trigger to throw an error and stop the modification, but it's ok for triggers to come afterwards if it's not too much to undo.
* Limits - why are limits bad?
	* Because, if two are equal, one might be excluded!
	* Christan was like "this might be on there since you asked about it"
* Postgres Window Functions

## Good study sources
* Assignment #3
* Exercises from book
* [Ryan Roden-Corrent's notes](https://github.com/murphyslaw480/cis4301-notes) - nicely formatted notes (yay LaTeX!)

## Indexes
* Helps lookup times
	* Creates hash table or a b-tree
		* Hash tables are good for quick lookups and point queries
			* Point queries: I want 1 row out of 1 billion rows
			* Ex - "Give me one specific birthday on Facebook"
		* B-trees are good for range queries
			* Ex - "Give me all of the items greater than some value"
* Why don't we index all the things?
	* MongoDB and a few others are basically indexes (make them quickly!)
	* You can't index everything
		* Hard to constantly update and also take up a lot of space

## Christan's Advice
* Be quick!
* "It won't be murder".......
* Do a ton of practice!
* Get partial credit!