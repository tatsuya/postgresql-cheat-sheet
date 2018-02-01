# PostgreSQL

<!-- MarkdownTOC autolink="true" bracket="round" depth="3" -->

- [General](#general)
	- [Checking version](#checking-version)
	- [Log into the prompt interface](#log-into-the-prompt-interface)
	- [View Roles](#view-roles)
	- [List all databases](#list-all-databases)
	- [Configure output mode](#configure-output-mode)
	- [Display information about current database connection](#display-information-about-current-database-connection)
- [SQL](#sql)
	- [SET ROLE](#set-role)
	- [INSERT](#insert)
	- [DELETE](#delete)
	- [UPDATE](#update)
	- [SELECT](#select)
	- [JOIN](#join)
		- [Self-join](#self-join)
	- [LIKE](#like)

<!-- /MarkdownTOC -->

## General

### Checking version

```
psql --version
```

### Log into the prompt interface

```
psql -d db_name
```

### View Roles

Get a list of roles.

```
\du
```

Get your own roles.

```
\du your_name
```

Get your own roles with description.

```
\du+
```

### List all databases

```
\l
```

with additional detail

```
\l+
```

### Configure output mode

Toggle output mode:

```
\x
```

Or call it explicitly:

```
\x on
\x off
\x auto
```

`auto` automatically adjusts output type depending on the screen size.

### Display information about current database connection

Check current connected database and user.

```
\conninfo
```

## SQL

### [SET ROLE](https://www.postgresql.org/docs/current/static/sql-set-role.html)

```sql
set role role_name;
```

### INSERT

```sql
INSERT INTO users (id, first_name, last_name, account_name, gender)
VALUES (1, 'Tatsuya', 'Oiwa', 'tatsuyaoiw', 'm');
```

### DELETE

```sql
DELETE FROM users WHERE id = 1;
```

Delete all users.

```sql
DELETE FROM users;
```

### UPDATE

```sql
UPDATE users SET account_name = 'foo' WHERE id = 1;
```

```sql
UPDATE users AS u1 SET account_name = u2.account_name FROM (VALUES
  (1, 'foo'),
  (2, 'bar'),
  (3, 'baz')
) AS b2(id, account_name) WHERE u1.id = u2.id;
```

### SELECT

```sql
SELECT * FROM companies WHERE user_id = (SELECT id FROM users WHERE acccount_name = 'tatsuyaoiw');
```

Count the number of duplicates per column value:

```sql
SELECT gender, COUNT(*) as c FROM users GROUP BY gender ORDER BY c DESC;
```

List of unique first names:

```sql
SELECT count(*) FROM (SELECT DISTINCT(first_name) FROM users) AS u;
```

### [JOIN](https://www.postgresql.org/docs/current/static/queries-table-expressions.html)

The words `INNER` and `OUTER` are optional in all forms. `INNER` is the default; `LEFT`, `RIGHT`, and `FULL` imply an outer join.

#### Self-join

See: [How to select the first/least/max row per group in SQL](https://www.xaprb.com/blog/2006/12/07/how-to-select-the-firstleastmax-row-per-group-in-sql/)

> One common solution is a so-called self-join.

```sql
SELECT e2.sensor_id, e2.event_type, e2.value FROM
  (SELECT sensor_id, event_type, max(time) as time FROM events GROUP BY sensor_id, event_type) AS e1
JOIN events AS e2 ON e1.sensor_id = e2.sensor_id AND e1.event_type = e2.event_type AND e1.time = e2.time
ORDER BY e2.sensor_id, e2.event_type
```

### LIKE

- `_`: any single character
- `%`: any sequence of zero or more characters.

Some examples:

```
'abc' LIKE 'abc'    true
'abc' LIKE 'a%'     true
'abc' LIKE '_b_'    true
'abc' LIKE 'c'      false
```

`LIKE` pattern matching always covers the entire string. Therefore, to match a sequence anywhere within a string, the pattern must start and end with a percent sign.

To match a literal underscore or percent sign without matching other characters, the respective character in pattern must be preceded by the escape character. The default excape character is the backslash `\`.
