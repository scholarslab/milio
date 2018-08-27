# CNHI Nancy Milio Collection

# Development
To set up the development environment, you will need a copy of Omeka, a copy of the data files (these folders within the omeka folder: audio, files, images) and any plugins and theme files.

You will also need a copy of the database as an .sql file.


## File Structure
```
├── Dockerfile              # creates the images used for omeka
├── config                  # folder that holds the php.ini file
│   ├── php.ini             # custom php.ini that increases file upload sizes
├── db_data                 # MySQL database files created after `docker-compose up` is ran for the first time. 
├── docker-compose.yml      # puts together the MySQL and Omeka images
├── initial_sql             # folder to hold the oringial db file
│   └── neatline_milio.sql  # the MySQL database file
└── omeka                   # folder containing all the omeka files. Subfolders listed hold unique data
    ├── audio               # audio files used on the site
    ├── files               # all of the uploaded files
    ├── images              # images used in footers, etc
    ├── plugins             # any plugins needed, go in here
    ├── themes              # theme folder, put the site's theme in here.
```

## To run
Just start docker-compose as normal
- `docker-compose up`

## To recreate the omeka image 
After changing the Dockerfile or docker-compose.yml, run
- `docker-compose up --build` to rebuild and run the images
- or `docker-compose build` to just rebuild the images
- or, find the image hash with `docker images`  and then remove it `docker rmi
 <hash>`, and then just run `docker-compose up`

## To stop
- Either send the term signal with `Ctrl-c` (pressing the 'Control' and 'C' keys)
- OR, in a different terminal window/session, but in the same directory as the docker-compose.yml file, run
  - `docker-compose down --volume`

# Production

Set up for production is the same as development, but run with '-d' flag.
- `docker-compose up -d`

Another option is to create images for MySQL and Omeka, and just use a
docker-compose.yml file to pull down those images. But this would require the
Omeka data to be in the image (which for this project is over 5GB) and for the
database information to be accessible within the image (Omeka user accounts,
emails and hashed passwords).

# Problems and Solutions
## Omeka and email
### Problem
Using the default PHP docker image does not allow for sending emails (for
instance, sending an email for a new user activation or password reset email).

### Solution
Solution to getting a webapp to email comes, in part, from:
https://magnier.io/docker-wordpress-email/

This solution relies on a not-best-practice of installing sendmail in the PHP
image for Omeka. A better alternative would be to have sendmail in a separate
container, and linked to the Omeka container.

The trick here is to have a Dockerfile that installs sendmail (which is already
needed in order to install ImageMagick), and then modify the command run by the
container. By default it automatically runs a command to start up Apache. We
modify it to restart sendmail, and then start up Apache.

Also, the 'hostname' option is needed in the docker-compose.yml file for the
Omeka service, which then sets correct parameters in the '/etc/hosts' and
'/etc/hostname' files.

