---
layout: page
title:	linux release
category: blog
description: 
---
# Preface

# get release

	OS=$(lsb_release -si)
	ARCH=$(uname -m | sed 's/x86_//;s/i[3-6]86/32/')
	VER=$(lsb_release -sr)
	lsb_release -a
