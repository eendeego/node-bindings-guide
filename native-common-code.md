---
layout: page
title: Native method template
---
# Common code for V8 native methods

## For each c++ header file (.h):

*   Use the #ifndef/#define c/c++ pattern
*   Create a namespace for your methods
*   Declare all javascript callable methods as:
    *   C++ `static Handle<Value> MethodName(const Arguments& args);`
    *   Note that this name is not visible to javascript (see below)
    *   If you want to adhere to the [Google C++ Style Guide](http://google-styleguide.googlecode.com/svn/trunk/cppguide.xml), use CamelCasing

Sample code:

    #ifndef MY_NATIVE_LIB_H_
    #define MY_NATIVE_LIB_H_

    #include <node.h>
    #include <v8.h>

    using namespace v8;

    namespace my_namespace {
      static Handle<Value> MethodAlpha(const Arguments& args);
      static Handle<Value> MethodBeta(const Arguments& args);
      static Handle<Value> MethodGamma(const Arguments& args);
      //...
    }

    #endif

## Prefix for each c++ code file (.cc/.cpp):

*   Import node and V8 relevant headers.
*   Import your own headers.
*   Use the `using namespace` cautiously (importing node and v8 namespaces is generally safe).
*   Declare an init function (see sample code), that:
    *   Exposes your C++ functions to javascript:
    *   Performs other necessary initialization

Sample code:


    #include <node.h>
    #include <node_buffer.h>
    #include <v8.h>

    #include <my_include>
    #include "my_other_include.h"

    using namespace node;
    using namespace v8;

    extern "C" void
    init(Handle<Object> target) {
      NODE_SET_METHOD(target, "methodAlpha" , my_namespace::MethodAlpha);
      NODE_SET_METHOD(target, "methodBeta"  , my_namespace::MethodBeta);
      NODE_SET_METHOD(target, "methodGamma" , my_namespace::MethodGama);
      // ...
    }

## Method/Function template:

*   Always declare a local HandleScope
*   Always return using the Close clause

Sample code:

    Handle<Value> my_namespace::MethodAlpha(const Arguments& args) {
      HandleScope scope;

      // My stuff goes here

      return scope.Close(something);
    }

See [arguments](arguments.html) and [returning values](returning.html).

Reference:

*   [V8 embedder's guide - Handles and Garbage Collection](https://developers.google.com/v8/embed#handles)
*   [nodejs addons](http://nodejs.org/api/addons.html)
