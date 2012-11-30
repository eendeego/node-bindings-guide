---
layout: page
title: Calling other native functions
---
# Calling other native functions

    Factory* factory = Isolate::Current()->factory();
    Handle<FixedArray> argArray = factory->NewFixedArray(argCount);
    argArray->set(i, v);
    //...
    Handle<JSObject> arguments =
      factory->NewArgumentsObject(inlined_function, argCount);

    for (int j = 0; j < js_args.argc(); j++) {
      i::Handle<i::String> arg =
          FACTORY->NewStringFromUtf8(i::CStrVector(js_args[j]));
      arguments_array->set(j, *arg);
    }
    i::Handle<i::JSArray> arguments_jsarray =
        FACTORY->NewJSArrayWithElements(arguments_array);

(To Do: Review this code sample.)
