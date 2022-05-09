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

