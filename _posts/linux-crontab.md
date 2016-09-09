---
layout: page
title:
category: blog
description:
---
# Preface

# example

	0 * * * * php a.php >> /dev/null 2>&1

# PATH

	$ man 5 crontab | grep -C5 PATH | tail
	$ vim /etc/crontab
	SHELL=/bin/sh
	PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

	# m h dom mon dow usercommand
	17 * * * *  root  cd / && run-parts --report /etc/cron.hourlyPATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

print path:

	* * * * * env > env_dump.txt


# debug
The easiest way is simply to send all STDOUT and STDERR to Syslog


	* * * * * echo "test message" 2>&1 |logger

To ensure your jobs are executed tail on /var/log/cron

	tail -f /var/log/cron

To see all the outputs in Syslog

	tail -f /var/log/messages
