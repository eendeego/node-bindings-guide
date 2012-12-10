---
layout: page
title: Getting Started
---
# Getting started

This section describes how to build a _hello world_ node.js module equivalent to:

    exports.hello = function() { return 'world'; };

This is the same example as the [addons](http://nodejs.org/api/addons.html) section on node's documentation, with a slightly different organization.

## Project layout

Node modules have a common directory layout, typically with an `index.js` as the main module and a `lib` directory for the submodules.

Native code in node modules usually resides in a `src` subdirectory in the project.

To build the project a configuration file for GYP - `binding.gyp` - or WAF - `wscript` - is necessary. Note that WAF is currently deprecated as a build system for node's native modules (and node itself).

The directory layout for a node module with native bindings is:

    hello-world
      + src
         - hello.cc
      - index.js
      - binding.gyp // See building with GYP below.
      - wscript     // See building with WAF below.

For this example, the contents of `index.js` should be:

    var addon = require('./build/Release/hello');

    console.log(addon.hello()); // 'world'

And the contents of `hello.cc`:


    #include <node.h>
    #include <v8.h>

    using namespace v8;

    Handle<Value> Method(const Arguments& args) {
      HandleScope scope;
      return scope.Close(String::New("world"));
    }

    void init(Handle<Object> target) {
      NODE_SET_METHOD(target, "hello", Method);
    }
    NODE_MODULE(hello, init)

To run this example (don't do it yet) run:

    node index.js

## Building with GYP

GYP is the current (as of node 0.8.x) way of building node native modules and node itself. To build node modules using GYP, [node-gyp](https://github.com/TooTallNate/node-gyp) must be installed:

    npm install -g node-gyp

Installing node-gyp will pull lots of modules from npm.

GYP is configured through the binding.gyp file. For this example it should contain:

    {
      "targets": [
        {
          "target_name": "hello",
          "sources": [ "src/hello.cc" ]
        }
      ]
    }

To setup node-gyp do:

    node-gyp configure

The first time `node-gyp configure` is executed, it will pull node's source code. This is OK.

To do the actual build:

    node-gyp build

To run the example:

    node index.js

The output should be:

    world


## Building with WAF

WAF is the _old way_ of building node's native modules and is included on its distribution.

*Use WAF only if you need to support old versions of node (below 0.8).*

WAF is configured through the `wscript`.

For the hello world example it sould contain:

    #!/usr/bin/env python

    from os import popen

    srcdir = '.'
    blddir = 'build'
    VERSION = '0.0.1'

    def set_options(opt):
      opt.tool_options('compiler_cxx')

    def configure(conf):
      conf.check_tool('compiler_cxx')
      conf.check_tool('node_addon')

    def build(bld):
      obj = bld.new_task_gen('cxx', 'shlib', 'node_addon')
      obj.target = "hello"
      obj.cxxflags = ["-Wall", "-g"]
      obj.source = ["src/hello.cc"]

Now, to setup WAF do:

    node-waf configure

It will produce an output similar to:

    Checking for program g++ or c++          : /usr/bin/g++ 
    Checking for program cpp                 : /usr/bin/cpp 
    Checking for program ar                  : /usr/bin/ar 
    Checking for program ranlib              : /usr/bin/ranlib 
    Checking for g++                         : ok  
    Checking for node path                   : not found 
    Checking for node prefix                 : ok /Users/me/opt/node
    'configure' finished successfully (0.236s)

The following files will be added to your project

    hello-world
      + build
        + c4che
           - Release.cache.py
           - build.config.py
        - build.log

To actually build the project, run:

    node-waf build

This will output:

    Waf: Entering directory `/Users/me/node-bindings-guide/getting-started/build'
    [1/2] cxx: src/hello.cc -> build/Release/src/hello_1.o
    [2/2] cxx_link: build/Release/src/hello_1.o -> build/Release/hello.node
    Waf: Leaving directory `/Users/me/node-bindings-guide/getting-started/build'
    'build' finished successfully (0.439s)

The following files will be generated:

    hello-world
      + build
        + Release
           + src
              - hello_1.o
           - hello.node

To execute the code:

    node index.js

Output:

    world

Reference:

*   [nodejs addons](http://nodejs.org/api/addons.html)
*   [github / jchia / waf-example](https://github.com/jchia/waf-example)
*   [github / luismreis / node-openvg](https://github.com/luismreis/node-openvg)
