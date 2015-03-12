# Apache-Rails-Passenger-Xubuntu
Instructions on installing Apache, Rails &amp; Passenger on xubuntu

=
###Preconditions
=

>You have a clean instance of ubuntu running.

You can get ubuntu from here: http://www.ubuntu.com/download

=
###Install Apache
=

Open a terminal and enter the following commands:

```
sudo apt-get upgrade
sudo apt-get update
sudo apt-get install apache2
sudo apt-get install vim        //or your favourite text editor
```

=
###Install Ruby
=

**Install rvm**

```
sudo apt-get install curl
\curl -L https://get.rvm.io | bash -s stable --ruby
source /home/darren/.rvm/scripts/rvm       //change darren to your directory home name
```

If you get an error about keys, it will produce a command for you to use to fix it, then do the command again.
The third command will be given to you when the first is finished.

**Update rvm**

```
rvm get stable --autolibs=enable
rvm install ruby
rvm --default use ruby-2.2.0
```

=
###Install rails
=

**Update gem manager**

```
gem update --system
```

**Install rails**

```
gem install rails
```

**Check the rails version**

```
rails -v
```

It should be the latest available, at the time of writing this: 4.2.0

=
###Passenger
=

**Install required libraries**

```
sudo apt-get install apache2-prefork-dev libapr1-dev libaprutil1-dev
gem install passenger
```

**Set up passenger**

```
passenger-install-apache2-module
```

This is a great installer, if you get an error when running the above command, it will give you the command that you need to run in order to fix it. Then re-run the above command.

Once the above command finishes, it will produce something that you need to paste into your apache2.conf file.
It will then produce an example virtual host for an app, which you can ignore.

=
###Restart apache
=

```
sudo etc/init.d/apache2 restart
```

If you get an error from the above command about setting the ServerName globally, do the following:

```
sudo vim etc/apache2/apache2.conf
```

If there is a line that contains: ServerName,
change it to:
```
ServerName 127.0.0.1:80
```
If ServerName is not there just add the above line.

Then restart apache again to make our changes take effect:

```
sudo etc/init.d/apache2 restart
```

Once this is done with success, navigate to localhost or 127.0.0.1 in your browser and you should see everything works message. yay :)

=
###Install sqlite for ruby
=

```
gem install sqlite3-ruby
```

=
###Install MySQL
=

When it asks for a password, you can set it to what you want. For the purposes of this I will use the password "password"

```
sudo apt-get install mysql-server
```

**Start the MySQL Server**

```
sudo etc/init.d/apache2 start
```

**Check that MySQL is running**

```
sudo netstat -tap | grep mysql
```

**Install MySQL for Rails**

```
gem install mysql2
```

=
###Create the rails blog application
=

In your home directory, create a new folder called git:

```
mkdir git
```

Then cd into this folder and follow the instructions found here: https://github.com/archerydwd/ror-blog to build the rails blog app.

Once you have finished this, cd into your new app and run the following:

```
bundle install
bundle exec rake db:migrate
```

The above installs missing gems and creates a migration for the database.

Then to get the delete function to work, we need to edit the link_to to button_to:

```
vim app/views/comments/_comment.html.erb
```
Change the delete line from link_to to button_to
Then:

```
vim app/views/articles/index.html.erb
```

And do the same thing again.

=
###Create the config file for the blog app
=

```
sudo touch etc/apache2/sites-available/ror_blog.conf
sudo vim etc/apache2/sites-available/ror_blog.conf
```

And insert the following making sure to replace darren with your own directory:

```
<VirtualHost *:80>
  ServerName 127.0.0.1:80
  DocumentRoot /home/darren/git/ror_blog/public
  <Directory /home/darren/git/ror_blog/public>
    AllowOverride all
    Options -MultiViews
  </Directory>
</VirtualHost>
```

=
###Disable the default site in apache
=

```
sudo a2dissite 000-default
```

If this does not work, do:

```
ls etc/apache2/sites-enabled
```

The above will give a list of sites that are currently enabled. If the name in the list is different from 000-default then issue the command replacing 000-default with the correct name.

=
###Enable the blog site
=

```
sudo a2ensite ror_blog.conf
```

Then we need to reload the apache server

=
###Relaod the Apache Server
=

```
sudo etc/init.d/apache2 reload
```

Navigate to localhost in your browser, if your getting an error:

```
sudo vim etc/apache2/apache2.conf
```

And find the following:

```
<Directory />
  Options FollowSymLinks
  AllowOverride None
  Require all denied
</Directory>
```

And change it to:

```
<Directory />
  Options FollowSymLinks
  AllowOverride None
  Require all granted
</Directory>
```

Then reload the server again:

```
sudo etc/init.d/apache2 reload
```

It should now work.




