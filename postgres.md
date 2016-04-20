
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

## Quotation marks ##
##### Do not use quotation marks for identifiers, unless you are defining a new type (which needs camelCase notation).
```sql
-- bad
UPDATE "foo" SET "bar" = x;

-- good
UPDATE foo SET bar = x;

-- good
CREATE TYPE foo AS (
  "fooId" integer,
  "title" text,
  "parentId" integer
);
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

## Tables names ##
##### Use singular words for table names.
```sql
--bad
SELECT foo FROM bars;

-- good
SELECT foo FROM bar;
```

##### Use the `join_table_name_with_table_name` pattern to define JOIN tables' name.
```sql
CREATE TABLE foo(foo_id serial);
CREATE TABLE bar(bar_id serial);

-- wrong
CREATE TABLE foo_bar(foo_id serial, bar_id serial);

-- good
CREATE TABLE join_foo_with_bar(foo_id serial, bar_id serial);
```

##### Use the `ref_table_name` pattern to define REFERENCE tables' name.
```sql
-- bad
CREATE TABLE nationality(nationality_id serial, title text);

-- good
CREATE TABLE ref_nationality(nationality_id serial, title text);
```

##### Use the `rt_type` pattern to define new types.
```sql
-- bad
CREATE TYPE foo AS ("barBaz" integer);

-- good
CREATE TYPE rt_foo AS ("barBaz" integer);
```

## Column names ##
##### Prefix table id with table name.
```sql
-- bad
CREATE TABLE foo(id serial);

-- good
CREATE TABLE foo(foo_id serial);
```

##### Prefix denormalized column with `denorm_`.
```sql
CREATE TABLE person (
  birth_date timestamp,
  denorm_age integer,
);
```

## Table constraints ##
##### `NULL` and `NOT NULL` constraints must be declared into the table declaration without explicit column reference.
```sql
-- bad
CREATE TABLE foo(bar integer, NOT NULL (bar));

-- good
CREATE TABLE foo(bar integer NOT NULL);
```

##### As they are unique, `PRIMARY KEY` constraints must be named based on the `pkey_*table*` pattern.
```sql
-- bad
ALTER TABLE foo ADD CONSTRAINT foo_foo_id_pkey PRIMARY KEY (foo_id);

-- good
ALTER TABLE foo ADD CONSTRAINT pkey_foo PRIMARY KEY (foo_id);
```

##### Other constraints must be explicitly named based on the `*key*_*table*_on_*column*(_and_*column*)` pattern.
Keys are: `fkey` for `FOREIGN KEY`, `key` for `UNIQUE`, `check` for `CHECK`.
```sql
CREATE TABLE foo(foo_id serial, bar_id integer);

-- bad
ALTER TABLE foo ADD CONSTRAINT foo_bar_id_fkey FOREIGN KEY bar_id REFERENCES bar(bar_id);

-- good
ALTER TABLE foo ADD CONSTRAINT fkey_foo_on_bar_id FOREIGN KEY bar_id REFERENCES bar(bar_id);

-- good
ALTER TABLE foo ADD CONSTRAINT check_foo_on_foo_id_and_bar_id CHECK (foo_id > bar_id);
```

## Indexes ##
##### Indexes must be explicitly named based on the `idx_*table*_on_*column*(_and_*column*)` pattern.
```sql
-- bad
CREATE INDEX ON foo(bar, baz);

-- good
CREATE INDEX idx_foo_on_bar_and_baz ON foo(bar, baz);
```

## Statements ##
##### Always specify the needed columns.
```sql
-- bad
SELECT * FROM foo;

-- good
SELECT foo_id, bar_id, creation_date FROM foo;
```

##### Specify table columns when inserting even if it is set to default.
```sql
CREATE TABLE foo(a, b default 0, c, d default 1);

-- bad
INSERT INTO foo VALUES (1, 2, 3, 4);

-- bad
INSERT INTO foo(a, c) VALUES (1, 3);

-- good
INSERT INTO foo(a, b, c, d) VALUES (1, 2, 3, 4);

-- good
INSERT INTO foo(a, b, c, d) VALUES (1, default, 3, default);
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

## Spaces and parentheses ##
##### Do not use space before parentheses when writing one-line code unless it is following a keyword.
```sql
-- bad
CREATE TABLE foo ();
INSERT INTO bar (baz) VALUES (1);
INSERT INTO bar(baz) VALUES(1);
CREATE FUNCTION foo_bar (in_a text) ...;

-- good
CREATE TABLE foo();
INSERT INTO bar(baz) VALUES (1);
CREATE FUNCTION foo_bar(in_a text) ...;
```

##### Use space before parentheses and comma dangle at line end when using multi-line code.
```sql
-- bad
CREATE table foo
(
  a,
  b,
  c
);

-- bad
CREATE table foo (
  a
  , b
  , c
);

-- good
CREATE TABLE foo (
  a,
  b,
  c
);
```

## Functions indentation ##
##### Respect proper function indentation, name the `$$` block and use multi-line parameters only if needed.
```sql
-- good
CREATE FUNCTION foo(in_a text, in_b integer)
RETURNS integer
VOLATILE SECURITY INVOKER
AS $body$
  SELECT foo_id FROM foo WHERE bar = in_a AND baz = in_b;
$body$ LANGUAGE sql;

-- if needed
CREATE FUNCTION foo (
  in_a text,
  in_b integer
)
RETURNS integer
VOLATILE SECURITY INVOKER
AS $body$
  SELECT foo_id FROM foo WHERE bar = in_a AND baz = in_b;
$body$ LANGUAGE sql;
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
