



Basic Usage
---
Let's decalre a book form and its three inputs with initial values in `init()` method. Remember to inherit the parent class `FormHelper`.

```php
  class BookForm extends FormHelper {
    function init() {
      $this->input("name", "My Book");
      $this->input("desc", "Its Description");
      $this->input("lang", "English");
    }
  }
  $f = new BookForm; // while constructing an object, the init() method will be called automatically.
  $f->renderNameValue("name"); // name="name" value="MyBook"
  $f->renderNameValue("desc"); // name="desc" value="Its Description"
  $f->renderNameValue("lang"); // name="lang" value="English"
```
Hence the code:

```html
	<input id="name" <?php $f->renderNameValue("desc") ?> />
```
will produce

```html
	<input id="name" name="desc" value="Its Description" />
```
The form helper will take care of the production of name-value pair for you.

**Advantage:** it encourages the seperation of UI code (HTML code) and logic code (PHP code) by introducing a form helper as the mediator between them.

Change the value of an input
---

```php
	$f->set("desc","New Description");
	$f->renderNameValue("desc"); // name="desc" value="New Description"
```

Seperate the render of name and value of input
---
Use `$f->get("input-name")` in some occasions like textarea which put its value in the inner HTML

```html
	<textarea name="desc">
	<?php echo $f->get("desc");?>
	</textarea>
```


Preparation to the Toturial Below
--

A form class describes its 3 inputs: name, desc, lang.

```php
class BookForm extends FormHelper {
  function init() {
    $this->input("name");
    $this->input("desc");
    $this->input("lang");
  }
}
```

A domain class represents its table in database

```php
class Book {
  public name;
  public desc;
  public lang;
  public static insert(Book b) {
    $db->insert(
      "INSERT INTO books (name,desc,lang) VALUES (?,?,?)",
      [$b->name,$b->desc,$b->lang]
    );	
  }
  public static find_by_id($id) {
    return $db->fetchOneObj("SELECT * FROM books WHERE id = ?",[$id]);
  }
}
```

Read data from POST array and display it on an HTML form
---

php code:

```php

$f->importFromArray($_POST);
```

html code:

```html
<form action="<?php $PHP_SELF ?>"  method="POST" >

  <input type="text" <?php $f->renderNameValue("name"); ?> />
  <textarea name="desc"><?php echo $f->get("desc");?></textarea>
  <input type="text" <?php $f->renderNameValue("lang"); ?> />
</form>

```

Read data from database and display it on an HTML form
---

php code:

```php
	$book = Book::find_by_id(3);
	$f->importFromModel($book);
```

the HTML code is the same as shown above.

Save data from POST array to database
---

```php

$f = new BookForm();

if($f->isCompleted($_POST)) {
  $f->importFromArray($_POST);
  $book = $f->exportToModel(Book);
  Book::insert($book);
}
```

Methods of FormHelper
---

**Input decleartion**

|Method|Description|
|------|-----------|
|`input($name,$value="")`||

**Basic value getter / setter and contain?**

|Method|Description|
|------|-----------|
|`get($name)`||
|`getInt($name)`||
|`contains($input)`||
|`getValues()`||
|`setValues($arr)`||

**about HTML form rendering**

|Method|Description|
|------|-----------|
|`renderNameIntValue($name)`||
|`renderNameValue($name) `||

**Array importing / examination**

|Method|Description|
|------|-----------|
|`isCompleted($arr)`||
|`isPartialCompleted($arr)`||
|`importFromArray($arr)`||

**Domain model importing / exporting**

|Method|Description|
|------|-----------|
|`exportToModel($className, $form_prefix = "", $form_suffix = "")`||
|`importFromModel($model, $form_prefix = "", $form_suffix = "")`||

