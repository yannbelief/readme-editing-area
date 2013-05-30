
Methods
---

**about HTML form rendering**

|Method|Description|
|------|-----------|
|`renderNameIntValue($name)`||
|`renderNameValue($name) `||

**about domain model importing / exporting**

|Method|Description|
|------|-----------|
|`exportToModel($className, $form_prefix = "", $form_suffix = "")`||
|`importFromModel($model, $form_prefix = "", $form_suffix = "")`||

**about input decleartion**

|Method|Description|
|------|-----------|
|```php input($name,$value="")```||

**about array importing / examination**

|Method|Description|
|------|-----------|
|`isCompleted($arr)`||
|`isPartialCompleted($arr)`||
|`importFromArray($arr)`||

|Method|Description|
|------|-----------|
|`get($name)`||
|`getInt($name)`||
|`contains($input)`||
|`getValues()`||
|`setValues($arr)`||


dd

Toturial Preparation
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
