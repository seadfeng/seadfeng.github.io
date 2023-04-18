---
layout: post
title: Ruby and Rails 获取当前或指定月份的日期数
date: 2023-04-18 14:21:48 +0800
author: sead
summary: Ruby and Rails 获取指定月份的日期数, 当前月份日期数:Time.days_in_month(Time.now.month, Time.now.year)
---
## Rails

### 当前月份日期数

```ruby
Time.days_in_month(Time.now.month, Time.now.year)
```
### 指定月份日期数

t为时间实例日期
```ruby
Time.days_in_month(t.month, t.year)
```

### 取月份的所有日期

```ruby
# Return Array
def get_days_in_month(month, year)
  days = Time.days_in_month(month, year)
  Array(1..days).map{|d| "#{year}-#{month}-#{"%02d" % d}" } # YYYY-MM-DD
end
puts get_days_in_month(2,2012)
```

## Ruby

```ruby
require 'date'

def days_in_month(month, year)
  Date.new(year, month, -1).day
end
puts days_in_month(2,2012)
```


原文:[How to get the number of days in a given month in Ruby, accounting for year?](https://stackoverflow.com/questions/1489826/how-to-get-the-number-of-days-in-a-given-month-in-ruby-accounting-for-year)