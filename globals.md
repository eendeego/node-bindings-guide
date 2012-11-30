---
layout: page
title: Accessing the global scope
---
# Accessing the global scope

```cpp
Handle<String> variable_name = String::New("global_variable_name");
Handle<Value> global_variable =
    Context::GetCurrent()->Global()->Get(variable_name);
```

Reference

*   See the `samples/lineprocessor.cc` file included in the V8 source code
