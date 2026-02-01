# Relational Model and Keys

## Part 1- English to Schema
```sql
- Student [__sID (integer)__, name (string), GPA (real)]
```
-----------------------------------------
| sID        | name     | gpa          |
|------------|----------|--------------|
| 1          | Harry    | 3.5          |
| 2          | Hermione | 4.0          |
| 3          | Ron      | 4.0          |
| 4          | Malfoy   | 3.9          |
-----------------------------------------

- Part 1(a): Grocery Store--
> Product inventory sheet / "stock keeping unit" = sku
```sql
Product [__sku (string)__, product_name (string), price (real), quantity (integer)]
```
-------------------------------------------------------
| sku        | product_name | price       | quantity  |
|------------|--------------|-------------|-----------|
| 12345      | Apple        | 0.50        | 100       |
| 67890      | Banana       | 0.30        | 150       |
| 11223      | Orange       | 0.70        | 200       |
-------------------------------------------------------

- Part 1(b): Grocery store + Aisle
> Product shelf display table
```sql
Product [__sku (string)__, product_name (string), price (real)]
```
--------------------------------------------------
| sku        | product_name | price               |
|------------|--------------|---------------------|
| 12345      | Apple        | 0.50                |
| 67890      | Banana       | 0.30                |
| 11223      | Orange       | 0.70                |
--------------------------------------------------
```sql
Aisle [__aisle_id (integer)__, aisle_name (string)]
```
-------------------------------
| aisle_id | aisle_name        |
|----------|-------------------|
| 1        | Fresh Produce     |
| 2        | Dairy             |
| 3        | Bakery            |
-------------------------------
```sql
ProductAisle [__sku (string)__, __aisle_id (integer)__]
```
----------------------------------
| sku        | aisle_id          |
|------------|-------------------|
| 12345      | 1                 |
| 67890      | 1                 |
| 11223      | 1                 |
| 12345      | 2                 |
---------------------------------

- Part 1(c): Car dealership
> Car shop management table
```sql
Car [__vin (string)__, make (string), model (string), year (integer), color (string)]
```
----------------------------------------------------------------------------------
| vin               | make    | model  | year | color   |
|-------------------|---------|--------|------|---------|
| 1HGCM82633A123456 | Honda   | Accord | 2003 | Blue    |
| 1HGCM82633A654321 | Toyota  | Camry  | 2010 | Red     |
| 1HGCM82633A789012 | Ford    | Focus  | 2015 | Black   |
----------------------------------------------------------------------------------
```sql
Salesperson [__ssn (string)__, name (string)]
```
-------------------------
| ssn          | name    |
|--------------|---------|
| 123-45-6789  | John    |
| 987-65-4321  | Jane    |
| 555-55-5555  | Bob     |
-------------------------
```sql
CarSalesperson [__vin (string)__, __ssn (string)__]
```
------------------------------------
| vin                 | ssn          |
|---------------------|--------------|
| 1HGCM82633A123456   | 123-45-6789  |
| 1HGCM82633A654321   | 987-65-4321  |
| 1HGCM82633A789012   | 555-55-5555  |
------------------------------------

## Part2 - SQL Table Declarations

-- Part 2(a): Patrons
```sql
CREATE TABLE Patrons (
  CardNum integer PRIMARY KEY,
  Name string
);
```
-- Part 2(b): Phones
```sql
CREATE TABLE Phones (
  CardNum integer,
  Phone string,
  PRIMARY KEY (CardNum, Phone),
  FOREIGN KEY (CardNum) REFERENCES Patrons(CardNum)
);
```
-- Part 2(c): CheckedOut 
```sql
CREATE TABLE CheckedOut (
  CardNum integer,
  Serial integer,
  PRIMARY KEY (Serial),
  FOREIGN KEY (CardNum) REFERENCES Patrons(CardNum),
  FOREIGN KEY (Serial) REFERENCES Inventory(Serial)
);
```
-- Part 2(d): Inventory
```sql
CREATE TABLE Inventory (
  Serial integer PRIMARY KEY,
  ISBN string,
  FOREIGN KEY (ISBN) REFERENCES Titles(ISBN)
);
```
-- Part 2(e): Titles
```sql
CREATE TABLE Titles (
  ISBN string PRIMARY KEY,
  Title string,
  Author string
);
```


## Part 3 - Fill in Tables
```sql
Car [__vin (string)__, make (string), model (string), year (integer), color (string)]
```
-----------------------------------------------------------------
| vin                | make    | model    | year | color     |
|--------------------|---------|----------|------|-----------|
| 1HGCM82633A123456  | Honda   | Accord   | 2003 | Blue      |
| 1HGCM82633A654321  | Toyota  | Camry    | 2010 | Red       |
| 1HGCM82633A789012  | Ford    | Focus    | 2015 | Black     |
| 1HGCM82633A456789  | Toyota  | Tacoma   | 2008 | Red       |
| 1HGCM82633A987654  | Toyota  | Tacoma   | 1999 | Green     |
| 1HGCM82633A678901  | Tesla   | Model 3  | 2018 | White     |
| 1HGCM82633A876543  | Subaru  | WRX      | 2016 | Blue      |
| 1HGCM82633A345678  | Ford    | F150     | 2004 | Red       |
-----------------------------------------------------------------

```sql
Salesperson [__ssn (string)__, name (string)]
```
-------------------------
| ssn          | name    |
|--------------|---------|
| 123-45-6789  | John    |
| 987-65-4321  | Jane    |
| 555-55-5555  | Bob     |
| 666-66-6666  | Arnold  |
| 777-77-7777  | Hannah  |
| 888-88-8888  | Steve   |
-------------------------

```sql
CarSalesperson [__vin (string)__, __ssn (string)__]
```
--------------------------------
| vin                | ssn          |
|--------------------|--------------|
| 1HGCM82633A123456  | 123-45-6789  |
| 1HGCM82633A654321  | 987-65-4321  |
| 1HGCM82633A789012  | 555-55-5555  |
| 1HGCM82633A456789  | 666-66-6666  |
| 1HGCM82633A987654  | 666-66-6666  |
| 1HGCM82633A456789  | 777-77-7777  |
| 1HGCM82633A345678  | 777-77-7777  |
| 1HGCM82633A678901  | 888-88-8888  |
--------------------------------

## Part 4 - Keys and Superkeys


| Attribute Sets  | Superkey? | Proper Subsets                            | Key? |
|-----------------|-----------|-------------------------------------------|------|
| {A1}            | No        | {}                                        | No   |
| {A2}            | No        | {}                                        | No   |
| {A3}            | No        | {}                                        | No   |
| {A1, A2}        | Yes       | {A1}, {A2}                                | Yes  |
| {A1, A3}        | No        | {A1}, {A3}                                | No   |
| {A2, A3}        | No        | {A2}, {A3}                                | No   |
| {A1, A2, A3}    | Yes       | {A1, A2}, {A1, A3}, {A2, A3} ,{A1, A2, A3}| No   |

## Part 5 - Abstract Reasoning
- If {x} is a superkey, then any set containing x is also a superkey.
  > True: If {x} is a superkey, then any set containing {x} is also a superkey.
- If {x} is a key, then any set containing x is also a key.
  > False: If {x} is a key, then any set containing {x} is not necessarily a key.
- If {x} is a key, then {x} is also a superkey.
  > True: If {x} is a key, then {x} is also a superkey.
- If {x, y, z} is a superkey, then one of {x}, {y}, or {z} must also be a superkey.
  > False: If {x, y, z} is a superkey, it does not mean one of {x}, {y}, or {z} must be a superkey.
- If an entire schema consists of the set {x, y, z}, and if none of the proper subsets of {x, y, z} are keys, then {x, y, z} must be a key.
  > True: If the entire schema consists of {x, y, z} and none of the proper subsets are keys, then {x, y, z} must be a key.
