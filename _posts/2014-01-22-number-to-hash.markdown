---
layout: post
title: "#to_hash"
date: 2014-01-22 22:03
comments: true
categories: ["Ruby"] 
---

How to transform all the instance variables from an object to a hash: 

```
def to_hash
  instance_variables.map(&:to_s).inject({}) do |hash, ivar|
    hash.merge(ivar.gsub('@','') => instance_variable_get(ivar))
  end
end
```

Enjoy!
