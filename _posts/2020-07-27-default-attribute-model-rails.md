---
layout: post
date: 27/07/2020
title: Default attribute value in model Rails
tags: ruby rails
---

In Rails, migrations let you define default attribute values by passing a :default option to the column method. But in some cases, you may need to set the default in the application. There are:

* When the default can be easily be changed
* When the default can be dynamic
* When the default values are part of your domain logic and you want to kept together with the rest of the domain logic of your application


## How to do

In Rails 5 you can implement it easily using **attribute**

```rb
class TimesheetEntry < ApplicationRecord
  attribute :category, :string, default: 'n/a'
end
```

If you’re using an older version of Rails or want the value of the default to depend on the value of other attributes at runtime

```rb
def category
  read_attribute(:category) || 'n/a'
end
```

