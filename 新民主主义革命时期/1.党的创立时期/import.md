# event.csv

根据event.csv简易建立Person结点（只含name）

```cypher
LOAD CSV WITH HEADERS FROM "file:///event.csv" AS line
with case right(line.mainCharacters,1) when '等' then left(line.mainCharacters,size(line.mainCharacters)-1) else line.mainCharacters end as linemc//将最后的字符'等'去除
unwind split(linemc,",") as x
merge (:Person{name:split(x,":")[0]})
```

根据event.csv建立Event结点

```cypher
LOAD CSV WITH HEADERS FROM "file:///event.csv" AS line
create (p:Event{name:line.name,datetime:line.datetime,duration:line.duration,location:line.location,summary:line.summary})
```

为Event建立索引以及unique

```cypher
create constraint on (p:Event)
assert p.name is UNIQUE
```

根据event.csv建立Event-Person关系

```cypher
LOAD CSV WITH HEADERS FROM "file:///event.csv" AS line
with case right(line.mainCharacters,1) when '等' then left(line.mainCharacters,size(line.mainCharacters)-1) else line.mainCharacters end as linemc, line//将最后的字符'等'去除
unwind split(linemc,",") as mc
with split(mc,":") as arr, mc, line.name as eventTitle
with case size(arr) when 2 then toInt(arr[1]) else 5 end as priority,arr[0] as personName,eventTitle
match (p:Person{name:personName}), (e:Event{name:eventTitle})
merge (p)-[:RELATION{type:'参与',priority:priority}]->(e)
```

# meeting.csv

根据meeting.csv简易建立Person结点（只含name）

```cypher
LOAD CSV WITH HEADERS FROM "file:///meeting.csv" AS line
with case right(line.mainCharacters,1) when '等' then left(line.mainCharacters,size(line.mainCharacters)-1) else line.mainCharacters end as linemc, line//将最后的字符'等'去除
unwind split(linemc,",") as x
merge (p:Person{name:split(x,":")[0]})
```

根据meeting.csv建立Meeting结点

```cypher
LOAD CSV WITH HEADERS FROM "file:///meeting.csv" AS line
create (p:Meeting{name:line.name,datetime:line.datetime,duration:line.duration,location:line.location,summary:line.summary})
```

为Meeting建立索引以及unique

```cypher
create constraint on (p:Meeting)
assert p.name is UNIQUE
```

根据meeting.csv建立Meeting-Person关系

```cypher
LOAD CSV WITH HEADERS FROM "file:///meeting.csv" AS line
with case right(line.mainCharacters,1) when '等' then left(line.mainCharacters,size(line.mainCharacters)-1) else line.mainCharacters end as linemc, line//将最后的字符'等'去除
unwind split(linemc,",") as mc
with split(mc,":") as arr, mc, line.name as eventTitle
with case size(arr) when 2 then toInt(arr[1]) else 5 end as priority,arr[0] as personName,eventTitle
match (p:Person{name:personName}), (e:Meeting{name:eventTitle})
merge (p)-[:RELATION{type:'参与',priority:priority}]->(e)
```

#event_event.csv

```cypher
LOAD CSV WITH HEADERS FROM "file:///event_event.csv" AS line
match (e1:Event{name:line.Event}),(e2:Event{name:line.Event2})
merge (e1)-[:RELATION{type:line.relation}]->(e2)
```

# meeting_meeting.csv

```cypher
LOAD CSV WITH HEADERS FROM "file:///meeting_meeting.csv" AS line
match (e1:Meeting{name:line.Meeting}),(e2:Meeting{name:line.Meeting2})
merge (e1)-[:RELATION{type:line.relation}]->(e2)
```

# event_meeting.csv

```cypher
LOAD CSV WITH HEADERS FROM "file:///event_meeting.csv" AS line
match (e1:Event{name:line.Event}),(e2:Meeting{name:line.Meeting})
merge (e1)-[:RELATION{type:line.relation}]->(e2)
```

# movement_event.csv

```cypher
LOAD CSV WITH HEADERS FROM "file:///movement_event.csv" AS line
match (e1:Movement{name:line.Movement}),(e2:Meeting{name:line.Event})
merge (e1)-[:RELATION{type:line.relation}]->(e2)
```

