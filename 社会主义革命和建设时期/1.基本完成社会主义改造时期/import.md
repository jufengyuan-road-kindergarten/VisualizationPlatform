# organization.csv

根据organization.csv简易建立Person结点（只含name）

```cypher
LOAD CSV WITH HEADERS FROM "file:///organization.csv" AS line
with case right(line.mainCharacters,1) when '等' then left(line.mainCharacters,size(line.mainCharacters)-1) else line.mainCharacters end as linemc//将最后的字符'等'去除
unwind split(linemc,",") as x
merge (:Person{name:split(x,":")[0]})
```

根据organization.csv建立Organization结点

```cypher
LOAD CSV WITH HEADERS FROM "file:///event.csv" AS line
create (p:Organization{name:line.name,summary:line.summary})
```

为Organization建立索引以及unique

```cypher
create constraint on (p:Organization)
assert p.name is UNIQUE
```

根据event.csv建立Organization-Person关系

```cypher
LOAD CSV WITH HEADERS FROM "file:///organization.csv" AS line
with case right(line.mainCharacters,1) when '等' then left(line.mainCharacters,size(line.mainCharacters)-1) else line.mainCharacters end as linemc, line//将最后的字符'等'去除
unwind split(linemc,",") as mc
with split(mc,":") as arr, mc, line.name as organizationTitle
with case size(arr) when 2 then toInt(arr[1]) else 5 end as priority,arr[0] as personName,organizationTitle
match (p:Person{name:personName}), (e:Organization{name:organizationTitle})
merge (p)-[:RELATION{type:'属于',priority:priority}]->(e)
```

