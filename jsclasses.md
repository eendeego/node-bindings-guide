---
layout: page
title: Creating a JavaScript Class
---

# Creating a JavaScript Class

In the main `.cc` of your module declare the module initializer:

```cpp
extern "C" void
init(Handle<Object> target)
{
  my_namespace::Initialize(target);
}
```

Declare a C++ class inside your own namespace) - preferably in a `.h` file:

### `MyClass.h`:

```cpp
namespace my_namespace {
  class MyClass : ObjectWrap {
  public:
    
    // Static module initializer
    static void Initialize (v8::Handle<v8::Object> target);
    
    // Constructor:
    MyClass ();
    
    // Destructor:
    virtual  ~Context   ();
    
    // Public Instance Methods
    Handle<Value>         DoStuff          ();
    static Handle<Value>  DoStuff          (const Arguments &args);
    Handle<Value>         DoMoreStuff      ();
    static Handle<Value>  DoMoreStuff      (const Arguments &args);

    
  private:
  
    // Initialization
    Handle<Value>         Init             ();
  }
}
```

Then, back in the main `.cc` file, declare the constructor, destructor and initialization methods.

```cpp
namespace my_namespace {

  //// Life-cycle functions

  // Destructor
  Context::~Context()
  {
    Close();
  }

  // Module Initializer
  void
  MyClass::Initialize(v8::Handle<v8::Object> target)
  {
    HandleScope scope;

    // Create constructor
    Local<FunctionTemplate> t = FunctionTemplate::New(New);
    t->InstanceTemplate()->SetInternalFieldCount(1);

    // Set prototype methods on the constructor:
    NODE_SET_PROTOTYPE_METHOD(t, "doStuff", DoStuff);
    NODE_SET_PROTOTYPE_METHOD(t, "doMoreStuff", DoMoreStuff);

    // Set constructor function on the module
    target->Set(String::NewSymbol("MyClass"), t->GetFunction());
  }

  void
  Initialize(Handle<Object> target)
  {
    MyClass::Initialize(target);
  }
}
```

You also need to implement the class methods `doStuff` and `doMoreStuff`. These methods come in pairs, one static and one instance method for each JS class method.

First we need an utility method for getting the context to get the instance from a static call:

```cpp
namespace my_namespace {
  
  // Utility Function for getting a context
  
  MyClass *
  MyClass::GetContext(const Arguments &args)
  {
    return ObjectWrap::Unwrap<MyClass>(args.This());
  }
  
}
```

Then we are ready to implement the instance methods

```cpp
namespace my_namespace {

  // Do Stuff:

  Handle<Value>
  MyClass::DoStuff()
  {
    // TODO: Actually do stuff here...
  }

  Handle<Value>
  MyClass::DoStuff(const Arguments& args)
  {
    HandleScope scope;
    return GetContext(args)->DoStuff();
  }
  
  // Do More Stuff:
  
  Handle<Value>
  MyClass::DoMoreStuff()
  {
    // TODO: Actually do stuff here...
  }

  Handle<Value>
  MyClass::DoMoreStuff(const Arguments& args)
  {
    HandleScope scope;
    return GetContext(args)->DoMoreStuff();
  }

}
```