# SQL Continued
### Yuyao TU
1. Find the name of the patron who has checked out the most books.
 ```sql
SELECT Name
FROM Patrons
NATURAL JOIN (
    SELECT CardNum, COUNT(*) AS BookCount
    FROM CheckedOut
    GROUP BY CardNum
    ORDER BY BookCount DESC
    LIMIT 1
) as Most
 ```
2. Find the Titles of all books that were written by an author whose name starts with 'K'. You can assume author names always start with an uppercase letter
 ```sql
 SELECT Title
FROM Titles
WHERE Author LIKE "%K"
 ```
3. Find the Authors who have written more than one book. Assume that two Authors with the same name are the same Author for this query.
 ```sql
 SELECT Author
FROM Titles
GROUP BY Author
HAVING COUNT(*) > 1
 ```
4. Find the Authors for which the library has more than one book in inventory (this includes multiple copies of the same book). Assume that two Authors with the same name are the same Author for this query.
 ```sql
 SELECT Author
FROM Titles
NATURAL JOIN (
    SELECT ISBN
    FROM Inventory
    GROUP BY ISBN
    HAVING COUNT(*) > 1
) AS MultBooks
 ```
5. The library wants to implement a customer loyalty program based on how many books each patron has checked out. Provide an SQL query that returns the names, number of books they have checked out, and loyalty level of each Patron. The loyalty level should be the string "Platinum" if they have checked out > 2 books, "Gold" if they have 2 books, "Silver" if they have 1 book, and "Bronze" if they have no books. Hint: remember that NULL represents an unknown in SQL (it does not represent 0).
 ```sql
 SELECT Name, NumCheckedOut,
CASE 
    WHEN NumCheckedOut > 2 THEN 'Platinum'
    WHEN NumCheckedOut = 2 THEN 'Gold'
    WHEN NumCheckedOut = 1 THEN 'Silver'
    WHEN NumCheckedOut = 0 THEN 'Bronze'
END AS LoyaltyLevel
FROM (
    SELECT Name, COUNT(Serial) as NumCheckedOut 
    FROM Patrons 
    LEFT JOIN CheckedOut ON CheckedOut.CardNum = Patrons.CardNum
    GROUP BY Name
) AS CheckedInfo;
 ```
