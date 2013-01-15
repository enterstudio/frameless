# frameless

## introduction

[“frameless”](https://github.com/dscape/frameless) is a framework to write command line programs. “frameless” allows you to write command line programs that are easy to read, and fun to write.

thus, “frameless” is two things: (1) a set of decisions focused around how commands should work; and (2) a node.js module that demonstrates those decisions

the main design goal behind “frameless” is to allow you to migrate your day-to-day scripts to the command line with minimal effort

“frameless” is designed with hackers in mind so all the internals are deliberately exported. this allows fellow developers to tailor behavior if they disagree with the decisions made by the maintainers of the “frameless” itself, as well as perform necessary customization in style and security

the best way to get a feel for “frameless” is to simply look at an node.js program that uses the framework. for example, you can check the [usage examples](https://github.com/dscape/frameless/tree/master/usage) listed in the project’s repository

“frameless” is free software, available under the apache open source license (v2). see the [license](https://raw.github.com/dscape/frameless/master/license.md) for more information

## basics

### default options

commands are expected to respond consistently to options such as `version`, `yes`, `raw`, `verbose`, `help`, `setup`. “frameless” will perform all these operations and behave according to the [principle of least astonishment](http://en.wikipedia.org/wiki/Principle_of_least_astonishment). these default options are available and customizable in `frameless.reserved_options`. pretty print styles that correspond to these options can also be overriden

``` javascript
var frameless = require('frameless');

// assuming placement in `bin/` folder
frameless.version = require('../package').version;

frameless.main(function (opts) {
  frameless.ok(frameless.reserved_options);
});
```

### prompt

“frameless” will prompt the end user for options that are required but were not provided at run time. i.e. if you run “frameless” without specifying `--name alice` in the following script “frameless” will prompt you for the name

``` javascript
frameless.main('name', function (input) {
  frameless.info('hello ' + input.name);
});
```

“frameless” prompt is built on top of the [flatiron/prompt](https://github.com/flatiron/prompt) module. all the arguments to the main function (except the callback) are assumed to be either a string (i.e. a mandatory option) or an object that represents a [flatiron/prompt option object](https://github.com/flatiron/prompt#prompting-with-validation-default-values-and-more-complex-properties)

### the confirmation prompt

“frameless” supports “are you sure?” prompts

``` javascript
frameless.confirm(true);
```

this check can then be overridden by the end user by using the `-y` or `--yes` option

## persisting options across different requests

“frameless” enables programs to save and reuse the options specified in a request by exposing the `frameless.save` function. calling this function with `true` will make frameless automatically record the options inputed in a dot file located in your home directory

``` javascript 
frameless.save(true);

frameless.main('name', 'password', function (opts) {
  frameless.info(frameless.dotfile, 'dotfile');
  frameless.info(frameless.sensitive_options,  'sensitive');
});
```

special considerations are taken for saving options which are member of an internal array called `frameless.sensitive_options`. “frameless” will attempt to encrypt options that include any of the substrings listed in this array, using the `frameless.cypher_type` and `frameless.key`. “frameless” ships with a default key, but you are encouraged to change it in your program. “frameless” will also attempt to read the `~/.ssh/id_rsa` private key and use it as an encryption key whenever possible

“frameless” allows you to select which options should be saved. the default is to save the option once you called the `frameless.save` method

``` javascript 
frameless.save(true);

frameless.main('name',
  { name: 'password', required: true, save: false}, function (opts) {
  frameless.info(frameless.dotfile, 'dotfile');
  frameless.info(frameless.sensitive_options,  'sensitive');
});
```

### pretty printing

“frameless” exports all methods that format output presented to user. please check the source code’s `style` section for a better understanding of all functions you can customize (e.g. `frameless.PS1`)

### publishing your module with npm

tbd. blog post.

## apps built using frameless

* [ghcopy](https://github.com/dscape/ghcopy)

## license

copyright 2011 nuno job <nunojob.com> (oO)--',--

licensed under the apache license, version 2.0 (the "license");
you may not use this file except in compliance with the license.
you may obtain a copy of the license at

    http://www.apache.org/licenses/LICENSE-2.0

unless required by applicable law or agreed to in writing, software
distributed under the license is distributed on an "as is" basis,
without warranties or conditions of any kind, either express or implied.
see the license for the specific language governing permissions and
limitations under the license.
