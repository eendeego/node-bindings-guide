# node-bindings-guide

You are looking at the _Node/V8 native bindings guide_ source. For the actual guide take a loot at [http://luismreis.github.com/node-bindings-guide/](http://luismreis.github.com/node-bindings-guide/).

The guide uses [Dinky theme](https://github.com/broccolini/dinky) and is build by the [github pages](http://pages.github.com/)'s [jekyll](https://github.com/mojombo/jekyll) site generator.

To build the site locally:

Use bundler to install jekyll:

    bundle

And then jekyll to generate the site:

    jekyll --safe --auto --server --base-url /node-bindings-guide/

To access it point your browser to `http://localhost:4000/node-bindings-guide/`.

The `_config.yml` file is already configured with the same settings as gh-pages.

For contributions, please check [contributing](https://github.com/luismreis/node-bindings-guide/blob/gh-pages/CONTRIBUTING.md).

------

Have fun building your own native libraries!
