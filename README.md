# JSON-SQL

Node.js library for mapping mongo-style query objects to SQL queries.

This library **is not** a driver, it is only translator, you should use specific driver for your db (for example https://github.com/brianc/node-postgres for PostgreSQL) to execute result query.

## Quick Start

Install it with NPM or add it to your package.json:

``` bash
$ npm install @tg1518/json-sql
```

Then:
查询有字段别名 三种写法 可以调用hasLiteral函数直接用sql语句
``` js
var jsonSql = require('@tg1518/json-sql')();

var sql = jsonSql.build({
	type: 'select',
	table: 'users',
	fields: ['name', 'age'],
	condition: {name: 'Max', id: 6}
});
var sql2 = jsonSql.build({
	type: 'select',
	table: 'users',
	fields: [
	{name:'users.id',alias: 'id'},
	'name', 'age',
	{name:hasLiteral('if(test_paper_user.type="是",4,3)'),alias:"type"}],
	condition: {name: 'Max', id: 6}
});
var sql3 = jsonSql.build({
	type: 'select',
	table: 'users',
	fields: {
	"users.id":"id",
	"name":"name",
	"age":"age",
	[hasLiteral('if(test_paper_user.type="是",4,3)')]:"type"
	},
	condition: {name: 'Max', id: 6}
});

sql.query
// sql string:
// select name, age from users where name = $p1 && id = 6;

sql.values
// hash of values:
// { p1: 'Max' }
```

## Documentation

Documentation is available at the [./docs directory](./docs).

## Examples

__Select with join:__

``` js
var sql = jsonSql.build({
	type: 'select',
	table: 'users',
	join: {
		documents: {
			on: {'user.id': 'documents.userId'}
		}
	}
});

sql.query
// select * from users join documents on user.id = documents.userId;

sql.values
// {}
```

__Insert:__

``` js
var sql = jsonSql.build({
	type: 'insert',
	table: 'users',
	values: {
		name: 'John',
		lastname: 'Snow',
		age: 24,
		gender: 'male'
	}
});

sql.query
// insert into users (name, lastname, age, gender) values ($p1, $p2, 24, $p3);

sql.values
// { p1: 'John', p2: 'Snow', p3: 'male' }
```

__Update:__

``` js
var sql = jsonSql.build({
	type: 'update',
	table: 'users',
	condition: {
		id: 5
	},
	modifier: {
		role: 'admin'
		age: 33
	}
});

sql.query
// update users set role = $p1, age = 33 where id = 5;

sql.values
// { p1: 'admin' }
```

__Remove:__

``` js
var sql = jsonSql.build({
	type: 'remove',
	table: 'users',
	condition: {
		id: 5
	}
});

sql.query
// delete from users where id = 5;

sql.values
// {}
```

For more examples, take a look at the [./docs directory](./docs) or [./tests directory](./tests).

## Tests

Clone repository from github, `cd` into cloned dir and install dev dependencies:

``` bash
$ npm install
```

Then run tests with command:

``` bash
$ gulp test
```

Or run tests coverage with command:

``` bash
$ gulp coverage
```

## License

[MIT](./LICENSE)
