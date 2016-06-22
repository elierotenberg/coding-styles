
## Uppercase and lowercase ##
##### Use uppercase for statements, operators and keywords.
```sql
-- bad
SELECT * FROM foo order by bar;

-- good
SELECT * FROM foo ORDER BY bar;
```

##### Use lowercase for functions.
```sql
-- bad
SELECT COUNT(*) FROM foo;

-- good
SELECT count(*) FROM foo;
```

##### Use lowercase for types.
```sql
-- bad
CREATE TABLE foo(bar INTEGER, baz TEXT);

-- good
CREATE TABLE foo(bar integer, baz text);
```

## Operators ##
##### Use same difference operator as other languages.
```sql
-- bad
SELECT baz FROM foo WHERE bar <> baz;

-- good
SELECT bar FROM foo WHERE bar != baz;
```

##### Do not use double pipes for concatenation to avoid `NULL` values.
```sql
-- bad
SELECT 'foo' || 'bar';

-- good
SELECT concat('foo', 'bar');
```

##### Do not use casting operator when not needed.
```sql
-- bad
SELECT cast('bar' AS integer);

-- good
SELECT 'bar'::integer;

-- only for immutable functions
CREATE FUNCTION foo()  RETURNS text IMMUTABLE AS $$
  SELECT integer 'bar';
$$ LANGUAGE sql ;
```

## Arrays ##
##### Use proper array declaration.
```sql
-- bad
SELECT '{1, 2, 3 + 4}';

-- bad
SELECT array[1,2,3+4];

-- good
SELECT ARRAY[1, 2, 3 + 4];
```

## Functions parameters ##
##### Use explicit parameters (with `in_` or `out_` prefix) instead of positional parameters.
```sql
-- bad
CREATE FUNCTION foo(text) RETURNS text AS $$
  SELECT $1;
$$ LANGUAGE sql;

-- bad
CREATE FUNCTION foo(bar text) RETURNS text AS $$
  SELECT bar;
$$ LANGUAGE sql;

-- good
CREATE FUNCTION foo(in_bar text) RETURNS text AS $$
  SELECT in_bar;
$$ LANGUAGE sql;
```

##### Parameters with a default value must be declared with the `DEFAULT` keyword.
```sql
-- bad
CREATE FUNCTION foo(in_bar text = 'bar') RETURNS text AS $$
  SELECT in_bar;
$$ LANGUAGE sql;

-- good
CREATE FUNCTION foo(in_bar text DEFAULT 'bar') RETURNS text AS $$
  SELECT in_bar;
$$ LANGUAGE sql;
```

##### Always specify parameters' name on non-standard function calls.
```sql
CREATE FUNCTION foo(in_a integer, in_b integer, in_c integer) RETURNS text AS $$
  SELECT in_a;
  SELECT in_b;
  SELECT in_c;
$$ LANGUAGE sql;

-- bad
SELECT foo(1, 2, 3);

-- good
SELECT foo(in_a := 1, in_b := 2, in_c := 3);

-- if needed
SELECT foo(in_c := 3, in_a := 1, in_b := 2);
```

##### Do not use `NATURAL JOIN` and `JOIN USING`, [it is unreliable](http://www.databasesoup.com/2013/08/fancy-sql-monday-on-vs-natural-join-vs.html?showComment=1376400799327#c2740364122038525231). Use instead `JOIN ON`.
```sql
-- bad
SELECT baz FROM foo NATURAL JOIN bar;

-- bad
SELECT baz FROM foo JOIN bar USING(foo_id);

-- good
SELECT baz FROM foo JOIN bar ON foo.foo_id = bar.foo_id;
```

##### Do not use unnecessary `INNER` and `OUTER` statements when using `JOIN`
```sql
-- bad
SELECT baz FROM foo INNER JOIN bar ON ...;

-- good
SELECT baz FROM foo JOIN bar ON ...;
```

##### Do not use `NOT IN`, [it never matches if there is but a single `NULL` in the list](http://stackoverflow.com/a/2246793/2554269).
```sql
-- bad
SELECT foo_id FROM foo WHERE foo_id NOT IN (SELECT foo_id FROM bar);

-- good
SELECT foo_id FROM foo WHERE NOT EXISTS (SELECT foo_id FROM bar);
```

##### Separate columns and their aliases
```sql
-- bad
SELECT f.bar FROM foo f;

-- good
SELECT f.bar FROM foo AS f;
```

## Queries indentation ##
##### Respect proper query indentation. Each statements must be right-aligned for the same depth. Each depth must be aligned one space behind its parent and is independent in terms of statements' right-alignment.
```sql
-- good
SELECT foo_id
  FROM foo
 WHERE foo_id IN (
         SELECT foo_id
           FROM bar
       );

-- good
  SELECT f.data,
         f.item,
         f.category
    FROM foo AS f
    JOIN bar AS b
      ON f.id = b.id
   WHERE f.data > b.data
     AND f.item > 0
GROUP BY f.category
  HAVING count(*) > 0
ORDER BY f.rank
   LIMIT 10;

-- good
WITH foo_with AS (
    SELECT name,
           sum(price) AS total_price,
           product
      FROM foo
  GROUP BY name
),
bar_with AS (
  SELECT name
    FROM foo_with
   WHERE total_foo > (
           SELECT total / 10
             FROM bar
         )
);

-- good
ALTER TABLE foo
        ADD  CONSTRAINT fkey_foo_on_bar_id
            FOREIGN KEY (bar_id)
             REFERENCES bar(bar_id),
        ADD CONSTRAINT check_foo_on_baz
                 CHECK (baz > 10);
```
