1. Répertoire vide dans lequel on insère un dossier "conf"
    - Copier le contenu de 000-default.conf :
    " <VirtualHost *:80>
        ServerName localhost
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/html/public

        <Directory /var/www/html/public>
            Options +FollowSymLinks
            AllowOverride All
            Require all granted
            FallbackResource /index.php
            RewriteEngine On
            RewriteCond %{REQUEST_FILENAME} !-f
            RewriteCond %{REQUEST_FILENAME} !-d
            RewriteCond %{REQUEST_FILENAME} !favicon.ico$
            RewriteRule ^(.*)$ /index.php/$1 [L,QSA]
        </Directory>

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost> "

2. Créer le fichier docker-compose.yml
    - Copier le contenu d'un docker-compose.yml :
    " name: symfony8-2503-prepa
      volumes:
        db_data: {}
      services:
        db: 
          image: mariadb:10.8.5
          container_name: symfony8-prepa-db
          restart: unless-stopped
          ports: ["3309:3306"]
          volumes:
            - db_data:/var/lib/mysql
          environment:
            MYSQL_ROOT_PASSWORD: rootpassword
            MYSQL_DATABASE: db_symfony
            MYSQL_USER: user_symfony
            MYSQL_PASSWORD: azer
        web:
          build:
            context: .
            dockerfile: Dockerfile
          container_name: symfony8-prepa-apache
          restart: unless-stopped
          ports: ["9001:80"]
          depends_on: ['db']
          links: ['db']
          volumes:
            - ./src:/var/www/html "
          - Modifier ce qui doit l'être
          - Démarrer Docker desktop "

3. Creer un ficher Dockerfile et copier le contenu d'un Dockerfile :
    " FROM php:8.4-apache

      ENV COMPOSER_ALLOW_SUPERUSER=1

      EXPOSE 80

      WORKDIR /var/www/html

      RUN apt-get update -qq && \
          apt-get install -qy \
          git \
          gnupg \
          unzip \
          zip && \
          curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer && \
          apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

          RUN docker-php-ext-install pdo_mysql opcache

          COPY ./conf/000-default.conf /etc/apache2/sites-available/000-default.conf

          RUN a2enmod rewrite "

4.  - docker compose up -d
    - Taper la commande "docker exec -it nomServeurWeb bash "

5. Taper (dans le contenaire) : " composer create-project symfony/skeleton:"8.0.*" . "
    

6. Ouvrir le fichier .env
    - composer require api
    - Taper dans le conteneur : " composer require symfony/maker-bundle --dev "
    - Ajouter la ligne " DATABASE_URL="mysql://user:secret@db:3306/db_test?serverVersion=11.8.5-MariaDB&charset=utf8mb4" "
    - Dans le conteneur, taper : php bin/console doctrine:database:create

7. Modifier la ligne dans le .env :
    - DATABASE_URL="mysql://"nom_utilisateur":"mdp"@"nom_db(MYSQL_DATABASE)":3306/db_test?serverVersion=10.8.5-MariaDB&charset=utf8mb4"

8. Commenter la ligne : " DATABASE_URL="postgresql://app:!ChangeMe!@127.0.0.1:5432/app?serverVersion=16&charset=utf8" "

9. En dehors du conteneur : Docker compose up -d

10. config/routes/api_plateform.yaml : commenter le prefix

11. Dans le conteneur : Composer update

13. Aller dans config/package/api_plateform.yaml et copier sous la version :
        version: 1.0.0
        formats:
        json: ['application/json'] # 1er de la liste = Format par défaut
        jsonld: ['application/ld+json'] # Autre format disponible
        html: ['text/html'] # Autre format disponible
        xml: ['application/xml', 'text/xml']
        csv: ['text/csv']

14. Dans le conteneur, taper : php bin/console make:entity
    Répondre yes pour avoir les deux lignes qui affichent les database
    
15. php bin/console make:migration

16. docker compose up -d --build

17. aller dans le conteneur et taper : composer update

18. Vérifier la version de mysql dans le conteneur de la base de données (Docker Desktop db) et appliquer la même version dans le .env si erreur

19. php bin/console doctrine:migrations:migrate

20. creer le crud : php bin/console make:crud 

## Relation entre bases :

21. php bin/console make:entity
22. Saisir le nom de l'entité de départ
23. Taper le nom de la deuxième table
24. point d'interogation, dans type taper "relation"
25. A quelle table doit elle être relaté : nom de la deuxième table
26. Add new property: yes
27. garder le même nom
28. delete orphans : no
29. php bin/console make:migration
