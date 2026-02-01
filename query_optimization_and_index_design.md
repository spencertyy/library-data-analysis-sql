# Indexes
Yuyao Tu
# Part 1 - Selecting Indexes
For each situation described, specify the minimal set of additional secondary indexes you should add to the table(s) to efficiently support all the queries, and briefly explain your reasoning. If no additional indexes are needed, write "None" for your answer. Assume all databases are very large.

Do not add any unnecessary indexes. Do not use indexes that are more complex than needed.

Recall that the primary key already has a primary index, but assume that we have NOT specified any foreign key constraints, so there won't be any indices automatically provided for them.
## A database contains the following table for former-employee records:

| eID (int) (primary key) |	Start Date (Date) |	End Date (Date) |
| ----------------------- | ----------------- | --------------- |

**There are two common queries performed on this table:**
* Find all employees that started after a certain date
    ``` SQL
    CREATE INDEX idx_start_date ON former_employee_records(Start_Date);
    ```
    `An index on StartDate allows efficient querying based on the start date.`

* Find all employees that started on a certain date, and worked until at least another certain date
    ``` SQL
    CREATE INDEX idx_start_end_date ON former_employee_records(StartDate, EndDate);
    ```
    `A composite index on StartDate and EndDate facilitates efficient searches using both columns.`

## A database contains the following table for tracking student grades in classes

| studentID (int) (primary key)	| className (varchar(10)) (primary key) | Grade (char(1)) |
| ----------------------------- | ------------------------------------- | --------------- |

This table only tracks whole letter grades, no '-' or '+' modifiers. Assume many students can take a class, and a student can take many classes. The primary key is created in the order (studentID, className).

**The common queries performed on this table are:**

* Get all students with a grade better than 'B'
* Get all classes where any student earned a grade worse than 'D'

    ```SQL
    CREATE INDEX idx_grade ON student_grades(Grade);
    ```
    `An index on Grade enables fast retrieval of students with grades above a certain level.`

    ` The previously created index on Grade covers this query as well.`

Using the same grade database, but now the common queries are:

* Get all classes ordered by class name
    ```SQL
    CREATE INDEX idx_class_name ON student_grades(className);
    ```
    ` An index on className allows efficient ordering of classes by name.`
* Get all students who earned an 'A' in a certain class

    ` None. The existing index on className and Grade covers this query.`

**Queries on the chess database**

Assume the only existing indexes are the primary index on each table (despite whatever indexes are on the actual tables. Remember, we're assuming there are NOT indexes created for foreign key constraints). Also assume the actual table sizes in the Chess database (ie big). Common queries are:

* select Name from Players where Elo >=2050;
    ```SQL
    CREATE INDEX idx_elo ON Players(Elo);
    ```
    ` An index on Elo speeds up the retrieval of players with a specified Elo rating. ` 
* select Name, gID from Players join Games where pID=WhitePlayer;
    ```SQL
    CREATE INDEX idx_white_player ON Games(WhitePlayer);
    ```
    ` An index on WhitePlayer in the Games table allows efficient joining with the Players table.` 

**Queries on the public Library database**

Assume the only existing indexes are the primary index on each table (despite whatever indexes are on the actual tables). Common queries are:

* select * from Inventory natural join CheckedOut;

    ` Both Inventory and CheckedOut tables have Serial as their primary key, which is already indexed. No additional indexes are needed. `

**More library queries:**

Assume the only existing indexes are the primary index on each table (despite whatever indexes are on the actual tables). Common queries are:

* select * from Inventory natural join CheckedOut where CardNum=2;
    ```SQL
    CREATE INDEX idx_checkedout_cardnum ON CheckedOut(CardNum);
    ```
    `An index on CardNum in the CheckedOut table allows efficient querying of records with a specific CardNum.`
* select * from Patrons natural join CheckedOut;
    ```SQL
    CREATE INDEX idx_checkedout_cardnum ON CheckedOut(CardNum);
    ```
    `An index on CardNum in the CheckedOut table facilitates joining with the Patrons table.`

**Still more library queries**

Assume the only existing indexes are the primary index on each table (despite whatever indexes are on the actual tables). Also assume that the Library has been auto-scaffolded as discussed in class.

```C#
var query =
  from t in db.Titles
  select new
  {
    Title = t.Title,
    Serials = from i in t.Inventory select i.Serial
  };
```
```SQL
CREATE INDEX idx_inventory_isbn ON Inventory(ISBN);
```
`Since Titles and Inventory tables join on ISBN, indexing ISBN in the Inventory table improves join performance.`

# Part 2 - B+ Tree Index Structures

**Students table:**

Consider the students table from #2 in Part 1 above. Assume that an int occupies 4 bytes, and a varchar(10) occupies 10 bytes.

* How many rows of the table can be placed into the first leaf node of the primary index before it will split?
    `4096 / 15 ≈ 273 rows`
    - studentID (int) takes 4 bytes
    - className (varchar(10)) takes 10 bytes
    - Grade (char(1)) takes 1 byte
    - Each row takes 15 bytes. 4096 / 15 ≈ 273 rows per leaf node.

* What is the maximum number of keys stored in an internal node of the primary index? (Remember to ignore pointer space. Remember that internal nodes have a different structure than leaf nodes.)
    `4096 / 14 = 292 keys` 
    - Each key is composed of studentID (4 bytes) and className (10 bytes)
    - Each key is 14 bytes
    - 4096 / 14 ≈ 292 keys per internal node

* What is the maximum number of rows in the table if the primary index has a height of 1? (A tree of height 1 has 2 levels and requires exactly one internal node)
    Since the height is 1, so the number of first leaf node * num of leaf nodes.
    `293 * 273 = 79989 rows`
    - A tree of height 1 has 2 levels: one internal node and several leaf nodes
    - Each internal node can point to 292 child nodes, each leaf node can hold 273 rows
    - Maximum rows = 293 * 273 = 79,989 rows

    
* What is the minimum number of rows in the table if the primary index has a height of 1? (A tree of height 1 has 2 levels). The minimum capacity of a node in a B+ tree is 50%, unless it is the only internal/leaf node. The minimum number of children of a root node is 2.
    `2 * 137 = 274 rows`
    - Minimum capacity of a node in a B+ tree is 50%
    - Each internal node must have at least 2 child nodes, each leaf node must be at least half full
    - Minimum rows per leaf node = 137
    - Minimum rows = 2 * 137 = 274 rows

* If there is a secondary index on Grade, what is the maximum number of entries a leaf node can hold in the secondary index?
    `273 `
    - The secondary index on Grade uses 1 byte.
    - The primary key (studentID and className) uses 14 bytes (4 bytes for studentID and 10 bytes for className).
    - Each entry is 15 bytes total (1 byte for Grade + 14 bytes for the primary key).
Therefore: 4096 bytes/15 bytes/entry=273 entries
- So:The maximum number of entries a leaf node can hold in the secondary index is 273.

**Another table**

Assume that for some table, rows occupy 128 bytes.

* What is the maximum number of leaf nodes in the primary index if the table contains 48 rows? 
    `3`  
    - Each leaf node can hold up to 32 rows (4096 / 128)
    - Minimum fill factor is 50%, so each node must hold at least 16 rows
    - Maximum leaf nodes = 48 / 16 = 3


* What is the minimum number of leaf nodes in the primary index if the table contains 48 rows?
    `2`
    - Each leaf node can hold up to 32 rows
    - 48 / 32 is 1.5
    - Minimum leaf nodes = 48 / 32 = 2
