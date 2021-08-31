# SQLZOO Solutions---2021
Solutions to some of SQLZOO Tutorials and Assesments with tables- 2021

<details open="open">
  <summary>Sections</summary>
  <ol>
    <li>
      <a href="#self-join-edinburgh-buses">Self JOIN Edinburgh Buses</a>
      <ul>
        <li><a href="#routes-and-stops">Routes and stops</a></li>
        <li><a href="#using-a-self-join">Using a self join</a></li>
      </ul>
    </li>
    <li>
      <a href="#getting-started">Getting Started</a>
      <ul>
        <li><a href="#prerequisites">Prerequisites</a></li>
        <li><a href="#installation">Installation</a></li>
      </ul>
    </li>
    <li><a href="#usage">Usage</a></li>
    <li><a href="#roadmap">Roadmap</a></li>
    <li><a href="#contributing">Contributing</a></li>
    <li><a href="#license">License</a></li>
    <li><a href="#contact">Contact</a></li>
    <li><a href="#acknowledgements">Acknowledgements</a></li>
  </ol>
</details>

## Sections:
1. [Self JOIN: Edinburgh Buses](#self-join:-edinburgh-buses)

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
<details open="open"><summary>Open to view long table</summary>
  
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
<details open="open"><summary>Open to view long table</summary>
  
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
