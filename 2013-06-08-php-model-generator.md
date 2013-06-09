PHP Model Generator 
---

**Alpha version**

PHP Model Generator is a command line tool aids to generate a domain model class according to the customized configuration file which user wrote.

**Requirement:** PHP 5.4+ and my [php-pdo-helper](https://github.com/yannbelief/php-pdo-helper)

**License:** Free for commercial and non-commercial use ([Apache License](http://www.apache.org/licenses/LICENSE-2.0.html) or [GPL](http://www.gnu.org/licenses/gpl-2.0.html))

Installation
---
```bash
	$ git clone git@github.com:yannbelief/php-model-generator.git
    $ cd php-model-generator
    $ sudo ./install.sh
```

 Now, model generator is avaiable through `mdlgen` command in your terminal.

Uninstallation
---

```bash
	$ sudo mdlgen-uninstall
```
Quick Start
---


| id     | category               | context  |
| ------ | ------------------ | -------- |
| 1      | bill      | high engery bills   |
| 2      | mirror   |  bathroom mirror broken   |


Firstly, write a file named `problem.schema.php` which contains the following content:

```php
<?php
$table = "Problem, problem";  // <the name of model class>, <the name of table in database>

$columns = <<<EOF
id
category
context
EOF;

$methods = <<<EOF
find 1 by id
find 1 by category
EOF;
?>
```
Then use `mdlgen` command to perform  the generation under a terminal.

```bash
	$ mdlgen problem.schema.php 
```
It will output

```php
class Problem {
	var $id;
    var $category;
    var $context;
    
	static function find_1_by_id($id) {
		$sql = "SELECT  *  FROM `problem`  WHERE `id` = ?";
		return self::model(DB::instance()->fetchOneObj($sql,[$id]));
	}

	static function find_1_by_category($category) {
		$sql = "SELECT  *  FROM `problem`  WHERE `category` = ?";
		return self::model(DB::instance()->fetchOneObj($sql,[$category]));
	}

	static function insert(Problem $o) {
		$sql = "INSERT INTO `problem` (`id`,`category`,`context`) VALUES (?,?,?);";
		$o->id = DB::instance()->insert($sql,array($o->id,$o->category,$o->context));
		return $o->id;
	}

	static function update(Problem $o) {
		$sql = "UPDATE `problem` SET `category` = ?,`context` = ? WHERE `id` = ?";
		return DB::instance()->execute($sql, array($o->category,$o->context,$o->id));
	}
	/* the following code is omitted*/
}
```
And we can redirect the output to a file instead of the terminal screen by giving a command like this:

```bash
	$ mdlgen problem.schema.php > Problem.php	
```

The model was done for us. Next, we are going to use it to perform database operations.

```bash
	$ git clone git@github.com:yannbelief/php-pdo-helper.git
```

```php
<?php
require("php-pdo-helper/db.php");
DB::$dsn = "mysql:host=<host_url>;dbname=<db>";
DB::$account = "<user_name>";
DB::$password = "<password>";

require("Problem.php");
$obj = Problem::find_1_by_name("mirror");
print_r($obj);

/*
Problem Object
(
    [id] => 2
    [category] => mirror
    [context] => bathroom mirror broken 
)
*/

$obj = Problem::find_1_by_id(1);
print_r($obj);

/*
Problem Object
(
    [id] => 1
    [category] => bill
    [context] => high engery bills
)
*/

?>
```
**Insertion**

```php
$pbm = new Problem;
$pbm->category = "window"
$pbm->context = "Moisture on Windows";
Problem::insert($pbm);
echo $pbm->id; // 3
```

**Updating**


```php
$pbm = Problem::find_1_by_id(3);
$pbm->context = "Heavy Dust with Windows";
Problem::update($pbm);
```
More about Selection
---

command  | generated method | form of return
---|---|---
find attr1 | `find_attr1($attr1)` | array of values
find attr1,attr2,... |`find_attr1_and_attr2_...($attr1,$attr2,...)` | array of objects
find | `find()` | array of objects
find by attr1,attr2,...  | `find_by_attr1_and_attr2($attr1,$attr2)` | array of objects
find 1 attr1 by attr2 | `find_1_attr1_by_attr2($attr1,$attr2)`| single value
find 1 attr1,attr2,... by attr_n | `find_1_attr1_and_attr2_..._by_attr_n($attr1,$attr2,...,$attr_n)`| single object
find 1 by attr1 | `find_1_attr1($attr1)`| single object

command  | generated SQL | form of return
---|---|---
find attr1 | `SELECT attr1 FROM tbl ` | array of values
find attr1,attr2,... |`SELECT attr1, attr2,... FROM tbl` | array of objects
find | `SELECT * FROM tbl` | array of objects
find by attr1,attr2,...  | `SELECT * FROM tbl WHERE attr1 = ?, attr2 = ?` | array of objects
find 1 attr1 by attr2 | `SELECT attr1 FROM tbl WHERE attr2 = ?`| single value
find 1 attr1,attr2,... by attr_n | `SELECT attr1, attr2,... FROM tbl WHERE attr_n = ?`| single object
find 1 by attr1 | `SELECT * FROM tbl WHERE attr1 = ?`| single object
