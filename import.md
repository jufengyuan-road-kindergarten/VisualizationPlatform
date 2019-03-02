# character1.csv

根据event.csv简易建立Person结点

```cypher
LOAD CSV WITH HEADERS FROM "file:///character1.csv" AS line
create (:Person{name:line.name,birthdate:line.birthdate,deathdate:line.deathdate,pname:line.pname,othername:line.othername,wordming:line.wordming,birthplace:line.birthplace,achievement:line.achievement})
```

建立索引和unique

```cypher
create constraint on (p:Person)
assert p.name is UNIQUE
```

