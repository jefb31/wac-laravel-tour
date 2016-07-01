# Note

![Tour](http://image.noelshack.com/fichiers/2016/26/1467333962-lara.jpg)


| Field         | Type          | Constraint  |
| ------------- |:-------------:| -----:|
| id            | integer       | AUTO_INCREMENT, UNSIGNED car ne peut pas être négatif |
| first_name    | varchar       | 35 caractères max |
| last_name     | varchar       | 35 caractères max |
| login         | varchar       | UNIQUE, 15 caractères max |
| email         | varchar       | UNIQUE, 255 caractères max |
| password      | varchar       | 255 caractères max |
| is_admin      | boolean       | 0 par défaut
