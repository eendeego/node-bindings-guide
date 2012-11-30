---
layout: page
title: Returning values from native code
---
# Returning values from native code

Returning values should always be done through the scope's Close method.

Note that Undefined, Null and some scalar values may be returned without going through the Close method, but this nay eventually break in the future.

## Undefined / Null

```cpp
return scope.Close(Undefined());
// or
return scope.Close(Null());
```

## Scalar values

```cpp
return scope.Close(Boolean::New(v)); // or
return scope.Close(True()); // or
return scope.Close(False());

return scope.Close(Number::New(v));
return scope.Close(Integer::New(v));
return scope.Close(Uint32::New(v));
return scope.Close(Int32::New(v));
```

## Native enums

```cpp
return scope.Close(Integer::New(v));
```

## Objects

```cpp
Local<Object> obj = Object::New();
// Set stuff (see below)
return scope.Close(obj);
```

## Buffers (node.js only)

```cpp
Buffer *slowBuffer = Buffer::New(length);
memcpy(Buffer::Data(slowBuffer), data, length);

Local<Object> globalObj = Context::GetCurrent()->Global();
Local<Function> bufferConstructor = Local<Function>::Cast(globalObj->Get(String::New("Buffer")));
Handle<Value> constructorArgs[3] = { slowBuffer->handle_, v8::Integer::New(length), v8::Integer::New(0) };
Local<Object> actualBuffer = bufferConstructor->NewInstance(3, constructorArgs);
return scope.Close(actualBuffer);
```

Reference:

*   [Creating a proper Buffer in a Node C++ Addon](http://sambro.is-super-awesome.com/2011/03/03/creating-a-proper-buffer-in-a-node-c-addon/)

## Native arrays (node.js only)

V8 source code includes a runtime called D8 that also declares native arrays, however, these objects do not make part of V8. node.js provides its own implementation for those objects.

The code below actually acts as if it was javascript calling the public native array creation functions.

```cpp
Handle<Value>    fun_val  = Context::GetCurrent()->Global()->Get(name);
Handle<Function> fun      = Handle<Function>::Cast(fun_val);

const unsigned argc = 1;
Local<Value> argv[argc] = { Local<Value>::New(Uint32::New(length)) };

Local<Object> result = fun->NewInstance(argc, argv);
return scope.Close(result);
```

Where name is one of:

```cpp
String::New("Int8Array")
String::New("Uint8Array")
String::New("Uint8ClampedArray")
String::New("Int16Array")
String::New("Uint16Array")
String::New("Int32Array")
String::New("Uint32Array")
String::New("Float32Array")
String::New("Float64Array")
```

## Wrapped values

```cpp
    External::Wrap(v)
```

Reference:

*   [node.js addons - Object Factory](http://nodejs.org/api/addons.html#addons_object_factory)
