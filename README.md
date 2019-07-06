## RocketChat GridFS to FileSystem/AmazonS3 Migration

This is a script for migrating files uploaded to [RocketChat](https://rocket.chat/) from the default `GridFS` upload store to FileSystem/AmazonS3.

    migrate -c [command] -d [targetPath] -r [dbname] -t [target] -d [destination]

### Help

Run `./migrate -h` to see all available options

### Commands
- **dump** :        dumps the GridFs stored files into the given folder/s3 bucket and writes a log files
- **updatedb** :    changes the database entries to point to the new store instead of GridFS
- **removeblobs** : removes migrated files from GridFS

### Steps

1. Backup your MongoDB database so that you won't loose any data in case of any issues. ([MongoDB Backup Methods](https://docs.mongodb.com/manual/core/backups/))
2. Change `Storage Type` in RocketChat under `Administration> File Upload` to `FileSystem` or `AmazonS3`. Update the relevant configuration under the corresponding head in configuration page.
3. Start copying files to the new store  
   - **File System**

            ./migrate -c dump -d /app/uploads -r rocketchat -t FileSystem -d ./uploads

   - **S3**

            ./migrate -c dump -d /app/uploads -r rocketchat -t AmazonS3 -d bucket_name

4. Update the database to use new store (use `-t AmazonS3` if you are migrating to S3)

        ./migrate -c updatedb -d /app/uploads -r rocketchat -t FileSystem

5. Check if everything is working correctly. Ensure that there are no files missing.
6. Remove obsolete data from GridFS

        ./migrate -c removeblobs -d /app/uploads -r rocketchat

### Requirements

- apt-get install python3 wget python3-pip -y
- pip3 install  pymongodb boto3
