# SQLZOO Solutions - 2021
Solutions to some of SQLZOO Tutorials and Assesments with tables- 2021
***
<details open="open">
  <summary>Sections</summary>
  <ol>
    <li>
      <a href="#self-join-edinburgh-buses">Self JOIN Edinburgh Buses</a>
    </li>
    <li>
      <a href="#helpdesk">Helpdesk</a>
      <ul>
        <li><a href="#easy-questions">Helpdesk Easy Questions</a></li>
        <li><a href="#medium-questions">Helpdesk Medium Questions</a></li>
      </ul>
    </li>
    <li>
      <a href="#guest-house">Guest House</a>
      <ul>
        <li><a href="#guest-house-easy-questions">Easy Questions</a></li>
        <li><a href="#guest-house-medium-questions">Medium Questions</a></li>
      </ul>
    </li>
      </li>
    <li>
      <a href="#adventure-works">Adventure Works</a>
      <ul>
        <li><a href="#adventure-works-easy-questions">Easy Questions</a></li>
        <li><a href="#adventure-works-medium-questions">Medium Questions</a></li>
      </ul>
    </li>
    <li>
      <a href="#university-timetables-neeps">University Timetables Neeps</a>
      <ul>
        <li><a href="#neeps-easy-questions">Easy Questions</a></li>
        <li><a href="#neeps-medium-questions">Medium Questions</a></li>
      </ul>
    </li>
    <li>
      <a href="#musicians">Musicians</a>
      <ul>
        <li><a href="#musicians-easy-questions">Easy Questions</a></li>
        <li><a href="#musicians-medium-questions">Medium Questions</a></li>
      </ul>
    </li>
    <li>
      <a href="#congestion-charging">Congestion Charging</a>
      <ul>
        <li><a href="#congestion-charging-easy-questions">Easy Questions</a></li>
        <li><a href="#congestion-charging-medium-questions">Medium Questions</a></li>
      </ul>
    </li>
    <li>
      <a href="#white-christmas">White Christmas</a>
    </li>
  </ol>
</details>


***

# _Tutorials_

## Self JOIN Edinburgh Buses

1. How many stops are in the database
```sql
  SELECT COUNT(name)
  FROM stops;
```
  |STOPS|
  
  |--- |
  |246|
2. Find the id value for the stop 'Craiglockhart'
```sql
  SELECT id 
  FROM stops 
  WHERE name = 'Craiglockhart';
```
  |id|
  |--- |
  |53|
3. Give the id and the name for the stops on the '4' 'LRT' service.
```sql
  SELECT stops.id, stops.name 
  FROM stops JOIN route ON stops.id = route.stop 
  WHERE company = 'LRT' 
    AND num = '4'
  ORDER BY pos;
```
  |id|name|
  |--- |--- |
  |19|Bingham|
  |177|Northfield|
  |149|London Road|
  |194|Princes Street|
  |115|Haymarket|
  |53|Craiglockhart|
  |179|Oxgangs|
  |85|Fairmilehead|
  |117|Hillend|
  
### Routes and stops
---

4. The query shown gives the number of routes that visit either London Road (149) or Craiglockhart (53). Run the query and notice the two services that link these stops have a count of 2. Add a HAVING clause to restrict the output to these two routes.
```sql
  SELECT company, num, COUNT(*)
  FROM route 
  WHERE stop = 149 
    OR stop = 53
  GROUP BY company, num
  having count(*) = 2;
```
  |company|num||
  |--- |--- |--- |
  |LRT|4|2|
  |LRT|45|2|
  
 5. Execute the self join shown and observe that b.stop gives all the places you can get to from Craiglockhart, without changing routes. Change the query so that it shows the services from Craiglockhart to London Road.
```sql
  SELECT a.company, a.num, a.stop, b.stop
  FROM route a 
    JOIN route b 
      ON (a.company = b.company 
        AND a.num = b.num)
  WHERE a.stop = 53 
    AND b.stop = 149;
```
  |company|num|stop|stop|
  |--- |--- |--- |--- |
  |LRT|4|53|149|
  |LRT|45|53|149|
  
 6. The query shown is similar to the previous one, however by joining two copies of the stops table we can refer to stops by name rather than by number. Change the query so that the services between 'Craiglockhart' and 'London Road' are shown. If you are tired of these places try 'Fairmilehead' against 'Tollcross'
```sql
  SELECT a.company, a.num, stopa.name, stopb.name
  FROM route a 
	  JOIN route b 
		  ON (a.company = b.company 
			  AND a.num = b.num)
	  JOIN stops stopa 
		  ON (a.stop = stopa.id)
	  JOIN stops stopb 
		  ON (b.stop = stopb.id)
  WHERE stopa.name = 'Craiglockhart' 
	  AND stopb.name = 'London Road';
```
  |company|num|name|name|
  |--- |--- |--- |--- |
  
  |LRT|4|Craiglockhart|London Road|
  |LRT|45|Craiglockhart|London Road|

### Using a self join
---

 7. Give a list of all the services which connect stops 115 and 137 ('Haymarket' and 'Leith')
```sql
  SELECT DISTINCT a.company, a.num
  FROM route a 
    JOIN route b 
      ON (a.company = b.company 
        AND a.num = b.num)
  WHERE (a.stop = 115 
    AND b.stop = 137); 
```
  |company|num|
  |--- |--- |
  |LRT|12|
  |LRT|2|
  |LRT|22|
  |LRT|25|
  |LRT|2A|
  |SMT|C5|

  8. Give a list of the services which connect the stops 'Craiglockhart' and 'Tollcross'
```sql
  SELECT DISTINCT a.company, a.num
  FROM route a 
    JOIN route b 
      ON (a.company = b.company 
        AND a.num = b.num)
    JOIN stops stopa 
      ON (a.stop = stopa.id)
    JOIN stops stopb 
      ON (b.stop = stopb.id)
  WHERE stopa.name = 'Craiglockhart' 
    AND stopb.name = 'Tollcross';
```
  |company|num|
  |--- |--- |
  |LRT|10|
  |LRT|27|
  |LRT|45|
  |LRT|47|

  9. Give a distinct list of the stops which may be reached from 'Craiglockhart' by taking one bus, including 'Craiglockhart' itself, offered by the LRT company. Include the company and bus no. of the relevant services.
```sql
  SELECT DISTINCT stopb.name, b.company, b.num
  FROM route a 
    JOIN route b 
      ON (a.company = b.company 
        AND a.num = b.num)
    JOIN stops stopa 
      ON (a.stop = stopa.id)
    JOIN stops stopb 
      ON (b.stop = stopb.id)
  WHERE stopa.name = 'Craiglockhart' 
```
<details><summary><strong><em>Open to view long table</em></strong></summary>
  
  |name|company|num|
  |--- |--- |--- |
  |Balerno|LRT|47|
  |Balerno Church|LRT|47|
  |Bingham|LRT|4|
  |Brunstane|LRT|45|
  |Canonmills|LRT|27|
  |Canonmills|LRT|47|
  |Cockburn Crescent|LRT|47|
  |Colinton|LRT|10|
  |Colinton|LRT|45|
  |Colinton|LRT|47|
  |Craiglockhart|LRT|10|
  |Craiglockhart|LRT|27|
  |Craiglockhart|LRT|4|
  |Craiglockhart|LRT|45|
  |Craiglockhart|LRT|47|
  |Crewe Toll|LRT|27|
  |Currie|LRT|45|
  |Currie|LRT|47|
  |Duddingston|LRT|45|
  |Fairmilehead|LRT|4|
  |Hanover Street|LRT|27|
  |Hanover Street|LRT|45|
  |Hanover Street|LRT|47|
  |Haymarket|LRT|4|
  |Hillend|LRT|4|
  |Hunters Tryst|LRT|27|
  |Leith|LRT|10|
  |Leith Walk|LRT|10|
  |London Road|LRT|4|
  |London Road|LRT|45|
  |Muirhouse|LRT|10|
  |Newhaven|LRT|10|
  |Northfield|LRT|4|
  |Northfield|LRT|45|
  |Oxgangs|LRT|27|
  |Oxgangs|LRT|4|
  |Princes Street|LRT|10|
  |Princes Street|LRT|4|
  |Riccarton Campus|LRT|45|
  |Silverknowes|LRT|10|
  |Silverknowes|LRT|27|
  |Tollcross|LRT|10|
  |Tollcross|LRT|27|
  |Tollcross|LRT|45|
  |Tollcross|LRT|47|
  |Torphin|LRT|10|
</details> 

  10. Find the routes involving two buses that can go from Craiglockhart to Lochend.
Show the bus no. and company for the first bus, the name of the stop for the transfer,
and the bus no. and company for the second bus.
```sql
  SELECT x.num, x.company, x.name, y.num, y.company 
  FROM 
    (SELECT DISTINCT stopb.name, b.company, b.num
    FROM route a 
      JOIN route b 
        ON (a.company = b.company 
          AND a.num = b.num)
      JOIN stops stopa 
        ON (a.stop = stopa.id)
      JOIN stops stopb 
        ON (b.stop = stopb.id)
    WHERE stopa.name = 'Craiglockhart') x 

    JOIN

    (SELECT DISTINCT stopc.name, c.company, c.num
    FROM route c 
    JOIN route d 
      ON (c.company = d.company 
        AND c.num=d.num)
      JOIN stops stopc ON (c.stop = stopc.id)
      JOIN stops stopd ON (d.stop = stopd.id)
    WHERE stopd.name = 'Lochend') y 

    ON (y.name = x.name) 

  ORDER BY x.num, name, y.num
```
<details><summary><strong><em>Open to view long table</em></strong></summary>
  
  |num|company|name|num|company|
  |--- |--- |--- |--- |--- |
  |10|LRT|Leith|34|LRT|
  |10|LRT|Leith|35|LRT|
  |10|LRT|Leith|87|LRT|
  |10|LRT|Leith|C5|SMT|
  |10|LRT|Princes Street|65|LRT|
  |10|LRT|Princes Street|C5|SMT|
  |27|LRT|Canonmills|34|LRT|
  |27|LRT|Canonmills|35|LRT|
  |27|LRT|Crewe Toll|20|LRT|
  |4|LRT|Haymarket|65|LRT|
  |4|LRT|Haymarket|C5|SMT|
  |4|LRT|London Road|20|LRT|
  |4|LRT|London Road|34|LRT|
  |4|LRT|London Road|35|LRT|
  |4|LRT|London Road|42|LRT|
  |4|LRT|London Road|46A|LRT|
  |4|LRT|London Road|65|LRT|
  |4|LRT|London Road|87|LRT|
  |4|LRT|London Road|87A|LRT|
  |4|LRT|London Road|C5|SMT|
  |4|LRT|Princes Street|65|LRT|
  |4|LRT|Princes Street|C5|SMT|
  |45|LRT|Duddingston|42|LRT|
  |45|LRT|Duddingston|46A|LRT|
  |45|LRT|London Road|20|LRT|
  |45|LRT|London Road|34|LRT|
  |45|LRT|London Road|35|LRT|
  |45|LRT|London Road|42|LRT|
  |45|LRT|London Road|46A|LRT|
  |45|LRT|London Road|65|LRT|
  |45|LRT|London Road|87|LRT|
  |45|LRT|London Road|87A|LRT|
  |45|LRT|London Road|C5|SMT|
  |45|LRT|Riccarton Campus|65|LRT|
  |47|LRT|Canonmills|34|LRT|
  |47|LRT|Canonmills|35|LRT|
</details> 

# _Assesments_

## Helpdesk

### Easy Questions
---

 1. There are three issues that include the words index" and "Oracle". Find the call_date for each of them
```sql
  SELECT DATE_FORMAT(call_date,'%Y-%m-%d %H:%i:%S') AS call_date, call_ref 
  FROM Issue
  WHERE Detail LIKE '%Oracle%' 
  AND Detail LIKE '%index%'	
```
 2. Samantha Hall made three calls on 2017-08-14. Show the date and time for each
```sql
  SELECT DATE_FORMAT(call_date,'%Y-%m-%d %H:%i:%S') AS call_date, First_name, Last_name
  FROM Issue JOIN Caller ON (Issue.caller_id = Caller.caller_id)
  WHERE Caller.first_name = 'Samantha' AND Caller.last_name = 'Hall' AND DATE_FORMAT(call_date,'%Y-%m-%d') = '2017-08-14'
```

 3. There are 500 calls in the system (roughly). Write a query that shows the number that have each status.
```sql
  SELECT status, COUNT(1) AS Volume from Issue
  GROUP BY status
```

 4. Calls are not normally assigned to a manager but it does happen. How many calls have been assigned to staff who are at Manager Level?
```sql
  SELECT COUNT(1) AS mlcc
  FROM Issue 
    JOIN Staff 
      ON (Issue.assigned_to = Staff.staff_code) JOIN Level ON (Staff.level_code = Level.level_code)
  WHERE Manager = 'Y'
```

 5. Show the manager for each shift. Your output should include the shift date and type; also the first and last name of the manager.
```sql
  SELECT DATE_FORMAT(Shift_date,'%Y-%m-%d') AS Shift_date, Shift_type, first_name, last_name
  FROM Shift y 
    LEFT JOIN Staff x  
      ON (y.manager = x.staff_code)
  ORDER BY Shift_date, last_name, shift_type
```

### Medium Questions
---

 6. List the Company name and the number of calls for those companies with more than 18 calls.
```sql
   SELECT Company_name, COUNT(1) as cc
   FROM Issue 
      JOIN Caller 
         ON (Issue.Caller_id = Caller.Caller_id) 
      JOIN Customer ON (Caller.company_ref = Customer.company_ref)
   GROUP BY company_name
   HAVING cc > 18 
```

 7. Find the callers who have never made a call. Show first name and last name
```sql
   SELECT first_name, last_name
   FROM Caller 
      LEFT JOIN Issue 
         ON (Caller.Caller_id = Issue.Caller_id)
   WHERE Call_ref is NULL
```

 8. For each customer show: Company name, contact name, number of calls where the number of calls is fewer than 5
```sql
   SELECT Company_name, first_name, last_name, nc
   FROM (SELECT Company_name, contact_id, COUNT(Issue.call_ref) AS nc
   FROM Caller 
      JOIN Issue 
         ON (Caller.caller_id = Issue.caller_id) 
      JOIN Customer 
         ON (Caller.company_ref = Customer.company_ref)
   GROUP BY Company_name, contact_id
   HAVING nc < 5) AS x
   JOIN Caller AS y 
      ON (x.contact_id = y.caller_id)
```

 9. For each shift show the number of staff assigned. Beware that some roles may be NULL and that the same person might have been assigned to multiple roles (The roles are 'Manager', 'Operator', 'Engineer1', 'Engineer2').
```sql
   SELECT DATE_FORMAT(Shift_date,'%Y-%m-%d') AS Shift_date, Shift_type, COUNT(DISTINCT(role)) AS cw
   FROM
   (SELECT shift_date, shift_type, manager AS role FROM Shift
   UNION
   SELECT shift_date, shift_type, Operator AS role FROM Shift
   UNION
   SELECT shift_date, shift_type, engineer1 AS role FROM Shift
   UNION
   SELECT shift_date, shift_type, engineer2 AS role FROM Shift) AS x
   GROUP BY x.shift_date, x.shift_type
```

 10. Caller 'Harry' claims that the operator who took his most recent call was abusive and insulting. Find out who took the call (full name) and when.
```sql
   SELECT first_name, last_name, call_date 
   FROM 
   (SELECT a.first_name, a.last_name, a.call_date, RANK() OVER (ORDER BY a.call_date desc) posn 
      FROM 
         (SELECT Staff.first_name, Staff.last_name, DATE_FORMAT(call_date,'%Y-%m-%d %H:%i:%S') AS call_date
            FROM Issue 
               JOIN Caller 
                  ON (Issue.caller_id = Caller.caller_id) 
               JOIN Staff 
                  ON (Issue.Taken_by = Staff.staff_code)
            WHERE Caller.First_name = 'Harry') AS a) AS b
   WHERE posn = 1
```

## Guest House

### Guest House Easy Questions
---

 1. Guest 1183. Give the booking_date and the number of nights for guest 1183.
```sql
   SELECT date_format(booking_date, '%Y-%m-%d') AS booking_date, nights 
   FROM booking
   WHERE guest_id=1183
```
 2. When do they get here? List the arrival time and the first and last names for all guests due to arrive ON 2016-11-05, order the output by time of arrival.
```sql
    SELECT arrival_time, first_name, last_name 
   FROM booking 
      JOIN guest 
         ON (booking.guest_id  = guest.id)
   WHERE booking_date = '2016-11-05'
   ORDER BY arrival_time
```
 3. Look up daily rates. Give the daily rate that should be paid for bookings with ids 5152, 5165, 5154 and 5295. Include booking id, room type, number of occupants and the amount.

```sql
   SELECT booking_id, room_type_requested, occupants, amount
   FROM booking 
      JOIN rate 
            ON (booking.room_type_requested=rate.room_type AND occupants = occupancy)
   WHERE booking_id IN ('5152','5165','5154', '5295') 
```
 4. Who’s in 101? Find who is staying in room 101 ON 2016-12-03, include first name, last name and address.

```sql
```
 5. How many bookings, how many nights? For guests 1185 and 1270 show the number of bookings made and the total number of nights. Your output should include the guest id and the total number of bookings and the total number of nights.

```sql
   SELECT guest_id, COUNT(*), sum(nights) 
   FROM booking
   WHERE guest_id IN ('1185', '1270')
   GROUP BY guest_id
```

### Guest House Medium Questions
---

 6. Ruth Cadbury. Show the total amount payable by guest Ruth Cadbury for her room bookings. You should JOIN to the rate table using room_type_requested and occupants.

```sql
   SELECT sum(amount*nights)
   FROM booking 
      JOIN rate 
         ON (room_type_requested = room_type and occupants = occupancy) 
      JOIN guest 
         ON (guest_id = id)
   WHERE first_name='Ruth' AND last_name = 'Cadbury'
```

 7. Including Extras. Calculate the total bill for booking 5346 including extras.

```sql
   SELECT bill1 + extras as total_bill
   FROM 
   (SELECT booking.booking_id, sum(rate.amount*nights) as bill1 FROM booking 
      JOIN rate ON (room_type_requested = room_type and occupants = occupancy) 
      WHERE booking_id='5346' GROUP BY booking_id) as a

   JOIN (SELECT extra.booking_id, sum(extra.amount) as extras FROM extra 
      WHERE extra.booking_id = '5346' GROUP BY booking_id) as b
      
   ON (a.booking_id = b.booking_id)
```

 8. Edinburgh Residents. For every guest who has the word “Edinburgh” in their address show the total number of nights booked. Be sure to include 0 for those guests who have never had a booking. Show last name, first name, address and number of nights. ORDER BY last name then first name.

```sql
   SELECT last_name, first_name, address, IFNULL(sum(nights), 0)
   FROM booking 
      RIGHT JOIN guest 
         ON (guest_id = id)
   WHERE address like '%Edinburgh%'
   GROUP BY last_name, first_name, address
   ORDER BY last_name, first_name
```

 9. How busy are we? For each day of the week beginning 2016-11-25 show the number of bookings starting that day. Be sure to show all the days of the week in the correct order.

```sql
   SELECT DATE_FORMAT(booking_date, '%Y-%m-%d') as i, COUNT(*) as arrivals
   FROM booking
   WHERE booking_date BETWEEN '2016-11-25' AND '2016-12-01'
   GROUP BY i
   ORDER BY i
```

 10. How many guests? Show the number of guests in the hotel ON the night of 2016-11-21. Include all occupants who checked in that day but not those who checked out.

```sql
   SELECT sum(occupants) 
   FROM (SELECT *, date_format(booking_date, '%d') + nights -1 as final_day
      FROM booking
      WHERE booking_date <= '2016-11-21'
      ORDER BY booking_date) as a 
   WHERE a.final_day >= 21
   SELECT
      SUM(occupants)
   FROM
      booking
   WHERE
      booking_date <= '2016-11-21'
         AND DATE_ADD(booking_date, INTERVAL nights DAY) > '2016-11-21';
```

## Adventure Works

### Adventure Works Easy Questions
---

 1. Show the first name and the email address of customer with CompanyName 'Bike World'

```sql
   SELECT FirstName, EmailAddress
   FROM Customer
   WHERE CompanyName = 'Bike World'

```
 2. Show the CompanyName for all customers with an address in City 'Dallas'.

```sql
   SELECT CompanyName
   FROM Customer as a 
      JOIN CustomerAddress as b 
         ON (a.CustomerID=b.CustomerID) 
      JOIN Address as c 
         ON (b.AddressID = c.AddressID)
   WHERE City = 'Dallas'
```
 3. How many items with ListPrice more than $1000 have been sold?

```sql
   SELECT COUNT(*)
   FROM Product as a 
      JOIN SalesOrderDetail as b 
         ON (a.ProductID = b.ProductID)
   WHERE ListPrice > 1000
```
 4. Give the CompanyName of those customers with orders over $100000. Include the subtotal plus tax plus freight.
```sql
   SELECT CompanyName, subtotal, taxamt, freight
   FROM Customer as a 
      JOIN SalesOrderHeader as b 
         ON (a.CustomerID = b.CustomerID)
   WHERE subtotal + taxamt + freight >100000
```
 5. Find the number of LEFT racing socks ('Racing Socks, L') ordered by CompanyName 'Riding Cycles'

```sql
   SELECT sum(orderqty)
   FROM Product as a 
      JOIN SalesOrderDetail as b 
         ON (a.productid = b.productid)
      JOIN SalesOrderHeader as c 
         ON (b.salesorderid = c.salesorderid) 
      JOIN Customer as d 
         ON (c.customerid = d.customerid)
   WHERE a.name = 'Racing Socks, L' and CompanyName = 'Riding Cycles'
```

### Adventure Works Medium Questions
---

 6. A "Single Item Order" is a customer order WHERE ONly ONe item is ordered. Show the SalesOrderID and the UnitPrice for every Single Item Order.


```sql
   SELECT c.salesorderid, sum(unitprice) as unitprice
   FROM Customer as a 
      JOIN SalesOrderHeader as b 
         ON (a.customerid = b.customerid) 
      JOIN SalesOrderDetail as c 
         ON (b.salesorderid = c.salesorderid) 
   GROUP BY c.salesorderid
   HAVING sum(orderqty) = 1
```

 7. WHERE did the racing socks go? List the product name and the CompanyName for all Customers who ordered ProductModel 'Racing Socks'.


```sql
   SELECT a.name, companyname
   FROM ProductModel as x 
      JOIN Product as a 
         ON (x.productmodelid = a.productmodelid)
      JOIN SalesOrderDetail as b 
         ON (a.productid = b.productid)
      JOIN SalesOrderHeader as c 
         ON (b.salesorderid = c.salesorderid) 
      JOIN Customer as d 
         ON (c.customerid = d.customerid)
   WHERE x.name = 'Racing Socks'
   ORDER BY a.name, companyname
```

 8. Show the product descriptiON for culture 'fr' for product with ProductID 736.


```sql
   SELECT descriptiON
   FROM Product as a
      JOIN ProductModel as b 
         ON (a.productmodelid = b.productmodelid)
      JOIN ProductModelProductDescription as c 
         ON (b.productmodelid = c.productmodelid)
      JOIN ProductDescription as d 
         ON (c.productdescriptionid = d.productdescriptionid)
   WHERE productid = 736 and culture = 'fr'
```

 9. Use the SubTotal value in SaleOrderHeader to list orders FROM the largest to the smallest. For each order show the CompanyName and the SubTotal and the total weight of the order.


```sql
   SELECT b.companyname, subtotal, sum(weight*orderqty) as total_weight
   FROM SalesOrderHeader as a 
      JOIN Customer as b 
         ON (a.customerid = b.customerid)
      JOIN SalesOrderDetail as c 
         ON (a.salesorderid = c.salesorderid)
      JOIN Product as d 
         ON (c.productid = d.productid)
   GROUP BY a.salesorderid, b.companyname, subtotal
   ORDER BY subtotal desc
```

 10. How many products in ProductCategory 'Cranksets' have been sold to an address in 'London'?

```sql
   SELECT sum(orderqty)
   FROM ProductCategory as a 
      JOIN Product as b 
         ON (a.productcategoryid = b.productcategoryid)
      JOIN SalesOrderDetail as c 
         ON (b.productid = c.productid)
      JOIN SalesOrderHeader as d 
         ON (c.salesorderid = d.salesorderid)
      JOIN Address as e 
         ON (d.billtoaddressid = e.addressid)
   WHERE city = 'London' and a.name = 'Cranksets'
```

## University Timetables Neeps

### Neeps Easy Questions
---

 1. Give the room id in which the event co42010.L01 takes place.

```sql
   SELECT room
   FROM event
   WHERE id = 'co42010.L01'
```
 2. For each event in module co72010 show the day, the time and the place.

```sql
   SELECT id as event, dow, tod, room
   FROM event
   WHERE modle = 'co72010'

```
 3. List the names of the staff who teach on module co72010.

```sql
   SELECT DISTINCT name
   FROM staff as a 
      JOIN teaches as b 
         on (a.id = b.staff) 
      JOIN event as c 
         on (b.event = c.id)
   WHERE modle = 'co72010'
```
 4. Give a list of the staff and module number associated with events using room cr.132 on Wednesday, include the time each event starts.

```sql
   SELECT name, modle, tod
   FROM staff as x 
      JOIN teaches as a 
         on (x.id = a.staff)
      JOIN event as b 
         on (a.event = b.id)
   WHERE room = 'cr.132' and dow = 'Wednesday'
```
 5. Give a list of the student groups which take modules with the word 'Database' in the name.

```sql
   SELECT a.id, d.name
   FROM student as a 
      JOIN attends as b 
         on (a.id = b.student)
      JOIN event as c 
         on (b.event = c.id)
      JOIN modle as d 
         on (c.modle = d.id)
   WHERE d.name like '%Database%'
   GROUP BY a.id, d.name
```

### Neeps Medium Questions
---

 6. Show the 'size' of each of the co72010 events. Size is the total number of students attending each event.

```sql
   SELECT b.event, sum(sze)
   FROM student as a 
      JOIN attends as b 
         on (a.id = b.student)
      JOIN event as c 
         on (b.event = c.id)
   WHERE modle = 'co72010'
   GROUP BY b.event
```

 7. For each post-graduate module, show the size of the teaching team. (post graduate modules start with the code co7).

```sql
   SELECT modle, COUNT(DISTINCT(staff))
   FROM event as a
      JOIN teaches as b 
         on (a.id = b.event)
   WHERE modle like 'co7%'
   GROUP BY modle
```

 8. Give the full name of those modules which include events taught for fewer than 10 weeks.

```sql
   SELECT DISTINCT c.name
   FROM event as a 
      JOIN occurs as b 
         on (a.id = b.event)
      JOIN modle as c 
         on (a.modle = c.id)
   GROUP BY a.id, c.name
   HAVING COUNT(week) < 10
```

 9. Identify those events which start at the same time as one of the co72010 lectures.

```sql
   SELECT id
   FROM (SELECT * FROM event WHERE not modle = 'co72010') as a
      JOIN
      (SELECT dow, tod
      FROM event 
      WHERE modle = 'co72010') as b 
         on (a.tod =b.tod and a.dow=b.dow)
```

 10. How many members of staff have contact time which is greater than the average?

```sql
   SELECT COUNT(*) as num_staff FROM (SELECT staff FROM 
   (SELECT staff, sum(duration) as c_time
   FROM event as a 
      JOIN teaches as b 
         on (a.id =b.event)
   GROUP BY staff) as x
   WHERE c_time > (SELECT avg(y.c_time) FROM (SELECT staff, sum(duration) as c_time
   FROM event as a 
      JOIN teaches as b 
         on (a.id =b.event)
   GROUP BY staff) as y)) as z
```

## Musicians

### Musicians Easy Questions
---

 1. Give the organiser's name of the concert in the Assembly Rooms after the first of Feb, 1997.

```sql
   SELECT m_name
   FROM concert as a 
      JOIN musician as b 
         on (a.concert_orgniser = b.m_no)
   WHERE concert_venue = 'Assembly Rooms' and date_format(con_date,'%Y-%m') > '1997-02'

```
 2. Find all the performers who played guitar or violin and were born in England.

```sql
   SELECT m_name
   FROM performer as a
      JOIN musician as b 
         on (a.perf_is = b.m_no)
      JOIN place as c 
         on (b.born_in = c.place_no)
   WHERE (instrument = 'violin' or instrument = 'guitar') and place_country = 'England'

```
 3. List the names of musicians who have conducted concerts in USA together with the towns and dates of these concerts.

```sql
   SELECT DISTINCT m_name, place_town, date_format(con_date, '%Y-%m-%d') as date
   FROM performance
      JOIN concert 
         on (performed_in =concert_no)
      JOIN musician 
         on (conducted_by = m_no)
      JOIN place 
         on (place_no=concert_in)
   WHERE place_country = 'USA'
```
 4. How many concerts have featured at least one composition by Andy Jones? List concert date, venue and the composition's title.

```sql
   SELECT date_format(con_date,'%Y-%m-%d') as date, concert_venue, c_title
   FROM musician
      JOIN composer 
         on (m_no = comp_is)
      JOIN has_composed 
         on (comp_no=cmpr_no)
      JOIN composition 
         on (cmpn_no=c_no)
      JOIN performance 
         on (c_no=performed)
      JOIN concert 
         on (performed_in = concert_no)
   WHERE m_name = 'Andy Jones'
```
 5. List the different instruments played by the musicians and avg number of musicians who play the instrument.

```sql
   SELECT instrument, num_mus/(SELECT sum(num_mus) FROM (SELECT instrument, COUNT(m_name) as num_mus
   FROM musician
      JOIN performer 
         on (m_no=perf_is)
   GROUP BY instrument) as b) as average_num_mus
   FROM (SELECT instrument, COUNT(m_name) as num_mus
   FROM musician
      JOIN performer 
         on (m_no=perf_is)
   GROUP BY instrument) as a
```

### Musicians Medium Questions
---

 6. List the names, dates of birth and the instrument played of living musicians who play a instrument which Theo also plays.

```sql
   SELECT m_name, date_format(born,'%Y-%m-%d') as date_birth , instrument
   FROM musician 
      JOIN performer 
         on (m_no = perf_is)
   WHERE instrument in
      (SELECT instrument
      FROM musician 
         JOIN performer on (m_no = perf_is)
      WHERE m_name like '%Theo%') and m_name not like '%Theo%' and died is null
```

 7. List the name and the number of players for the band whose number of players is greater than the average number of players in each band.

```sql
   SELECT band_name, COUNT(*) as num
   FROM musician
      JOIN performer 
         on (m_no = perf_is)
      JOIN plays_in 
         on (perf_no=player)
      JOIN band 
         on (band_id=band_no)
   GROUP BY band_name
   HAVING num > (SELECT avg(num) FROM (SELECT band_name, COUNT(*) as num
   FROM musician
      JOIN performer on (m_no = perf_is)
      JOIN plays_in on (perf_no=player)
      JOIN band on (band_id=band_no)
   GROUP BY band_name) as a) 
```

 8. List the names of musicians who both conduct and compose and live in Britain.

```sql
   SELECT DISTINCT m_name
   FROM musician
      JOIN composer 
         on (m_no = comp_is)
      JOIN has_composed 
         on (comp_no=cmpr_no)
      JOIN performance 
         on (m_no=conducted_by)
      JOIN place 
         on (living_in = place_no)
   WHERE place_country in ('England', 'Scotland')
```

 9. Show the least commonly played instrument and the number of musicians who play it.

```sql
   SELECT instrument, COUNT(m_name) as num
   FROM musician
      JOIN performer 
         on (m_no = perf_is)
   GROUP BY instrument
   HAVING num = (SELECT min(num) FROM (SELECT instrument, COUNT(m_name) as num
   FROM musician
   JOIN performer on (m_no = perf_is)
   GROUP BY instrument) as a) 
```

 10. List the bands that have played music composed by Sue Little; Give the titles of the composition in each case.

```sql
   SELECT band_name, c_title
   FROM performance 
      JOIN band 
         on (gave = band_no)
      JOIN composition 
         on (performed = c_no)
      JOIN has_composed 
         on (cmpn_no=c_no)
      JOIN composer 
         on (cmpr_no=comp_no)
      JOIN musician 
         on (m_no = comp_is)
   WHERE m_name = 'Sue Little'
   ORDER BY band_name
```

## Congestion Charging

### Congestion Charging Easy Questions
---

 1. Show the name and address of the keeper of vehicle SO 02 PSP.


```sql
   SELECT name, address
   FROM keeper as a
      JOIN vehicle as b 
         on (a.id=b.keeper)
   WHERE b.id = 'SO 02 PSP'

```
 2. Show the number of cameras that take images for incoming vehicles.

```sql
   SELECT COUNT(*) as num
   FROM camera
   WHERE perim = 'IN'

```
 3. List the image details taken by Camera 10 before 26 Feb 2007.

```sql
   SELECT *
   FROM image
   WHERE camera = 10 and date_format(whn, '%Y-%m-%d') < '2007-02-26'
```
 4. List the number of images taken by each camera. Your answer should show how many images have been taken by camera 1, camera 2 etc. The list must NOT include the images taken by camera 15, 16, 17, 18 and 19.

```sql
   SELECT camera, COUNT(*) as num_images
   FROM image
   WHERE camera NOT BETWEEN 15 and 19
   GROUP BY camera
```
 5. A number of vehicles have permits that start on 30th Jan 2007. List the name and address for each keeper in alphabetical order without duplication.

```sql
   SELECT DISTINCT name, address
   FROM permit as a 
      JOIN vehicle as b 
         on (a.reg=b.id)
      JOIN keeper as c 
         on (c.id = b.keeper)
   WHERE date_format(sdate, '%Y-%m-%d') = '2007-01-30'
   ORDER BY name
```

### Congestion Charging Medium Questions
---

 6. List the owners (name and address) of Vehicles caught by camera 1 or 18 without duplication.

```sql
   SELECT DISTINCT name, address
   FROM image as a
      JOIN vehicle as b 
         on (a.reg=b.id)
      JOIN keeper as c 
         on (b.keeper = c.id)
   WHERE camera = 18 or camera = 1
```

 7. Show keepers (name and address) who have more than 5 vehicles.

```sql
   SELECT name, address
   FROM keeper as a 
      JOIN vehicle as b 
         on (a.id =b.keeper)
   GROUP BY name, address
   HAVING COUNT(b.id) > 5
```

 8. For each vehicle show the number of current permits (suppose today is the 1st of Feb 2007). The list should include the vehicle.s registration and the number of permits. Current permits can be determined based on charge types, e.g. for weekly permit you can use the date after 24 Jan 2007 and before 02 Feb 2007.

```sql
   SELECT reg, sum(case
   WHEN chargetype = 'Daily' then sDate = '2007-02-01'
   WHEN chargetype = 'Weekly' then sdate > '2007-01-24' and sdate < '2007-02-02'
   WHEN chargetype = 'Monthly' then  DATE_ADD(sdate, INTERVAL 1 month) >=  '2007-02-01'
   WHEN chargetype = 'Annual' then  DATE_ADD(sdate, INTERVAL 1 year) >=  '2007-02-01'
   end) as currpermits
   FROM permit
   GROUP BY reg
```

 9. Obtain a list of every vehicle passing camera 10 on 25th Feb 2007. Show the time, the registration and the name of the keeper if available.

```sql
   SELECT date_format(whn, '%Y-%m-%d %H:%i:%s') as time, reg, name
   FROM image as a 
      JOIN vehicle as b 
         on (a.reg = b.id)
      LEFT JOIN keeper as c 
         on (b.keeper = c.id)
   WHERE a.camera = 10 and date_format(whn, '%Y-%m-%d') = '2007-02-25'
```

 10. List the keepers who have more than 4 vehicles and one of them must have more than 2 permits. The list should include the names and the number of vehicles.

```sql
   SELECT name, COUNT(DISTINCT(b.id)) as num_vehic
   FROM keeper as a 
      JOIN vehicle as b 
         on (a.id = b.keeper)
   GROUP BY name
   HAVING num_vehic > 4 and name in (SELECT x.name FROM (SELECT name, reg, COUNT(reg) as num_permits
   FROM permit as a
      JOIN vehicle as b 
         on (b.id = a.reg)
      JOIN keeper as c 
         on (c.id = b.keeper) 
   GROUP BY name, reg) as x
   WHERE num_permits > 2)
```

# _Challenges_

## White Christmas

 1. The units are 10th of a degree Celcius. The columns are yr and dy for year and day of month. The next twelve columns are for January through to December. Show the average daily temperature for August 10th 1964
```sql
   SELECT m8/10 as temp_c FROM hadcet
     WHERE yr=1964 AND dy=10

```
 2. Charles Dickens is said to be responsible for the tradition of expecting snow at Christmas Daily Telegraph. Show the temperature on Christmas day (25th December) for each year of his childhood. He was born in February 1812 - so he was 1 (more or less) in December 1812. Show the twelve temperatures.
```sql
   SELECT yr-1811 as age, m12/10 as temp FROM hadcet
     WHERE yr BETWEEN 1812 and 1812+11 AND dy=25 

```
 3. We declare a White Christmas if there was a day with an average temperature below zero BETWEEN 21st and 25th of December. For each age 1-12 show which years were a White Christmas. Show 'White Christmas' or 'No snow' for each age.

```sql
   SELECT yr-1811 as age, 
      CASE WHEN MIN(m12/10)<0 THEN 'White Christmas'
      ELSE 'No Snow' END as type_christmas FROM hadcet
     WHERE yr BETWEEN 1812 and 1812+11 and dy BETWEEN 21 and 25
   GROUP BY age
```
 4. A person's White Christmas COUNT (wcc) is the number of White Christmases they were exposed to as a child (BETWEEN 3 and 12 inclusive assuming they were born at the beginning of the year and were about 1 year old on their first Christmas). Charles Dickens's wcc was 8. List all the years and the wcc for children born in each year of the data set. only show years WHERE the wcc was at least 7.

```sql
   SELECT y.yr as yob, sum(type) as wcc FROM (SELECT yr, case WHEN min(m12/10) <= 0 then 1 else 0 end as type FROM hadcet
   WHERE dy BETWEEN 21 and 25
   GROUP BY yr) as x
   CROSS JOIN (SELECT yr FROM hadcet
   WHERE dy BETWEEN 21 and 25
   GROUP BY yr) as y
   WHERE x.yr BETWEEN y.yr+2 and y.yr+11
   GROUP BY y.yr
   HAVING wcc>=7
```
