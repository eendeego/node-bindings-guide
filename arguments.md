---
layout: page
title: Receiving arguments
---
# Receiving function call arguments

*   Arguments are always passed as arrays via the `const Arguments& args` parameter
*   The args parameters exposes each argument as an array element (see below).

## Argument count

Use the Length method to get the argument count:

    args.Length()

Reference:

*   [nodejs addons](http://nodejs.org/api/addons.html)

## Scalar values

    v = args[n]->BooleanValue();
    v = args[n]->NumberValue();
    v = args[n]->IntegerValue();
    v = args[n]->Uint32Value();
    v = args[n]->Int32Value();

Reference:

*   [nodejs addons](http://nodejs.org/api/addons.html)

## Native enums

Native enums (previously returned by another native function) are casted back to C++ via:

    v = static_cast<EnumType>(args[n]->Uint32Value())

## Javascript objects

    Local<Object> obj = arg[n]->ToObject();

or

    Local<Object> obj = args[n].As<Object>();

## Strings

    String::AsciiValue v(args[n]->ToString());

Reference Code:

*   [png module](https://github.com/pkrumins/node-png/blob/master/src/png.cpp#L67)

## Buffers (node.js only)

    Local<Object> bufferObj    = args[n]->ToObject();
    char*         bufferData   = Buffer::Data(bufferObj);
    size_t        bufferLength = Buffer::Length(bufferObj);

## NativeArrays (node.js only)

V8 source code includes a runtime called D8 that also declares native arrays, however, these objects do not make part of V8. node.js provides its own implementation for those objects.

    Local<Object>  array  = args[n]->ToObject();
    Handle<Object> buffer = array->Get(String::New("buffer"))->ToObject();
    unsigned int   offset = array->Get(String::New("byteOffset"))->Uint32Value();
    int            length = array->Get(String::New("byteLength"))->Uint32Value();

    // Actual data
    ptr = (MyType*) &((char*) buffer->GetIndexedPropertiesExternalArrayData())[offset]

## Wrapped values

The main purpose of wrapped values is to encapsulate opaque native values, such as file handles, graphics context handles or even C++ object references.

    MyType v = (MyType) External::Unwrap(args[n]);

Reference:

*   [node.js addons - Object Factory](http://nodejs.org/api/addons.html#addons_object_factory)
