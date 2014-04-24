CakePHP IMAP Custom Datasource
===============================

Custom datasource for CakePHP (2.x) to interact with your mail server with IMAP functionality.

Put the ImapSource.php datasource in your `app/Model/Datasource` directory and just setup like the following.

```php
<?php
//app/Model/CustomEmail.php

class CustomEmail extends AppModel
{
    // Important:
    public $useDbConfig = 'myCustomEmail';
    public $useTable = false;

    // Whatever:
    public $displayField = 'subject';
    public $limit = 10;

    // Semi-important:
    // You want to use the datasource schema, and still be able to set
    // $useTable to false. So we override Cake's schema with that exception:
    function schema($field = false)
    {
        if (!is_array($this->_schema) || $field === true) {
            $db =& ConnectionManager::getDataSource($this->useDbConfig);
            $db->cacheSources = ($this->cacheSources && $db->cacheSources);
            $this->_schema = $db->describe($this, $field);
        }
        if (is_string($field)) {
            if (isset($this->_schema[$field])) {
                return $this->_schema[$field];
            } else {
                return null;
            }
        }
        return $this->_schema;
    }
}
```

Now you need to define credentials in your `app/Config/Database.php` with the following code.

```php
<?php
//app/Config/Database.php

    public $myCustomEmail = array(
        'datasource' => 'ImapSource',
        'server' => 'YourIMAPServerHost',
        'username' => 'IMAPServerUsername',
        'password' => 'yourIMAPPassword',
        'port' => 'IMAPServerPort',
        'ssl' => true,
        'encoding' => 'UTF-8',
        'error_handler' => 'php',
        'auto_mark_as' => array(
            'Seen',
            // 'Answered',
            // 'Flagged',
            // 'Deleted',
            // 'Draft',
        ),
    );
```

Now from your controller just access your email like below:

```php
<?php
// app/Controller/MailsController.php

//your controller codes ...
public function index()
{
  $this->loadModel('CustomEmail');
  $emails = $this->CustomEmail->find('first');
  var_dump($emails); die();
}
```

Now you will get the email. See the debug output. If you have any question please feel free to mail me at shaharia.azam@gmail.com
