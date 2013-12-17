---
title: When ceylon.test met meta-model
author: Tom&#225;&#353; Hradec
layout: blog
unique_id: blogpage
tab: blog
tags: [test, tools, progress]
---

Ceylon has support for unit testing since milestone four, 
but its functionality was pretty limited due lack of annotations and meta-model at that time.

Fortunately this is not true anymore! 
With first version of Ceylon was also released completely rewritten `ceylon.test` module.
So lets see what is new and how can be used now.


## Tests annotations

Tests are now declaratively marked with annotation `test`
and can be written like nullary top-level functions or methods inside top-level class, 
in case that you want to group multiple tests together.

Inside tests, assertions can be evaluated by using the language's `assert` statement 
or with the various _assert..._ functions, for example `assertEquals`, `assertThatException` etc.

<!-- try: -->
    class YodaTest() {
     
        test
        void shouldBeJedi() {
            assert(yoda is Jedi, 
                   yoda.midichloriansCount > 1k);
        }
        
        ...

Common initialization logic, which is same for several tests, 
can be placed into functions or methods and marked with annotations `beforeTest` or `afterTest`.
Test framework will invoke them automatically before or after each test in its scope.
So marked top-level functions will be invoked for each test in same package 
and marked methods will be invoked for each test in same class.

<!-- try: -->
    class DeathStarTest() {
     
        beforeTest
        void init() => station.chargeLasers();
     
        afterTest
        void dispose() => station.shutdownSystems();
        
        ...
     
Sometimes you want to temporarily disable a test or a group of tests. 
This can be done via the <code>ignore</code> annotation.
This way the test will not be executed, but will be covered in summary tests result. 
Ignore annotation can be used on concrete test, or class which contains test, or event on whole package or module.

<!-- try: -->
    test
    ignore("still not implemented")
    void shouldBeFasterThanLight() {


## Test command

Our command line toolset has been enhanced by the new command `ceylon test`, 
which allow simply execute tests in specified modules.

The following command will execute all tests in module `com.acme.foo/1.0.0`
and will print report about them to console.

<!-- try: -->
<!-- lang:bash -->
    $ ceylon test com.acme.foo/1.0.0

You can execute only particular tests with usage of `--test` option, 
where syntax for value is similar to declaration literals. 
The following examples will show you, how to execute tests only in specified package, class or function.

<!-- try: -->
<!-- lang:bash -->
    $ ceylon test --test='package com.acme.foo.bar' com.acme.foo/1.0.0
    $ ceylon test --test='class com.acme.foo.bar::Baz' com.acme.foo/1.0.0
    $ ceylon test --test='function com.acme.foo.bar::baz' com.acme.foo/1.0.0


More details about this command can be found  
[here](/documentation/current/reference/tool/ceylon/subcommands/ceylon-test.html).


## Next version

In upcoming version, we will introduce another improvements.

There will be test suite annotation, which allow combine several tests or test suites and run them together.

<!-- try: -->
    testSuite({`class YodaTest`,
               `class DarthVaderTest`,
               `function starOfDeathTestSuite`})
    shared void starwarsTestSuite() {}

    
Support for declaratively specify custom test listeners, which will be notified during test execution.    

<!-- try: -->
    testListeners({`class DependencyInjectionTestListener`,
                   `class TransactionalTestListener`})
    package com.acme;
    
    
And support for declaratively specified custom implementation of test executor, which is responsible for running tests.

<!-- try: -->
    testExecutor(`class ArquillianTestExecutor`)
    package com.acme;


_Please note, that these API is not final yet, and can change.
If you want to share with us with your thoughts about it, don't hesitate and [contact us](/community)._
