**EXC** | DEV | [Documentation](./doc_index.md) | Version 1.0<BR>

# Databases #


## Managed DB Connections ##

The easiest way to get you started using a db is to use managed connections.

You define a managed connection in your `app.php` file under the key `$options['using']`.

```PHP
<?php
$options['using'] = [   //which modules to load
	"exc://exc.storage.db"=>[ //this adds the DB functionality
		"connections"=>[ //here we configure some connections
			"users" => ["driver"=>"mysql", "host"=>"192.168.100.175", "port"=>3306, "dbname"=>"app_users", "username"=>"ctk","password"=>"jose"]
		]
	]
];
```

In the above example we added the connection named `"users"` that points to the MYSQL database `"app_users"`.

## Using a DB Connection ##

The first step is to get an instance of your managed db connection using its name.

```php
<?php
$db = \exc\storage\db::connection("users");
```

The property `$db->ready` is true if your db is connected and ready for you to use. The property `$db->errorMsg` contains an error message provided by the DB driver in case of an error.

```php
<?php
if (!$db->ready){
	error_log("DB Error: " . $db->errorMsg );
	return;
}
```

To insert a record we use `$db->insert($tableName, $values)`. The argument `$values` is an associative array of key-value pairs.

```php
<?php

	$record = [
		"email" => "jose@me.com",
		"fname" => "Jose",
		"lname" => "Cuevas"
	];

	$ok = $db->insert("users", $record);
	if(!$ok){
		error_log("DB Error: " . $db->errorMsg );
		return;
	}

	//Returns the auto generated id used in the last query
	$id = $db->getInsertId();
```

EXC allows you to easily store a PHP array in a `text` column in your table.

```php
<?php

	$record = [
		"email" => "jose@me.com",
		"fname" => "Jose",
		"lname" => "Cuevas"
		"locations" => [  
			"001"=>["last_used"=> "2019/03/25", "access_level"=>5 ],
			"025"=>["last_used"=> "2018/05/20", "access_level"=>2 ]
		]
	];

	$ok = $db->insert("users", $record);
```

We use `$db->get($table, [$limit], [$offset], [$orderBy])` to read from our database.

```php
<?php

	$rs = $db->where("email", "jose@me.com")->get("users");
	if($rs->read()){
		$fname = $rs->fields['fname'];
	}
```

The function `$db->where()` is used to add a criteria.

```php
<?php

	$rs = $db->where("active", 1)->get("users");
	while($rs->read()){
		$user = $rs->fields;
	}
```

You can use `$db->where()` with pairs of column names and values. The first argument is a column name and the following argument is the corresponding criteria. For example:

```php
<?php
	$rs = $db->where("active", 1, "category", "regular")->get("users");
```

By default this will become equalities in your query. A column name may also include an operator at the end to change the comparison operation.

```php
<?php
	$rs = $db->where("rate>=", 5000)->get("users");
```

The criteria for `$db->where()` can also be an array.
```php
<?php
	$findCriteria = [
		"active"=>1,
		"category"=>"regular"
	];
	$rs = $db->where($findCriteria)->get("users");
```

When required you can also specify the criteria as a string. When you do so you are responsible of sanitizing the string to avoid any potential SQL Injections. You also have to ensure that the syntax is compatible with the DB driver that you are using.

```php
<?php
	$findCriteria = "(active=1 and category='regular')";
	$rs = $db->where($findCriteria)->get("users");
```

To update a record we use `$db->update($table, $data)`.

```php
<?php
	$data = [
		"active"=>0,
	];
	$ok = $db->where("id", $id)->update("user", $data);
```
