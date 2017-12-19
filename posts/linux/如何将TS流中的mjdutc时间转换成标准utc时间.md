### 软硬件环境

* ubuntu 16.04 64bit

### 前言

在EIT表中，有一个字段start_time来描述节目开始的时间，它是一个总共40bit的数值，包含以MJD和UTC表示的时间及日期。其中前16bit表示MJD日期，其余24bit按4bit BCD编码，表示6个数字。如下图所示

![](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/ts/eit.jpg)

本文就来将它转换成标准UTC时间。

### 什么是MJD

百度百科的解释

> 儒略日（Julian day，JD）是指由公元前4713年1月1日，协调世界时中午12时开始所经过的天数，多为天文学家采用，用以作为天文学的单一历法，把不同历法的年表统一起来。如果计算相隔若干年的两个日期之间间隔的天数，利用儒略日就比较方便。

### 什么是UTC

> 协调世界时（英：**C**oordinated **U**niversal **T**ime ，法：**T**emps **U**niversel **C**oordonné），又称世界统一时间，世界标准时间，国际协调时间。英文（CUT）和法文（TUC）的缩写不同，作为妥协，简称UTC。

### 转换成UTC

以下是C语言的转换实现代码

```
//这里的p_byte就是start_time时间
struct tm tm;
double mjd;
int k = 0;

memset(&tm, 0, sizeof(tm));
mjd = (p_byte[0] << 8) | p_byte[1];

tm.tm_year = (int) ((mjd - 15078.2) / 365.25);
tm.tm_mon = (int) (((mjd - 14956.1) - (int) (tm.tm_year * 365.25)) / 30.6001);
tm.tm_mday = (int) mjd - 14956 - (int) (tm.tm_year * 365.25) - (int) (tm.tm_mon * 30.6001);

if ((tm.tm_mon == 14) || (tm.tm_mon == 15))
    k = 1;

tm.tm_year += k;
tm.tm_mon = tm.tm_mon - 2 - k * 12;

/*
 * bcd码转化成int
 * */
tm.tm_sec = p_byte[4]-(p_byte[4] >> 4)*6;
tm.tm_min = p_byte[3]-(p_byte[3] >> 4)*6;
tm.tm_hour = (p_byte[2]-(p_byte[2] >> 4)*6) + 8;

p_tot->i_utc_time = mktime(&tm);
```

### 备注

如果是要计算北京时间，则需要在上述转换之后的基础上再加上8个小时，因为北京是处于东八区。