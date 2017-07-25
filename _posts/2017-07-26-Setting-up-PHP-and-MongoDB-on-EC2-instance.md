---
layout: post
tags: php, aws, mongodb
title: Setting up PHP and MongoDB on EC2 instance
comments: true
---
At some point in the past an unfortunate decision was made to store arrays of JSON objects as text files, for maintenance purposes. As the number of files started to grow rapidly, the efficiency of this solution became very low. A better decision to keep them in a NoSQL database was made. And since there was a need to look into the records, the most popular document oriented one was chosen: MongoDB. Now the task was to feed this database with all the stored text files. I have decided to set up a dedicated EC2 instance that will be used for this migration. Below you will find a set of commands that need to be issued in order to have a working PHP & MongoDB stack on EC2.
<!--more-->

The environment doesn't have the Apache server installed as there was no need for one. Only pure system with PHP (as it was the easiest for me to write a script in it) and MongoDB.

{% gist 7e6786ee5f28ec2ba15f6c2d60773a5b %}