# SQL
## Yuyao Tu
Part 1 - SQL Command Line
- Good 

Part 2 - Creating the Dealership
- is been created in mysql
## Part 3 - Simple Retrieval Queries
1. Get the ISBNs of all books by <Author>
```sql
select ISBN from Titles where Author = 'Kennedy';
+----------------+
| ISBN           |
+----------------+
| 978-0062278791 |
+----------------+
```
2. Get Serial numbers (descending order) of all books by <ISBN>
```sql
select Serial from Inventory where ISBN = '978-0394823379' order by serial DESC
+--------+
| Serial |
+--------+
|   1005 |
|   1004 |
+--------+
```
3.  Get the Serial numbers and ISBNs of all books checked out by <Patron’s name>
```sql
select c.Serial, i.ISBN from CheckedOut c join Inventory i on c.Serial = i.Serial join Patrons p on c.CardNum = p.CardNum where p.Name = 'Joe';
+--------+----------------+
| Serial | ISBN           |
+--------+----------------+
|   1001 | 978-0547928227 |
|   1004 | 978-0394823379 |
+--------+----------------+
```
4.  Get phone number(s) and Name of anyone with <ISBN> checked out
```sql
select ph.phone, pa.Name from Phones ph join Patrons pa on ph.CardNum = pa.CardNum join CheckedOut co on pa.CardNum = co.CardNum join Inventory i on co.Serial = i.Serial where i.ISBN = '978-0394823379';
+----------+------+
| phone    | Name |
+----------+------+
| 555-5555 | Joe  |
| 888-8888 | Dan  |
| 999-9999 | Dan  |
+----------+------+
```
## Part 4 - Intermediate Retrieval Queries

1. Find the Authors of the library's oldest <N> books. Assume the lowest serial number is the oldest book.
```sql
select t.Author from Titles t join Inventory i on t.ISBN = i.ISBN order by i.Serial ASC LIMIT 5;
+----------+
| Author   |
+----------+
| Tolkien  |
| Tolkien  |
| Faulkner |
| Seuss    |
| Seuss    |
+----------+
```
2. Find the name and phone number of the person who has checked out the most recent book. Assume higher serial numbers are newer. Note that this query is not concerned with the absolute highest serial number, it is concerned with the highest one that has been checked out.
```sql
select p.Name, ph.Phone from CheckedOut co join Patrons p on co.CardNum = p.CardNum join Phones ph on p.CardNum = ph.CardNum order by co.Serial DESC LIMIT 1;
+------+----------+
| Name | Phone    |
+------+----------+
| Dan  | 888-8888 |
+------+----------+
```
3. Find the phone number(s) and name of anyone who has checked out any book.
```sql
select distinct p.Name, ph.Phone from CheckedOut co join Patrons p on co.CardNum = p.CardNum join Phones ph on p.CardNum = ph.CardNum;
+------+----------+
| Name | Phone    |
+------+----------+
| Joe  | 555-5555 |
| Dan  | 888-8888 |
| Dan  | 999-9999 |
+------+----------+
```
4. Find the Authors and Titles of the books who have NOT been checked out by anyone. The query should not return duplicates.
 ```sql
select distinct t.Author, t.Title from Titles t left join Inventory i on t.ISBN = i.ISBN left join CheckedOut co on i.Serial = co.Serial where co.Serial is NULL;
+----------+------------------------+
| Author   | Title                  |
+----------+------------------------+
| Tolkien  | The Lord of the Rings  |
| Herbert  | Dune                   |
| Tolkien  | The Hobbit             |
| Simmons  | Hyperion               |
| Simmons  | Endymion               |
| Faulkner | The Sound and the Fury |
+----------+------------------------+
```
## Part 5 - Chess Queries
1. Find the names and IDs of any player with the 10 highest Elo ratings.
 ```sql
 select Name,pID from Players order by Elo DESC LIMIT 10;
 +-------------------------+-------+
| Name                    | pID   |
+-------------------------+-------+
| Carlsen, Magnus         | 37304 |
| Kasparov, Garry         | 41095 |
| Caruana, Fabiano        | 37553 |
| So, Wesley              | 37642 |
| Vachier-Lagrave, Maxime | 38368 |
| Anand, Viswanathan      | 38991 |
| Nakamura, Hikaru        | 37332 |
| Aronian, Levon          | 37821 |
| Kramnik, Vladimir       | 37718 |
| Mamedyarov, Shakhriyar  | 37682 |
+-------------------------+-------+
 ```
2. Find the names and Elo ratings of any player who has ever played a game as black.
 ```sql
 select distinct p.Name, p.Elo from Games g join Players p on g.BlackPlayer = p.pID;
 ```
3. Find the names of any player who has ever won a game as white.
 ```sql
 select distinct p.Name from Games g join Players p on g.WhitePlayer = p.pID where g.Result = 'W';
 ```
4. Find the names of any player who played any games between 2014 and 2018 in Budapest HUN .
 ```sql
 SELECT DISTINCT p.Name
FROM Games g
JOIN Events e ON g.eID = e.eID
JOIN Players p ON g.BlackPlayer = p.pID OR g.WhitePlayer = p.pID
WHERE e.Site = 'Budapest HUN' AND e.Date BETWEEN '2014-01-01' AND '2018-12-31';

 ```
5. Find the Sites and dates of any event in which Garry Kasparov won a game.
 ```sql
SELECT Events.Site, Events.Date 
FROM Events
JOIN Games ON Events.eID = Games.eID
JOIN Players ON (Games.WhitePlayer = Players.pID AND Games.Result = 'W') 
  OR (Games.BlackPlayer = Players.pID AND Games.Result = 'B')
WHERE Players.Name = 'Kasparov, Garry';
+---------------+------------+
| Site          | Date       |
+---------------+------------+
| Batumi GEO    | 2001-09-17 |
| Munich GER    | 2002-11-13 |
| Reykjavik ISL | 2004-03-18 |
+---------------+------------+
 ```
6. Find the names of all opponents of Magnus Carlsen. An opponent is someone who he has played a game against. Hint: Both Magnus and his opponents could play as white or black.
 ```sql
SELECT DISTINCT Opponents.Name 
FROM Players AS Opponents
JOIN Games AS WhiteGames ON Opponents.pID = WhiteGames.BlackPlayer
JOIN Players AS Carlsen ON WhiteGames.WhitePlayer = Carlsen.pID
WHERE Carlsen.Name = 'Carlsen, Magnus'
UNION
SELECT DISTINCT Opponents.Name 
FROM Players AS Opponents
JOIN Games AS BlackGames ON Opponents.pID = BlackGames.WhitePlayer
JOIN Players AS Carlsen ON BlackGames.BlackPlayer = Carlsen.pID
WHERE Carlsen.Name = 'Carlsen, Magnus';

+---------------------+
| Name                |
+---------------------+
| Salgado Lopez, Ivan |
| Predojevic, Borki   |
| Movsesian, Sergei   |
| So, Wesley          |
| Milov, Vadim        |
| Onischuk, Alexander |
| Svidler, Peter      |
| Lagno, Kateryna     |
| Adams, Michael      |
| Gudbrandsen, Gustav |
| Nakamura, Hikaru    |
| Schandorff, Lars    |
| Wang, Yue           |
| Pelletier, Yannick  |
+---------------------+
 ```
