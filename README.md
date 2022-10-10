# About

This repo provides a simple way to automate sending a simple email with a few highlights from your Kindle highlights.

It is intended to be run on a server you control, using a cronjob.

To set up this app:

1.  Clone this repo

2.  Set up a `.env` file in the project's root dir with values as follows (replace your own values of course):

```shell
AMAZON_PASSWORD="your_amazon_password"
AMAZON_USERNAME="your_amazon_email@gmail.com"
SMTP_LOGIN="your_smtp_login"
SMTP_PASSWORD="your_smtp_password"
SMTP_PORT="your_smtp_port"
SMTP_SERVER="your_smtp_server_address"
```

3. Create a script like this in your linux environment (i.e. home directory):

```shell
#!/bin/bash
cd /path/to/this/project
BUNDLE_GEMFILE=/path/to/this/project/Gemfile bundle exec rake -f /path/to/this/project/Rakefile
```

4. Make this script executable with `chmod +x script_filename.sh`

5. Set up a crontab to run this file every day with `crontab -e`. For every day at midnight, add this to your crontab file: `0 0 * * * ~/email_highlights.sh`

Inspired by [@tobi](https://github.com/tobi)
