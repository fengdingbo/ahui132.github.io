---
layout: page
title:	linux yum
category: blog
description: 
---
# Preface

# yum & rpm

## find package
via file

	yum provides `which cmd`
	rpm -qf <file>

via package name

	rpm -qp <package name>

Example:

	yum provides /usr/bin/ab
	yum install httpd-tools

## remove

	rpm -e <package>
	yum remove <package>

# find command

	which nutcracker
	type -a nutcracker

