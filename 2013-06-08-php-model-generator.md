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

Firstly, write a file named `problem.schema.php` which contains the following content:

```php
<?php
$table = "Problem, problem";  // <the name of model class>, <the name of table in database>

$columns = <<<EOF
id
name
context
EOF;

$methods = <<<EOF
find by id
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
    var $name;
    var $context;
    
    static function find_by_id($id) {
    	$sql = "SELECT * FROM `problem` WHERE `id` = ?";
        return self::models(DB::instance()->fetch($sql,[$id]));        
    }

	static function insert(Problem $o) {
		$sql = "INSERT INTO `problem` (`id`,`name`,`context`) VALUES (?,?,?);";
		$o->id = DB::instance()->insert($sql,array($o->id,$o->name,$o->context));
		return $o->id;
	}

	static function update(Problem $o) {
		$sql = "UPDATE `problem` SET `name` = ?,`context` = ? WHERE `id` = ?";
		return DB::instance()->execute($sql, array($o->name,$o->context,$o->id));
	}
	/* the following code is omitted*/
}
```
And we can redirect the output to a file instead of the terminal screen by giving a command like this:

```bash
	$ mdlgen problem.schema.php > Problem.php	
```

The model was done for us. Now, let's use it to perform database operations.

