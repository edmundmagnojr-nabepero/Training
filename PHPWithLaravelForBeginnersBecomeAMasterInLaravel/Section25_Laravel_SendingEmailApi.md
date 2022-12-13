# Section 25: Laravel - Sending Email / API

### Section Intro with Edwin Diaz

### New laravel installation
```bash
$ laravel new mail
$ cd mail
$ git init
$ git add .
$ git commit -m "initial commit"
$ git remote add origin https://github.com/edmundmagnojr-nabepero/mail.git
$ git push -u origin master
```
### Overview on Mailgun

### Email Verification

### Configuration
```php
#set in env
MAIL_MAILER=mailgun
MAILGUN_DOMAIN=
MAILGUN_SECRET=

#set in mail.php
'from' => [
        'address' => env('MAIL_FROM_ADDRESS', 'example@email.com'),
        'name' => env('MAIL_FROM_NAME', 'Example'),
    ],
```

### Sending email part 1 - route
```php
Route::get('/', function () {
    $data = [
        'title'=>'Hi student I hope you like the course',
        'content'=>'This laravel course was created with a lot of love and dedication for you'
    ];
    Mail::send('emails.test', $data, function($message){
        $message->to('edmund.magno@nabepero.co.jp', 'Edmund')->subject('Hello students how are you?');
    });
});
```

### Sending email part 2 - Finished
```bash
$ composer require guzzlehttp/guzzle
```
```php
<h1>{{$title}}</h1>

<p>{{$content}}</p>
```

### Custom domain

### Resources
[Mail](https://laravel.com/docs/5.2/mail)
