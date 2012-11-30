---
layout: page
title: Creating javascript objects in native code
---
# Creating javascript objects in native code

## Creating and accessing objects

    Local<Object> obj = Object::New();
    obj->Get(String::NewSymbol("key"));
    obj->Get(index);
    obj->Set(String::NewSymbol("key"), value);
    obj->Set(index, value);

## Creating and accessing arrays

    Handle<Array> array = Array::New(length);
    array->Set(n, v);
