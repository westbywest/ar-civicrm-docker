# AR Drupal+CiviCRM Docker Environment
Docker environment to stand up Drupal+CiviCRM for Arch Reactor website testing.

## Directory Structure
`drupal`:  
Contents copied and adapted from https://lab.civicrm.org/michaelmcandrew/civicrm-docker/-/blob/master/5/drupal

`drupal/archreactor`:  
Folder of expanded contents of site backup tarball, expecting subfolders `archreactor.org` and `private`

`drupal/civicrm-docker-custom`:  
Customized Dockerfiles and associated assets

`drupal/db-civicrm`:  
MariaDB volume for CiviCRM database

`drupal/db-drupal`:  
MariaDB volume for Drupal database

## Importing site backup SQL
Update SQL dumps to satisfy current version Maria DB:  
```
sed -i 's/utf8mb4_0900_ai_ci/utf8mb4_unicode_520_ci/g' archreactor/archreactor.org/archreactor.sql
sed -i 's/utf8mb4_0900_ai_ci/utf8mb4_unicode_520_ci/g' archreactor/archreactor.org/civicrm.sql
```

Manually import drupal SQL into MariaDB container (if needed):
```
docker exec -i containername mysql -uroot -ppassword mariaDBroot < dump.sql
```

Disable HTTPS autoforward in securelogin module, needed to load URLs like http://ardev.localhost :
```
drush pm-disable securelogin
```

List users, D7 only:
```
drush uinf $(drush sqlq "SELECT GROUP_CONCAT(uid) FROM users")
```

Drupal 7/8 set user's password via username or uid:
```
drush user-password USERNAME --password="SOMEPASSWORD"
drush upwd 1 --password="password"
```

## References
This CiviCRM repo provides the docker compose/Dockerfile content this repo is based on:  
https://lab.civicrm.org/michaelmcandrew/civicrm-docker

Dockerfile source:  
https://lab.civicrm.org/michaelmcandrew/civicrm-docker/-/blob/master/5/drupal/php8.1/Dockerfile?ref_type=heads
