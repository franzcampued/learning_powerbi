# Data Modeling Fundamentals
- Tables
- Relationships
- Primary Keys (Primary, Composite, **Surrogate - for datawarehouses**)
- Foreign Keys
- Cardinality

# Set Operations (Vertical and evaluate row through all column values)
- Union All (can include duplicates)
- Union (no duplicates)
- Intersect (look for rows appear on both sets)
- Except (look for only rows from the first set that do not appear in the second set)

# Joins
- Inner Join
- Outer Join
    - Left (left values as base)
    - Right (right values as base)
    - Full 
- Anti Join
    - Left (reverse of right join)
    - Right (reverse of left join)
    - Full (reverse of inner joinn)
- Cross Join (create queries which show possible combinations)

# Join Path Problems
- Loop (having just more than one (e.g 3 columbs) direct relationship between the same two tables.)
This makes the query e.g 3 columns (foreign) should be equal same with the primary which return very few columns

e.g sample ON Clause
DimDate.DateKey = FactResellerSales.DueDateKey AND DimDate.DateKey = FactResellerSales.OrderDateKey AND DimDate.DateKey = FactResellerSales.ShipDateKey

- Chasm trap (The date table has a one-to-many relationship to each of the two sales tables—creating a many-to-one-to-many rela‐ tionship between the two sales tables.)
This can lead to duplication in the 2nd join since 1st join return multiple values that that ON CLAUSE will be using as basis of 2nd join

2023-08-02 2023-08-02 20 2023-08-02 200
2023-08-02 2023-08-02 30 2023-08-02 200 (duplicate)

- Fan trap ()
You can step into a fan trap in situations where you want to aggregate on a value on the “one” side of a relationship, while joining a table on the “many” side of the same relationship

1 100                1 10
1 100 (duplicate)    2 20
2 200                1 30
3 300                1 40