---
layout: post
title: Rails Engine 多语言路由如何写？
date: 2023-05-11 01:58:52 +0800
categories: [Blog]
tags: [Rails, Ruby, Engine]
author: sead
summary: Rails Engine 多语言路由代码
---

默认语言en, 路由前缀:
- en => /
- es => /es
- fr => /fr
- it => /it
...

## 路由 routes

```ruby
# config/routes.rb
Rails.application.routes.draw do
  # 正则最后是重点,用来配首页
  scope '(:locale)', locale: /es|fr|it|de|ja|zh|/ do
        mount Blorgh::Engine, at: '/'
  end
end
```

## 控制器 controller

```ruby
module Blorgh
  class ApplicationController < ActionController::Base
    before_action :set_locale
    private
    def set_locale
      I18n.locale = if params[:locale].present?
        params[:locale]
      else
        I18n.default_locale
      end
    end
  end
end
```

