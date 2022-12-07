# Section 8: Laravel Fundamentals - Database - LaravelMigrations

### Intro to chapter with Edwin

### Environment configurations


### New - Windows OS - Migrations


### New - MAC OS - Migrations


### Creating migrations and dropping
+ make new migration
```bash
php artisan make:migration create_posts_table --create="posts"
```
+ migrate
```bash
php artisan migrate
```
+ rollback last migration
```bash
php artisan migrate:rollback
```

### Adding columns to existing tables using migrations
+ edit table
```bash
php artisan make:migration add_is_admin_column --table="posts"
```
+ add column
```php
$table->integer('is_admin')->unsigned();
```
+ drop column
```php
$table->dropColumn('is_admin');
```

### Some more migration commands
+ delete all tables
```bash
php artisan migrate:reset
```
+ migrate all migrations again
```bash
php artisan migrate:refresh
```
+ shows status of migrations
```bash
php artisan migrate:status
```

### Resources
[Database: Migrations](https://laravel.com/docs/5.2/migrations)
