# class 2 advanced sql

## window function </br>
func() over(partition by _ order by _)</br>
reference:https://zhuanlan.zhihu.com/p/390381181</br>

移动平均</br>
```
avg(score) over(partition by class order by score rows 2 preceding) as current_avg
```
lag</br>
```
lag(score,1) over(partition by class order by score) as lag_score
```


## Correlated subqueries
Correlated subqueries are used for row-by-row processing. Each subquery is executed once for every row of the outer query.</br>
https://blog.csdn.net/qq_24099547/article/details/118734973


## common table expression</br>
https://www.postgresql.org/docs/current/queries-with.html</br>

**Recursive CTE**</br>
A recursive CTE is a CTE that references itself. A recursive CTE must contain a **UNION ALL** statement and, to be recursive, have a second query definition that references the CTE itself.</br>

A useful example is this query to find all the direct and indirect sub-parts of a product(tree-travelsal).</br>
https://learnsql.com/blog/sql-recursive-cte/</br>
其中的最短路径问题：graph min-distance：</br>
用edges来写无向图的边，用instr来判定无环图
```
WITH RECURSIVE edges AS (
  SELECT id, neighbor, value FROM graph
  UNION ALL
  SELECT neighbor, id, value 
  FROM graph
), cte(id,neighbor,path,distance) as(
  select id,neighbor,cast(id||'->'||neighbor as char(100)) as path,value from edges where id = 'A'
  UNION ALL
  SELECT c.id,d.neighbor,cast(path||'->'||d.neighbor as char(100)),c.distance+d.value
  from cte c
  inner join edges d
  on d.id = c.neighbor
  and instr(path,d.neighbor)<=0
  )
 select id,neighbor,path,min(distance) from cte group by neighbor;
```

## sqlite mannual
https://sqlite.org/cli.html#getting_started


# class 3 Database storage
**buffer pool**： between hard disk and execution engine</br>
<img src="https://github.com/samanthajiang/DATABASE-SYSTEMS-CMU-15-445-645/blob/main/buffer%20pool.png" width="700" height="350" /></br>
**mmap**：why not using os mmap to manage the memory？because the os has no idea about what query is doing,which page should be removed</br>
<img src="https://github.com/samanthajiang/DATABASE-SYSTEMS-CMU-15-445-645/blob/main/mmap.png" width="700" height="350" /></br>
</br>
**Two main problems for database storage:**</br>
Problem #1: How the DBMS represents the database in files on disk **->this class**</br>
Problem #2: How the DBMS manages its memory and moves data back-and-forth from disk.</br>

**Manage database in files on disk**</br>
**1. File Storage**: </br>
The <ins>storage manager</ins> is responsible for maintaining a database's files</br>
It organizes the files as a collection of pages.</br>
→ Tracks data read/written to pages.</br>
→ Tracks the available space</br>

A <ins>page</ins>(database page:512B-16KB,但一般atomic write是4KB) is a fixed-size block of data. Each page is given a unique identifier.</br>
→ The DBMS uses an <ins>indirection layer</ins> to <ins>map</ins> page IDs to physical locations.</br>
<img src="https://github.com/samanthajiang/DATABASE-SYSTEMS-CMU-15-445-645/blob/main/heap_file.png" width="700" height="350" /></br>


**2.Page Layout**: </br>
<img src="https://github.com/samanthajiang/DATABASE-SYSTEMS-CMU-15-445-645/blob/main/page_header.png" width="700" height="350" /></br>

<img src="https://github.com/samanthajiang/DATABASE-SYSTEMS-CMU-15-445-645/blob/main/slotted_pages.png" width="700" height="350" /></br>
<img src="https://github.com/samanthajiang/DATABASE-SYSTEMS-CMU-15-445-645/blob/main/record_id.png" width="700" height="350" /></br>

**3.Tuple Layout**: </br>
A tuple is essentially a sequence of bytes.</br>
It's the job of the DBMS to interpret those bytes into attribute types and values.</br>
<img src="https://github.com/samanthajiang/DATABASE-SYSTEMS-CMU-15-445-645/blob/main/tuple.png" width="700" height="280" /></br>
</br>
Prejoin(denormalize) of tuples with related key at the same place to save I/O time: </br>
<img src="https://github.com/samanthajiang/DATABASE-SYSTEMS-CMU-15-445-645/blob/main/foregin_key.png" width="300" height="200" />
<img src="https://github.com/samanthajiang/DATABASE-SYSTEMS-CMU-15-445-645/blob/main/prejoin.png" width="580" height="200" /></br>
