# docker-local
### docker-powered WordPress local dev tool

## Requirements

* docker
* docker-compose

### Optional but recommended

* degit `npm install -g degit`

## Steps

1. Set up a directory you want you project to live in.
1. From within that dir clone the github project: `/docker-local`, or with `degit`, run: `degit /docker.local myprojectdir`
1. This will create a project with this structure:
```
- myprojectdir

- - data/
- - site/
- - .gitignore
- - docker-compose.yml
- - README.md

```
1. If the project already exists, copy SQL file, and uploads dir to the `.data` (anything WP needs to run your site). Download copies of these from backups.
1. cd into the `site` and git checkout your project there.


## commands needed to run on first container load

`docker exec -it name_of_wp_container /bin/bash`
then:
(current process)

`chown www-data:www-data -R wp-content/plugins`
`chown www-data:www-data wp-content/uploads` (if used)
`chown www-data:www-data wp-content`

Note non-recursives where needed

and whatever else you need to give permissions to, (but usually not the theme which the container doesn't have to write to...)

This is to ensure the images will be loaded and used correctly locally.

### SQL commands to set
run mysql as root:
`mysql -uroot -pwordpress`

then at the mysql> prompt:
`use wordpress`

then run these commands in order and as three commands (changing your name and email as needed):

```
INSERT INTO wp_users (user_login, user_pass, user_nicename, user_email, user_status, user_registered)
VALUES ('localadmin', MD5('pass123'), 'YOUR NAME', 'EMAIL@domain.com', 0, NOW());

INSERT INTO wp_usermeta (umeta_id, user_id, meta_key, meta_value)
VALUES (NULL, (Select max(id) FROM wp_users), 'wp_capabilities', 'a:1:{s:13:"administrator";s:1:"1";}');

INSERT INTO wp_usermeta (umeta_id, user_id, meta_key, meta_value)
VALUES (NULL, (Select max(id) FROM wp_users), 'wp_user_level', '10');
```


---------------


## @TODO

* automate updated wp-content to user www-data
** docker exec -it <container name> /bin/bash
* add wp_cli to container?
* automate SQL user set up
