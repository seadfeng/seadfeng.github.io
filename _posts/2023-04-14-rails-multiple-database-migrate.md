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
$ MIGRATIONS_PATH=db/clickhouse rake railties:install:migrations from=gemname #重点
```

### railties:install:migrations 代码片段 （rails 7.0.4.3）

代码中使用了MIGRATIONS_PATH环境变量替换默认的'db/migrate',有的懒不能偷懒，指南中没有的内容需要挖源码

```ruby
# /usr/local/bundle/gems/activerecord-7.0.4.3/lib/active_record/railties/databases.rake 
namespace :railties do
  namespace :install do
    # desc "Copies missing migrations from Railties (e.g. engines). You can specify Railties to use with FROM=railtie1,railtie2"
    task migrations: :'db:load_config' do
      to_load = ENV["FROM"].blank? ? :all : ENV["FROM"].split(",").map(&:strip)
      railties = {}
      Rails.application.migration_railties.each do |railtie|
        next unless to_load == :all || to_load.include?(railtie.railtie_name)

        if railtie.respond_to?(:paths) && (path = railtie.paths["db/migrate"].first)
          railties[railtie.railtie_name] = path
        end

        unless ENV["MIGRATIONS_PATH"].blank?
          railties[railtie.railtie_name] = railtie.root + ENV["MIGRATIONS_PATH"]
        end
      end

      on_skip = Proc.new do |name, migration|
        puts "NOTE: Migration #{migration.basename} from #{name} has been skipped. Migration with the same name already exists."
      end

      on_copy = Proc.new do |name, migration|
        puts "Copied migration #{migration.basename} from #{name}"
      end

      ActiveRecord::Migration.copy(ActiveRecord::Tasks::DatabaseTasks.migrations_paths.first, railties,
                                    on_skip: on_skip, on_copy: on_copy)
    end
  end
end
```