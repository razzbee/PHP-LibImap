# php-libimap
php-libimap is a PHP imap library in pure OOP, the idea is to make the native php_imap functions easier to use with performance tunning in mind.



###Installation
using composer , run :
composer require razzbee/php-libimap


###Usage 

####Initialise class

```php
$imap = new \PHPLibImap\IMAP;
```

**connect an imap server**


```php
$imap->connectServer([
   'host' => 'localhost',
   'port' => 993,
   'imap_username' => 'username@domain.tld',
   'password' => 'secret',
   'mailbox_name' => 'INBOX',
   'enableSSL' => true,
]);
```

**host** : Your Imap mail server hostname , example : localhost , 19.10.10.2 , imap.domain.tld or domain.tld 


**port** : your mailserver's imap's port , the default for secure connection is 993 and no secure connection is 143


**imap_username** : The full qualified email address of mail account , example: you@example.com 


**password** : your mail account password.


**mailbox_name** : (optional) , the mailbox to open on an initial connection, if non is specified, INBOX will be used.


**enableSSL** : (optional) , This is for those using a custom port, so that the library will know if the port needs a secure connection 



###Switching Mailbox 
After Connecting to the server, the optional mailbox_name  supplied in the connection parameters is opened automatically , you can change or switch mailbox using 

```php
$imap-> switchMailBox('mailbox_name');
```
the mailbox_name must be one of the names of the mailfolders or boxes in your mailserver , example: INBOX , Drafts , Spam  or Trash 

###Fetch MailBoxes (Mail Folders) 

You can also list available mail boxes in mailserver 

```php
$mailBoxes = $imap->getMailBoxes($pattern);
```

$pattern: (optional) This is either \* or % where :


\* Means the library should fetch all the mailboxes including top level mail boxes and sub folders


**%**  Means the library should fetch all sub folders in the current mailbox 


read more here : http://php.net/manual/en/function.imap-getmailboxes.php




###Get Info About a mailbox 

```php
$mailBoxInfo = $imap->getMailBoxInfo($forceNew=false);
```

**$forceNew** : (optional) The mailbox info is always prefetched and kept, but if you want a fresh copy of the info to be refetched , set this to true ..



***Count Total Recent Messages**
```php
$totalRecent = $imap->getMailBoxInfo()->recent;
```

###Count Total Unread Messages In the MailBox 

```php
$totalMsgNo = $imap->getTotalMessages();

//or

$totalMsgNo = $imap->getMailBoxInfo()->messages;
```

###Fetch Mails From a MailBox 
This library has many ways to fetch mails from a mailbox , we have also optimized this particular feature for speed and performance ..

####Fetch MailBox Items overview By UID 

```php
$mails = $imap->fetchMailBoxItems("mailbox_name")
              ->select(uid1,uid2,uid3,uid4...uidN)
			  ->getResults();
```


**mailbox_name** : optional mailbox name , if none is supplied, the current opened mailboxed will be used 

**select(uid1,uid2, ... )**: Select mail headers by the uid or the integer id(s) of the mail(s) , you must provide a valid uid else an exception will be generated 


**getResults** : Generate  and get the results 


####Fetch Mail Overview using range 
```php
$mails = $imap->fetchMailBoxItems("mailbox_name")
              ->range(1,10)
			  ->orderById('desc')
			  ->getResults();
```

**range(arg1,arg2)** : The range of mail uid , the above example will pull mailbox item overview info of uid 1 to 10 , This method assumes you already know your range already..

**orderById** : Order the results by the uid , the default is DESC (Descending Order), this means the latest mails overview info will be shown first


####Fetch Mail Overview using limit and Offset 
Like mysql limit and offset , this library supports the limit and offset using the limit($limit,$offset) method..
```php
$mails = $imap->fetchMailBoxItems("mailbox_name")
              ->limit(1,10)
			  ->orderById('desc')
			  ->getResults();
```
***limit($limit,$offset=0)*** : The limit method accepts two arguments the limit and offset ,internally , the limits and offsets are been calculated automatically to ranges , this method is safer to use if you have no idea about the range values or number of data in the mailbox. The offset is optional and defaults to 0
