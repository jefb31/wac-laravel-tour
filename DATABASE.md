# Database

Toutes les tables devront contenir les options "softDelete" et "timestamp" qui vont créer 2 champs supplémentaires : "created_at" et "updated_at".

**Documentation :** https://laravel.com/docs/5.2/migrations#creating-columns

N'oublier pas de référencer les clef etrangères (FOREIGN KEY) dans vos migrations :

```
$table->foreign('category_id')->references('id')->on('categories');
```

# Users

| Field         | Type          | Constraint  |
| ------------- |:-------------:| -----:|
| id            | integer       | UNSIGNED car ne peut pas être négatif |
| first_name    | varchar       | 35 caractères max |
| last_name     | varchar       | 35 caractères max |
| login         | varchar       | UNIQUE, 15 caractères max |
| email         | varchar       | UNIQUE, 255 caractères max |
| password      | varchar       | 255 caractères max |

# Films

| Field         | Type          | Constraint  |
| ------------- |:-------------:| -----:|
| id            | integer       | UNSIGNED car ne peut pas être négatif |
| category_id   | integer       | UNSIGNED car ne peut pas être négatif, FOREIGN KEY de categories.id |
| name          | varchar       | 100 caractères max |
| description   | text          | 2000 caractères max |
| picture_url   | text          | 2000 caractères max |


# Categories

| Field         | Type          | Constraint  |
| ------------- |:-------------:| -----:|
| id            | integer       | UNSIGNED car ne peut pas être négatif  |
| name          | varchar       | 25 caractères max |

# Tickets

| Field         | Type          | Constraint  |
| ------------- |:-------------:| -----:|
| id            | integer       | UNSIGNED car ne peut pas être négatif |
| user_id       | integer       | UNSIGNED car ne peut pas être négatif, FOREIGN KEY de users.id |
| film_id       | integer       | UNSIGNED car ne peut pas être négatif, FOREIGN KEY de films.id |
| price         | float         | UNSIGNED car ne peut pas être négatif |
