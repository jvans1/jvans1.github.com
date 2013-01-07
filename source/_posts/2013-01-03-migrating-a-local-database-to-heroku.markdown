---
layout: post
title: "Migrating a local database to heroku"
date: 2013-01-03 12:34
comments: true
published: true
categories: 
---


If you deploy an app to heroku and want to use a local database, you need to take a few extra steps to get it working. You can't push your database directly up to heroku, instead you have to push it to somewhere that heroku can pull from. I used [amazon s3](http://aws.amazon.com/s3/) cloud storage. If you're not familier with s3, just setup an account and create a new bucket.  A bucket on s3 is similar to the file system on your computer. You can create subfolders within each bucket and upload individual files to them. 

The first thing you need to do is export your database on your local machine. I used postgres and my command looked like this:
        pg_dump -h localhost -U jvannem -Fc mydb > db.sql

 This exports your database into raw sequal statements which can recreate the original database later on. The ```-Fc``` flag tells it to use a custom format which is the most flexible format available and works with the ```pg_restore``` command that's used to recreate the database. It also compresses the file by default which is nice when working with large databases. ```-h``` and ```-U``` flags specify the host where the postgres server is and the username for that database respepectively. 

After you create a new bucket on s3, uploading is a pretty straightforward process. When the upload is complete, it's important to remember to change your permissions settings on s3. The default settings will throw an error when you try to import with heroku. If you change the settings to everyone, heroku will have access to the data.

{% img [class]  /images/permissions.png 500 [title text [alt text]] %}

Once the file is uploaded to s3 and the permissions are set, you can tell heroku to restore the database from the url to the s3 file. 
        heroku pgbackups:restore DATABASE 'https://url.tomydatabase.com'
 Depending on the size of the database it might take a little while but that's all you need to get your local database up to heroku!

