---
layout: page
title:
category: blog
description:
---
# Preface

# Date
> https://zh.wikipedia.org/wiki/ISO_8601

The only format in the spec is a simplified version of ISO-8601:

	2004-05-03T17:30:08+08:00
	2004-05-03T17:30:08; // chomre safari 默认0时区(更大), firefox 则默认是当前东8时区(更小: 这才是准确的)

	2004-05-03T17:30:08+8:00;//invalid date

If you change the space to a T, you'll be in spec:

	var dateString = "2015-12-31 00:00:00";
	var d = new Date(dateString.replace(' ', 'T'));

# moment
getTime():

	moment(point.date+" +08:00").valueOf()

# timer
You need to give JavaScript a reference to the interval:

	var t = setTimeout(function() {
		timer(counter + 1);
	}, 3000);

	var t = setTimeout(function() {
		timer(counter + 1);
	}, undefined);//立即执行:0ms

Then you can clear it like so:

	$("#stop").click(function () {
	   clearTimeout(t);
	});

# getdate

## yesterday

	var date = new Date();
	date ; //# => Fri Apr 01 2011 11:14:50 GMT+0200 (CEST)
	date.setDate(date.getDate() - 1);

## Date

	$d = new Date("October 13, 1975 11:13:00");
	$d = new Date(miliseconds);

	//date & time
	Date(); 		"Mon Apr 28 2014 23:25:02 GMT+0800 (CST)"
	.toString(); 	"Mon Apr 28 2014 23:25:02 GMT+0800 (CST)"
	.toUTCString();	"Mon, 28 Apr 2014 15:25:02 GMT" //UTC & GMT 是一样的(除了精度上有区别)
	.toGMTString();	"Mon, 28 Apr 2014 15:25:02 GMT"
	.toLocaleString(); "4/28/2014 11:25:02 PM"

	//date part
	.getDate(); //1~31 getUTCDate()
	.getMonth(); //0~11 getUTCMonth()
	.getFullYear(); //四位数字	getUTCFullYear()
	//day
	.getDay(); //一周中的某天0~6(0是星期天)

	//time part
	.getHours()	返回 Date 对象的小时 (0 ~ 23)。 getUTCHours()
	.getMinutes()	返回 Date 对象的分钟 (0 ~ 59)。	getUTCMinutes()
	.getSeconds()	返回 Date 对象的秒数 (0 ~ 59)。 getUTCSeconds()
	.getMilliseconds()	返回 Date 对象的毫秒(0 ~ 999)。getUTCMilliseconds()

	//unixstimestamp in miliseconds
	.valueOf()
	.getTime()	返回 1970 年 1 月 1 日至今的毫秒数。
	Date.parse("Jul 8, 2005 0:0:32"); //返回指定时间的毫秒数
	.UTC()	根据世界时返回 1970 年 1 月 1 日 到指定日期的毫秒数。

	//unixstimestamp in seconds
	new Date().getTime()/1000 | 0
	+new Date/1000 | 0

short:

	Date.prototype.formatMMDDYYYY = function(){
		return (this.getMonth() + 1) +
				"/" +  this.getDate() +
				"/" +  this.getFullYear();
	}
	Date.prototype.format = function(d){
		var d = this;
		return d.getFullYear()+'-' + (d.getMonth()+1)+'-' + d.getDate()+' ' + d.getHours()+':' + d.getMinutes()+':' + d.getSeconds()+''
	}

### compare

	d1 > d2

### set

	//day
	.setDate()	设置 Date 对象中月的某一天 (1 ~ 31)。

	//Month
	.setMonth()	设置 Date 对象中月份 (0 ~ 11)。

	//Year
	.setFullYear()	设置 Date 对象中的年份（四位数字）。 setYear()	使用 setFullYear() 方法代替。

	//Hours & Minutes & Seconds
	.setHours()	设置 Date 对象中的小时 (0 ~ 23)。
	.setMinutes()	设置 Date 对象中的分钟 (0 ~ 59)。
	.setSeconds()	设置 Date 对象中的秒钟 (0 ~ 59)。
	.setMilliseconds()	设置 Date 对象中的毫秒 (0 ~ 999)。

	//add time
	d.setSeconds(d.getSeconds() + 10);
	Date.prototype.add= function(seconds){
		this.setTime(this.getTime() + (seconds * 1000));
		return this;
	}

	//Time
	.setTime(millisec)	以毫秒设置 Date 对象。d.setTime(77771564221)
	new Date(millisec)	以毫秒设置 Date 对象. new Date(77771564221)

str to time

	new Date("October 13, 1975 11:13:00");
	new Date("2016-01-01 11:13:00");
	new Date("2016-01-01 11:13:00");
	new Date("2016-06-03");
	new Date(2016, 05,03);//05+1 需要加1

	(new Date("2016-1-1")).getDay();

UTC to miliseconds

	Date.UTC(1970, 9, 21)
