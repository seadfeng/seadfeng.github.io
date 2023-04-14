---
layout: post
title: Ruby on Rails gem下多数据库怎样迁移？
date: 2023-04-14 22:37:09 +0800
categories: [Blog]
tags: [Ruby, Rails, Rails 7, 多数据库, clickhouse, postgresql]
author: sead
summary: Ruby on Rails 多数据库迁移, Rails multiple database migrate, Rails + postgresql + clickhouse
---

最近开发用的Rails项目用了postgresql + clickhouse，踩了不少坑. 

项目使用了 [rails engines](https://guides.rubyonrails.org/engines.html)，如下方式创建

```console
$ rails plugin new blorgh --mountable
```

详细文档 https://guides.rubyonrails.org/engines.html


## Rails多数据库如何复制gem的迁移文件到项目中？

### blorgh下迁移文件路径

- db/migrate
- db/clickhouse

```console
$ rake railties:install:migrations from=gemname # 默认复制db/migrate
```

### 创建clickhouse迁移tasks

```ruby
# blorgh/lib/tasks/blorgh_tasks.rake
namespace :blorgh do
  namespace :clickhouse do
    desc 'Copy clickhouse migrations from gem to application'
    task migrations: :'db:load_config' do
      railties = {}
      railties['blorgh'] = Gem.loaded_specs['blorgh'].full_gem_path + '/db/clickhouse'
      on_skip = Proc.new do |name, migration|
        puts "NOTE: Clickhouse Migration #{migration.basename} from #{name} has been skipped. Migration with the same name already exists."
      end

      on_copy = Proc.new do |name, migration|
        puts "Copied clickhouse migration #{migration.basename} from #{name}"
      end

      ActiveRecord::Migration.copy("#{Rails.root}/db/clickhouse", railties, on_skip: on_skip, on_copy: on_copy)
    end
  end
end
```

### 执行迁移文件复制

```console
$ rake blorgh:clickhouse:migrations
```